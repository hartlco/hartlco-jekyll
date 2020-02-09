---
layout: post
title: "CI and TestFlight deployments using GitHub Actions"
date: "2020-02-09 18:06"
---

A typical post of the category: "Better write a blog post about it, otherwise I will forget how to make it work"

Early on in the development of Icro I integrated [fastlane](https://fastlane.tools) to run continuous integration tests on [bitrise](https://www.bitrise.io) and build and uploaded release version from a local Mac. As GitHub Actions is now available and offers similar features as bitrise for open source projects, I decided to move the CI tests over and also start using it for Apple TestFlight deployments.

## CI

Thanks to the already defined lanes, the migration to run unit tests on every push and pull request was trivially easy.

The [Fastfile](https://github.com/hartlco/Icro/blob/master/fastlane/Fastfile) just uses the `run_tests` action configured with the correct workspace and scheme:

```
default_platform(:ios)

platform :ios do
  desc "Run unit tests"
  lane :tests do
    run_tests(workspace: "Icro.xcworkspace",
              derived_data_path: "derivedData",
              devices: ["iPhone Xs"],
              scheme: "Icro")
  end
  ...
end
``` 

The GitHub action [workflow file](https://github.com/hartlco/Icro/blob/master/.github/workflows/test.yml) defines when the action should be run, installs the bundle dependencies (including fastlane) and runs the lane

```
name: CI

on: [push, pull_request]

jobs:
  build:

    runs-on: macos-latest

    steps:
    - uses: actions/checkout@v1
    - name: submodules-init
      uses: snickerbockers/submodules-init@v4
    - name: Bundle Install
      run: bundle install
    - name: Build and test
      run: bundle exec fastlane tests
```

## TestFlight deployment

Running unit tests usually does not require complicated code-signing steps or access to shared accounts, and passwords. All this is required to deploy builds to TestFlight.
As a first step, the certificates and provisioning profiles need to be made accessible for GitHub actions. Luckily fastlane also helps with this: [match](https://docs.fastlane.tools/actions/match/). Following the guide to integrate fastlane match, I created a repository accessible for GitHub Actions to store encrypted profiles and certificates. The passphrase for the certificates is saved inside GitHub Secrets. Fastlane match will then install the certificates in the keychain of the Action runner to successfully sign the build.
The added lane to the [Fastfile](https://github.com/hartlco/Icro/blob/master/fastlane/Fastfile) looks like this:

```
lane :beta_ci do
    bump_version
    create_keychain(
      name: "Fastlane_CI",
      password: "CI_Password",
      default_keychain: true,
      unlock: true,
      timeout: 3600,
      add_to_search_list: true,
    )
    match(type: "development", readonly: true, keychain_name: 'Fastlane_CI', keychain_password: 'CI_Password')
    match(type: "appstore", readonly: true, keychain_name: 'Fastlane_CI', keychain_password: 'CI_Password')
    build_app(workspace: "Icro.xcworkspace", scheme: "Icro")
    upload_to_testflight(skip_waiting_for_build_processing: true, username: "icro@hartl.co")
  end
```

`bump_version` is just a simple internal lane to set the build number to the number of commits on `master`. We need to call `create_keychain` to use a specialised keychain on the Action runner. Otherwise, the build would be blocked forever, during the signing, a keychain-popup to access the installed certificates would block the execution. Two `match` actions will install the certificates for `development` and `appstore` to ensure a successful signing. Using a restricted App Store Connect user, the build gets uploaded to TestFlight. The Fastfile does not have access the defined GitHub secrets. Therefore the passphrase for match and the password for the App Store Connect account are defined as environment variables on the [deployment workflow file](https://github.com/hartlco/Icro/blob/master/.github/workflows/deploy.yml).

```
name: Deploy

on:
  release:
    types: [published]

jobs:
  build:

    runs-on: macos-latest

    steps:
    - uses: actions/checkout@v1
    - name: submodules-init
      uses: snickerbockers/submodules-init@v4
    - name: Bundle Install
      run: bundle install
    - name: Set Appstore Connect User
      run: bundle exec fastlane fastlane-credentials add --username icro@hartl.co --password ${{ secrets.APPSTORE_PASSWORD }}
    - name: Fastlane Beta CI
      run: bundle exec fastlane beta_ci
      env:
        MATCH_PASSWORD: ${{ secrets.KEYSTORE_PASSPHRASE }}
        FASTLANE_PASSWORD: ${{ secrets.APPSTORE_PASSWORD }}
```

The deployment is triggered on every newly created release inside GitHub (just a tag). The workflow will install the bundles again, set the fastlane credentials by accessing the `APPSTORE_PASSWORD` inside GitHub secrets. As a final step, the lane gets called with the `match` passphrase and App Store Connect password defined.

With this setup in place, every push and pull request to Icro will run unit tests, every new release will automatically create and upload a new build to App Store Connect.