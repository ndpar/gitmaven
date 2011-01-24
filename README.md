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
    $ git checkin -m 'Initial checkin'

## Release project

    $ mvn release:prepare
    $ mvn release:perform

When asked for new development version, increment last number (i.e. keep default value).

## Fix bugs in maintenance branch

    $ mvn release:branch -DbranchName=maint-1.0.x
    $ git checkout maint-1.0.x

When asked for new working copy version, increment second number and set last one to zero.

    edit-add-commit-loop
    $ mvn release:prepare release:perform

## Merge fixes to master

    $ git checkout master
    $ git merge maint-1.0.x

While resolving conflicts, keep the latest (HEAD) version.

    $ git add .
    $ git ci -m "Merged branch maint-1.0.x"

## Configure remote Git repository (optional)

Master branch

    $ git checkout master
    $ git remote add origin git@github.com:ndpar/gitmaven.git
    $ git push origin master

Maintenance branch

    $ git checkout maint-1.0.x
    $ git-publish-branch

## Push to remote Git repository (optional)

Master branch

    $ git checkout master
    $ git push
    $ git push --tags

Maintenance branch

    $ git checkout maint-1.0.x
    $ git push
    $ git push --tags

### Resources

[git-publish-branch](http://git-wt-commit.rubyforge.org/#git-publish-branch) script

