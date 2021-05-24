# Can we read command line arguments in settings.gradle?

Based on the following [Stack Overflow questions][4]

I want to read command line arguments in settings.gradle so that i can add only those submodules in include what i am passing command line.

Can we read command line arguments in settings.gradle?

# Read project properties

You can't read whole command line arguments in settings gradle file, though what you can do is to [read project properties in settings file][1] and those can be passed with the command line arguments.

For example if you want to specify to include **sub-project-1** in your Gradle build you would have to provide this value in a [project property][2] like following:

```
gradlew build -Pincluded.projects=sub-project-2
```

Note CLI command with option **-P** defines project property. It has to have specified key and value. In this case key is **included.projects** and value is **sub-project-1**.

In settings file you can read it with following [getProperties()][3] method on Project object. `getProperties().get(String key)`.

Following is the settings script if you have sub modules with names:
- sub-module-1
- sub-module-2
- sub-module-3

It will read property that includes list of modules to include in build script. If property is empty then all modules will be included, otherwise it selects passed in sub projects names and includes only present ones. There is no validation on sub project names.

```groovy
// Define all the sub projects
def subprojects = ['sub-project-1', 'sub-project-2', 'sub-project-3'] as Set

// Read all subprojects from the project properties.
// Example of passed in project properties with Gradle CLI with the -P option
// `gradlew build -Pincluded.projects=sub-project-1,sub-project-3`
def includedProjectsKey="included.projects"
def projectsToIncludeInput = hasProperty(includedProjectsKey) ? getProperties().get(includedProjectsKey) : ""

Set<String> projectsToInclude = []
if(projectsToIncludeInput != "") {

  // Include passed in sub projects from project arguments
  projectsToIncludeInput.toString().split(",").each {
    projectsToInclude.add(it)
  }
} else {

  // Include all sub projects if none is specified
  projectsToInclude = subprojects
}

// Include sub projects
projectsToInclude.each {
  include it
}
```

[1]: https://docs.gradle.org/current/javadoc/org/gradle/api/initialization/Settings.html
[2]: https://docs.gradle.org/current/userguide/build_environment.html#sec:project_properties
[3]: https://docs.gradle.org/current/javadoc/org/gradle/api/Project.html#getProperties--
[4]: https://stackoverflow.com/questions/67660971/can-we-read-command-line-arguments-in-settings-gradle