# Example Protoc Plugin in Java

This repository contains an example of a protoc plugin written in Java.

This example plugin generates text files that list the messages and fields defined in proto files.

Given the following input proto file:
```
syntax = "proto3";

message Foo {
  int32 foo_f1 = 1;
  string foo_f2 = 2;

  message Bar {
    repeated int64 bar = 1;
  }

  message Baz {
    map<string, int32> map_str_to_int32 = 1;
  }
}

message XYZ {
}
```

The plugin will generate a text file like this:
```
|- Foo(foo_f1: INT32, foo_f2: STRING)
   |- Bar(bar: List<INT64>)
      |- Baz(map_str_to_int32: List<MapStrToInt32Entry>)
         |- MapStrToInt32Entry(key: STRING, value: INT32)
      |- XYZ()
```

Note that repeated fields are represented as having type `List<X>`. Note how
the map field is transformed to a list of key-value pairs - this
transformation is performed inside protoc.

# Building and running

1. Clone this repository:

```
git clone http://github.com/thesamet/protoc-plugin-in-java

cd protoc-plugin-in-java
```

2. Build the plugin

```
gradle installDist
```

This should generate an executable at `build/install/myplugin/bin/myplugin`
(as well as a `bat` file for Windows users)

3. Try it out!

```
protoc --plugin=protoc-gen-example=build/install/myplugin/bin/myplugin \
       protos/a.proto protos/b.proto -I protos \
       --example_out=protos
```

on Windows it would be something like:

```
protoc.exe --plugin=protoc-gen-example=build\install\protoc-plugin-in-java\bin\MyProtocPlugin.bat protos\a.proto protos\b.proto -I protos  --example_out=protos
```

You will see `a.txt` and `b.txt` generated in the `protos` directory.
