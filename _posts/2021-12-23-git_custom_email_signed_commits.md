---
title: "Git commits with noreply email for better privacy"
tags: ["git", "commit", "gpg"]
toc: true
---

# Introduction
Github public repositories could expose your email address leading to receving spams. We'll cover how to protect your email, 
and commit with a noreply/different email and verifying the commits.

# Steps to protect your email

## See for yourself
Clone a public repository in github and run the following command

```bash
# You can clone any of your favorite repository/library

git clone https://github.com/stretchr/testify && cd testify
git log --format=email --abbrev-commit | grep -i mail | sort -u
```
You can see email addresses of all contributors visible to the public and bots.

## Configure with noreply email
In order to protect your email, go to your [email settings](https://github.com/settings/emails) and ensure you've checked the following settings

* Keep my email addresses private
* Block command line pushes that expose my email

The first option helps with web commits and uses no-reply email address. 
Since we operate mostly on commandline we should update our git config `user.email`

```bash
# changes your email address globally for all repositories 
# for further commits

git config --global user.email username@users.noreply.github.com
```

drop `--global` flag if you want this change to be made for a specific repository or while setting up & testing these changes.

## [Verify](Verify) Commits with GPG Key
Depending on your account creation github docs mentions noreply email as `ID+username@users.noreply.github.com` or `username@users.noreply.github.com`, irrespective
of commiting with both emails, my commits were shown as unverified in git. 

![unverified commits screenshot](/wiki/img/unverified_git_commits.png)

To have verified/signed commits, create a gpg key, associate it with noreply email and copy the key to your profile keys by running the following commands.

```bash
gpg --full-generate-key
gpg --list-secret-keys --keyid-format=long
gpg --armor --export 3AA5C34371567BD2 > gpg.key
```
copy `gpg.key` content and add the key to your profile [settings](https://github.com/settings/keys)

* `3AA5C34371567BD2` is sample ID, you should be using the GPG key first row id followed by crypto-algorithm, for e.g. `ed25519/<whatever>`
* while creating key, pick defaults and key your `username@user.noreply.github.com` or custom email
* when you're listing keys ensure the email is associated with the generated key.

Atlast, do the following
```bash
git config --global user.signingkey 3B45F8F2DBFF3CFE
git config --global commit.gpgsign true
```

Make a commit in your repository and verify the commits are signed successfully by running
```bash
git log --show-signature
```
In order to update older commits in the repository, follow the steps in [Changing Email Addresses Globally](https://git-scm.com/book/en/v2/Git-Tools-Rewriting-History) docs section.

# References
* [github docs: commit email address](https://docs.github.com/en/account-and-profile/setting-up-and-managing-your-github-user-account/managing-email-preferences/setting-your-commit-email-address)
