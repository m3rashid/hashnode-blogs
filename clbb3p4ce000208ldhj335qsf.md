# Creating your own blockchain network

#### Starting with the most basic question "What is Block Chain ?"

The blockchain is another revolutionary technology that can change the ways of the internet just like open-sourced software did. Just like them, blockchain will take up some time to cool off and get reasonably priced for everyone to use in modern developments.

It could take years to reach the real potential that lies within. As blockchain is a distributed P2P ledger system, anyone can see other users’ entries, but undoubtedly no one can alter it.

You can only update the blockchain using a [consensus algorithm](https://101blockchains.com/consensus-algorithms-blockchain/). So, when a new set of information gets uploaded to the system, no one can alter it. The blockchain will contain accurate and reliable information on the ledger.

The core idea behind blockchains is their decentralized nature. You will be fascinated by the fact of how it all works inside. Blockchain might sound simple, but inside there are a lot of protocols and algorithms that make it happen.

For more info, refer to this BlockChain Technology [Guide](https://101blockchains.com/ultimate-blockchain-technology-guide/)

![](https://101blockchains.com/wp-content/uploads/2018/07/How_Does_a_Blockchain_work-768x515.jpg.webp align="left")

![](https://101blockchains.com/wp-content/uploads/2020/01/How-to-Build-A-Blockchain-In-Python-768x768.png.webp align="left")

Picture Taken from [101blockchains](https://101blockchains.com/blockchain-infographics/)

#### Below I attach my code on How I created my blockchain network in flask (python) and [nodejs](https://nodejs.org/en/) (javascript)

```py
# blockchain.py
import json
from hashlib import sha256
from time import time


class Blockchain(object):
    def __init__(self):
        self.chain = []
        self.current_transactions = []
        self.new_block(previous_hash=1, proof=100)

    def proof_of_work(self, last_proof):
        # This is where the consensus algorithm is implemented
        proof = 0
        while self.valid_proof(last_proof, proof) is False:
            proof += 1
        return proof

    @staticmethod
    def valid_proof(last_proof, proof):
        # This validates the block
        guess = f'{last_proof}{proof}'.encode()
        guess_hash = sha256(guess).hexdigest()
        return guess_hash[:4] == "0000"

    def new_block(self, proof, previous_hash=None):
        # This creates new blocks 
        # and then adds to the existing chain
        block = {
            'index': len(self.chain) + 1,
            'timestamp': time(),
            'proof': proof,
            previous_hash: previous_hash or self.hash(self.chain[-1]),
        }
        # Set the current transaction list to empty.
        self.current_transactions = []
        self.chain.append(block)
        return block

    def new_transaction(self, sender, recipient, amount):
        # This adds a new transaction to already existing transactions
        # This creates a new transaction 
        # which will be sent to the next block.
        # It will contain three variables 
        # including sender, recipient and amount
        self.current_transactions.append(
            {
                'sender': sender,
                'recipient': recipient,
                'amount': amount,
            }
        )
        return self.last_block['index'] + 1

    @staticmethod
    def hash(block):
        # Used for hashing a block
        # The follow code will create a SHA - 256 block hash
        # and also ensure that the dictionary is ordered
        block_string = json.dumps(block, sort_keys=True).encode()
        return sha256(block_string).hexdigest()

    @property
    def last_block(self):
        # Calls and returns the last block of the chain
        return self.chain[-1]
```

```py
# main.py
from uuid import uuid4
from flask import Flask, request, jsonify
from blockchain import Blockchain

app = Flask(__name__)
node_identifier = str(uuid4()).replace('-', '')

# Initializing blockchain
blockchain = Blockchain()


@app.route('/mine', methods=['GET'])
def mine():
    # Here we make the proof of work algorithm work
    last_block = blockchain.last_block
    last_proof = last_block['proof']
    proof = blockchain.proof_of_work(last_proof)

    # rewarding the miner for his contribution. 
    # 0 specifies new coin has been mined
    blockchain.new_transaction(
        sender="0",
        recipient=node_identifier,
        amount=1,
    )

    # now create the new block and add it to the chain
    previous_hash = blockchain.hash(last_block)
    block = blockchain.new_block(proof, previous_hash)
    response = {
        'message': 'The new block has been forged',
        'index': block['index'],
        'transactions': block['transactions'],
        'proof': block['proof'],
        'previous_hash': block['previous_hash']
    }
    return jsonify(response), 200


@app.route('/transactions/new', methods=['POST'])
def new_transaction():
    values = request.get_json()
    # Checking if the required data is there or not
    required = ['sender', 'recipient', 'amount']
    if not all(k in values for k in required):
        return 'Missing values', 400

    # creating a new transaction
    index = blockchain.new_transaction(values['sender'], values['recipient'], values['amount'])
    response = {'message': f'Transaction is scheduled to be added to Block No. {index}'}
    return jsonify(response), 201


@app.route('/chain', methods=['GET'])
def full_chain():
    response = {
        'chain': blockchain.chain,
        'length': len(blockchain.chain)
    }
    return jsonify(response), 200


if __name__ == '__main__':
    app.run(host="0.0.0.0", port=5000)
```

```js
// blockchain.js

const uuid = require("uuid");
const crypto = require("crypto");

class BlockChain {
  constructor() {
    this.chain = [];
    this.currentTransactions = [];
    this.newBlock(1, 100);
  }

  proofOfWork(lastProof) {
    let proof = 0;
    while (this.validProof(lastProof, proof) === false) {
      proof++;
    }
    return proof;
  }

  validProof(lastProof, proof) {
    const guess = `${lastProof}${proof}`.toString();
    const hash = crypto
        .createHash("sha256")
        .update(guess)
        .digest("hex");
    return hash.startsWith("0000");
  }

  newBlock(proof, previousHash = null) {
    const block = {
      index: this.chain.length + 1,
      timestamp: Date.now(),
      proof: proof,
      previousHash: previousHash,
    };
    this.currentTransactions = [];
    this.chain.push(block);
    return block;
  }

  newTransaction(sender, recipient, amount) {
    this.currentTransactions.push({
      sender,
      recipient,
      amount,
    });
    return this.lastBlock().index + 1;
  }

  hash(block) {
    return crypto
      .createHash("sha256")
      .update(JSON.stringify(block))
      .digest("hex");
  }

  lastBlock() {
    return this.chain[this.chain.length - 1];
  }
}

module.exports = {
  blockChain: new BlockChain(),
  nodeIdentifier: uuid.v4().toString().replaceAll("-", ""),
};
```

```js
// controllers.js
const { blockChain, nodeIdentifier } = require("./blockchain");

const mine = (req, res) => {
  const lastBlock = blockChain.lastBlock();
  const lastProof = lastBlock.proof;
  const proof = blockChain.proofOfWork(lastProof);

  blockChain.newTransaction(0, nodeIdentifier, 1);

  const prevhash = blockChain.hash(lastBlock);
  const block = blockChain.newBlock(proof, prevhash);
  return res.status(200).json({
    ...block,
    message: "The new block has been forged",
  });
};

const newTransaction = (req, res) => {
  const { sender, recipient, amount } = req.body;
  if (!sender || !recipient || !amount) throw new Error("Invalid transaction");

  const index = blockChain.newTransaction(sender, recipient, amount);
  return res.status(201).json({
    message: "Transaction is scheduled to be added to Block No. " + index,
  });
};

const chain = (req, res) => {
  return res.status(200).json({
    chain: blockChain.chain,
    length: blockChain.chain.length,
  });
};

const makeSafe = (check) => (req, res, next) => {
  Promise.resolve(check(req, res, next)).catch(next);
};

module.exports = {
  mineRouter: makeSafe(mine),
  newTransactionRouter: makeSafe(newTransaction),
  chainRouter: makeSafe(chain),
};
```

```js
// index.js
const express = require("express");
const cors = require("cors");

const {
  chainRouter,
  mineRouter,
  newTransactionRouter,
} = require("./controllers");

const app = express();
app.use(express.json());
app.use(express.urlencoded({ extended: true }));
app.use(
  cors({
    credentials: true,
    origin: "http://localhost:3000",
    optionsSuccessStatus: 200,
  })
);

app.get("/mine", mineRouter);
app.post("/transactions/new", newTransactionRouter);
app.get("/chain", chainRouter);

app.all("/", (_, res) => {
  return res.json({ message: "Server is OK" });
});

// Global error handler
app.use((err, req, res, _) => {
  console.error(err);
  return res.status(500).json({
    message: err.message || "INTERNAL SERVER ERROR",
  });
});

process.on("uncaughtException", (error) => {
  console.error(error);
  process.exit(1);
});

const port = process.env.PORT || 5000;
app.listen(port, () => console.log(`Listening on port ${port}`));
```

Here is the complete repository on GitHub [https://github.com/m3rashid/blocked-chain](https://github.com/m3rashid/blocked-chain). Please give it a star and contribute to improvements