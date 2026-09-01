# RoboCodo releases

This public repository contains official Windows installers and update metadata for RoboCodo.

RoboCodo source code is maintained separately in a private repository. Release artifacts are produced by a verified build that rejects source maps, original TypeScript files, inline source content, and packaged development dependencies.

## Downloading

Download the newest Windows installer from [Releases](https://github.com/FE-Engineer-Youtube/robocodo-releases/releases/latest).

> RoboCodo is currently an unsigned preview. Windows may display a publisher warning until code signing is enabled.

## Contents

Each release is expected to contain only:

- `RoboCodo-Setup-<version>-x64.exe`
- `RoboCodo-Setup-<version>-x64.exe.blockmap`
- `latest.yml`

The block map and YAML file support application updates; end users normally only need the installer.
