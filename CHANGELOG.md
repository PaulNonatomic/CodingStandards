# Changelog

All notable changes to these coding standards will be documented in this file.

## [Unreleased]

### Added
- **Internal member ordering**: Added guidance for placing `internal` members after serialized fields but before protected members
- **Explicit interface implementation ordering**: Added guidance to treat explicit interface implementations like internal members in the member ordering hierarchy
- **Protected field naming**: Documented that protected fields (including serialized protected fields) use `PascalCase`

### Changed
- **Member ordering**: Updated from `Public → Serialized protected → Serialized private → Protected → Private` to `Public → Serialized protected → Serialized private → Internal / Explicit interface → Protected → Private`
- **Code examples**: Updated `PlayerController` example to demonstrate internal members, explicit interface implementations, and PascalCase for serialized protected fields
