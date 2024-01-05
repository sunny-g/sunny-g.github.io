+++
title = "Quantum Computer-Proof Digital Signatures, Part 1 - Lamport Signatures"
+++

For my MVP (minimal viable product) project at [Hack Reactor](http://hackreactor.com), I developed a browser [library](https://github.com/sunny-g/lamport-merkle.js) that implements two quantum-computer-proof digital signature algorithms known as the [Lamport](https://en.wikipedia.org/wiki/Lamport_signature) and [Merkle signature schemes](https://en.wikipedia.org/wiki/Merkle_signature_scheme). In this post, I'll be going over the Lamport scheme and how it works.

# Keypair Structure

Unlike keypairs of traditional encryption algorithms, Lamport keys are not mathematically tied to one another nor are they derived from hard-to-solve problems like prime factorization (this is in fact the reason this algorithm is quantum computer-proof: so long as our secure hashing function can't have solving its inverse optimized by quantum computing, we should be safe). Our private key then is simply composed of 256 pairs of 32 byte numbers while the public key is made up of the hashes of each number:

```js
var LamportKeypair = function() {
  this._privKey = [];
  this.pubKey = [];
  this.used = false;

  for (var i = 0; i < 256; i++) {
    var num1 = random32ByteString();
    var num2 = random32ByteString();
    var hash1 = hash(num1);
    var hash2 = hash(num2);

    this._privKey.push([num1, num2]);
    this.pubKey.push([hash1, hash2]);
  }
};
```

# Message Signing

To sign a message with this key, we only have to do two things:

Generate a 32 byte (256 bit) hash of the message.
For each bit in the message, publish either the 1st or 2nd random number of that bit's corresponding pair of numbers within our private key.
These steps end up looking like this:

```js
LamportKeypair.prototype.sign = function(msg) {
  var msgHash = hash(msg);
  var signature = [];

  var that = this;
  // this iterates over every bit in a hash string
  // callback takes the bit and its index in the string
  eachBit(msgHash, function(bit, bitIdx) {
    signature.push( that._privKey[bitIdx][bit] );
  });
  return signature;
};
```

But wait, if we publish the numbers in our private key, doesn't that defeat the purpose of making it private?

Well yes, and this highlights the usability weakness of this signature scheme: we can only use each keypair once. The workaround for this is to incorporate multiple keys into a Merkle tree, but that's a topic for a later post.

# Message Verification

To verify the message, all we need to do now is check to see that every number in our signature hashes to one of the values in its corresponding pair in our public key:

```js
LamportKeypair.prototype.verify = function(msg, signature) {
  var msgHash = hash(msg);
  var authentic = true;

  var that = this;
  eachBit(msgHash, function(bit, bitIdx) {
    if (hash(signature[bitIdx]) !== that.pubKey[bitIdx][bit]) {
      authentic = false;
    }
  });
  return authentic;
};
```

# Why This Works

To be a valid digital signature algorithm, we have to satisfy one main condition (and maybe a few others that I'm forgetting): *the public key cannot leak any information about the private key*. Since our public key only contains the hashes of these random numbers (and since hash functions can't be run in reverse, that is, from a hash tell us the input), we satisfy that condition and can now know with certainty one thing: **the only person who could have published the numbers in the signature and the hashes in the public key is the same person who possessed all of the random numbers of the private key in the first place.**

I hope this post has piqued your interest in non-traditional encryption schemes; I have a [companion post](http://blog.sunnyg.io/2014/12/13/merkle-key-trees-and-signature-scheme/) about how Merkle trees can mitigate the one-time-expiry property of Lamport signatures. If you want to check out my repo, you can find it (and star it hopefully?) [here](https://github.com/sunny-g/lamport-merkle.js). Thanks for reading!
