# Configirator

Thing to automatically load config from different formats. Supports custom formats. 
Uses [kotlinx.serialization](https://github.com/Kotlin/kotlinx.serialization).

Currently, it supports 
[JSON](https://github.com/Kotlin/kotlinx.serialization/blob/master/formats/README.md#JSON) (`json`), 
[HOCON](https://github.com/Kotlin/kotlinx.serialization/blob/master/formats/README.md#HOCON) (`conf`, `hocon`), 
[TOML](https://github.com/Peanuuutz/tomlkt) (`toml`), 
[YAML](https://github.com/charleskorn/kaml) (`yaml`) and
[Properties](https://github.com/Kotlin/kotlinx.serialization/blob/master/formats/README.md#properties) (`properties`)

Latest version: https://github.com/Com6235/maven-libs/packages/2156249

## Using as a dependency
### Maven

After you added the repository, you can start using my package by adding this to your `pom.xml`
```xml
<dependency>
    <groupId>io.github.com6235</groupId>
    <artifactId>configurator</artifactId>
    <version>${your desired version}</version>
</dependency>
```

And don't forget to add `kotlinx.serialization` plugin to your `plugins`

```xml
<properties>
    <kotlin.version>2.0.0-RC3</kotlin.version>
    <serialization.version>1.7.0-RC</serialization.version>
</properties>
<!-- other things -->
<build>
    <plugins>
        <plugin>
            <groupId>org.jetbrains.kotlin</groupId>
            <artifactId>kotlin-maven-plugin</artifactId>
            <version>${kotlin.version}</version>
            <executions>
                <execution>
                    <id>compile</id>
                    <phase>compile</phase>
                    <goals>
                        <goal>compile</goal>
                    </goals>
                </execution>
            </executions>
            <configuration>
                <compilerPlugins>
                    <plugin>kotlinx-serialization</plugin>
                </compilerPlugins>
            </configuration>
            <dependencies>
                <dependency>
                    <groupId>org.jetbrains.kotlin</groupId>
                    <artifactId>kotlin-maven-serialization</artifactId>
                    <version>${kotlin.version}</version>
                </dependency>
            </dependencies>
        </plugin>
    </plugins>
</build>
```

### Gradle

After you added the repository, you can start using my package by adding this to `dependencies`:

In `build.gradle.kts`:
```kotlin
dependencies {
    implementation("io.github.com6235:configurator:${your desired version}")
}
```

In `build.gradle`:
```groovy
dependencies {
    implementation 'io.github.com6235:configurator:${your desired version}'
}
```

And don't forget to add `kotlinx.serialization` plugin to your `plugins`

In `build.gradle.kts`:
```kotlin
plugins {
    kotlin("plugin.serialization") version "2.0.0-RC3"
}
```

In `build.gradle`:
```groovy
plugins {
    id 'org.jetbrains.kotlin.plugin.serialization' version '2.0.0-RC3'
}
```

## Examples

### Print some info from resources

```kotlin
import io.github.com6235.configurator.*
import kotlinx.serialization.Serializable

@Serializable
data class Config(val helpPages: List<String>)

fun main() {
    val configStream = this::class.java.getResourceAsStream("config.json")!! // change to any format, that it supports
    val config = ConfigLoader(Config.serializer()).loadConfig(configStream, "json") // change to any format, that it supports
    
    println(config.helpPages)
}
```

### Read from input and add to a JSON

```kotlin
import io.github.com6235.configurator.*
import kotlinx.serialization.Serializable

@Serializable
data class Config(val name: String)

fun main() {
    val loader = ConfigLoader(Config.serializer())
    while (true) {
        val s = readln()
        loader.saveConfig(Config(s), Path("./name.json"))
    }
}
```

### Use custom serialization properties

```kotlin
import io.github.com6235.configurator.*
import kotlinx.serialization.Serializable

@Serializable
data class Config(val name: String)

fun main() {
    val loader = ConfigLoader(Config.serializer())
    while (true) {
        val s = readln()
        loader.saveConfig(
            Config(s), 
            Path("./name.nbt"), 
            NbtLoader(
                Config.serializer(), 
                Nbt { compression = NbtCompression.Gzip; variant = NbtVariant.Bedrock }
            )
        )
    }
}
```