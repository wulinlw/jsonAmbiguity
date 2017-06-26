# jsonAmbiguity
golang json.Unmarshal(), string or int64 |float, i don't care ~

Edit from go 1.8.3 "encoding/json" package  
Just modify json.Unmarshal()  
Package name does't change, if you use this packge with Official pakeage  
Please set alias name, see example~!

## install  
> go get github.com/wulinlw/jsonAmbiguity

## example
```golang
package main

import (
	"encoding/json"
	"fmt"

	jsonAmbiguity "github.com/wulinlw/jsonAmbiguity"
)

type Role struct {
	Attr struct {
		Lv    int64 `json:"lv"`
		Money int64 `json:"money"`
	} `json:"attr"`
	Net struct {
		Ip  string `json:"ip"`
		Mac string `json:"mac"`
	} `json:"net"`
	C int64 `json:"c"`
}

func main() {
	//Official pakeage "encoding/json"
	//mac was defined string in struct, nevertheless mac type Actual is int
	str := `{"net":{"ip":"8.8.8.8","mac":0},"attr":{"lv":15,"money":3},"c":1}`
	logData := &Role{}
	err := json.Unmarshal([]byte(str), logData)
	fmt.Println(logData)
	fmt.Println(err)
	//output:
	//&{{0 0} {8.8.8.8 } 0}
	//json: cannot unmarshal number into Go struct field .mac of type string
	//when error happend, json package stop decodeing, so, I loss all of value
	//behind error position

	//mac was defined string in struct, nevertheless mac type Actual is int
	str2 := `{"attr":{"lv":15,"money":3},"net":{"ip":"8.8.8.8","mac":0},"c":1}`
	logData2 := &Role{}
	jsonAmbiguity.Unmarshal([]byte(str2), logData2)
	fmt.Println(logData2)
	//output:
	//&{{15 3} {8.8.8.8 0} 1}

	//money was defined int64 in struct, nevertheless money type Actual is string
	str3 := `{"attr":{"lv":15,"money":"3"},"net":{"ip":"8.8.8.8","mac":0},"c":1}`
	logData3 := &Role{}
	jsonAmbiguity.Unmarshal([]byte(str3), logData3)
	fmt.Println(logData3)
	//output:
	//&{{15 3} {8.8.8.8 0} 1}

	//money was defined int64 in struct, nevertheless money type Actual is float
	str4 := `{"attr":{"lv":15,"money":3.82},"net":{"ip":"8.8.8.8","mac":0},"c":1}`
	logData4 := &Role{}
	err = jsonAmbiguity.Unmarshal([]byte(str4), logData4)
	fmt.Println(logData4)
	//output:
	//&{{15 3} {8.8.8.8 0} 1}
}
```



