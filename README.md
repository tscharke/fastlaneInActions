# Motivation

The idea is to build and ship a **React-Native**-App (iOS and Android) with [Fastlane](https://fastlane.tools) and using [GitHub-Actions](https://help.github.com/en/articles/about-github-actions) to do this.

Or in simpler words: Use GitHub-Actions as a CI for a React-Native-App.

I wanna try to document my way to get this goal here.

So this repository is heavily under constructions (**work in progress**)!

# Prerequisites

- A Mac with macOX >= 10.14.6
- Xcode >= 10.3
- Cocoapods >= 1.7.1 👉 https://cocoapods.org
- Global installed react-native-cli >= 2.0.1 👉`npm install -g react-native-cli`

# Start

To start the React-Native-App on your local machine do…

1. Open your terminal
2. Switch to you project-folder
3. Go to your _ios_-folder 👉 `cd ios`
4. Install Cocoapods by running `pod install`
5. Go back to your project-folder 👉`cd ..`
6. Run the App with `yarn app:ios` or open [./ios/MyAwesomeProject.xcodeproj](ios/MyAwesomeProject.xcodeproj) with Xcode.
7. 🎉🎉🎉
