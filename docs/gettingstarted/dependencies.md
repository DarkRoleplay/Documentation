# Dependencies
Forge has some support for managing and loading mod dependencies.
You can even include them into your mod jar, but in that case you should check the other mods license.

!!! note
  Dependency ≠ Dependency. There are so called `soft` & `hard` dependencies, where the latter one resembels what most people imagine under a dependency. Another mod that you need in order to use this Mod. A soft dependency on the other hand just means that you can add optional support to a mod, without requirering it.

## Setting up a dependency
There are 2 main ways of implementing another mod into your workspace so you can use it.
Either make it manually, which works for all mods. Or make it semi automatic, which you can't use for all mods.

### Manually adding dependencies
In order to add a dependency manually you've got to create a `libs` folder within your workspace.
Now you just need to download the mod you want to add as a dependency, throw it into the libs folder and run:
`gradlew setupDecompWorkspace` and the corresponding command for your IDE.

### Adding dependencies using Maven
In order to add a dependency you need to ensure that the mod actually has a maven repository.
If that's the case you need to edit your gradle.build and add the following to it:
```
  repositories {
    maven {
      name 'mod1 name'
      url 'url'
    }
    maven {
      name 'mod2 name'
      url 'url'
    }
  }

  dependencies {
    <mode> "<address on maven>:<mod name>:<version that is in maven>"
    <mode> "<address on maven>:<other mod name>:<version that is in maven>:[optional type]"
  }
```
Usually mods provide an example of what you need to enter into the field on their github page.
It also might be the case that multiple mods share one `repository`, in that case you need to add the repository just once.

Considering the dependencies you may even need to add multiple lines for a single mod.
It depends on the mode you choose:
(Development Environment = `dev env`)
| mode           | explanation                                                                                                      |
| --------------:|:---------------------------------------------------------------------------------------------------------------- |
| runtime        | This will not add the source code. This will just add the mod as a playable version to your dev env              |
| provided       | Adds the source code, so that you can use it but don't have the mod when testing in your dev env                 |
| compile        | Combines `runtime` and `provided`, you get access to the source code and have a playable version in your dev env |
| deobfProvided  | Equal to `provided`, but it deobfuscate the mod if it's not providing a deobfuscated jars                        |
| deobfCompile   | Equal to `compile`, but it deobfuscate the mod if it's not providing a deobfuscated jars                         |

!!! tip
  `deobfCompile` is usually the best choice.

# ToDo
---

The mod repository
------------------

The mod repository is a Maven-like repository containing mods and libraries. An artifact in this repository is identified by its Maven coordinate: `groupId:artifactId:version:classifier@extention`. Classifer and extension are optional. Forge can archive, manage and load mods and libraries in this repository. The mod repository may contain multiple versions of mods and libraries, including snapshot versions.

Forge can archive a jar in the repository if its `Maven-Artifact` manifest attribute is defined. The value of this attribute should be its Maven coordinate.

The mod repository supports snapshot artifacts. If the artifact version ends with `-SNAPSHOT`, the artifact will be resolved as the version with the latest timestamp. The timestamp can be set as the `Timestamp` manifest attribute, which should be the time since the epoch in milliseconds.


Dependency extraction
---------------------

Forge provides a simple way to embed dependencies in a mod and have them extracted at runtime. By placing dependency jars in your own jar, Forge can extract them into the mod repository and load them. This can be used as an alternative to shading, and comes with the potential benefit of resolving dependency version conflicts.

The contained dependencies of a jar file are marked by the `ContainedDeps` manifest attribute. Its value should be a space separated list of the names of contained jar files that will be extracted. These jar files should be placed in `/META-INF/libraries/{entry}`.

Forge will inspect the manifest of the contained jar to determine its Maven coordinate so that it may be archived. If a file `/META-INF/libraries/{entry}.meta` exists, Forge will read this as the jar's manifest instead. The dependency will be archived in the local repository according to its `Maven-Artifact` manifest attribute.

### Maven Example `JEI (Just Enough Items)`
```
repositories {
  maven {
    name = "Progwml6 maven"
    url = "http://dvs1.progwml6.com/files/maven"
  }
}

dependencies {
  // compile against the JEI API but do not include it at runtime
  deobfProvided "mezz.jei:jei_1.12.2:4.11.0.203:api"
  // at runtime, use the full JEI jar
  runtime "mezz.jei:jei_1.12.2:4.11.0.203"
}
```
