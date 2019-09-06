# Repo to standardize the way I publish npm packages

You can install `yarn` from [here](https://yarnpkg.com/en/docs/install#debian-stable).
Make sure you have Node installed on your machine (this also gives you `npm` out of the box). You can install Node from [here](https://nodejs.org/en/download/).

### Creating the project directory

The first step is to create the project directory which you aim to publish. This step requires you to have yarn or npm installed.

```
mkdir project-directory-name
cd project-directory-name
yarn init
```

The CLI wizard will guide you through a series of steps to generate your `package.json` configuration. Alternatively you can use `yarn init -y` flag to get a default configuration.
