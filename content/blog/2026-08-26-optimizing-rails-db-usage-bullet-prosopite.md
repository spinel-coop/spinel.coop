+++
_schema = "blog"
date = 2026-08-26T07:00:00.000Z
title = "Optimizing Rails DB usage: Bullet vs. Prosopite"
slug = "optimizing-rails-db-usage-bullet-prosopite"
draft = true
+++
One of the worst parts of N+1 performance issues in Rails is how easy it is to inadvertently introduce them. And while using `includes` to preload the associations causing the problem generally works, sometimes you may (again) be inadvertently introducing different performance issues if you are not careful.

So we weren’t surprised when a client recently hired us because N+1s were causing most of their high API latency issues. What did surprise us was finding both Bullet and prosopite already present in their Gemfile. We were unsure at first about which tool would be best for the job.

This post will share where we landed and what we learned to help shed some light for folks who also aren’t sure which of these tools to choose.

But let's first quickly recap.

## What is an N+1 query?

Consider the following:

`Post.all.map(&:comments)`

If you go ahead and check request logs, you'll see one (1) query issued to fetch all posts, plus (+) one query for each (N) post to fetch its comments. That's why these are called N+1, although I guess 1+N would be a more descriptive name because the 1 needs to happen before the N.

The most common way to fix these issues is to tell Rails to load all related records upfront through preload, eager\_load, or includes.

* preload will load records using a separate query for each table involved, effectively turning N+1s into 1+1s.
* eager\_load, on the contrary, will use a LEFT OUTER JOIN to load everything through a single query, so that'd be the 1+0 solution. The disadvantage is more complex queries are likely to be more costly.
* includes will normally delegate to preload , unless the situation requires joining the tables, for example, when a condition is set on the association.

## Detecting N+1 queries: Bullet

Historically, [<u>Bullet</u>](https://github.com/flyerhzm/bullet) has been the reference tool for finding N+1 queries in Rails. It works by keeping track of association usage and how many records each association loads in order to detect N+1s. In order to do this, it needs to monkeypatch ActiveRecord internals.

While that generally works, we found it often runs into false positives and it’s of course hard to maintain: changes in internal ActiveRecord APIs may break Bullet and force Bullet to change in order to adapt. Looking at the monkeypatches themselves illustrates this even more clearly: Bullet needs to [add a new file like that one](https://github.com/flyerhzm/bullet/blob/ecd16a22c40d950d738ad2c69293bdbae18af00b/lib/bullet/active_record81.rb) every time there's a new version of Rails.

As a result, we found Bullet to brittle for the job and decided to look into the alternative.

## Detecting N+1 queries: prosopite

Prosopite subscribes to `active_record.sql` hooks using ActiveSupport's instrumentation API, and looks for multiple instances of the same query issued from the exact same backtrace. We found this approach not only more generic and stable, but also less subject to false positives.

So, with more reliable detection and minimal false positives — something very nice from a detection tool — we found our tool of choice for detecting N+1 queries: prosopite.

## How we’ve actually used Bullet (hint: it’s not the way you think)

In the real world, things aren’t so black and white--which is where our client work comes in.

At the start of our work with the team that hired us, we found that their API response only needed to include some related model data if certain query parameters were used. Yet we were adding the preloads unconditionally, making some requests perform unnecessary database queries.

So after coming close to reintroducing some unnecessary queries for our client while fixing N+1s, we decided to enable Bullet to get some guardrails in place. The process didn't come without a few gotchas, though!

First, Bullet documents it's possible to enable its features independently, which is useful for us because we want to keep `prosopite` for detecting N+1s. We thought this would do the trick:

```
Bullet.n_plus_one_query_enable = false
Bullet.unused_eager_loading_enable = true
```

However, it did not work when we tried it. Because N+1 detection is disabled, Bullet fails to track association usage properly and incorrectly flags many associations as unused. Fortunately it was an easy fix which we sent upstream:[<u>https://github.com/flyerhzm/bullet/pull/776</u>](https://github.com/flyerhzm/bullet/pull/776) .

After including that fix, Bullet detected a bunch of issues in our client's codebase. It still run into some more false positives though: if a `has_many :through` association has an associated scope with conditions, it will similarly be flagged as unused by Bullet, even if that's not really the case. The fix here was trickier, since as we explained earlier, it involves patching more ActiveRecord internals to get association usage properly tracked. We also sent this second fix upstream:[<u>https://github.com/flyerhzm/bullet/pull/777</u>](https://github.com/flyerhzm/bullet/pull/777/changes/b4ac3719daaa8fd0dd2a4c4835e48d7f8abf5e5e).

With this second patch in place, we were left with only true positives and all that was left was fixing them, tada!

Or so we thought. There's still one small catch. Sometimes our client's codebase will preload a `has_many :through` association but then will only use the intermediate associations. Bullet flags this as an unused eager load, which I find questionable. For example:

```
Author.includes(:comments).all.each {|author| author.comments}

Author.includes(:comments).all.each {|author|

author.posts}Author.includes(:comments).all.each {|author|

author.posts.map(&:comments)}
```

Bullet is of course fine with the first example, but flags the second as an unused eager load. That makes sense, as it's only necessary to eager load :posts in the second case. However, it also flags the third example as an unused eager load, even though it's not. All eagerly loaded records are necessary to prevent N+1 queries.

I understand what Bullet is flagging here, the `comments` association for each `Author` record is getting populated but not getting used later. You can of course work around the problem with `Author.includes(posts: :comments)`, but I have the feeling folks may prefer the more concise version, as long as it does not create any unnecessary DB queries.

For now, we've gone with the fix of making our `includes` (admittedly) more verbose in exchange for more accuracy, but I may revisit this as an optional feature for Bullet! What do you think? Reply to us at [hello@spinel.coop](mailto:hello@spinel.coop) and let’s continue the conversation! Join our mailing list below for posts like this delivered directly to your inbox. And of course, if you’re ready to work with us, [book an intro chat with us](https://savvycal.com/spinel/client?d=60&amp;sid=53ac2a0e-3ea9-4c71-9423-86066b1ec381&amp;from=2026-08-20) to find out how we can help you solve where you’re stuck!

&nbsp;