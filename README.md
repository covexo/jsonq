
This is a fork of github.com/jmoiron/jsonq which seems not to be maintained anymore. This fork extends the original project in a way that allows to query yaml data as well as json data.

---

# yamlq
Simplify your golang yaml/json usage by extracting fields or items from arrays and objects with a simple, hierarchical query. [API Documentation](http://godoc.org/github.com/jmoiron/jsonq) on godoc.org.

This package is meant to make working with complex feeds a bit more easy. If you have simple feeds you want to model with struct types, check out [jflect](http://github.com/str1ngs/jflect), which will create struct definitions given a yaml or json document.

# installing

```
go get github.com/covexo/yamlq
```

# usage

Given some json data like:

```javascript
{
	"foo": 1,
	"bar": 2,
	"test": "Hello, world!",
	"baz": 123.1,
	"array": [
		{"foo": 1},
		{"bar": 2},
		{"baz": 3}
	],
	"subobj": {
		"foo": 1,
		"subarray": [1,2,3],
		"subsubobj": {
			"bar": 2,
			"baz": 3,
			"array": ["hello", "world"]
		}
	},
	"bool": true
}
```

Decode it into a `map[interface{}]interface{}`:

```go
import (
	"strings"
	"encoding/json"
	"github.com/covexo/yamlq"
)

data := map[string]interface{}{}
dec := json.NewDecoder(strings.NewReader(jsonstring))
dec.Decode(&data)
yq := yamlq.NewQuery(data)
```

From here, you can query along different keys and indexes:

```go
// data["foo"] -> 1
yq.Int("foo")

// data["subobj"]["subarray"][1] -> 2
yq.Int("subobj", "subarray", "1")

// data["subobj"]["subarray"]["array"][0] -> "hello"
yq.String("subobj", "subsubobj", "array", "0")

// data["subobj"] -> map[string]interface{}{"subobj": ...}
obj, err := yq.Object("subobj")
```

Missing keys, out of bounds indexes, and type failures will return errors.
For simplicity, integer keys (ie, {"0": "zero"}) are inaccessible
by `yamlq` as integer strings are assumed to be array indexes.

The `Int` and `Float` methods will attempt to parse numbers from string
values to ease the use of many real world feeds which deliver numbers as strings.

