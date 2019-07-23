Protoc plugins for generating graphql schema

If you use micro-service architecture with grpc for backend and graphql api gateway for frontend you will find yourself
repeating a lot of code for translating from one transport layer to another (which many times may be a source of bugs)

This repository aims to simplify working with grpc trough protocol buffers and graphql by generating code.

Install:
-

```sh
go install github.com/danielvladco/go-proto-gql/protoc-gen-gql
go install github.com/danielvladco/go-proto-gql/protoc-gen-gogqlgen
go install github.com/danielvladco/go-proto-gql/protoc-gen-gqlgencfg
```

Usage Examples:
-
The protoc compiler expects to find plugins named `proto-gen-<PLUGIN_NAME>` on the execution `$PATH`. So first:

```sh
export PATH=${PATH}:${GOPATH}/bin
```

---
`--gql_out` plugin will generate graphql files with extension .graphqls 
rather than go code which means it can be further used for any other language or framework.

Example: 
```sh
protoc --gql_out=paths=source_relative:. -I=. -I=./example/ ./example/*.proto
```

If you still want to generate go source code instead of graphql then use 
http://github.com/99designs/gqlgen plugin, and map all the generated go types with all the generated graphql types. 
Luckily `--gqlgencfg_out` plugin does exactly this. 

---
`--gqlgencfg_out` plugin generates yaml configs that can be used further by the http://github.com/99designs/gqlgen library.

Example: 
```sh
protoc --gqlgencfg_out=paths=source_relative:. -I=. -I=./example/ ./example/*.proto
```

The generated go code will work fine if you don't have any `enum`s, `oneof`s or `map`s. For this purpose use `--gogqlgen_out` plugin.

---
`--gogqlgen_out` plugin generates generates methods for implementing
`github.com/99designs/gqlgen/graphql.Marshaler` and `github.com/99designs/gqlgen/graphql.Unmarshaler` interfaces. Now proto `enum`s, `oneof`s and `map`s will work fine with graphql. 

This plugin also creates convenience methods that will implement generated by the `gqlgen` `MutationResolver` and `QueryResolver` interfaces.

NOTE: to generate with gogo import add `gogoimport=true` as a parameter

Example:
```sh
protoc --gogqlgen_out=gogoimport=false,paths=source_relative:. -I=. -I=./example/ ./example/*.proto
``` 

---
See `/example` folder for more examples.

TODO:
- Create a better implementation of `map`s and `oneof`s.
- Add comments from proto to gql for documentation purposes.
- Add more documentation.

## Community:
I am one on this. Will be very glad for any contributions so feel free to create issues and forks.

## License:

`go-proto-gql` is released under the Apache 2.0 license. See the LICENSE file for details.
