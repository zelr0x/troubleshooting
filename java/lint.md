## Suppress proprietary / internal API warning
Add this to `pom.xml`:
```xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-compiler-plugin</artifactId>
    <configuration>
        <compilerArgument>-XDenableSunApiLintControl</compilerArgument>
    </configuration>
</plugin>
```
And then use:
```java
@SuppressWarnings("sunapi")
```
