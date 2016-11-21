# PlUES Release Process Manual

This document describes how to release of the projects involved in the PlUES
Timetable Validation Tool and project.

Project releases have to follow this logical order to avoid problems in the process:

1. mincer
* model-generator
* data
* models
* plues

## Mincer

### 1. Release

* `git flow release start <version>`
* `lein ancient` to check if the dependencies are up to date.
* `lein test`

If everything works as expected

Bump the version number, e.g. from 3.0.0-SNAPSHOT to `3.0.0`

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

    git flow release start <version>

Update the `Makefile`:

* set `MODEL_GENERATOR_VERSION` and `MODEL_GENERATOR_VERSION` to the
  corresponding release versions as chosen in the steps above.
* Once builds above have finished run `make data.mch` to verify everything works as expected.

If everything works as expected:

    git add Makefile
    git commit

    git flow release finish

### 2. Next development version

In the develop branch:

1. Update `Makefile`
 * set `MODEL_GENERATOR_VERSION` and `MODEL_GENERATOR_VERSION` to the
   corresponding `SNAPSHOT` versions as chosen in the steps above.
* Once builds above have finished run `make data.mch` to verify everything works as expected.
* Commit changes.

### 3. Push Everything

    git push origin master:master --tags
    git push origin develop:develop

### 4. Creating sqlite3 Files for Distribution:

    make philfak-data.sqlite3 flavor=philfak
    make wiwi-data.sqlite3 flavor=wiwi
    make cs-data.sqlite3 flavor=cs

## Models

### 1. Release


    git flow release start <version>
    (cd data; git checkout master; git pull)
    git add data
    git commit -m 'Updated submodule to the latest release'

#### Run tests:

**Setup:**

 * Requirements for python test runner are defined in `tests/requirements.txt`
  * Install with `pip install -r tests/requirements.txt` as appropiate.
 * Update `tests/data/raw/Makefile`
  * set `MODEL_GENERATOR_VERSION` and `MODEL_GENERATOR_VERSION` to the
  corresponding release versions as chosen in the steps above.
 * run `make test-data` to regenerate test data using the latest versions of **model-generator** and **mincer**.

**Run Tests**

    make solver7_tests tests

If everything works as expected commint changes and:

    bumpversion --verbose release
    git flow release finish

### 2. Next development version


In the develop branch:

* Update `tests/data/raw/Makefile`
 * set `MODEL_GENERATOR_VERSION` and `MODEL_GENERATOR_VERSION` to the
   corresponding `SNAPSHOT` versions as chosen in the steps above.
* run `make test-data` to regenerate test data using the latest versions of **model-generator** and **mincer**.
* commint changes

then run:

    (cd data; git checkout develop; git pull)
    git commit -m 'Updated submodule to the latest development version'
    bumpversion --verbose major|minor|patch

### 3. Push Everything

    git push origin master:master --tags
    git push origin develop:develop

## PlUES

### 1. Release

    git flow release start <version>
    (cd model-generator; git checkout master)
    git add model-generator
    git commit -m 'Updated submodule to the latest release'

#### Update `src/main/resources/main.properties`:

 * Set `model_version` to released version of models project, as chosen above.

Commit changes:

    git add src/main/resources/main.properties
    git commit -m 'Updated required version of models'

#### Run tests

    ./gradlew clean check -Pheadless=true

If everything works as expected:

    bumpversion --verbose release
    git flow release finish

### 2. Next development version

In the develop branch:

    (cd model-model-generator; git checkout develop)
    git commit -m 'Updated submodule to the latest development version'
    bumpversion --verbose major|minor|patch

### 3. Push Everything

    git push origin master:master --tags
    git push origin develop:develop

### 4. Preparing Distribution Artifacts

#### 1. Packaging `models`

go to the models repository checkout.

In the corresponding branch for the release (e.g. master) run:

    make dist

This copies and packages all relevant model files to `dist/models.zip`.

Copy the created dist/models.zip file to the resources direcotry in the plues
project: `src/main/resources`.

#### 2. Create Distributable Packages

In the `plues` repositry run:

    ./gradlew clean createDmg winZip distTar

this step creates the following artifacts, which can be distributed:

* `build/distributions/plues-<VERSION>.dmg` (Mac Os)
* `build/distributions/plues-<VERSION>.tar`
* `build/launch4j/plues-2.0.0-<VERSION>-win.zip` (Windows)

