# MavenPrivate
Instruction of how to create a private maven center.

## Explanation:

For big projects, library dependecies are always making developers head big.
Using Maven and jCenter are good idea, but it needs a period of time to publish your code.
Here I will introduce how to make your private maven.
It is very useful for group work, you can set up this in a public server or company's server.
The sample instruction I have shown is setting on my laptop only.
In total it has 4 big steps, so don't worry about it, let's see how it works.

## Content

* [**Step 1 : Install Nexus**](#step1)
* [**Step 2 : Create Repository**](#step2)
* [**Step 3 : Upload Library**](#step3)
* [**Step 4 : Use Library**](#step4)

## <a name='step1'>Step 1: Install Nexus

1. install Nexus from [here](https://www.sonatype.com/download-oss-sonatype).

2. unzip and goto file bin using following command.

	```
	cd nexus-3.3.1-01/bin/
	```
3. run command line:

	```
	./nexus start
	```
	
4. In browser, type in

	```
	127.0.01:8081
	```
	Then you will see the dash board of Nexus
	
	![nexus.jpeg](https://github.com/MelbPaulZ/MavenPrivate/blob/master/nexus.jpeg?raw=true)


    
## <a name='step2'>Step 2: Create Repository

1. choose hosted repository, maven2, fill in repository id, name, then save.

## <a name='step3'>Step 3: Upload library

In this example

* My repo url is `http://127.0.0.1:8081/repository/paulunimelb/`
 
* Nexus userName is `admin`
    
* Nexus password is `admin123`
    
* The group I want is `com.unimelb.itime`
    
* The artifactId I want is `closablebindingview`
    
* The version I want is `1.0.0`



In android 'build.gradle' file, add following:

```
apply plugin: 'maven'
apply plugin: 'signing'
...
signing {
    required { gradle.taskGraph.hasTask("uploadArchives") }
    sign configurations.archives
}

uploadArchives {
    configuration = configurations.archives

    repositories.mavenDeployer {
        beforeDeployment { MavenDeployment deployment -> signing.signPom(deployment) }

        repository(url: 'http://127.0.0.1:8081/repository/paulunimelb/') {
            authentication(userName: 'admin', password: 'admin123')
        }

        pom.project {
            version '1.0.0'
            artifactId 'closabledatabindingview'
            groupId 'com.unimelb.itime'
            packaging 'aar'
            url 'http://127.0.0.1:8081/repository/paulunimelb/'
            description 'dependences lib'
        }
    }
}
```  

Now run 

```
./gradlew tasks
```
you should see 

```
Upload tasks
------------
uploadArchives - Uploads all artifacts belonging to configuration ':closabledatabindingview:archives'
```


Next run  

```
./gradlew uploadArchives
```

If no error, your code has been successfully upload in library.

## <a name='step4'>Step 4: Use library
Because we don't use maven center, we have to provide our own url, add the following in **build.gradle**

```
allprojects {
    repositories {
        jcenter()
        mavenCentral()
        maven{
            url 'http://127.0.0.1:8081/repository/paulunimelb/'
        }
    }
}
```

Add following dependency:

```
dependencies{
    compile 'com.unimelb.itime:closabledatabindingview:1.0.0'
}
```
