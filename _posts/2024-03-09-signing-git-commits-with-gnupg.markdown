---
layout: post
title:  "Signing Git commits with GnuPG"
author: André Felipe
date:   2024-03-09 15:20:25 -0300
---

This post discuss the use of digital signatures and how to configure Git and GitHub to sign and verify commits using GnuPG keys -- also known as GPG keys.  

## Digital signatures and signed commits

Digital signatures are product of cryptographic protocols that provide means to authenticate the alleged authorships of signed messages. They also guarantee the integrity of signed messages, ensuring that the messages were not modified after signed.  

Git can be configured to sign commits using a criptographic protocol such as GnuPG. Signed commits can be verified by anyone who knows the signer's public key; including GitHub, which marks commits with valid signatures with a "verified" badge.  

The following sections explain how to generate a GnuPG key pair and how to use it to sign and verify commits on Git and Github.  

## Generating a GnuPG key pair

List available GnuPG keys in your computer to see if you already have an usable key pair.  

```
gpg --list-keys
```

If there is a key pair you want to use, skip to the next section. Otherwise, generate a new key pair.  

```
gpg --full-generate-key
```

You will be asked to choose the cryptographic algorithm that will be used to generate the keys. As there are many of them, some of which will be replaced over time, I recommend that you just accept the options suggested by the GnuPG application. Those should be safe settings for this scenario.  

You will also be asked for an expiration policy. The simplest option here is to not set an expiration term, creating a non-expirable key pair.  

Then you will have to set the identity behind the signature keys. You must give a name and an email that are used both in your `git config` and your GitHub account. You can also add a descriptive commentary.  

Finally, you will choose a passphrase for your new key pair. After that, you can list your GnuPG keys again. You should see your newly created key pair.  

## Setting a GnuPG key on Git

To set your private GnuPG key on Git, you have to get its id by executing the following command.  

```
gpg --list-secret-keys --keyid-format=long
```

The output of the above command should look like this:  

```
sec   ed25519/2C33BE84F853D7BE 2024-03-09 [SC]
      36D3F228934F50219211F1C62C33BE84F853D7BE
uid               [   plena  ] Fulano de Tal <fulano@mail.com>
ssb   cv25519/136C603051B6AECA 2024-03-09 [E]
```

In the above example, the key id is `2C33BE84F853D7BE`. Use the following command with your own key id to set it as Git's signing key.  

```
git config --global user.signingkey 2C33BE84F853D7BE
```

Finally, set git to always sign new commits.

```
git config --global commit.gpgsign true
```

## Setting a GnuPG key on GitHub

To add your public key to GitHub, print and copy it by replacing `2C33BE84F853D7BE` with your own key id in the following command.  

```
gpg --armor --export 2C33BE84F853D7BE
```

Now go to your GitHub account settings. In the "SSH and GPG keys" section, paste and add your public key. GitHub should now be able to verify commits signed with your private key.  

## Enabling vigilant mode on GitHub

Optionally, you can enable vigilant mode. This will show a "unverified" badge on commits on which the author matches your name and email, but there is no valid signature for such identity.  

![Screenshot of verified and unverified commits on GitHub](/assets/images/signed-commits.png "Screenshot of verified and unverified commits on GitHub")
*Screenshot of verified and unverified commits on GitHub*

Note that this configuration will also be applied to all your previous commits.  

### References

https://csrc.nist.gov/Projects/Digital-Signatures 

https://git-scm.com/book/en/v2/Git-Tools-Signing-Your-Work  

https://docs.github.com/en/authentication/managing-commit-signature-verification  
