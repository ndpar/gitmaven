# Releasing Maven projects hosted in local Git repository

## Setup local Git repository

    $ touch .gitignore
    $ git init
    $ git add .
    $ git commit -m 'Initial checkin'

The result Git tree

    * 84a7df07 (HEAD, master) Initial checkin

## Setup Maven project

Create Maven project (scaffolding and POM file). The default project version is

    <version>1.0-SNAPSHOT</version>

Configure `scm` section to use local Git repository.

    <scm>
        <connection>scm:git:file://.</connection>
        <developerConnection>scm:git:file://.</developerConnection>
        <url>scm:git:file://.</url>
        <tag>HEAD</tag>
    </scm>

Configure `distributionManagement` section to point to your Maven repository.

    <distributionManagement>
        <repository>
            <id>releases</id>
            <url>http://maven.ndpar.com:9191/nexus/content/repositories/releases</url>
        </repository>
        <snapshotRepository>
            <id>snapshots</id>
            <url>http://maven.ndpar.com:9191/nexus/content/repositories/snapshots</url>
        </snapshotRepository>
    </distributionManagement>

Configure Maven `release` plugin to use local checkout

    <build>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-release-plugin</artifactId>
                <configuration>
                    <autoVersionSubmodules>true</autoVersionSubmodules>
                    <localCheckout>true</localCheckout>
                </configuration>
            </plugin>
        </plugins>
    </build>

Commit the changes

    $ git add .
    $ git commit -m 'Set up Maven project'

The result Git tree

    * 7e850012 (HEAD, master) Set up Maven project
    * 84a7df07 Initial checkin

## Master release

    $ mvn --batch-mode release:prepare release:perform

The new project version on `master` branch

    <version>1.1-SNAPSHOT</version>

The result Git tree

    * bc7b6556 (HEAD, master) [maven-release-plugin] prepare for next development iteration
    * 1598e062 (gitmaven-1.0) [maven-release-plugin] prepare release gitmaven-1.0
    * 7e850012 Set up Maven project
    * 84a7df07 Initial checkin

## New maintenance branch

Spawn the maintenance branch from the release commit

    $ git checkout -b maint-1.0 gitmaven-1.0

Switch to maintenance versioning

    $ mvn versions:set -DnewVersion=1.0.0-SNAPSHOT

Commit

    $ git add .
    $ git commit -m 'New maintenance branch'

The result Git tree

    * 2e7cc323 (HEAD, maint-1.0) New maintenance branch
    * 1598e062 (gitmaven-1.0) [maven-release-plugin] prepare release gitmaven-1.0
    * 7e850012 Set up Maven project
    * 84a7df07 Initial checkin

## Maintenance release

On the maintenance branch

    $ mvn --batch-mode release:prepare release:perform

New project version

    <version>1.0.1-SNAPSHOT</version>

Merge the changes into `master` branch favoring the master version

    $ git checkout master
    $ git merge -s ours maint-1.0

Double check the project version, it must be the same as before

    <version>1.1-SNAPSHOT</version>

The result Git tree

    *   31273b40 (HEAD, master) Merge branch 'maint-1.0'
    |\
    | * c1f1772a (maint-1.0) [maven-release-plugin] prepare for next development iteration
    | * 3ff5dd60 (gitmaven-1.0.0) [maven-release-plugin] prepare release gitmaven-1.0.0
    | * 2e7cc323 New maintenance branch
    * | bc7b6556 [maven-release-plugin] prepare for next development iteration
    |/
    * 1598e062 (gitmaven-1.0) [maven-release-plugin] prepare release gitmaven-1.0
    * 7e850012 Set up Maven project
    * 84a7df07 Initial checkin

## Configure remote Git repository (optional)

Master branch

    $ git checkout master
    $ git remote add origin git@github.com:ndpar/gitmaven.git
    $ git push origin master

Maintenance branch

    $ git checkout maint-1.0
    $ git-publish-branch

## Push to remote Git repository (optional)

Master branch

    $ git checkout master
    $ git push
    $ git push --tags

Maintenance branch

    $ git checkout maint-1.0
    $ git push
    $ git push --tags

### Resources

[git-publish-branch](http://git-wt-commit.rubyforge.org/#git-publish-branch) script

