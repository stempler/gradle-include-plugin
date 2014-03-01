gradle-include-plugin
=====================

Parametrizable includes for Gradle build scripts. The plugin allows to include external build scripts quite similar to using `apply from: ...`. In contrast to *apply from* you can directly call specific methods defined in the included script and if applicable provide parameter values.

*gradle-include-plugin* has been tested with Gradle 1.11.

Usage
-----

First apply the plugin in your build file:

```groovy
buildscript {
	repositories {
		mavenCentral()
	}
	dependencies {
		classpath 'org.standardout:gradle-include-plugin:0.1'
	}
}

apply plugin: 'include'
```

Specify scripts to include like this:

```groovy
include {
  from 'modules/test.groovy'
}
```

This will execute the script body, like `apply from: 'modules/test.groovy'` would do.

Adding a closure allows to call specific methods defined in the included script - the script body will not be executed, unless you call it explicitly inside the closure with `run()`:

```groovy
include {
  from 'modules/test.groovy', {
    hello('world')
  }
}
```

The included **modules/test.groovy** for these examples could look like this:

```groovy
def hello(String who = 'Gradle') {
  project.logger.info "Hello $who!"
}

hello()
```

So for the first include example the script would be run and **hello** executed without parameters, for the second include example instead the **hello** method is explicitly called with a custom parameter (and `Hello world!` is logged).

The [sample project for bnd-platform](https://github.com/stempler/bnd-platform-sample) also contains some examples on using the *gradle-include-plugin*. Check it out to see more complex and complete examples of using the plugin.

### Available properties

The following properties are injected into the script:

* **project** - the Gradle project
* **thisFile** - the File object representing the included script
* **thisDir** - the directory the included script resides in (also a File object)
 
Apart from this any properties or methods that are not found in the script will be resolved against the project. So the script that is included can be defined similar to a normal build script.

### Including directories

You can also specify a directory instead of a script file to be included. The plugin will look for files with `.gradle` or `.groovy` extensions. If a file is found that has the same name as the directory, only this file is included, if no such file is found all script files encountered are included. If no closure is applied, each file is executed, otherwise the methods called in the closure are called on each script.

```groovy
include {
  from 'modules' // run all scripts
}
```

```groovy
include {
  from('modules') {
    hello() // all scripts must have a hello method that is executed
  }
}
```

License
-------

This software is licensed under the
[Apache License, Version 2.0](http://www.apache.org/licenses/LICENSE-2.0).

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
