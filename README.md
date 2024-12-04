### Template expansion with `ProcessResources` and configuration cache

This is a MCVE toy project illustrating a difficulty I'm having using a child CopySpec on `ProcessResources` and Gradle's configuration cache.

Note that configuration cache is enabled by default.

Steps to reproduce:

`$ ./gradlew build`

Expected outcome:

New file at `build/resources/main/META-INF/resources/target/myResult.js` with content:
```
window.myKey='deadbeef'
```

Actual outcome:

```
Could not load the value of field `properties` of `org.gradle.api.internal.file.copy.DefaultCopySpec$MapBackedExpandAction` bean found in field `mainSpec` of task `:processResources` of type `org.gradle.language.jvm.tasks.ProcessResources`.
> null array

* Try:
> Run with --stacktrace option to get the stack trace.
> Run with --info or --debug option to get more log output.
> Run with --scan to get full insights.
> Get more help at https://help.gradle.org.
BUILD FAILED in 83ms
Configuration cache entry stored.
```

Workaround:

`$ ./gradlew build --no-configuration-cache`

\- OR \-

Switch the comments to define `propz` on lines 8 and 9, using definition `def propz = ['REPLACE_WITH_VALUE': 'deadbeef']`

## Conclusion

Template expansion + configuration caching do not play well with Java 9+ collection factory methods like `Map#of`.

Note that the runtime type when using Groovy's inline map syntax is `LinkedHashMap`.  When using `Map#of`, we get `java.util.ImmutableCollections$Map1`.