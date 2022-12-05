# Benchmarking Nodejs and Golang Servers

In this test, I made two servers, one with node (express) and the other with golang (gin) and tested out their performance in handling requests. I used Apache bench for this and logged the results into a log file at the end. Let me give you a glimpse of the servers made to test out.

Make a directory named `nodejs` and `yarn init` inside it, this will spit out a `package.json` file which will contain all the dependencies of the app. Next, create a folder to collect all the logs we are going to do afterwards. I made a folder named `express/logs` to store all the logs. Then I gave sufficient permissions to the log files to be able to store logs inside it (just a UNIX thing). Then I wrote a basic javascript file named `express.js` which will act as the server in our case.

```bash
mkdir nodejs && cd nodejs
yarn init -y && yarn add express
mkdir express && cd express && mkdir logs && cd logs
touch test1.log test2.log test3.log
chmod 775 test1.log test2.log test3.log
```

```javascript
// express.js
const express = require("express");
const app = express();

app.use(express.urlencoded({ extended: true }));
app.use(express.json());

let hitCount = 0;

app.get("/", (req, res) => {
  hitCount += 1;

  const healthcheck = {
    uptime: process.uptime(),
    message: "OK",
    timestamp: Date.now(),
  };
  console.log("HIT:", hitCount, healthcheck);
  console.log();
  try {
    return res.status(200).json({ message: "Hello World" });
  } catch (error) {
    healthcheck.message = error;
    return res.status(503).send();
  }
});

app.listen(3000, () => console.log("Running on port 3000"));
```

