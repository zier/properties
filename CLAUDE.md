# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a Go library for reading and writing Java-style properties files with support for recursive property expansion. The library handles both ISO-8859-1 and UTF-8 encodings and provides type-safe property access with struct decoding capabilities.

## Common Development Commands

```bash
# Run tests
go test ./...

# Run tests with verbose output
go test -v ./...

# Run benchmarks
go test -bench=. ./...

# Run tests for a specific file pattern
go test -v -run TestProperties

# Build the module
go build ./...

# Format code
go fmt ./...

# Vet code for potential issues
go vet ./...
```

## Architecture

The codebase is structured around several key components:

### Core Components

- **properties.go**: Main Properties struct with getter/setter methods and property expansion logic
- **load.go**: File/URL loading functions with encoding detection (UTF8, ISO-8859-1)
- **decode.go**: Struct decoding functionality using reflection and struct tags
- **parser.go**: Properties file parser that converts tokens into key-value pairs
- **lex.go**: Lexical analyzer that tokenizes properties file content
- **integrate.go**: Integration utilities for working with flag package

### Key Features

1. **Property Expansion**: Recursive expansion of `${key}` expressions with environment variable support
2. **Multiple Input Sources**: Files, URLs, strings, maps, and command-line flags
3. **Type Safety**: Typed getters (GetString, GetInt, GetBool, etc.) with default values
4. **Struct Decoding**: Decode properties into Go structs using struct tags
5. **Comment Preservation**: Maintains comments and key ordering for round-trip editing

### Error Handling

The library uses a configurable error handling system via `ErrorHandler` function:

- Default: `LogFatalHandler` (calls `log.Fatal`)
- Alternative: `PanicHandler` (panics on error)
- Custom handlers can be provided

### Testing

- Main test file: `properties_test.go`
- Component-specific tests: `load_test.go`, `decode_test.go`, etc.
- Internal assert package: `assert/` for test utilities
- Go version-specific tests for compatibility

## Module Information

- Module path: `github.com/zier/properties`
- Go version requirement: 1.19+
- No external dependencies (uses only standard library)

## Property File Format

Supports standard Java properties format with:

- Key-value separators: `=`, `:`, or space
- Comment prefixes: `#` or `!`
- Multi-line values with backslash continuation
- Unicode escape sequences
- Environment variable expansion: `${VAR}`
