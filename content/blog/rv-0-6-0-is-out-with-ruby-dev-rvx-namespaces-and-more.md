+++
date = 2026-07-22T22:45:00.000Z
title = "rv 0.6.0 is out, with `ruby-dev`, `rvx @namespaces`, and more"
slug = "rv-0.6.0-released"
aliases = [ "/news/rv-0.6.0-released" ]
+++
We're running a bit behind on announcements, so here's our post about releasing `rv` version 0.6.<br><br>The headline features added to version 0.6 are daily releases of `ruby-dev`, adding namespace support to `rvx`, and supporting gem servers that require authentication, for projects that use Sidekiq Pro, Avo Pro, and the like. We also added a self-update command.

## \`ruby-dev\` daily releases

The daily releases of `ruby-dev` are especially helpful if you want to test your application against the latest Ruby version that's currently in development — you don't have to download Ruby source code, or compile it, you can simply install a build of Ruby from git that's less than a day old, any time you want, by running `rv ruby install dev`. We run nightly builds of Ruby and publish them to GitHub Releases, where anyone using `rv` can try them out by spending a second or two installing. No more recompiling Ruby every time you want to try the latest — just try it.

## rvx namespaces

The second change helps anyone trying to use `rvx` to run CLI tools from gems. Before, it was possible to run `rvx gist` and everything would work as expected. However, gem servers with namespaces, like [gem.coop](https://gem.coop), didn't have a way to instantly run gems from a namespace. Now, it's as easy as `rvx @indirect/card` to immediately run the CLI tool from the gem named `card` in the [gem.coop](https://gem.coop) namespace `@indirect`.

## server authentication support

On the gem installation side of things, we built support for server authentication. That means `rv` can now install Gemfiles that use the private gem servers for Sidekiq Pro and Avo Pro, or any Gemfile that uses an entirely private authenticated gem server.

## \`rv self-update\`

Finally, we added a self-update command. Many of our users install using Homebrew, and get updates automatically as part of Homebrew's updating system. For everyone else installing `rv` directly, the built-in self-update means that you don't have to re-run the installer every time there's a new version. Just run `rv selfupdate` to get the latest version, once you've installed 0.6.0 or higher at least once.

## more version 6.0 updates

This version also includes dozens of bugfixes, and several significant compatibility improvements when working with Bundler configuration and lockfiles. Check out [the changelog](https://github.com/spinel-coop/rv/blob/main/CHANGELOG.md#rv-060-15-june-2026) for the full list of all 43 changes.<br><br>We're continuing our work on installing full `Gemfile`s, as well as adding additional features to make `rv` the best way to use Ruby and gems. If you'd like to help test our changes, give us feedback about how `rv` could be better, or even help us implement new features, we'd love to have your help. Head over to [rv.dev](https://rv.dev) to get started, and we'll see you soon!