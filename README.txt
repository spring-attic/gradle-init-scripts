Gradle init scripts[1] deployed to https://repo.springsource.org/init_scripts

Files are deployed as Artifactory Filtered Resources[2], which allows
username and password information to be customized on the fly during
download.

Download all init scripts into your GRADLE_HOME directory:

    $ cd ~/.gradle

    $ wget --user=bob --ask-password --reject=.html --no-directories --recursive https://repo.springsource.org/init_scripts
    Password for user 'bob': <bob's-encrypted-password>[3]
    ...

Build as usual; init.gradle will automatically be applied and provides
your Artifactory credentials when attempting to download artifacts:

    $ gradle build

Create Gradle wrapper scripts[4]:

    $ gradle -I ~/.gradle/wrapper.gradle wrapper

Publish project artifacts to http://repo.springsource.org [5]

    $ gradle -I ~/.gradle/publish.gradle artifactoryPublish

[1] http://www.gradle.org/init_scripts
[2] http://wiki.jfrog.org/confluence/display/RTF/Filtered+Resources
[3] http://wiki.jfrog.org/confluence/display/RTF/Centrally+Secure+Passwords#CentrallySecurePasswords-UsingYourSecurePassword
[4] http://gradle.org/current/docs/userguide/gradle_wrapper.html
[5] http://wiki.jfrog.org/confluence/display/RTF/Gradle+Artifactory+Plugin
