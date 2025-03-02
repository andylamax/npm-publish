# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

---

## [Unreleased]

### Verified Versions

- Kotlin: 1.6.21
- Gradle: 7.4.2
- JDK: 11

### Added

### Changed

### Removed

---

## [3.0.1] - 2022-06-27

### Verified Versions

- Kotlin: 1.7.0
- Gradle: 7.4.2
- JDK: 11

### Added

### Changed
- Kotlin version bumped to `1.7.0`
- Downgraded language version to `1.4` for better gradle support

### Removed

---

## [3.0.0] - 2022-05-03

### Verified Versions

- Kotlin: 1.6.21
- Gradle: 7.4.2
- JDK: 11

### Added

- New documentation site
- Functional and Integration test coverage
- Gradle caching support for configuration, `NpmAssembleTask` and `NpmPackTask` (including remote caches)
- Detekt analyser and formatter

### Changed

- Kotlin plugin version bumped to `1.6.20`
- `npmPublishing` extension renamed to `npmPublish`
- Default packaging directories changed from `build/publications/npm` to `build/packages`
- `publication` and `repository` DSLs removed. Use regular gradle's `register` DSL
- All API reworked to use gradle provider API. Replace all `=` assignments with proper `.set()` invocation.
- `NpmPackageAssembleTask` renamed to `NpmAssembleTask`
- `NpmPackage::moduleName` renamed to `NpmPackage::packageName`
- `NpmPackage::nodeJsDir` renamed to `nodeHome` and moved to `NpmPublishExtension`. It is now shared across all
  publications.
- `NpmPackage::destinationDir` moved to `NpmAssembleTask`.
- `NpmRegistry::dry` propagated to `NpmPackTask` and `NpmPublishTask` with `--dry` cli option added. Both still default
  to `NpmPublishExtension::dry`
- All plugin tasks made a lot more abstract and reusable. Especially `NodeExecTask` and `NpmExecTask` allowing for
  custom npm script execution on your packages or any other generic tooling.

### Removed

- Support for `LEGACY` compiler. Stick with `2.1.x` if you still depend on it.
- All eager configuration
- Custom kotlin delegates for gradle lazy api
- `dev.petuska:kon` dependency
- `dev.petuska:gradle-lazy-delegates` dependency
- `dev.petuska:klip` dependency
- All kotest dependencies
- Spotless plugin

---

## [2.1.2] - 2022-01-24

### Verified Versions

- Kotlin: 1.6.10
- Gradle: 7.3.3
- JDK: 11

### Added

### Changed

- Kotlin plugin version bumped to `1.6.10`
- Simplified `JsonObject` DSL builders by rebasing it onto `dev.petuska:kon`
- Implemented proper nested `package.json` template merging
- Rolled back to good old `ktlint` formatting

### Removed

---

## [2.1.1] - 2021-09-02

### Verified Versions

- Kotlin: 1.5.30
- Gradle: 7.2.0
- JDK: 11

### Added

- Sample GH action introduced to showcase CI publishing

### Changed

- Fixed #24: `types` configuration property was ignored
- Reworked ts-consumer to showcase module inter-dependencies

### Removed

---

## [2.1.0] - 2021-08-22

### Verified Versions

- Kotlin: 1.5.21
- Gradle: 7.2.0
- JDK: 11

### Added

- Added support for setting configuration properties via environment variables too

### Changed

- Fixed #24: `types` configuration property was ignored
- Kotlin version bumped to `1.5.21`

### Removed

---

## [2.0.4] - 2021-07-21

### Verified Versions

- Kotlin: 1.5.10
- Gradle: 7.1.1
- JDK: 11

### Added

- New sandbox module to check that plugin can work with both, nodejs() and browser() flavours at the same time
- New `ts-consumer` module in the sandbox to showcase how to include packed K/JS modules into plain TS module
- Dynamic version ranges for increased compatibility

### Changed

- Updated versions
- Fixed a bug that always expected version to be a string

### Removed

- Removed some duplicated code in sandbox

---

## [2.0.3] - 2021-06-18

### Verified Versions

- Kotlin: 1.5.10
- Gradle: 7.0.2
- JDK: 11

### Added

### Changed

- \#19 `licence` field typo fix to get it working with npm (big thanks to @gaebel)

### Removed

---

## [2.0.2] - 2021-06-09

### Verified Versions

- Kotlin: 1.5.10
- Gradle: 7.0.2
- JDK: 11

### Added

### Changed

- \#16 Fix NpmPublishTask workingDir scope

### Removed

---

## [2.0.1] - 2021-05-28

### Verified Versions

- Kotlin: 1.5.10
- Gradle: 7.0.2
- JDK: 11

### Added

### Changed

- Eased `duplicationRules` for assemble task. Will now warn instead of fail.

### Removed

---

## [2.0.0] - 2021-05-25

### Verified Versions

- Kotlin: 1.5.10
- Gradle: 7.0.2
- JDK: 11

### Added

### Changed

- Plugin id changed from `lt.petuska.npm.publish` to `dev.petuska.npm.publish`
- Root package changed to `dev.petuska.npm.publish`.
  To migrate, just replace `lt.` part to `dev.` in all imports you might have (tasks, DSL, etc...)

### Removed

---

[Unreleased]: https://github.com/mpetuska/npm-publish/compare/3.0.1...HEAD

[3.0.1]: https://github.com/mpetuska/npm-publish/compare/v3.0.0...3.0.1

[3.0.0]: https://github.com/mpetuska/npm-publish/compare/v2.1.3...3.0.0

[2.1.2]: https://github.com/mpetuska/npm-publish/compare/v2.1.1...2.1.2

[2.1.1]: https://github.com/mpetuska/npm-publish/compare/v2.1.0...v2.1.1

[2.1.0]: https://github.com/mpetuska/npm-publish/compare/v2.0.4...v2.1.0

[2.0.4]: https://github.com/mpetuska/npm-publish/compare/v2.0.3...v2.0.4

[2.0.3]: https://github.com/mpetuska/npm-publish/compare/v2.0.2...v2.0.3

[2.0.2]: https://github.com/mpetuska/npm-publish/compare/v2.0.1...v2.0.2

[2.0.1]: https://github.com/mpetuska/npm-publish/compare/v2.0.0...v2.0.1

[2.0.0]: https://github.com/mpetuska/npm-publish/releases/tag/v2.0.0
