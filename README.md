# Motivation

The idea is to build and ship a **React-Native**-App (iOS and Android) with [Fastlane](https://fastlane.tools) and using [GitHub-Actions](https://help.github.com/en/articles/about-github-actions) to do this.

Or in simpler words: Use GitHub-Actions as a CI for a React-Native-App.

I wanna try to document my way to get this goal here.

So this repository is heavily under constructions (**work in progress**)!

# Contents

- [Prerequisites](#prerequisites)
- [Start the App](#start-the-app)
  - [iOS](#iOS)
- [Build the App with Fastlane](#build-the-app-with-fastlane)
  - [Apple](#apple)
    - [Prerequisites (Build with Fastlane)](<#prerequisites-(build-with-fastlane)>)
      - [Create a dotfile with personal Apple-Account-Credentials](#create-a-dotfile-with-personal-apple-account-credentials)
      - [Setup Apple-Signing-Credentials](#setup-apple-signing-credentials)
- [Fastlane](#fastlane)

# Prerequisites

- A Mac with macOX >= 10.14.6
- Xcode >= 10.3
- The latest Xcode command line tools 👉 `xcode-select --install`
- Cocoapods >= 1.7.1 👉 https://cocoapods.org
- Global installed react-native-cli >= 2.0.1 👉`npm install -g react-native-cli`
- Fastlane >= 2.133.0

# Start the App

## iOS

To start the React-Native-App on your local machine do…

1. Open your terminal
2. Switch to you project-folder
3. Go to your _ios_-folder 👉 `cd ios`
4. (Optional) Install Cocoapods by running `pod install`
5. (Optional) Go back to your project-folder 👉`cd ..`
6. Run the App with `yarn ios:app` or open [./ios/MyAwesomeProject.xcodeproj](ios/MyAwesomeProject.xcodeproj) with Xcode.
7. 🎉🎉🎉

**Note**: The steps 4 and 5 are optional and needed only the very first time.

# Build the App with Fastlane

## Apple

To build the React-Native-App with **Fastlane** just call…

```bash
yarn ios:deploy
```

inside a terminal. This runs the script `fastlane ios beta --env development` (as you can see in [package.json](package.json#L11)). It's only syntactic sugar around the original fastlane-command.

**Note**: If it's the **first time** you run the deploy-script/fastlane-command, please follow the steps in [Prerequisites (Build with Fastlane)](<Prerequisites-(Build-with-Fastlane)>).

### Prerequisites (Build with Fastlane)

#### Create a dotfile with personal Apple-Account-Credentials

Inside our fastlane [Appfile](./fastlane/Appfile) we use an `APP_IDENTIFIER`, an `APPLE_ID` and an `APPLE_TEAM_ID`. This values are received out of a **Dotfile** which wasn't commit and ship with the repository, because it contains private information.

To create this **Dotfile** run the following command and add for `APPLE_ID` and `APPLE_TEAM_ID` your personal Apple-Account-Credentials:

```bash
cat <<EOF >> ./fastlane/.env.development
NODE_BINARY=node
APP_IDENTIFIER=org.reactjs.native.example.MyAwesomeProject
APPLE_ID=
APPLE_TEAM_ID=
EOF
```

This creats your personal Dotfile [./fastlane/.env.development](./fastlane/.env.development) you can modified as you like.

**Note**: Do **NOT checkin** this file. It's already listed in [gitignore](.gitignore#L60).

#### Setup Apple-Signing-Credentials

Before we can run a fast*lane* we have to setup credentials to sign and identify the App. So…

- Open [./ios/MyAwesomeProject.xcodeproj](ios/MyAwesomeProject.xcodeproj) with Xcode.
- Show the "Project Navigator".
- Select _MyAwesomeProject_ (with a single click).
- Inside the "Editor" you will see **PROJECT** and **TARGEST**.
- Select _MyAwesomeProject_ from the **TARGEST**-Group (with a single click).
- Now you can see the Groups **Identity** and **Signing**.
- For this project and scenario we leave all values the way they are set by default. You should see…
  - **Display Name**: `MyAwesomeProject`
  - **Bundle Idenitfier**: `org.reactjs.native.example.MyAwesomeProject`
  - **Version**: `1.0`
  - **Build**: `1``

And now the **IMPORTANT** part! Inside the Group **Signing**…

- Make sure for **Automatically manage sinigning** what you `enable` the checkbox.
- And for **Team** select your personal Apple-Account.
- We're done - close Xcode, please.

# Fastlane

Currently we use [Fastlane-Gym](https://docs.fastlane.tools/actions/gym/) to build the app only and assume that we **automatically manage sinigning** and **automatically create a provisioning profile** (for `org.reactjs.native.example.MyAwesomeProject`) if this not exist.

To use Fastlane we created the two Files: [Appfile](./fastlane/Appfile) and [Fastfile](./fastlane/Fastfile).

The `Fastfile` you can regard as a make/**build**-file. And `Appfile` contains the **configuration** we use inside the `Fastfile`. If you take a closer look to the `Appfile` you see that we reference values/creedentials out of our local [Dotfile](#create-a-dotfile-with-personal-apple-account-credentials).

If everything works as designed we get a IPA-File in our defined Output-Directory `./fastlane/builds/` that we have configured [here](./fastlane/Fastfile#L25)

# Issues

## Build a React-Native-App

- [x] Setup a React-Native-App
- [x] Setup a fastlane workflow to build the App for iOS
- [ ] Setup a GitHub-Action to run Fastlane

## Ship a React-Native-App

- [ ] 👉 (https://github.com/tscharke/fastlaneInActions/issues)
