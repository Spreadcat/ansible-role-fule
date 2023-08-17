# Changelog

All notable changes to spreadcat.fule will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

### Changed

- Fixed some minor linting issues.
- Disabled galaxy dependency for molecule testing.

### Added

- Added information about the requirement of `gathered_facts` when running the role.

## [2.1.0] - 2023-07-28

### Changed

- Updated `meta/main.yml` with corrected namespace, description and role name.

## [2.1.0] - 2023-07-28

### Added

- Changelog file for this project.
- Support for deploying a changelog file into a role.
- Added test scenario for changelog file deployment.

### Changed

- Default scenario is now checking for Changelog file.
- Disabled molecule dependency on ansible galaxy in all scenarios.
- Changed ansible config parameter for callback plugins.
- Updated readme file.
- Updated gitinore file to exclude vscode directories.

### Fixed

- Markdown syntax error in readme file.
