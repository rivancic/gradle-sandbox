# Use Maven BOM

## Generate BOM

This really simple example shows how to import Maven BOM for a particular configuration in a client application.

Dependencies constraints are defined in bom project.

```groovy
dependencies {
    constraints {
        api("com.google.code.gson:gson:2.8.7")
        api("org.slf4j:slf4j-api:1.7.30")
    }
}
```

I picked up gson and slf4j-api libraries as they are often used in real projects.

## Apply BOM

Now to apply constraints in your actual Gradle project you apply platform for specific configuration.

```groovy
dependencies {
  implementation(platform(project(":bom")))
  ...
}
```

With this constraints are defined and you do not need to explicitly specify version for your declared dependencies.

```groovy
dependencies {
    implementation("com.google.code.gson:gson")
    compileOnly("org.slf4j:slf4j-api")
}
```

## Result

If you run `gradlew dependencies` then you can see in the result  that for dependencies that wont be resolved there is proper dependency for the configuration.


```
compileOnly - Compile only dependencies for source set 'main'. (n)
\--- org.slf4j:slf4j-api (n)

implementation - Implementation only dependencies for source set 'main'. (n)
+--- project bom (n)
\--- com.google.code.gson:gson (n)
```

And for configurations that will resolve dependencies there are correct versions set.

```
compileClasspath - Compile classpath for source set 'main'.
+--- org.slf4j:slf4j-api -> 1.7.30
+--- project :bom
|    +--- com.google.code.gson:gson:2.8.7 (c)
|    \--- org.slf4j:slf4j-api:1.7.30 (c)
\--- com.google.code.gson:gson -> 2.8.7

runtimeClasspath - Runtime classpath of source set 'main'.
+--- project :bom
|    \--- com.google.code.gson:gson:2.8.7 (c)
\--- com.google.code.gson:gson -> 2.8.7
```

Check graph below on how configuration [gets resolved into actual classpaths](https://docs.gradle.org/current/userguide/java_library_plugin.html#sec:java_library_configurations_graph).

<img src="https://docs.gradle.org/current/userguide/img/java-library-ignore-deprecated-main.png">