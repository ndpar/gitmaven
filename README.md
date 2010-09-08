# Releasing Maven projects hosted in local Git repository

## Setup Maven project

- Create Maven project.
- Configure `scm` section to use local Git repository.

    <scm>
        <connection>scm:git:file://.</connection>
        <developerConnection>scm:git:file://.</developerConnection>
        <url>scm:git:file://.</url>
    </scm>

- Configure `distributionManagement` section to point to your remote Maven repository.
- Configure Maven release plugin to use local checkout

    <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-release-plugin</artifactId>
        <configuration>
            <localCheckout>true</localCheckout>
        </configuration>
    </plugin>

## Setup local Git repository

    $ git init
    $ git add .
    $ git ci -m 'Initial checkin'

## Release project

    $ mvn release:prepare
    $ mvn release:perform

## Fix bugs in maintenance branch

    $ mvn release:branch -DbranchName=maint-1.0.x
    $ git co maint-1.0.x

## Push to remote Git repository (optional)

    $ git remote add origin git@github.com:ndpar/gitmaven.git
    $ git push origin master
    $ git push --tags

