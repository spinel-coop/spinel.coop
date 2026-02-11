+++
date = '2026-01-06T09:37:00+07:00'
title = 'Announcing <code>rv clean-install</code>'
slug = 'rv-clean-install'
+++

# rv 0.5: CLI tools + windows

For 6 months now, rv has been installing Ruby versions in under a second, thanks to our precompiled Rubies. Today, we’re releasing rv 0.5, which brings that same focus on speed to installing Ruby on Windows, running scripts with rv run, and running and installing all sorts of gems and binaries via rvx and the rv tool commands.

The new features in rv version 0.5 are:
1. Windows support.
2. New subcommand: rv run
3. New command: rvx aka rv tool run
4. New subcommand family: rv tool
Let's look at each of these in more detail.

**Windows support**
Thanks to the solid Windows support in the Rust language, the rv tool itself has always compiled on Windows, but the [rv-ruby](https://github.com/spinel-coop/rv-ruby) project doesn’t create any precompiled Rubies for Windows (at least, not yet!) Without precompiled Windows Rubies, there wasn’t much point in making rv available on Windows. New contributor [@case](https://github.com/case) changed all that, by adding support for the Windows Ruby binaries created by [the RubyInstaller2 project](https://github.com/oneclick/rubyinstaller2). Now that rv can install Ruby on Windows, it finally makes sense to release rv for Windows.
![](unknown.png)
To make installing rv on Windows easy, we’ve added a PowerShell one-line installer script to the installation instructions for every release. Check out our 0.5 release for Windows [right here](https://github.com/spinel-coop/rv/releases/tag/v0.5.0)!

One important caveat: if you use PowerShell, there is already a built-in rv command that removes environment variables. For anyone who wants to leave the existing PowerShell alias in place, we offer two other options: running rv.exe, or by running our Windows-specific binary, named rvw. Either command will get you the same fast Ruby and gem tools, without conflicting with the PowerShell alias.

Windows support has historically been a tricky situation for Bundler, so if you run into any problems or unexpected behavior, definitely [let us know](https://github.com/spinel-coop/rv/issues/new)!
**rv run**
With a big boost from new contributor [phromo](https://github.com/phromo), we now have a top-level rv run command. What can you run with rv run? Anything! Well, anything that's in your PATH or on disk. That opens up a few great new use-cases.

You can use rv run irb to immediately get an interactive Ruby prompt in under a second — even if Ruby wasn't previously installed (installing Ruby via rv is _fast_). If you have a Ruby script, you can use rv run myscript.rb, and we'll install the right Ruby and run your script with it. 

You can even use rv to make your Ruby scripts executable, whether or not Ruby is already installed! Just put #!/usr/bin/env rv run ruby as your shebang in any script, and it’ll become executable via an rv-provided Ruby.
![](unknown-1.png)We're excited to see what everyone comes up with, now that it's easier to run Ruby scripts and commands than ever before.
**rvx**
We’ve added a new command, rvx, which runs a binary from a Ruby gem (we call these “tools”). You can do rvx rubocop to run the latest version of rubocop, or rvx rubocop@1.82 for a specific version. Give it a try — it’s very fast! The first time rvx runs a tool, it’ll download all required gems (in parallel), install them (in parallel), then run the tool. If you rerun the tool again, it’ll reuse all the cached downloads and installations, so it’ll execute near-instantly. Here’s an example!
![](unknown-2.png)By default, rvx looks for an executable and a gem with the same name (e.g. rvx nokogiri runs the nokogiri binary provided by the nokogiri gem). If you wanted to instead run the racc binary that nokogiri provides, just do rvx --from nokogiri racc.
![](unknown-3.png)For 6 months now, rv has been using precompiled Rubies to install your Ruby versions in under a second. We hope we can bring that same focus on speed to downloading, installing and running gems via rvx. We’re very grateful to the Rust ecosystem, which lets us use awesome crates like tokio and rayon to download and install gems in parallel. We hope the rest of the Ruby ecosystem embraces parallelization — please talk to us if you’d like to collaborate on speeding up other Ruby workflows!
**rv tool**
Under the hood, rvx is just an alias for rv tool run. When you run a tool, it’ll be downloaded and installed if you don’t already have it. You can manually install, uninstall and list your tools with rv tool install, rv tool list and rv tool uninstall.
![](unknown-4.png)
**rv selfupdate**
Thanks to new contributor [duckinator](https://github.com/duckinator), you can update rv using itself: rv selfupdate should Just Work. This is the first release with an rv selfupdate command, so to upgrade from 0.4 to 0.5 you’ll have to do it the old-fashioned way. But when we release 0.5.1, you’ll be able to run rv selfupdate to easily upgrade.

In addition to these headlining features, we saw dozens of smaller improvements, fixes, optimizations, and generally making things work better and faster. Thank you to all of our contributors!
