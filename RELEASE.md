# PlUES Release Process Manual

This document describes how to release of the projects involved in the PlUES
Timetable Validation Tool and project.

[TOC]

## Introduction

Project releases have to follow this logical order to avoid problems in the process:

1. mincer
* model-generator
* data
* models
* plues

## Mincer

### 1. Release
* Remember to pull/fetch-merge.
* `git flow release start <version>`
* `lein ancient` to check if the dependencies are up to date.
* `lein test`

If everything works as expected

Bump the version number, e.g. from `3.0.0-SNAPSHOT` to `3.0.0`

* `bumpversion --verbose release`
* and finish the release with `git flow release finish`

### 2. Next Development Version

In the develop branch:

`bumpversion --verbose minor` or `bumpversion --verbose patch` to set the next
version number. E.g. after releasing `3.0.0`

    `bumpversion --verbose minor` would create version `3.1.0-SNAPSHOT`

### 3. Push everything

    git push origin master:master --tags
    git push origin develop:develop

## Model-Generator

### 1. Release

Remember to pull/fetch-merge.

steps:

    git flow release start <version>
    ./gradlew check

If everything works as expected:

    bumpversion --verbose release
    git flow release finish

### 2. Next development version

In the develop branch set the version for the development version with:

    bumpversion --verbose major|minor|patch

### 3. Push Everything

    git push origin master:master --tags
    git push origin develop:develop

## Data

### 1. Release

Remember to pull/fetch-merge.

    git flow release start <version>
    bumpversion release

Update the `Makefile`:

* set `MINCER_VERSION` and `MODEL_GENERATOR_VERSION` to the
  corresponding release versions as chosen in the steps above.
* Once builds above have finished run `make data.mch` to verify everything works as expected.

If everything works as expected:

    git add Makefile
    git commit

    git flow release finish

### 2. Next development version

In the develop branch:

1. Update `Makefile`
 * set `MINCER_VERSION ` and `MODEL_GENERATOR_VERSION` to the
   corresponding `SNAPSHOT` versions as chosen in the steps above.
* Once builds above have finished run `make data.mch` to verify everything works as expected.
* Commit changes.

bump version to next development version:

    bumpversion --verbose patch|major|minor

### 3. Push Everything

    git push origin master:master --tags
    git push origin develop:develop

### 4. Creating sqlite3 Files for Distribution:

    make philfak-data.sqlite3 flavor=philfak
    make wiwi-data.sqlite3 flavor=wiwi
    make cs-data.sqlite3 flavor=cs

## Models

### 1. Release

Remember to pull/fetch-merge.

    git flow release start <version>
    git submodule update --init
    (cd data; git checkout master; git pull origin master)
    git add data
    git commit -m 'Updated submodule to the latest release'

#### Run tests:

**Setup:**

 * Requirements for python test runner are defined in `tests/requirements.txt`
  * Install with `pip install -r tests/requirements.txt` as appropiate.
 * Update `tests/data/raw/Makefile`
  * set `MINCER_VERSION` and `MODEL_GENERATOR_VERSION` to the
  corresponding release versions as chosen in the steps above.
  * `git add tests/data/raw/Makefile`
  * `git commit -m 'Updated test dependencies to release versions'`
 * run `make test-data` to regenerate test data using the latest versions of **model-generator** and
   **mincer**.


**Run Tests**

    make data.mch solver7_tests tests

If everything works as expected commit changes and:

    git add tests/data/raw
    git commit -m 'Regenerated test data files'
    bumpversion --verbose release
    git flow release finish

### 2. Next development version


In the develop branch:

* Update `tests/data/raw/Makefile`
 * set `MINCER_VERSION` and `MODEL_GENERATOR_VERSION` to the
   corresponding `SNAPSHOT` versions as chosen in the steps above.
* run `make test-data` to regenerate test data using the latest versions of
**model-generator** and
  **mincer**.
* commit changes
  * `git add tests/data/raw/Makefile`
  * `git commit -m 'Updated test dependencies to development versions'`
  * `git add tests/data/raw`
  * `git commit -m 'Regenerated test files'`


then run:

    (cd data; git checkout develop; git pull)
    git add data
    git commit -m 'Updated submodule to the latest development version'
    bumpversion --verbose major|minor|patch

### 3. Push Everything

    git push origin master:master --tags
    git push origin develop:develop

## Handbook

The handbook also follows the git-flow process.

### 1. Release

#### Start release process

Remember to pull/fetch-merge.

    git flow release start <version>

#### Bump version number

    bumpversion --verbose release

#### Update Handbook for release

Update tool Version numbers and URLs in `dokumentation.md`:

* Section 1.1 links and versions
* Section 2.1: Version


#### Check handbook

run the following command to generate the pdf and html versions of the handbook to check that everything workds as expected

    make


If everything is as expected commit changes:

    git add dokumentation.md
    git commit -m 'Updated version numbers'

And finish the release with:

    git flow release finish
    git push origin master:master --tags
    git push origin develop



### 2. Next Version

In the **develop** branch set the version for the next release:

    bumpversion major|minor|patch

#### Update Handbook for development

