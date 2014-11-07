# envconfig

[![Build Status](https://travis-ci.org/kelseyhightower/envconfig.png)](https://travis-ci.org/kelseyhightower/envconfig)

```Go
import "github.com/kelseyhightower/envconfig"
```

## Documentation

See [godoc](http://godoc.org/github.com/kelseyhightower/envconfig)

## Usage

Set some environment variables:

```Bash
export MYAPP_DEBUG=false
export MYAPP_PORT=8080
export MYAPP_USER=Kelsey
export MYAPP_RATE="0.5"
```

Write some code:

```Go
package main

import (
    "fmt"
    "log"

    "github.com/kelseyhightower/envconfig"
)

type Specification struct {
    Debug bool
    Port  int
    User  string
    Rate  float32
}

func main() {
    var s Specification
    err := envconfig.Process("myapp", &s)
    if err != nil {
        log.Fatal(err.Error())
    }
    format := "Debug: %v\nPort: %d\nUser: %s\nRate: %f\n"
    _, err = fmt.Printf(format, s.Debug, s.Port, s.User, s.Rate)
    if err != nil {
        log.Fatal(err.Error())
    }
}
```

Results:

```Bash
Debug: false
Port: 8080
User: Kelsey
Rate: 0.500000
```

## Struct Tag Support

Envconfig supports the use of struct tags to specify alternate
environment variables.

For example, consider the following struct:

```Go
type Specification struct {
    MultiWordVar `envconfig:"multi_word_var"`
}
```

Whereas before, the value for `MultiWordVar` would have been populated
with `MYAPP_MULTIWORDVAR`, it will now be populated with
`MYAPP_MULTI_WORD_VAR`.

```Bash
export MYAPP_MULTI_WORD_VAR="this will be the value"

# export MYAPP_MULTIWORDVAR="and this will not"
```

## Nested structs

```Go
type Specification struct {
    Nested Nested
}

type Nested struct {
    Value string
}
```

Specification.Nested.Value will be populated if envconfig finds a variable
named `MYAPP_NESTED_VALUE`. Be careful with values set before `Process()` because
the nested struct will be overwritten with a new instance of its type.

## Maps and Slices

```Go
type Specification struct {
    Slice []string
    Map   map[string]string    
}
```

```Bash
export MYAPP_SLICE="foo,bar,baz"
export MYAPP_MAP="a:1,b:2,c:3"
```
```Go
Specification.Slice == []string{"foo", "bar", "baz"}
Specification.Map == map[string]string{"a": "1", "b": "2", "c": "3"}
```

* Currently, only string-related maps and slices are supported

