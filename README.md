# iso8583
A golang implementation to marshal and unmarshal iso8583 message.

[![Build Status](https://travis-ci.org/ideazxy/iso8583.svg?branch=master)](https://travis-ci.org/ideazxy/iso8583)

### Example

```go
package main

import (
	"fmt"

	"github.com/ideazxy/iso8583"
)

type Data struct {
	No   *iso8583.Numeric      `field:"3" length:"6" encode:"bcd"`
	Oper *iso8583.Numeric      `field:"26" length:"2" encode:"ascii"`
	Ret  *iso8583.Alphanumeric `field:"39" length:"2"`
	Sn   *iso8583.Llvar        `field:"45" length:"23" encode:"bcd,ascii"`
	Info *iso8583.Lllvar       `field:"46" length:"42" encode:"bcd,ascii"`
	Mac  *iso8583.Binary       `field:"64" length:"8"`
}

func main() {
	data := &Data{
		No:   iso8583.NewNumeric("001111"),
		Oper: iso8583.NewNumeric("22"),
		Ret:  iso8583.NewAlphanumeric("ok"),
		Sn:   iso8583.NewLlvar([]byte("abc001")),
		Info: iso8583.NewLllvar([]byte("你好 golang!")),
		Mac:  iso8583.NewBinary([]byte("a1s2d3f4")),
	}
	msg := iso8583.NewMessage("0800", data)
	msg.MtiEncode = iso8583.BCD
	b, err := msg.Bytes()
	if err != nil {
		fmt.Println(err.Error())
	}
	fmt.Printf("% x\n", b)
}
```

Then you will get:

```
08 00 20 00 00 40 02 0c 00 01 00 11 11 32 32 6f 6b 06 61 62 63 30 30 31 00 14 e4 bd a0 e5 a5 bd 20 67 6f 6c 61 6e 67 21 61 31 73 32 64 33 66 34
```