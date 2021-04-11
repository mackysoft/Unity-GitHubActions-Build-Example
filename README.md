# Unity-GitHubActions-Build-Example
 
[![Build](https://github.com/mackysoft/Unity-GitHubActions-Build-Example/actions/workflows/build.yaml/badge.svg?branch=main)](https://github.com/mackysoft/Unity-GitHubActions-Build-Example/actions/workflows/build.yaml)

An example of building a project using Unity and GitHub Actions.

> Qiita: [UnityとGitHubActionsを使って自動ビルドする【複数プラットフォーム同時ビルド】](https://qiita.com/makihiro_dev/items/03208a83d4c3a1fb426b)


## 🔰 Tutorial

### 1. Prepare your project repository

Prepare a repository for the Unity project that contains the tests.


### 2. Acquire ULF

Acquire the ULF file to activate your Unity license.
Use the following tool to acquire the ULF file.

https://github.com/mackysoft/Unity-ManualActivation


### 3. Register ULF to Secrets

1. Select the `Settings > Secrets` menu in the project repository.
2. Click the `New repository secret` button.
3. Enter "UNITY_LICENSE" in Name and copy and paste the contents of the ULF file in Value.
4. Click the `Add secret` button.

You can now treat the contents of the ULF file as an environment variable while keeping its contents private.


### 4. Write the YAML to build on GitHub Actions.

Create a `build.yaml` under the `.github/workflows/` folder of the repository, and write the following.

```yaml:build.yaml
name: Build

on:
  pull_request: {}
  push: { branches: [main] }

jobs:
  build:
    name: ${{ matrix.targetPlatform }}
    runs-on: ubuntu-latest
    strategy:
      fail-fast: false
      matrix:
        targetPlatform:
          - StandaloneOSX # Build a macOS standalone (Intel 64-bit).
          - StandaloneWindows # Build a Windows standalone.
          - StandaloneWindows64 # Build a Windows 64-bit standalone.
          - StandaloneLinux64 # Build a Linux 64-bit standalone.
          - iOS # Build an iOS player.
          - Android # Build an Android .apk standalone app.
          - WebGL # WebGL.

    steps:
      # Checkout
      - name: Checkout
        uses: actions/checkout@v2
        with:
          fetch-depth: 0
          lfs: true

      # Cache
      - name: Cache
        uses: actions/cache@v2
        with:
          path: Library
          key: Library-${{ matrix.targetPlatform }}
          restore-keys: Library-

      # Build
      - name: Build
        uses: game-ci/unity-builder@v2
        env:
          UNITY_LICENSE: ${{ secrets.UNITY_LICENSE }}
        with:
          targetPlatform: ${{ matrix.targetPlatform }}

      # Upload Build
      - name: Upload Build
        uses: actions/upload-artifact@v2
        with:
          name: Build-${{ matrix.targetPlatform }}
          path: build/${{ matrix.targetPlatform }}
```


### 5. Build

1. Push or Pull Request to the repository and run the workflow to build the project.
2. Wait for the workflow to complete.

If you see a ✔ mark as shown below, you have succeeded.

![BuildResult](https://user-images.githubusercontent.com/13536348/114309797-70967000-9b23-11eb-9f02-bf50925c825b.jpg)

You can download the built artifacts from Artifacts in the upper right corner.


#  📔 Author Info

Hiroya Aramaki is a indie game developer in Japan.

- Blog: [https://mackysoft.net/blog](https://mackysoft.net/blog)
- Twitter: [https://twitter.com/makihiro_dev](https://twitter.com/makihiro_dev)


#  📜 License

This repository is under the MIT License.