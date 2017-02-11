# FX Gson

[![Bintray](https://img.shields.io/bintray/v/joffrey-bion/maven/fx-gson.svg)](https://bintray.com/joffrey-bion/maven/fx-gson/_latestVersion)
[![Travis Build](https://img.shields.io/travis/joffrey-bion/fx-gson/master.svg)](https://travis-ci.org/joffrey-bion/fx-gson)
[![Dependency Status](https://www.versioneye.com/user/projects/57327660a0ca35004baf8bfb/badge.svg)](https://www.versioneye.com/user/projects/57327660a0ca35004baf8bfb)
[![GitHub license](https://img.shields.io/badge/license-MIT-blue.svg)](https://github.com/joffrey-bion/fx-gson/blob/master/LICENSE)

FX Gson is a set of type adapters for [Google Gson](https://github.com/google/gson) to serialize JavaFX properties as 
their values, and deserialize values into properties.

FX Gson simply removes the property "wrapping" and delegates the serialization of the value to the Gson. This means that 
any configuration you add to Gson regarding a type will be taken into account when serializing a property of that type. 
This is true for objects and primitives.

## Why use FX Gson?

In JavaFX, POJOs usually contain `Property` objects instead of primitives. When serialized, we usually don't want to
see the internals of such `Property` objects in the produced JSON, but rather the actual value held by the property.

For instance, suppose the `Person` class is defined like this:

    public class Person {
        private final StringProperty firstName;
        private final StringProperty lastName;

        public Person(String firstName, String lastName) {
            this.firstName = new SimpleStringProperty(firstName);
            this.lastName = new SimpleStringProperty(lastName);
        }
        
        // getters / setters / prop getters
    }
    
Here is how it is serialized:

<table>
    <tr>
        <th>With vanilla Gson</th>
        <th>With FxGson-configured Gson</th>
    </tr>
    <tr>
        <td>
        <pre>{
    "firstName": {
        "name": "",
        "value": "Hans",
        "valid": true,
        "helper": {
            "observable": {}
        }
    },
    "lastName": {
        "name": "",
        "value": "Muster",
        "valid": true,
        "helper": {
            "observable": {}
        }
    }
}</pre>
        </td>
        <td>
            <pre>{
    "firstName": "Hans",
    "lastName": "Muster"
}</pre>
        </td>
    </tr>
</table>

This produces a more human-readable output, and make manual modifications of the JSON easier.

## Usage

All you need to know is [in the wiki](https://github.com/joffrey-bion/fx-gson/wiki/Basic-FX-Gson-usage), but here is a 
quick overview.

You can use FX Gson in multiple ways depending on the degree of customization you need:
- directly [create a ready-to-go `Gson`](https://github.com/joffrey-bion/fx-gson/wiki/Basic-FX-Gson-usage#simple-ways-matter) able to serialize JavaFX properties

    ```java
    // to handle only Properties and Observable collections
    Gson fxGson = FxGson.create();
    
    // to also handle the Color & Font classes
    Gson fxGsonWithExtras = FxGson.createWithExtras();
    ```

- [create a pre-configured `GsonBuilder`](https://github.com/joffrey-bion/fx-gson/wiki/Basic-FX-Gson-usage#using-pre-configured-gsonbuilders) that you can further configure yourself

    ```java
    Gson fxGson = FxGson.coreBuilder()
                        .registerTypeAdapterFactory(new MyFactory())
                        .disableHtmlEscaping()
                        .create();
    
    Gson fxGsonWithExtras = FxGson.fullBuilder()
                                  .registerTypeAdapter(Pattern.class, new PatternSerializer())
                                  .setPrettyPrinting()
                                  .create();
    ```

- [add JavaFX configuration to an existing `GsonBuilder`](https://github.com/joffrey-bion/fx-gson/wiki/Basic-FX-Gson-usage#adding-javafx-support-to-an-existing-gsonbuilder)

    ```java
    GsonBuilder builder = MyLib.getBuilder();
    Gson gson = FxGson.addFxSupport(builder).create();
    ```

- [cherry-pick some pieces of FX Gson configuration](https://github.com/joffrey-bion/fx-gson/wiki/Customize-FX-Gson) and customize it to fit your needs

### Add the dependency

#### Manually
 
You may directly download the JAR from 
[FX Gson's Bintray Repository](https://bintray.com/joffrey-bion/maven/fx-gson/_latestVersion), although I recommend
using a build tool such as [Gradle](https://gradle.org/).
 
#### In Gradle

    repositories {
        jcenter()
    }

    dependencies {
        compile 'org.hildan.fxgson:fx-gson:2.0.1'
    }

#### In Maven

I personally recommend using Gradle, but if you must:

    <dependency>
      <groupId>org.hildan.fxgson</groupId>
      <artifactId>fx-gson</artifactId>
      <version>2.0.1</version>
      <type>pom</type>
    </dependency>
    
Note: the artifact is on Bintray JCenter, not in Maven Central, so make sure you point your maven to JCenter by adding 
the repo to your `settings.xml` using the URL `http://jcenter.bintray.com`.

You can find a complete XML example in the "Set me up!" blue rectangle on the 
[JCenter home page](https://bintray.com/bintray/jcenter).

## License

Code released under [the MIT license](https://github.com/joffrey-bion/io-utils/blob/master/LICENSE)
