
Gradle [init scripts](http://www.gradle.org/init_scripts), as deployed to https://repo.springsource.org/gradle-init-scripts. Note that authentication is required for the gradle-init-scripts repository, meaning that only SpringSource employees will have access. This is because the files are deployed as [filtered resources](http://wiki.jfrog.org/confluence/display/RTF/Filtered+Resources) within Artifactory, allowing dynamic content such as one's Artifactory username and password information to be customized on the fly during download. Requiring users to authenticate ensures that this filtering happens properly.

# init.gradle

## Purpose

`init.gradle` decorates any maven repositories declared in your project's Gradle build scripts, adding your Artifactory username and password credentials.  This is important because most of the repositories at repo.springsource.org are configured in such a way that they will issue a 401 auth challenge if a user attempts to download any new artifacts, i.e. jars coming from remote repositories that have not previously been cached within Artifactory.  This is important to avoid repo.springsource.org acting as an "open proxy" to the world.  Authenticated users (SpringSource employees, build servers, etc) may download and cache new artifacts, but the anonymous user may not.  This means that anonymous users are able to download all SpringSource-created artifacts and all their transitive dependencies, but not dependencies of their own applications that have not otherwised been previously cached.

As described in the [Gradle documentation on init scripts](http://www.gradle.org/init_scripts), if a file named `init.gradle` is present in your `GRADLE_HOME` directory, it will be automatically applied when running gradle builds.  This means that

    $ ./gradlew build

becomes the equivalent of

    $ ./gradlew --init-script ~/.gradle/init.gradle build

## Instructions

### Download via browser

Go to https://repo.springsource.org/gradle-init-scripts/init.gradle, enter your LDAP username and password when prompted. Save the file into your `GRADLE_HOME` directory, e.g. `~/.gradle`.  Notice that your username and encrypted password values have automatically been filtered into the file on download.

### Or download via command line

Accessing artifactory outside the browser requires use of an encrypted password.  Retrieve your encrypted password from Artifactory at https://repo.springsource.org/webapp/profile.html ([detailed instructions](http://wiki.jfrog.org/confluence/display/RTF/Centrally+Secure+Passwords#CentrallySecurePasswords-UsingYourSecurePassword))

Download `init.gradle` into your `GRADLE_HOME` directory (`wget` is used below, but may be done via the browser as well):

    $ cd ~/.gradle
    $ wget --user=me --ask-password https://repo.springsource.org/gradle-init-scripts/init.gradle
    Password for user 'bob': <my-encrypted-password>
    ...

### Build as usual

`init.gradle` will automatically be applied and provides your Artifactory credentials when attempting to download artifacts. You will notice that the init script has been applied by its output on the command line:

    $ gradle build
    ...
    Applying init.gradle to add Artifactory credentials
    ...

Note that `init.gradle` requires Gradle 1.0-milestone-6 or better to work.  It acts as a no-op for any earlier versions.


# Staying up to date

Authenticated users may [place a watch](http://wiki.jfrog.org/confluence/display/RTF/Watches) on `init.gradle` or the entire gradle-init-scripts repository in order to be emailed any time a change is made.  When notified, simply repeat the instructions above to download and use the new version.


# Administrative notes (using deploy.sh)

`init.gradle` (and any other init scripts) are deployed to Artifactory using the `deploy.sh` script also available in this repository. Bash, curl, and 'buildmaster' credentials are required.

## Usage

Run without arguments for usage information:

    $ ./deploy.sh
    usage: ./deploy.sh file1 file2 ...
      - each file will be deployed to https://repo.springsource.org/gradle-init-scripts
      - provide the encrypted password for user 'buildmaster' when prompted
      - shell expansion is allowed, e.g.: ./deploy.sh *.gradle

## Deploy file(s)

    $ ./deploy.sh init.gradle
    Deploying init.gradle to https://repo.springsource.org/gradle-init-scripts/init.gradle
    Enter host password for user 'buildmaster': <buildmaster-encrypted-password>

This command will return with something similar to the following (per the Artifactory [REST API](http://wiki.jfrog.org/confluence/display/RTF/Artifactory's+REST+API#Artifactory%27sRESTAPI-DeployArtifact)), indicating that the file has been successfully deployed.

    {
      "uri" : "https://repo.springsource.org/gradle-init-scripts/init.gradle",
      "repo" : "gradle-init-scripts",
      "path" : "/init.gradle",
      "created" : "2012-01-11T12:53:47.227Z",
      "createdBy" : "buildmaster",
      "downloadUri" : "https://repo.springsource.org/gradle-init-scripts/init.gradle",
      "mimeType" : "text/x-groovy-source",
      "size" : 1079,
      "checksums" : {
        "sha1" : "08a4ec00ca8ce316e979222e95a767d7c651c1fb",
        "md5" : "a00d93f9b188910e74228877a9b21f99"
      },
      "originalChecksums" : {
      }
    }
