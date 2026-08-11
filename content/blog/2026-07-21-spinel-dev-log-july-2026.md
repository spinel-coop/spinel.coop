+++
_schema = "blog"
date = 2026-08-12T07:00:00.000Z
title = "Spinel dev log July 2026"
slug = "Spinel-dev-log-July-2026"
draft = true
+++
Welcome to the Spinel dev log! When we’re not [<u>working with client teams</u>](https://spinel.coop/), we’re building open source tools that make it easier to be a software developer, especially for Rubyists. Here’s what’s new with our OSS projects in the past month:

### [rv](https://github.com/spinel-coop/rv), the next-generation Ruby version and project manager

* We added \`rv fmt\` command, built on [librubyfmt](https://github.com/fables-tales/rubyfmt/tree/trunk/librubyfmt), to format Ruby files quickly and consistently without needing any additional gems or tools.
* We fixed Powershell integration to correctly auto-update the Ruby version.
* We added \`rvx --with\` option, to run tools with optional dependencies. For example, \`rvx --with pg sequel\` lets you use the Sequel gem to connect to Postgres. Thanks, [@gilesbowkett](https://github.com/gilesbowkett)!

### [rv-ruby](https://github.com/spinel-coop/rv-ruby), precompiled Ruby binaries for x86 and ARM, on macOS or Linux

* We added Ruby 3.4.10, Ruby 4.0.6 and Ruby 3.3.12.
* We updated to OpenSSL 3.6.2, libFFI 3.5.2 and libxcrypt 4.5.2.
* We updated our build system for Homebrew’s 6.0.0 release.

### [Dyad](https://codeberg.org/sstephenson/dyad), our portable shell tool for running two programs together with shared fate

* We added channels, a way to pipe any file descriptor from one program to any file descriptor on the other.
* We fixed a bug that caused “Terminated” messages to appear in some shells when exiting.
* We addressed an edge case that could cause errors when running Dyad with unusual environment variable names.

### [brut-pack](https://codeberg.org/sstephenson/brut-pack), our tool that bundles a multi-file shell program into a single portable script

* We added support for symbolic links in bundles, as long as they stay within the bundle’s own directory.
* We fixed bundles to run their program with the caller’s environment unmodified.

### Thank you!

Thank you to all the contributors to [<u>rv</u>](https://github.com/spinel-coop/rv/graphs/contributors?from=5%2F2%2F2026) and [<u>rv-ruby</u>](https://github.com/spinel-coop/rv-ruby/graphs/contributors?from=5%2F2%2F2026)! If you’re interested in our open source projects, we’d love to have you join us as a contributor too. Get started by checking out the projects’ READMEs.

### Stay in touch

If our open source work has helped you or your company, let us know! Tag us [on Bluesky](https://bsky.app/profile/spinel.coop) and [Mastodon](https://indieweb.social/@spinel@ruby.social) or [reach out to us directly](mailto:hello@spinel.coop). We’d love to hear from you.