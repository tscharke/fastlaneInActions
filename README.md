![Badge](https://github.com/tscharke/fastlaneInActions/workflows/CI/badge.svg)

The idea is to build ~~and ship~~ a **React-Native**-App (iOS ~~and Android~~\*) with [Fastlane](https://fastlane.tools) and [GitHub-Actions](https://help.github.com/en/articles/about-github-actions).

Or in simpler words: Using GitHub-Actions as a **CI** for a **React-Native**-App's.

I'm trying to document my way to get this goal here.

\*_Note: Currently the React-Native-App is only **built** and that only for **iOS** 🤷‍_

# TOC

- [Prerequisites](#Prerequisites)
  - [Creating a certificate and identifier](#Creating-a-certificate-and-identifier)
    - [Creating a profile with fastlane](#Creating-a-profile-with-fastlane)
      - [Fastlane match](#Fastlane-match)
      - [Create a certificate git repository](#Create-a-certificate-git-repository)
        - [The URI to your certificate repository](#The-URI-to-your-certificate-repository)
      - [Run match and create & store your certificates & provisioning profiles](#Run-match-and-create-&-store-your-certificates-&-provisioning-profiles)
- [Build the React-Native-App on your machine](#Build-the-React-Native-App-on-your-machine)
  - [Create a dotfile with personal Apple-Account-Credentials](#Create-a-dotfile-with-personal-Apple-Account-Credentials)
  - [Build app with fastlane](#Build-app-with-fastlane)
- [Run the React-Native-App on your machine](#Run-the-React-Native-App-on-your-machine)
- [Build the React-Native-App on GitHub](#Build-the-React-Native-App-on-GitHub)
  - [Where is the magic](#Where-is-the-magic)
  - [Using this with your own repository](#Using-this-with-your-own-repository)
    - [Set some secrets on your repository](#Set-some-secrets-on-your-repository)

# Prerequisites

- A machine with macOX >= 10.14.6
- Xcode >= 10.3
- The latest Xcode `command line tools` 👉 `xcode-select --install`
- Cocoapods >= 1.7.1 👉 https://cocoapods.org
- Global installed react-native-cli >= 2.0.1 👉`npm install -g react-native-cli`
- Ruby >= 2.3.3p222
- Fastlane >= 2.133.0
- Apple ID
- Apple Developer Account 👉 https://developer.apple.com/account

## Creating a certificate and identifier

The next steps are a walk through at [Apple's Developer Account-Platform](https://developer.apple.com/account) inside the section [Certificates, Identifiers & Profiles](https://developer.apple.com/account/resources/certificates/list).

1. Create a new certificate of type `iOS Development`. If you already own one, then this step is _optional_.
2. Create a new [identifier](https://developer.apple.com/account/resources/identifiers/list) with following properties: <br/>

   | Type         | Value                              | Comment                                               |
   | ------------ | ---------------------------------- | ----------------------------------------------------- |
   | Platform     | iOS, tvOS, watchOS                 | Required                                              |
   | Description  | FastlaneInActionsApp               | Optional, choose a name you like                      |
   | Bundle ID    | `de.dermeer.van.fastlaneInActions` | Required, cause we need exactly this `APP_IDENTIFIER` |
   | Capabilities | Use the defaults                   |                                                       |

3. The profile we will automatically create with `fastlane` on the local machine.

### Creating a profile with fastlane

#### Fastlane match

We use [fastlane's `match`](https://docs.fastlane.tools/actions/match) to create and manage all required certificates & provisioning profiles and stores them in a separate git repository.

With this, `match` is a implementation of the [codesigning.guide concept](https://codesigning.guide) that you should read through unconditionally!

So you have to create such a **certificate git repository** first.

#### Create a certificate git repository

You can create your certificate repository on GitHub and Bitbucket. I use GitHub myself, so that I also briefly describe the way how to set it up for GitHub here:

1. Open [https://github.com/new](https://github.com/new).
2. Choose a repository name (e.g `certificates`).
3. Make sure it's a **private** repository.
4. Do not initialize this repository with a readme nor a license.
5. Create the repository.

And there's one last step…To give fastlane's `match` access to this repository you've to create a [_Personal access tokens_](https://github.com/settings/tokens) at GitHub.

Create/Generate a new token with a name of your choise and select the scope `repo` only.

##### The URI to your certificate repository

Keep the generated token, as we use it to create the url to your certificate repository.

The URL for fastlane's `match` follow this pattern: `https://{token}@{url to your repository}`. Means…

- Copy the **https**-uri of your above created repository 👉 [remember?](#create-a-certificate-git-repository) (e.g. `https://github.com/johndoe/certificates.git`).
- Create the url with aboves pattern, your token and the url of your repository.

A typcial url for fastlane's `match` looks like this: `https://4711@github.com/johndoe/certificates.git`

#### Run match and create & store your certificates & provisioning profiles

With this setup we're prepared to use fastlane's `match` on your local machine to create all missing certificates & provisioning profiles and store them in your personal and **private** certificate repository.

You can now…

- open a terminal,
- moving to the folder in which you checked out this repository,
- running `fastlane match development`.

During this process fastlane's `match` ask you for some data. It needs the url to the certificate repository (**IMPORTANT**: use the **https**-url with **token**), your **Apple Id** and an **APP_IDENTIFIER** (in our case `de.dermeer.van.fastlaneInActions`).

If the process finnished it stored all newly created certificates & provisioning profiles at your private certificate repository and at Apple's Developer Account-Platform inside the section [Profiles](https://developer.apple.com/account/resources/profiles/list)

# Build the React-Native-App on your machine

## Create a dotfile with personal Apple-Account-Credentials

If you've checkout this repository you'll find already a [Matchfile](fastlane/__Matchfile) inside the [fastlane folder](./fastlane). This `Matchfile` provides some information for fastlane. Which kind of information and more you can read in [fastlane's docs for `match`](https://docs.fastlane.tools/actions/match).

Relevant for now is that our `Matchfile` uses the environment variables to set…

- `URL_TO_FASTLANE_CERTIFICATES_REPO` and
- `APP_IDENTIFIER`.

To make our life easier we will set this environment variables via a **Dotfile** with name `.env`. You can do it by running this bash command (replace the placeholder with your own values) inside the folder in which you checked out this repository:

```bash
cat <<EOF >> ./fastlane/.env
APP_IDENTIFIER=de.dermeer.van.fastlaneInActions
URL_TO_FASTLANE_CERTIFICATES_REPO={ THE URL TO YOUR CERTIFICATION-REPOSITORY* }
EOF
```

\* **IMPORTANT**: This is the **https**-url with **token**!!!

This creats your personal Dotfile [./fastlane/.env](./fastlane/.env) and you can modified as you like and needed.

Do **NOT checkin** this Dotfile. It's already listed in [gitignore](.gitignore#L57).

## Build app with fastlane

At this point you're well prepared and we can build (and only build) the React-Native-App with fastlane by running the command `fastlane ios buildAndShip` or simpler by running the script with yarn: `yarn ios:buildAndShip`.

If your setup was successful, at the end you will find an IPA-file and dSYM-file in folder [./fastlane/builds](./fastlane/builds).

# Run the React-Native-App on your machine

Running the React-Native-App on your machine is possible by unsing the React-Native CLI: `react-native run-ios` or by running the script with yarn: `yarn ios:app`.

It will starts the iOS-Simulator and will show the default "Welcome to React"-App:

![Welcome to React](https://facebook.github.io/react-native/img/homepage/phones.png)

# Build the React-Native-App on GitHub

The idea on the goal of this project was to build a **React-Native**-App with Fastlane on GitHub-Actions and therefor using GitHub as a CI.

You can see the result in the **Actions**-panel of this repository at GitHub 👉[https://github.com/tscharke/fastlaneInActions/actions](https://github.com/tscharke/fastlaneInActions/actions)

## Where is the magic

All _magic_ to build the React-Native-App with GitHub-Actions are exist in the Workflows yml-file [.github/workflows/ci.yml](.github/workflows/ci.yml).

An intruduction what GitHub-Actions are and how these can be used you find very well explained in the [GitHub-Actions documentation](https://help.github.com/en/github/automating-your-workflow-with-github-actions).

Short what is being done here or better whats happend in our Workflows yml-file:

1. Make sure what all runs on `macOX`.
2. Install `node` (for this we use the predefined Action [actions/setup-node](https://github.com/actions/setup-node)).
3. Checkout the current repository.
4. Install all dependencies (`yarn install`).
5. Run all test (`yarn test`).
6. Run `fastlane` to build our React-Native-App (`yarn ios:buildAndShip`).

If you wonder from where the `fastlane` comes? GithHub-Actions directly provided a `fastlane` for use (thanks to Jens N. for this tip 🙏). So it's not necessary to install `ruby` nor `gems`.

The installation of `pods` we do directly in our [Fastfile](./fastlane/Fastfile#L40) with [fastlane's `cocoapods`-Action](https://docs.fastlane.tools/actions/cocoapods).

## Using this with your own repository

Feel free to clone this repository or simple copy the Workflows YML-File [.github/workflows/ci.yml](.github/workflows/ci.yml).

As you can see - inside the Workflows YML-File - that we're sharing following environment-variables to the CI-System:

| Name                              | Value                              | Description                                                                            |
| --------------------------------- | ---------------------------------- | -------------------------------------------------------------------------------------- |
| APP_IDENTIFIER                    | `de.dermeer.van.fastlaneInActions` |                                                                                        |
| URL_TO_FASTLANE_CERTIFICATES_REPO | **SECRET**                         | The **https**-url (with **token**) to your personal and private certificate repository |
| MATCH_PASSWORD                    | **SECRET**                         | Password of your certificate repository                                                |
| CI                                | true                               |                                                                                        |
| CI_KEYCHAIN_NAME                  | `CI_KEYCHAIN`                      |                                                                                        |
| CI_KEYCHAIN_PASSWORD              | **SECRET**                         | Password for the internal used keychain. You may bet on any value you like             |

### Set some secrets on your repository

What is it with the **SECRET** marke values?

This are values you **never share** public and they're needed on your repository too. So please provide this environment-variables as [GitHub-Secrets](https://help.github.com/en/github/automating-your-workflow-with-github-actions/virtual-environments-for-github-actions#creating-and-using-secrets-encrypted-variables) on your own repository.

#### Names of secrets to set on your repository

- URL_TO_FASTLANE_CERTIFICATES_REPO\*
- MATCH_PASSWORD
- CI_KEYCHAIN_PASSWORD

\* This is the **https**-url with **token**!!!
