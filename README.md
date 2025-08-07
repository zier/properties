# Properties Library

A Go library for reading and writing Java-style properties files with support for recursive property expansion and struct decoding.

## Features

- Read properties from files, URLs, strings, and maps
- Write properties to files with comment preservation
- Type-safe property access with default values
- Struct decoding with reflection and struct tags
- Property expansion with `${key}` expressions
- Support for UTF-8 and ISO-8859-1 encodings
- **Modified behavior for nested structs with empty properties tags**

## Installation

```bash
go get github.com/zier/properties
```

## Basic Usage

### Loading Properties

```go
package main

import (
    "fmt"
    "github.com/zier/properties"
)

func main() {
    // From string
    p := properties.MustLoadString(`
name=John Doe
age=30
active=true
score=95.5
`)

    // Access values with defaults
    name := p.GetString("name", "Unknown")
    age := p.GetInt("age", 0)
    active := p.GetBool("active", false)
    score := p.GetFloat64("score", 0.0)

    fmt.Printf("Name: %s, Age: %d, Active: %t, Score: %.1f\n",
               name, age, active, score)
}
```

### Complex Nested Example

```go
type Server struct {
    Host string
    Port int
}

type Database struct {
    Driver   string
    Host     string
    Port     int
    Name     string
}

type AppConfig struct {
    Name     string
    Version  string
    Server   Server   `properties:"server"`     // Prefix: "server."
    Database Database `properties:""`           // No prefix (modified behavior)
    Debug    bool     `properties:",default=false"`
}

func main() {
    props := properties.MustLoadString(`
Name=WebApp
Version=1.0.0
server.Host=0.0.0.0
server.Port=8080
Driver=postgres
Host=db.example.com
Port=5432
Name=myapp_db
Debug=true
`)

    var config AppConfig
    err := props.Decode(&config)
    if err != nil {
        log.Fatal(err)
    }

    fmt.Printf("App: %s v%s\n", config.Name, config.Version)
    fmt.Printf("Server: %s:%d\n", config.Server.Host, config.Server.Port)
    fmt.Printf("Database: %s://%s:%d/%s\n",
               config.Database.Driver,
               config.Database.Host,
               config.Database.Port,
               config.Database.Name)
    fmt.Printf("Debug: %t\n", config.Debug)
}
```

## Fork Differences

This fork modifies the original behavior in the following way:

### Original Library Behavior

```go
type Outer struct {
    Inner Inner `properties:""`  // Same as no tag
}
// Requires properties: Inner.Field1, Inner.Field2
```

### This Fork's Behavior

```go
type Outer struct {
    Inner Inner `properties:""`  // Empty tag enables no-prefix mode
}
// Requires properties: Field1, Field2 (no prefix)
```

**Use Cases for No-Prefix Nested Structs:**

- Flattening configuration structures
- Legacy property file compatibility
- Simpler property naming schemes
- Avoiding deep nesting in property keys

## Best Practices

1. **Use defaults for optional fields**: Always provide sensible defaults
2. **Validate decoded structs**: Check business logic constraints after decoding
3. **Use custom field names**: Make property keys more readable
4. **Handle errors gracefully**: Don't use Must\* variants in production without recovery
5. **Choose appropriate nesting**: Use `properties:""` sparingly for flat structures

## Contributing

This is a fork of the original [magiconair/properties](https://github.com/magiconair/properties) library with modifications for nested struct handling.

## License

BSD-style license (see LICENSE file)
