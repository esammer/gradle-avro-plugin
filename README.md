# gradle-avro-plugin

An [Apache Avro][apache-avro] code generation plugin for Gradle.

[apache-avro]: http://avro.apache.org/

## Building

This plugin uses Gradle as its build tool. To build the project, run the
following.

`./gradlew check`

## Using this Plugin

Avro schema files must be placed in `src/$name/avro` where `$name` is the name
of any Gradle source set. Typically, users have two source sets: `main` and
`test` which are created by including the `java` plugin in a project.

By default, this plugin will do _nothing_. To enable code generation, you must
provide a configuration for each source set containing Avro schemas. If the
defaults are OK, you may provide an empty configuration closure. Here's a
minimal configuration to enable code generation for the `main` source set.

```gradle
buildscript {

  repositories {
    mavenCentral()
  }

  dependencies {
    classpath 'com.rocana.gradle:gradle-avro-plugin:1.0.0-SNAPSHOT'
  }
}

apply plugin: 'java'
apply plugin: 'com.rocana.gradle.avro'

dependencies {
  compile 'org.apache.avro:avro:1.7.6'
}

avro {
  /*
   * Enable Avro code generation for the main source set.
   * Files in src/main/avro will be compiled into Java
   * and stored in $buildDir/generated-avro-main/..
   */
  main {
  }
}
```

A task named `compile$nameAvro` for each source set, where `$name` is the name
of the source set. Additionally, a dependency is created on the respective Avro
compilation task from the corresponding Java compilation task (e.g.
`compileJava`, `compileTestJava`). This means that running `./gradlew
compileJava` in your project will invoke `compileMainAvro` first, as you'd
expect. Note that the Java plugin doesn't include the source set name `main` in
the compilation task name while this plugin _does_ include it in the Avro task
name.

The generated code is placed in the directory `$buildDir/genrated-avro-$name`
where `$name` is the name of the source set. This directory is added to the list
of source directories for the source set. Again, this is probably the behavior
you'd expect; after the Avro classes are generated, they will be compiled.

## Configuration

The following properties may be configured for each source set.

`stringType` (String) - Set the class to use when representing strings. Valid
options are `CharSequence` and `String`. The default is `String`.

`fieldVisibility` (String) - Set the access method and level for the generated
members. Valid options are `PUBLIC`, `PUBLIC_DEPRECATED`, and `PRIVATE`. The
default is `PUBLIC_DEPRECATED`.

`createSetters` (boolean) - When true, generate setter methods for each field.
The default is false.

`templateDirectory` (File) - Set to a directory to use custom Velocity
templates during code generation. By default, the Avro-included Velocity
templates are used.

`generatedSourceDirectory` (File) - The directory into which generated code is
written. The default is to use `$buildDir/generated-avro-$name` where `$name`
is the name of the source set.
