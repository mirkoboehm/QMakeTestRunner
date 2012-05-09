# QMakeTestRunner

QMakeTestRunner assists in setting library paths when running unit tests in products built with QMake. It makes _make test_ work out of the box right after makefiles have been generated.

## Motivation, aka "The Problem"

QMake as a build system is simple and complete for building code, but it lacks a simple way to run unit tests that depend on libraries that are part of the project itself. In this case, paths that are created in the build directory need to be added to the dynamic linker library path before the tests can be executed. Especially with shadow builds, this would require manual setup, and _make test_ would not work out of the box after QMake has been run.

## QMakeTestRunner, aka "The Solution"

As soon as any target in a QMake project has the _testcase_ attribute assigned to *CONFIG*, QMake automatically generates a target called _check_ that runs this test. Unfortunately, _make check_ does not allow to manipulate the linker paths before running the tests either. QMakeTestRunner contains boilerplate code that wraps QMake's _make check_ target with a new one called _make test_, and through variables specified in the QMake file, allows to specify paths to be added to the linker path.

## Usage

QMakeTestRunner is intended to be used as a git submodule to the main project. It should not require any modifications to be used. It requires Python to be in the path, checks for it, and the QMake run will fail if Python cannot be detected. When using other version control systems, QMakeTestRunner should be small enough to simply be copied into the project as a subdirectory.

### Project configuration example

The following example adds the directory _libs/_ in the project build directory to the linker path. It assumes the QMakeTestRunner repo is located under _3rdparty/QMakeTestRunner_. The path where the dynamic library is generated in below the output (build) directory, which is why it is prefixed with *OUT_PWD*:

<pre><code>TEST_LIB_PATHS += $$OUT_PWD/libs
include( 3rdparty/QMakeTestRunner/testtarget.pri )
</code></pre>

To trigger extra diagnostic output of the test runner, add 

<pre><code>TEST_VERBOSE = 1
</code></pre>

before including test target.pri.

### _make test_

Once testtarget.pri has been included, a _test_ target is defined in the makefiles. Simply run _make test_ to execute the original _make check_ with the necessary paths.

### Mac OSX Frameworks

Frameworks on OSX are a different kind of library. The framework path is passed in to the dynamic linker using a different environment variable. To specify a framework path, set the _TEST_FRAMEWORK_PATHS_ variable before including _testtarget.pri_.

<pre><code>TEST_FRAMEWORK_PATHS +=  $$OUT_PWD/frameworks</pre></code>

# License, contributions, issues and support

_QMakeTestRunner_ is Free Software licensed under the GPL, version 3. Contributions to it are welcome, please propose them as Github pull requests. To submit a bug report or feature wish, please use the Github issue tracker for the project. 

Commercial support for QMaketestRunner or general issues with QMake is provided by [Agile Workers Software](https://github.com/AgileWorkersSoftware), we are happy to help.
