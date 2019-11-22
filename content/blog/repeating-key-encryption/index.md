---
title: Repeating Key Encryption in Ruby
date: "2019-11-22T16:54:57-0500"
description: "Bitwise operations in Ruby learned while implementing repeating key encryption"
---
In my spare time I like to work on some encryption challenges provided by [cryptopals](https://cryptopals.com/). In working through some of the earlier challenges I realised that I had never tried to manipulate data at a bit or byte level in Ruby, mainly because I was never required to in the work that I do. One of the challenges requires you to implement repeating key encryption, which is where I encountered the need to work on strings on a bitwise basis. Repeating key encryption is an XOR cipher that works by XORing each bit of the phrase against the corresponding bit of the key. XORing is simply taking the binary representation of values and setting those values that match as '0' and those that differ as '1'. This can be seen in the following:

```ruby
# String ASCII character values
a = "a".ord # => 97
z = "Z".ord # => 90

# Binary representation of these character values
# Integer#to_s can take an argument in the inclusive range of 2 - 32 and will output a string in that base
a.to_s(2) # => "1100001"
z.to_s(2) # => "1011010"

# XORed value
# '^' is the bitwise XOR operation
xor = a ^ z # => 59

# Binary representation
xor.to_s(2) # => "0111011" (I added the leading '0' for clarity of the XOR operation)
```

Let's now encrypt something. First, we need a phrase to encrypt:

```ruby
phrase = "This is something we can encrypt" # => "This is something we can encrypt"
```

We also need a key:

```ruby
key = "hunter2" # => "*******"
```

Conceptually this works in the following way:

```
T h i s   i s   s o m e t h i n g   w e   c a n   e n c r y p t
| | | | | | | | | | | | | | | | | | | | | | | | | | | | | | | |
h u n t e r 2 h u n t e r 2 h u n t e r 2 h u n t e r 2 h u n t
```

Each character in the top line will be XORed against the character below it. The repeating key in the bottom line shows us where the name for this form of encryption comes from. We can encrypt in Ruby with the following code:

```ruby
encrypted_phrase = phrase.bytes.map.with_index do |char, index|
  char ^ key.bytes[index % key.length]
end.map(&:chr).join
# => "<\x1D\a\aE\eAH\x06\x01\x19\x00\x06Z\x01\e\tT\x12\x17\x12\v\x14\x00T\x00\x1CQ\x1A\f\x1E\x00"
```

And decrypt by doing the opposite:

```ruby
encrypted_phrase.bytes.map.with_index do |char, index|
  char ^ key.bytes[index % key.length]
end.map(&:chr).join
# => "This is something we can encrypt"
```

Some notes on what is performed above:

- `String#bytes` will break a string into its bytes and return it in an array. Fortunately for us each character represents a byte
- `index % key.length` returns the remainder of `index / key.length` which is always less than `key.length`. This lets us repeatedly iterate over the key while we iterate over the phrase we're encrypting
- `Integer#chr` returns the ASCII representation of an integer. This only works in the ASCII value range (0-255)

An XOR cipher is an example of symmetric encryption as the key is both used to encrypt and decrypt. In practice, you should assume that anyone or anything sharing a symmetric encryption key have the same privileges i.e. don't encrypt anything with that key that you don't want someone else with the key to see. This also brings in questions of key management. Finally, an XOR cipher is not a secure form of encryption as it is vulnerable to frequency analysis attacks that guess the key from the frequency of the values returned in the output.
