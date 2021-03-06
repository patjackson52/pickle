# Pickle

| Lib | Plugin | Processor |
| --- | --- | --- |
| [ ![Download](https://api.bintray.com/packages/fourlastor/maven/pickle-lib/images/download.svg) ](https://bintray.com/fourlastor/maven/pickle-lib/_latestVersion) | [ ![Download](https://api.bintray.com/packages/fourlastor/maven/pickle-plugin/images/download.svg) ](https://bintray.com/fourlastor/maven/pickle-plugin/_latestVersion) | [ ![Download](https://api.bintray.com/packages/fourlastor/maven/pickle-processor/images/download.svg) ](https://bintray.com/fourlastor/maven/pickle-processor/_latestVersion) |

Pickle is an implementation of Cucumber for Android which generates the tests classes at compile time instead of runtime.

The main advantage over [cucumber-android](https://github.com/cucumber/cucumber-android) is that you won't need to use a different instrumentation runner for your Cucumber tests, and that, being generated, you can debug and inspect the tests, which makes it easier to debug errors.

## Installation

Add this to your app `build.gradle` dependencies.

```gradle

ext.pickleVersion = '1.3.2'
ext.cucumberVersion = '4.4.0' // Last supported version. Do not use never versions since annotation packages were moved.

buildscript {
  repositories {
    jcenter()
  }

  dependencies {
    classpath "com.fourlastor:pickle-plugin:$pickleVersion"
  }
}

dependencies { 
  androidTestImplementation "io.cucumber:cucumber-java:$cucumberVersion"
  androidTestImplementation "com.fourlastor:pickle-lib:$pickleVersion"
  androidTestAnnotationProcessor "com.fourlastor:pickle-processor:$pickleVersion"
  
  // For Kotlin projects with `kapt`
  // kaptAndroidTest "com.fourlastor:pickle-processor:$pickleVersion"

}

```

If you enable pickle on unit tests (see configuration below), remember to apply the annotation processor on your test variant!

## Configuration

Add the following to your app `build.gradle`

Make sure to apply this plugin **before** the kotlin plugin, if using one. 

```gradle
apply plugin: 'com.fourlastor.pickle'

pickle {
    packageName = 'com.example.test' // package where tests will be generated
    strictMode = true // activate/deactivate strict mode (defaults to true)
    androidTest {
      enabled = true // enables pickle on androidTest (defaults to true)
      featuresDir = 'features' // location of features inside `androidTest/src/assets`
    }
    unitTest {
      enabled = false // enables pickle on unit tests (defaults to false)
      featuresDir = project.file('src/test/features') // absolute path to location of feature files for unit tests
    }
}
```

Test will be generated and you can run them as you would run normal Android/unit tests :tada:

Disabling strict mode will generate tests only for scenario having all the steps/background steps defined, and empty tests annotated with `@Ignore` for scenario with missing steps. The default behavior (strict) will fail at compile time if some scenarios are missing any step.

## Modules

There are 3 modules: library, plugin, and annotation processor.

### Library

The library contains only an annotation class (`Pickle`) which is required for the other 2 modules to work. This class holds the settings passed to the plugin.

### Plugin

The plugin is responsible for triggering a new build in case the assets change, and to provide the extension to configure Pickle. It registers a code generation task that will create a new class representing the current state of the assets (contains a hashcode of the files).

### Annotation Processor

The annotation processor will look for the hash class generated by the plugin (`PickleHash`, annotated with `Pickle`), read the settings from the annotation and generate test classes representing the feature files.

## Sample

To open the samples, open a new project in Intellij IDEA and select the `sample` folder, which contains 2 project: `app-kotlin` and `app-java`.
 
It uses [composite builds](https://docs.gradle.org/current/userguide/composite_builds.html) to load the project's libraries, so you can freely modify them and it will pick them up. 

## Known Issues

- Cucumber Expressions are not supported yet. [Issue #52](https://github.com/fourlastor/pickle/issues/52).
    - IntelliJ IDEA Plugin recently started to use Cucumber Expressions by default for parameters. Convert them to Regex for Pickle to understand.
- The package of `Given/When/Then` annotations were moved with recent Cucumber versions. New locations are not supported yet. [Issue #48](https://github.com/fourlastor/pickle/issues/48).

