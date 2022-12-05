# Webhooks

Webhooks are one way that apps can send automated messages or information to other apps. It's how PayPal tells your accounting app when your clients pay you, how Twilio routes phone calls to your number, and how WooCommerce can notify you about new orders in Slack.

They're a simple way your online accounts can "speak" to each other and get notified automatically when something new happens. In many cases, you'll need to know how to use webhooks if you want to automatically push data from one app to another.

Webhooks are automated messages sent from apps when something happens. They have a message—or payload—and are sent to a unique URL—essentially the app's phone number or address. Webhooks are almost always faster than polling and require less work on your end.

Poling is asking for the resource every time. For example, in a chat application based on poling, the clients keep on pinging/asking the server if there are any new messages available for it.

Let's Say you want to make an invoice for an order. The app that creates this invoice is on the receiving end—it’s the app that needs the order data.

You'd first open your invoice app, make an invoice template, and copy its webhook URL—something like [yourapp.com/data/12345](http://yourapp.com/data/12345). Then open your eCommerce store app, and add that URL to its webhook settings. That URL is your invoice app's phone number, essentially. If another app pings that URL (or if you enter the URL in your browser's address bar), the app will notice that someone is trying to send it data.

Your eCommerce store got the order and knows it needs to send the details to [yourapp.com/data/12345](http://yourapp.com/data/12345). It then writes the order in a serialization format. The simplest of those formats is called “form-encoded”, and means your customer's order would look something like this: `Customer=bob&value=10.00&item=paper`

That's the simple version. Technically, webhooks are "user-defined callbacks made with HTTP". Webhooks are data and executable commands sent from one app to another over HTTP instead of through the command line in your computer, formatted in XML, JSON, or form-encoded serialization. They're called webhooks since they're software hooks—or functions that run when something happens—that work over the web. And they're typically secured through obscurity—each user of an application gets a unique, random URL to send webhook data to—though they can optionally be secured with a key or signature.

Webhooks typically are used to connect two different applications. When an event happens on the trigger application, it serializes data about that event and sends it to a webhook URL from the action application—the one you want to do something with based on the data from the first application. The action application can then send a callback message, often with an HTTP status code like 302 to let the trigger application know if the data was received successfully or 404 if not.

Webhooks are similar to APIs but simpler. An API is a full language for an app with functions or calls to add, edit, and retrieve data. The difference is, with an API, you have to do the work yourself. If you build an application that connects to another with an API, your application will need to have ways to ask the other app for new data when it needs it. Webhooks, on the other hand, are for one specific part of an app, and they're automated. You might have a webhook just for new contacts—and whenever a new contact is added, the application will push the data to the other application's webhooks URL automatically. It's a simple, one-to-one connection that runs automatically.

That's all about webhooks, comment down your thoughts and suggestions.