[![Gitpod ready-to-code](https://img.shields.io/badge/gitpod-ready_to_code-blue?logo=gitpod&style=flat-square)](https://gitpod.io/#https://github.com/mpetuska/npm-publish)
[![Slack chat](https://img.shields.io/badge/kotlinlang-green?logo=slack&style=flat-square)](https://kotlinlang.slack.com/team/UL1A5BA2X)
[![Dokka docs](https://img.shields.io/badge/docs-dokka-orange?style=flat-square)](http://mpetuska.github.io/npm-publish)
[![Version gradle-plugin-portal](https://img.shields.io/maven-metadata/v?label=gradle%20plugin%20portal&logo=gradle&metadataUrl=https%3A%2F%2Fplugins.gradle.org%2Fm2%2Fdev.petuska%2Fnpm-publish%2Fmaven-metadata.xml&style=flat-square)](https://plugins.gradle.org/plugin/dev.petuska.npm.publish)
[![Version maven-central](https://img.shields.io/maven-central/v/dev.petuska/npm-publish?logo=apache-maven&style=flat-square)](https://mvnrepository.com/artifact/dev.petuska/npm-publish/latest)

# NPM-PUBLISH GRADLE PLUGIN

Gradle plugin enabling NPM publishing (essentially `maven-publish` for NPM packages). Integrates seamlessly with
Kotlin/JS/MPP plugin if applied, providing auto configurations.

> The plugin was last tested with Kotlin 1.5.10 & Gradle 7.0.2

## Setup

Here's a bare minimum setup when using together with one of the kotlin plugins. This setup would produce the following
tasks:

* `assembleJsNpmPublication`
* `packJsNpmPublication`
* `publishJsNpmPublicationToNpmjs`
* `assembleIrNpmPublication`
* `packIrNpmPublication`
* `publishIrNpmPublicationToNpmjs`

```kotlin
plugins {
  id("dev.petuska.npm.publish") version "<VERSION>"
  kotlin("multiplatform") version "1.4.32" // Optional, also supports "js"
}

kotlin {
  // Legacy mode
  js(Legacy) {
    binaries.library()
    browser() // or nodejs()
  }
  // OR IR mode
  js(IR) {
    binaries.library()
    browser() // or nodejs()
  }
}

npmPublishing {
  repositories {
    repository("npmjs") {      
      registry = uri("https://registry.npmjs.org")
      authToken = "asdhkjsdfjvhnsdrishdl"
    }
  }
}
```

### Configuration

You can add publications and npm repositories or override existing configuration defaults via npmPublishing extension.
When kotlin JS/MPP plugin is applied, this plugin will automatically create a publication for each JS target you JS/MPP
project has.

#### DSL

```kotlin
npmPublishing {
  readme = file("README.MD") // (optional) Default readme file
  organization = "my.org" // (Optional) Used as default scope for all publications
  access = PUBLIC // or RESTRICTED. Specifies package visibility, defaults to PUBLIC
  /*
    Enables kotlin jar dependencies (including their transitive dependencies) to be resolved bundled automatically for autogenerated publications.
    Defaults to true and can be overridden for each publication.
    
    Is disabled for IR binaries as they already come with all kotlin dependencies bundled into js output file
   */
  bundleKotlinDependencies = true
  /*
    Adds all bundled dependencies to npm-shrinkwrap.json. Defaults to true and can be overridden for each publication.
    Does not generate a file, even if enabled if there are no bundledDependencies resolved
   */
  shrinkwrapBundledDependencies = true
  /*
    (Optional) Enables run npm publishing with `--dry-run` (does everything except uploading the files). Defaults to false.
   */
  dry = false
  /*
    Overriding default version. Defaults to project.version or rootProject.version, whichever found first
   */
  version = "1.0.0"

  repositories {
    repository("npmjs") {
      registry = uri("https://registry.npmjs.org") // Registry to publish to
      authToken = "asdhkjsdfjvhnsdrishdl" // NPM registry authentication token
      otp = "gfahsdjglknamsdkpjnmasdl" // NPM registry authentication OTP
    }
    repository("bintray") {
      access = RESTRICTED
      registry = ("https://dl.bintray.com/mpetuska/dev.petuska.npm") // Registry to publish to
      authToken = "sngamascdgb" // NPM registry authentication token
      otp = "miopuhimpdfsazxfb" // (Optional) NPM registry authentication OTP
      dry = true // (Optional) Overrides extension default
    }
  }
  publications {
    val jsOne by getting { // Publication build for target declared as `kotlin { js("jsOne") { nodejs() } }`
      scope = "not.my.org" // Overriding package scope that defaulted to organization property from before
      version = "1.0.0-custom" // Overriding version for this publication. Defaults to extension default version
    }
    publication("customPublication") { //Custom publication
      bundleKotlinDependencies = true // Overrides the global default for this publication
      shrinkwrapBundledDependencies = true // Overrides the global default for this publication
      nodeJsDir =
        file("~/nodejs") // NodeJs home directory. Defaults to $NODE_HOME if present or kotlinNodeJsSetup output for default publications
      moduleName = "my-module-name-override" // Defaults to project name
      scope = "other.comp" // Defaults to global organisation
      readme = file("docs/OTHER.MD") // Defaults to global readme
      destinationDir =
        file("$buildDir/vipPackage") // Package collection directory, defaults to File($buildDir/publications/npm/$name")
      main = "my-module-name-override-js.js" // Main output file name, set automatically for default publications
      types = "my-module-name-override-js.d.ts" // TS types output file name, set automatically for default publications

      // Entirely Optional

      dependencies {
        npm("snabbdom", "*")
        npmDev("typescript", "*")
        npmOptional("webpack", "*")
        npmPeer("react", "*")
      }
      files { assemblyDir -> // Specifies what files should be packaged. Preconfigured for default publications, yet can be extended if needed
        from("$assemblyDir/../dir")
        // Rest of your CopySpec     
      }
      pakageJsonFile =
        file("static/package.template.json") // If set will be used as-is ignoring further configurations while getting renamed to package.json regardless of the actual name.
      packageJsonTemplateFile =
        file("templates/package.template.json") // Will be used as a template for default settings, `packageJson` DSL can override its settings.
      packagejson = { // Full package.json override
        main = "./dist/yet-another-override.js"
        types = "./dist/yet-another-override.d.ts"
      }
      packageJson { // Will be patched on top of default generated package.json
        private = false
        bundledDependencies = jsonSet("kotlin") // Suppresses and replaces autogenerated bundled dependencies
        bundledDependencies("kotlin") { // Always includes "kotlin" dependency and filters out the rest by the spec
          -"kotlin-test" // Exclude "kotlin-test" dependency
          +"kotlin-test" // Include "kotlin-test" dependency
          -"kotlin.*".toRegex() // Exclude all dependencies starting with "kotlin"
          +"kotlin.*".toRegex() // Include all dependencies starting with "kotlin"
        }
        keywords = jsonArray(
          "kotlin"
        )
        publishConfig {
          tag = "latest"
        }
        "customField" to jsonObject {
          "customValues" to jsonArray(1, 2, 3)
        }
      }
    }
  }
}
```

#### Properties

Most of the DSL configuration options can also be set/overridden via gradle
properties (`./gradlew task -Pprop.name=propValue`), `gradle.properties` or `local.properties`. Bellow is the full list
of supported properties:

Extension:

* `npm.publish.readme`
* `npm.publish.organization`
* `npm.publish.access`
* `npm.publish.bundleKotlinDependencies`
* `npm.publish.shrinkwrapBundledDependencies`
* `npm.publish.dry`
* `npm.publish.version`

Publication:

* `npm.publish.publication.<name>.bundleKotlinDependencies`
* `npm.publish.publication.<name>.shrinkwrapBundledDependencies`
* `npm.publish.publication.<name>.scope`
* `npm.publish.publication.<name>.moduleName`
* `npm.publish.publication.<name>.version`
* `npm.publish.publication.<name>.main`
* `npm.publish.publication.<name>.types`
* `npm.publish.publication.<name>.readme`
* `npm.publish.publication.<name>.nodeJsDir`
* `npm.publish.publication.<name>.destinationDir`

Repository:

* `npm.publish.repository.<name>.dry`
* `npm.publish.repository.<name>.access`
* `npm.publish.repository.<name>.registry`
* `npm.publish.repository.<name>.otp`
* `npm.publish.repository.<name>.authToken`

## Tasks

The plugin generates the following gradle tasks for various configuration elements:

* NpmPackageAssembleTask: generated for each publication and named
  as `assemble<UpperCammelCasePublicationName>NpmPublication`
* NpmPackTask: generated for each publication and named as `pack<UpperCammelCasePublicationName>NpmPublication`
* NpmPublishTask: generated for each publication + repository combination and named
  as `publish<UpperCammelCasePublicationName>NpmPublicationTo<UpperCammelCaseRepositoryName>`

All created tasks are added as dependencies to grouping tasks to allow for group-invocation:

* NpmPackageAssembleTask: `assemble` task in `build` group
* NpmPackTask: `pack` task in `build` group
* NpmPublishTask: `publish` task in `publishing` group

## Dependency Resolution

Npm dependencies detected/declared for each publication are resolved into relevant package.json dependency block by this
priority order by their name (descending priority):

1. Optional
2. Peer
3. Dev
4. Normal

This ensures that any given dependency does not appear in multiple dependency scopes.

## Known Issues

* [#6435](https://github.com/npm/npm/issues/6435): npm and yarn tries to download bundled dependencies. Can be overcome
  for npm (sadly not yarn) with `shrinkwrapBundledDependencies` option. Note that it works fine for both package
  managers when installing from a tarball. Bug [#2143](https://github.com/npm/cli/issues/2143) on the new npm repo, so
  please vote for that to get it fixed. Bug [#8436](https://github.com/yarnpkg/yarn/issues/8436) on the yarn repo, so
  please vote for that to get it fixed.