Update tool Version numbers and URLs in `dokumentation.md`:

* Section 1.1 Links and versions
* Section 2.1: Version

Commit `dokumentation.md` and push everything:

    git push origin develop:develop

## PlUES

### 1. Release

#### Start Release

    git flow release start <version>

#### Update model-generator submodule

    (cd model-generator; git checkout master; git pull)
    git add model-generator
    git commit -m 'Updated submodule to the latest release'

#### Update required model version in `src/main/resources/main.properties`:

 * Set `model_version` to released version of models project, as chosen above.

Commit changes:

    git add src/main/resources/main.properties
    git commit -m 'Updated required version of models'

#### Update Handbook URL

 * Set `handbook-url-html` and `handbook-url-pdf` to the URL of the released handbook in `src/main/resources/main.properties`.

Commit changes:

    git add src/main/resources/main.properties
    git commit -m 'Updated handbook url'

#### Update ProB2

* Update `build.gradle`. Switch from `SNAPSHOT` to latest ProB2 release. [Maven Central](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22de.prob2.kernel%22)

```
git add build.gradle
git commit -m 'Updated ProB2 to latest release'
```

#### Run tests

    ./gradlew clean check -Pheadless=true

If everything works as expected:

    bumpversion --verbose release
    git flow release finish

### 2. Next development version

In the **develop** branch:

#### Update model-generator submodule

    (cd model-generator; git checkout develop; git pull)
    git add model-generator
    git commit -m 'Updated submodule to the latest development version'

#### Update required model version in `src/main/resources/main.properties`:

 * Set `model_version` to released version of models project, as chosen above.

Commit changes:

    git add src/main/resources/main.properties
    git commit -m 'Updated required version of models'

#### Update Handbook URL

 * Set `handbook-url-html` and `handbook-url-pdf` to the URL of the latest development version handbook in `src/main/resources/main.properties`.

Commit changes:

    git add src/main/resources/main.properties
    git commit -m 'Updated handbook url'

#### Update ProB2

* Update `build.gradle`. Switch to the current `SNAPSHOT` of ProB2. [Maven Central](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22de.prob2.kernel%22)

```
git add build.gradle
git commit -m 'Updated ProB2 to SNAPSHOT release'
```

#### Set next development version

    bumpversion --verbose major|minor|patch

### 3. Push Everything

    git push origin master:master --tags
    git push origin develop:develop

## Distribution

#### 1. Packaging `mincer`

On the corresponding branch, i.e. `master`.

Download release build from https://www3.hhu.de/stups/downloads/plues/mincer/ or run:

    lein uberjar launch4j

Upload `mincer-<VERSION>-standalone.jar` and mincer-<VERSION>.exe` to the corresponding tag/release at https://github.com/plues/mincer/releases/.


#### 2. Packaging `model-generator`

On the corresponding branch, i.e. `master`.

Download the release to be published from https://www3.hhu.de/stups/downloads/plues/model-generator/ or run:

     ./gradlew buildStandaloneJar

Upload `model-generator-4.2.0-SNAPSHOT.jar` and `model-generator-standalone-4.2.0-SNAPSHOT.jar` to the corresponding tag/release at https://github.com/plues/model-generator/releases.

#### 3. Packaging `data`

On the corresponding branch, i.e. `master`.

Generate all databases:

    make philfak-data.sqlite3
    make wiwi-data.sqlite3 flavor=wiwi
    make cs-data.sqlite3 flavor=cs

Upload `philfak-data.sqlite`, `wiwi-data.sqlite3` and `cs-data.sqlite3` to the corresponding release at https://github.com/plues/data/releases.


#### 4. Packaging `models`

go to the models repository checkout.

In the **corresponding branch** for the release (e.g. master) run:

    make very_clean dist

This copies and packages all relevant model files to `dist/models.zip`.

#### Add to PlUES

Copy the created dist/models.zip file to the resources direcotry in the plues
project: `src/main/resources`.

### Publish on github

Upload `dist/models.zip` to the corresponding release at https://github.com/plues/models/releases.

#### 5. Packaging `handbook`

Go to the corresponding branch (e.g. **master**) in you `handbook` checkout and generate the handbook files with:

    make


#### Add to PlUES
Copy the generated `.html` and `.pdf` files to your plues checkout:

    cp handbook-<version>.html <plues checkout>/src/main/resources/doc/handbook.html
    cp handbook-<version>.pdf <plues checkout>/src/main/resources/doc/handbook.pdf

#### Publish on github

Upload `handbook-<version>.html` and `handbook-<version>.pdf` to the corresponding release at https://github.com/plues/handbook/releases.



#### 6. Create Distributable Packages of `plues`

On the corresponding branch, i.e. `master`.

**IMPORTANT** Make sure `src/main/resources/local.properties` does not override any settings.

In the `plues` repositry run:

    ./gradlew clean createDmg winZip distTar

this step creates the following artifacts, which can be distributed:

* `build/distributions/plues-<VERSION>.dmg` (Mac Os)
* `build/distributions/plues-<VERSION>.tar`
* `build/launch4j/plues-<VERSION>-win.zip` (Windows)
