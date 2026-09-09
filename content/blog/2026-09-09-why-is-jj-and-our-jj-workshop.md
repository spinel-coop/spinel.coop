+++
_schema = "blog"
date = 2026-09-09T00:00:00-07:00
title = "Source control doesn't need to be scary! (Find out why at our jj workshop)"
slug = "version-control-doesnt-have-to-be-scary"
draft = true
+++
We're [doing a workshop at Euruko 2026](https://app.swapcard.com/widget/event/euruko-2026-brno/planning/UGxhbm5pbmdfNDU1MzU4MA==) next week. We'll be talking about jj, the [jujutsu version control system](https://github.com/jj-vcs/jj). jj is a new source management tool that is compatible with git hosting but a huge improvement on git overall. With git, it's easy to dig yourself into a hole that is hard or (in some cases) impossible to get out of. jj is a **powerful**, **simpler**, and **safer** alternative that will help you avoid those version control traps.

## git is powerful — it’s also complicated, and dangerous

Git has an entire cottage industry of tools that wrap the git command. lazygit, GitTower, VisualGit, git-flow, GitHub Desktop, and more aliases and scripts than you can possibly shake a stick at. The underlying reason for its cinematic universe of "simplifying" tools is that git is extremely complex. Each command can do a lot of wildly different things, depending on the exact options you pass. On top of that complexity, git is a dangerously sharp knife. You can easily run a git command that permanently changes something about your repo, and get into a state that is hard or maybe even impossible to recover from.<br><br>Speaking from personal experience, it's always wise to create a complete copy of your .git repo before you do something big and scary like `filter-branch` or edit commits in the reflog, or do a huge rebase that creates massive conflicts throughout the history of your repo.<br><br>The biggest area where git is dangerous is editing history. git doesn't do anything to keep past versions of history around, and while you can sometimes restore past histories using reflog tricks, or even manually walking the tree of discarded commits (that aren't garbage collected yet), *it's just not guaranteed*.

## jj offers full safety

jj, on the other hand, makes this entire type of work fully safe with a history you can review and revert to at any time. With jj, you have total safety to make any change, including rebase, rewrite, merge, etc, knowing **you can always run `jj undo` as many times as needed**.<br><br>Here's a quick example comparing git and jj's ways of amending a commit in your current working branch. In this example, the chain of commits looks something like this:

```
z0 (main) -> a5 -> b6 -> c7 -> d8 (HEAD, change-branch)
```

**git**

```
<edit here>
git add .
git commit -m "fixup! a5"
git log -5
git rebase -i HEAD~5 # or main, a5~, or a5^
```

**jj**

```
jj edit a5
<edit here>
jj new change-branch
```

There are several ways to do this operation in git, but they each have tradeoffs! Rebasing more commits than necessary can create new conflicts that need to be resolved, rebasing exactly the right number of commits can be tricky to count correctly, and remembering the difference between `~` and `^` and how to choose the exact commit you want is hard.<br><br>In contrast to git, jj offers the same power, but with clearer commands and a simpler mental model. On top of that simplicity, jj also offers full safety via undo for any command, as well as **full compatibility with any git repo**. You can personally use jj with any team that uses git. From the perspective of the team or the repo, jj is just another git client. You can gain all of the advantages of jj, without losing anything from GitHub, GitLab, or any other git host.

## what you’ll learn in our workshop

jj provides clear, safe tools to:

* rebase safely and easily, without breaking your repo
* rewrite history without losing previous versions
* be fully compatible with any git host
* manage source history with confidence

In this workshop, we're going to cover the details of exactly how jj can offer this kind of safety and flexibility. We're also going to show how to use jj to accomplish common git workflows, and cover workflows in jj that are impractical or sometimes even impossible using just git. git is much more powerful than what came before, but can still feel scary and dangerous to use today, 20 years after it was introduced. jj is living proof **source control doesn't need to be scary!**

<br>After this workshop, you'll have a clear grasp of the conceptual changes jj has made compared to git, and be confident as you manage changes. We'll show exactly how jj provides simplicity, understandability, and safety for your daily work. If you'll be at Euruko this year, [come to our workshop](https://app.swapcard.com/widget/event/euruko-2026-brno/planning/UGxhbm5pbmdfNDU1MzU4MA==) and we'll show you how. If you're not at Euruko this year, we'd love to run this workshop for your team. <a href="hello@spinel.coop" target="_blank" rel="nofollow noopener">Drop us a line</a> or <a href="https://savvycal.com/spinel/client" target="_blank" rel="nofollow noopener">book a call with us</a> if you're interested.