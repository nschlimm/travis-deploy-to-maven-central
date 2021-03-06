# Deploy to Maven Central from Travis CI

[![Build Status](https://travis-ci.com/stefanbirkner/travis-deploy-to-maven-central.svg?branch=master)](https://travis-ci.com/stefanbirkner/travis-deploy-to-maven-central)

This is an example repository that demonstrates how a library can be
automatically published to [Maven Central](https://search.maven.org/) using
[Travis CI](https://travis-ci.com).

This repository is published under the
[MIT license](http://opensource.org/licenses/MIT). Feel free to copy whatever
is useful for you.


## Sources

This repository is built on my experience with publishing libraries to Maven
Central and Nathan Fischer's fantastic walkthrough ["Complete guide to
continuous deployment to maven central from Travis CI"](http://www.debonair.io/post/maven-cd/).


## Prerequisites

You need [GnuPG 2.x](https://gnupg.org/) for signing the artifacts and the
[Travis Client](https://github.com/travis-ci/travis.rb#readme) for encrypting
secrets. If you don't have them on your local machine then you can use a VM.
This repository supports [Vagrant](https://www.vagrantup.com/). With

    vagrant up

you can start a VM with the necessary tools.


## Create GPG keys

Artifacts that are uploaded to Maven Central must be signed. Therefore you need
GPG keys. Create your GPG keys with

    gpg --generate-key

Export your public and private key

    gpg --export --armor someone@somewhere > deployment/signingkey.asc
    gpg --export-secret-keys --armor someone@somewhere >> deployment/signingkey.asc


## Store Secrets

Travis needs to know your passphrase, and Sonatype credentials for deploying to
Maven Central. These are secrets and therefore you must not store them as
plaintext in your repository. Therefore Travis provides [support for encrypting
secrets.](https://docs.travis-ci.com/user/encryption-keys/) The ciphertexts can
be stored in the repository. First you need to login to Travis

    travis login --pro

and then encrypt the secrects and add them to you `.travis.yml` file

    travis encrypt PASSPHRASE=... --pro --add
    travis encrypt SONATYPE_USERNAME=... --pro --add
    travis encrypt SONATYPE_PASSWORD=... --pro --add

You also need to encrupt the file with the GPG keys.

    travis encrypt-file deployment/signingkey.asc deployment/signingkey.asc.enc --pro --add

Delete the unencrypted file afterwards

    rm deployment/signingkey.asc

The flag `--pro` must not be used if your project is still built on
travis-ci.org (all new projects are built on travis-ci.com).