Pretty much everything goes the same in the case of Golang. I created a folder `golang` and `go mod init` inside it. This will create a `go.mod` file to store all the dependencies and their versions for the project. A [github.com](http://github.com) remote URL is given to be able to convert it to a module and be able to store it on GitHub (just in case). This will enable other people to be able to download our code as a module and use it in their codebases. Then as earlier, we created log files and gave them relevant UNIX permissions. Then I wrote a basic go file named `gin.go` which will act as the server in our case.

```bash
mkdir golang && cd golang
go mod init github.com/m3rashid/load-testing-golang
go get github.com/gin-gonic/gin
mkdir gin && cd gin && mkdir logs && cd logs
touch test1.log test2.log test3.log
chmod 775 test1.log test2.log test3.log
```

```go
// gin.go
package main

import (
	"fmt"
	"net/http"
	"time"

	"github.com/gin-gonic/gin"
)

var startTime time.Time

func uptime() time.Duration {
	return time.Since(startTime)
}

func init() {
	startTime = time.Now()
}

type HealthCheck struct {
	uptime    string
	message   string
	timestamp string
}

func main() {
	r := gin.Default()
	r.GET("/", func(c *gin.Context) {

		healthCheck := HealthCheck{
			uptime:    uptime().String(),
			message:   "OK",
			timestamp: time.Now().Format(time.RFC3339),
		}

		fmt.Printf("HealthCheck: %+v", healthCheck)

		c.JSON(http.StatusOK, gin.H{
			"message": "Hello World",
		})
	})
	r.Run(":4000")
}
```

Since golang is a compiled language, I compiled it to a binary by running `go build golang/gin.go`. In the end, I did three tests, and the load/concurrency goes as follows. Also, One may argue that Nodejs is single-threaded and hence cannot use all the available resources of the host machine, so just for those guys, I tested dedicated using PM2 on cluster mode at the max number of connections.

| test | Load(total req) | Concurrency (req/sec) |
| --- | --- | --- |
| 1 | 500 | 100 |
| 2 | 10000 | 500 |
| 3 | 100000 | 1000 |

Then I tested the servers, The results came as follows.

### Test 1

| Parameter | NodeJs | Go | NodeJs (PM2) |
| --- | --- | --- | --- |
| Concurrency Level: | 100 | 100 | 100 |
| Time taken for tests \[s\]: | 0.411 | 0.096 | 0.746 |
| Complete requests: | 500 | 500 | 500 |
| Failed requests: | 41 | 0 | 37 |
| Total transferred \[bytes\]: | 135456 | 74000 | 135961 |
| HTML transferred \[bytes\]: | 31956 | 12500 | 32461 |
| Mean request \[#/sec\]: | 1216.91 | 5230.84 | 669.86 |
| Time per request \[ms\]: | 82.176 | 19.117 | 149.285 |
| Time per request (mean, across all concurrent requests) \[ms\]: | 0.822 | 0.191 | 1.493 |
| Transfer rate \[KB/s\]: | 321.95 | 756.02 | 177.88 |

| Param (NodeJs) | min | mean | \[+/-sd\] | median | max |
| --- | --- | --- | --- | --- | --- |
| Connect: | 0 | 4 | 2.7 | 4 | 11 |
| Processing: | 4 | 72 | 22.3 | 72 | 116 |
| Waiting: | 3 | 45 | 18.4 | 47 | 84 |
| Total: | 14 | 76 | 20.9 | 73 | 116 |

| Param (Go) | min | mean | \[+/-sd\] | median | max |
| --- | --- | --- | --- | --- | --- |
| Connect: | 0 | 6 | 1.6 | 5 | 11 |
| Processing: | 3 | 12 | 9.4 | 8 | 40 |
| Waiting: | 0 | 9 | 9.2 | 5 | 37 |
| Total: | 7 | 18 | 9.7 | 13 | 44 |

| Param (NodeJs, PM2) | min | mean | \[+/-sd\] | median | max |
| --- | --- | --- | --- | --- | --- |
| Connect: | 0 | 1 | 1.4 | 0 | 5 |
| Processing: | 10 | 131 | 25.0 | 126 | 205 |
| Waiting: | 10 | 130 | 25.2 | 126 | 204 |
| Total: | 14 | 132 | 24.6 | 127 | 205 |

![](https://camo.githubusercontent.com/704ecd24b4e8269facb88fa3a9f88382ae33a42e40a1f1bbdb6b949233ab8114/68747470733a2f2f64726976652e676f6f676c652e636f6d2f75633f6578706f72743d766965772669643d314263517848744c384d4462754373754d343932746569616137424c4a7170716f align="left")

### Test 2

| Parameter | NodeJs | Go | NodeJs (PM2) |
| --- | --- | --- | --- |
| Concurrency Level: | 100 | 100 | 100 |
| Time taken for tests \[s\]: | 6.441 | 1.359 | 6.776 |
| Complete requests: | 10000 | 10000 | 10000 |
| Failed requests: | 993 | 0 | 1024 |
| Total transferred \[bytes\]: | 2708897 | 1480000 | 2718865 |
| HTML transferred \[bytes\]: | 638897 | 250000 | 648865 |
| Mean request \[#/sec\]: | 1552.45 | 7358.63 | 1475.73 |
| Time per request \[ms\]: | 64.414 | 13.589 | 67.763 |
| Time per request (mean, across all concurrent requests) \[ms\]: | 0.644 | 0.136 | 0.678 |
| Transfer rate \[KB/s\]: | 410.69 | 1063.55 | 391.83 |

| Param (NodeJs) | min | mean | \[+/-sd\] | median | max |
| --- | --- | --- | --- | --- | --- |
| Connect: | 0 | 2 | 1.0 | 2 | 10 |
| Processing: | 9 | 62 | 7.8 | 62 | 97 |
| Waiting: | 1 | 48 | 9.4 | 48 | 92 |
| Total: | 9 | 64 | 7.8 | 64 | 98 |

| Param (Go) | min | mean | \[+/-sd\] | median | max |
| --- | --- | --- | --- | --- | --- |
| Connect: | 0 | 5 | 1.3 | 5 | 11 |
| Processing: | 1 | 8 | 5.1 | 7 | 56 |
| Waiting: | 0 | 6 | 5.1 | 5 | 54 |
| Total: | 1 | 13 | 4.8 | 13 | 59 |

| Param (NodeJs, PM2) | min | mean | \[+/-sd\] | median | max |
| --- | --- | --- | --- | --- | --- |
| Connect: | 0 | 0 | 0.5 | 0 | 6 |
| Processing: | 7 | 67 | 7.0 | 67 | 125 |
| Waiting: | 3 | 67 | 6.9 | 66 | 124 |
| Total: | 9 | 67 | 6.8 | 67 | 125 |

![](https://camo.githubusercontent.com/9f7a0098ed013bd22838f87d5a1989f10cafaf8fafb352f5125e3d442c007531/68747470733a2f2f64726976652e676f6f676c652e636f6d2f75633f6578706f72743d766965772669643d316a3474535046504961496d72363849676b6b4b6638484237446c744a5650524c align="left")

### Test 3

| Parameter | NodeJs Server | Go Server | NodeJs (PM2) |
| --- | --- | --- | --- |
| Concurrency Level: | 500 | 500 | 500 |
| Time taken for tests \[s\]: | 64.228 | 12.523 | 54.266 |
| Complete requests: | 100000 | 100000 | 100000 |
| Failed requests: | 91134 | 0 | 10001 |
| Total transferred \[bytes\]: | 27188891 | 14800000 | 27188887 |
| HTML transferred \[bytes\]: | 6488891 | 2500000 | 6488887 |
| Mean request \[#/sec\]: | 1556.96 | 7985.54 | 1842.76 |
| Time per request \[ms\]: | 321.139 | 62.613 | 271.332 |
| Time per request (mean, across all concurrent requests) \[ms\]: | 0.642 | 0.125 | 0.543 |
| Transfer rate \[KB/s\]: | 413.40 | 1154.16 | 489.28 |

| Param (NodeJs) | min | mean | \[+/-sd\] | median | max |
| --- | --- | --- | --- | --- | --- |
| Connect: | 0 | 10 | 5.5 | 9 | 40 |
| Processing: | 43 | 311 | 27.9 | 307 | 622 |
| Waiting: | 3 | 234 | 44.7 | 239 | 460 |
| Total: | 43 | 320 | 28.4 | 316 | 632 |

| Param (Go) | min | mean | \[+/-sd\] | median | max |
| --- | --- | --- | --- | --- | --- |
| Connect: | 0 | 27 | 6.3 | 27 | 50 |
| Processing: | 0 | 35 | 9.7 | 34 | 170 |
| Waiting: | 0 | 26 | 9.1 | 24 | 158 |
| Total: | 0 | 62 | 9.4 | 62 | 198 |

| Param (NodeJs, PM2) | min | mean | \[+/-sd\] | median | max |
| --- | --- | --- | --- | --- | --- |
| Connect: | 0 | 0 | 1.5 | 0 | 26 |
| Processing: | 15 | 270 | 27.8 | 270 | 400 |
| Waiting: | 2 | 270 | 27.8 | 269 | 399 |
| Total: | 28 | 271 | 27.3 | 270 | 400 |

![](https://camo.githubusercontent.com/8c3e052b4318a2dfe0c3e2ebcdebaed5180960d398030a4d27634600834df7b5/68747470733a2f2f64726976652e676f6f676c652e636f6d2f75633f6578706f72743d766965772669643d31767a4f7655557a354f506651752d6b43397a795955426d596c61486e6a4a5f49 align="left")

### Results

| Param | Golang | NodeJs | NodeJs (PM2) |
| --- | --- | --- | --- |
| Failed Requests: | Test 1: 0% | Test 1: 8.2% | Test 1: 7.4% |
| \- | Test 2: 0% | Test 2: 9.93% | Test 2: 10.24 |
| \- | Test 3: 0% | Test 3: 91.13% | Test 3: 10.00 |
| Time per request (mean over all tests): | 31.77ms | 155.90ms | 162.79ms |
| Requests per second (mean over all tests): | 6858.34 | 1442.11 | 1329.45 |
| Transfer rate (mean over all tests): | 991.24 KB/s | 382.01 KB/s | 353.00 KB/s |
| Waiting time (mean over all tests): | 13.67ms | 109ms | 155.67ms |

### Inference

In each of the cases above, Golang simply rocks in each of the parameters above. The main parameters of our concern are :

*   Failed Requests (Most important): Even in the test 1 category, Nodejs fails to deliver the good performance required for a server-side application.
    
*   Number of bytes transferred (should be lesser as both the servers are sending the same thing)
    
*   Requests per second (determines the actual concurrency throughput): Should be maximum
    
*   Time per request (Golang has an edge here because it is compiled and nodejs (javascript) is interpreted): Should be minimum. Responses must be as quick as possible to provide a good user experience
    
*   Transfer rate (how much the server is capable of): Even if both the test is done on the same network and with the same bandwidth, Golang provides faster delivery (not by a very high margin)
    
*   Waiting time (ms, Average time to wait before handling the request): Requests need to be fulfilled as quickly as possible, and waiting time must be less as a whole