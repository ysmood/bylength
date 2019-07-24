# byframe

[![GoDoc](https://godoc.org/github.com/ysmood/byframe?status.svg)](http://godoc.org/github.com/ysmood/byframe)
[![Build Status](https://travis-ci.org/ysmood/byframe.svg?branch=master)](https://travis-ci.org/ysmood/byframe)

It's a low overhead length header format with dynamic header length.
So you don't waste resource on the header itself when framing data.

- With 1 bytes of header it can describe the length of 128B.

- With 5 bytes of header it can describe the length of 32GB.

- No dependency

This lib also contains functions to only encode and decode the header,
so you have the full flexibility to decide how to use it,
such as TCP framing, database indexing, etc.

## Format

The data is byte stream, each frame has two parts the header and body.

```txt
|     frame     |
| header | body |
```

### Header

Each byte (8 bits) in the header has two parts, "continue" and "fraction":

```txt
byte index |    0     | 1 2 3 4 5 6 7 |
sections   | continue |   fraction    |
```

If the "continue" is 0, the header ends.
If the "continue" is 1, then the followed byte should also be part of the header.

Sum all the fractions together, we will get the size of the message.

For example:

```txt
|                         frame                              |
|                      header                         | body |
| continue |   fraction    | continue |   fraction    |      |
|    0     | 1 0 0 0 0 0 0 |    1     | 1 1 0 1 0 0 0 | ...  |
```

So the size of the body is 0b1101000,1000000 bytes.