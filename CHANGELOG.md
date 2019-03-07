# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## 1.0.0

### Added

- This file.
- All lesson branches as monorepo.
- Yarn, workspaces, build scripts, and readme.
- `prettier#1.16`.

### Changed

- Buefy's css import has been changed to `import 'buefy/dist/buefy.css'`
- Update `vue-rx#5.0.0->6.1.0`.
- Update `rxjs#5.5.12->6.4.0`.
- Update `buefy#0.6.7->0.7.3`.
- Update `sass-loader#6.0.7->7.1.0`.
- Replace `.map`, `.pluck`, and `switchmap`, with `.pipe` and imported rxjs operators
- Directly import `Observable` methods from rxjs
- Replace `.catch` with the `catchError` operator
- Fix Yoda's id

### Removed

- All lesson branches.
- `Rx` import in *main.js* files
