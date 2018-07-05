# Pickle

Pickle is an implementation of Cucumber for Android which generates the tests classes at compile time instead of runtime.

The main advantage over [cucumber-android](https://github.com/cucumber/cucumber-jvm/tree/master/android) is that you won't need to use a different instrumentation runner for your Cucumber tests, and that, being generated, you can debug and inspect the tests, which makes it easier to debug errors.

## Installation

Add this to your app `build.gradle` dependencies.

```gradle

ext.pickleVersion = '1.0.0'

buildscript {
  repositories {
    jcenter()
  }

  dependencies {
    classpath 'com.fourlastor:pickle-plugin:$pickleVersion'
  }
}

dependencies {
  androidTestImplementation 'com.fourlastor:pickle-lib:$pickleVersion'
  androidTestApt 'com.fourlastor:pickle-processor:$pickleVersion'
}

```

## Configuration

Add the following to your app `build.gradle`

Make sure to apply this plugin **before** the kotlin plugin, if using one. 

```gradle
apply plugin: 'com.fourlastor.pickle'

pickle {
    featuresDir = 'features' // location of features inside `androidTest/src/assets`
    packageName = 'com.example.test' // package where tests will be generated
    strictMode = false // activate/deactivate strict mode (optional, defaults to true)
}
```

Test will be generated and you can run them as you would run normal Android Tests :tada:

Disabling strict mode will generate tests only for defined scenarios, a scenario is considered defined if all the steps and background steps for that scenario are defined. The default behavior (strict) will fail at compile time if some scenarios aren't defined.

## Modules

There are 3 modules: library, plugin, and annotation processor.

### Library

The library contains only an annotation class (`Pickle`) which is required for the other 2 modules to work. This class holds the settings passed to the plugin.

### Plugin

The plugin is responsible for triggering a new build in case the assets change, and to provide the extension to configure Pickle. It registers a code generation task that will create a new class representing the current state of the assets (contains a hashcode of the files).

### Annotation Processor

The annotation processor will look for the hash class generated by the plugin (`PickleHash`, annotated with `Pickle`), read the settings from the annotation and generate test classes representing the feature files.

