---
title: 'Publishing a Rust crate automatically using Travis'
published: true
---

Publishing a Rust crate is pretty darn simple in the first place, but because we're developers, there's no reason why we shouldn't make it even simpler.

Enter Travis. It's a continuous integration and delivery platform, and it's free for open-source projects! You'll need to [sign up for Travis](https://travis-ci.com/) using your GitHub account, then we can get started!

# Step 1: Add a Travis config to your Rust project

The prerequisite to this tutorial is that you have a Rust project you'd like to publish! Assuming so, create a new file in the project's root directory called `.travis.yml` (notice the leading `.` character), and fill it with the following contents:

```yaml
language: rust
script: cargo test
deploy:
  on:
    branch: master
  provider: cargo
  token: $CRATES_TOKEN
```

Let's briefly go through this line by line.

1. We specify `rust` as the language for our builds. This tells Travis to install dependencies pertaining to the Rust language, including the Rust compiler and Cargo.
2. We add a `script`, which tells Travis what command to run to execute our tests. This will happen on every push for every branch.
3. We add a `deploy` step, which will execute only on the `master` branch using the `cargo` deployment provider, which is a built-in Travis deployment provider. We pass in the `$CRATES_TOKEN` environment variable as our deploy `token` (we'll generate this and add it to our Travis environment in step 3).

This may look super-minimal, but it contains all the configuration we need to publish our crate!

_The official Travis documentation recommends using an encrypted value as the deploy token, but I couldn't get this to work so I'm using an environment variable instead!_

# Step 2: Enable Travis for your project

There are a couple of ways to do this, but I recommend using the Travis CLI. Install it using [these instructions](https://github.com/travis-ci/travis.rb#installation).

Once the Travis CLI installed, you'll need to log in using the following command:

```bash
$ travis login
We need your GitHub login to identify you.
This information will not be sent to Travis CI, only to GitHub.
The password will not be displayed.

Try running with --github-token or --auto if you don't want to enter your password anyway.

Username: rkh
Password: *******************

Successfully logged in!
```

Once signed in, open a terminal and navigate to your project's root directory. Then simply run this command to enable your project on Travis:

```bash
$ travis enable
travis-ci/travis.rb: enabled :)
```

# Step 3: Configure your Crates.io API token

First, go to [crates.io/me](https://crates.io/me) (you may need ot sign in or sign up), and under API Access click "New Token". Give this token a name pertaining to your project, and click "Create". Then, copy the token to the clipboard.

Now go to [Travis](https://travis-ci.com/) and navigate to your project's settings. Under Environment Variables, create a new variable with the name `CRATES_TOKEN`, and paste in your new API token as the value.

# Step 4: Push to GitHub

Run a `git push` to upload your Travis configuration to GitHub. This should trigger a Travis build, which will run your Rust tests and publish your crate!

# Bonus: Recommended branching structure

Now that you've set up Travis to publish your project every time new code appears on `master`, you probably don't want to push code to that branch too often. I recommend setting up a `develop` branch, which you can do your development work on. Once you're ready to publish a new version, just bump the version number and merge `develop` into `master`, and Travis will handle the rest!

For a more flexible and useful approach to branching, I'd recommend looking into [Git Flow](https://www.atlassian.com/git/tutorials/comparing-workflows/gitflow-workflow).
