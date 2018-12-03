##Android studio upload library to maven local

####  In your module level build.gradle(library), simply copy below code


```
apply plugin: 'maven'
...

uploadArchives{
    repositories{
        mavenDeployer {
            repository(url: "file://localhost" + System.getProperty("user.home") + "/.m2/repository" )
            pom.groupId = 'dependencydemo'
            pom.artifactId = 'sdk'
            pom.version= '1.0'
        }
    }
}
```


and run 
```
./gradlew uploadArchives
```

Done

Your library will be upload into your local repository, i.e. "/Users/puzhao/.m2/repository/"
