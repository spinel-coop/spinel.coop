+++
_schema = "blog"
date = 2026-09-23T00:00:00-07:00
title = "Why you should check your gem's lines of code"
slug = "why-you-should-check-your-gems-lines-of-code"
draft = true
+++
Back in April while I was a [guest on the Dead Code podcast](https://shows.acast.com/dead-code/episodes/seeds-of-devastation-with-kasper-timm-hansen), I mentioned an idea I’d been brewing on for a while: that the number of lines of code is still a useful metric when you’re writing gems and evaluating whether you want to depend on them.

What I mean is, when you’re looking at a gem’s README, try to imagine a rough idea of what you think the implementation will look like in terms of lines of code. Then generate a [\`cloc\`](https://github.com/AlDanial/cloc/) count and see how off you were. (Side note: passing \`--by-file\` is also interesting).

If it was less than you expected, then maybe the implementation is worth diving into further and seeing if there’s something interesting they’re doing that you could learn from. Did they use an interesting \`Enumerable\` method you haven’t seen before that you can look up now? Are they needing to do work to have thread safety that you have not thought about before? Is there just some interesting organization in how they’ve arranged classes? That’s all stuff you can freely be inspired by!

Now, if on the other hand, there were more lines of code than you expected? You may have the problem space wrong and it’s more complex than you thought. That’s worth acknowledging! It’s also possible the gem implements the approach in a way that’s too abstracted and dense, and you may not want to depend on it after all.

\### Breezy reads

I’ve been writing gems for a long time, and I’ve been really focused on trying to make them conceptually clear. In my experience, if the overall concept is easily identifiable I find that it naturally helps reign in complexity, which then also constrains the lines of code.

The ultimate goal of this practice is to try to condense the gem, so it’s ultimately easier to read and audit for a new developer. I’ve been calling this a design focus on “breezy reads.”

With this type of design a senior developer should be able to get what’s going on in a gem in less than an hour, ideally.

As examples, my [ActiveRecord::AssociatedObject](https://gem.coop/@kaspth/active_record-associated_object) and [ActiveJob::Performs](https://gem.coop/@kaspth/active_job-performs) gems are both under 100 lines of code. Associated Objects are POROs nestled within a parent ActiveRecord to help extract domain logic into much smaller collaborator objects. Performs sets an app-wide convention for how jobs are integrated into your Domain Model. Giving both of these gems a tight scope means that it’s much easier to see when things don’t belong, and decide that they won’t be included in these gems.

To me, both of these ideas are worth about 100 lines of code. Any more, and I think we’d be overspending to the point that they’re not worth working on or maintaining.

Here’s what it looks like, with the core of ActiveRecord::AssociatedObject being around ~70 lines of Ruby:

\`\`\`ruby

\# frozen\_string\_literal: true

&nbsp;

class ActiveRecord::AssociatedObject

extend ActiveModel::Naming

include ActiveModel::Conversion

&nbsp;

class &lt;&lt; self

def inherited(new\_object)

new\_object.associated\_via(new\_object.module\_parent)

end

&nbsp;

def associated\_via(record)

unless record.respond\_to?(:descends\_from\_active\_record?) && record.descends\_from\_active\_record?

raise ArgumentError, "#\{record\} isn't a valid namespace; can only associate with ActiveRecord::Base subclasses"

end

&nbsp;

@record, @attribute\_name = record, model\_name.element.to\_sym

alias\_method record.model\_name.element, :record

end

&nbsp;

attr\_reader :record, :attribute\_name

delegate :primary\_key, :unscoped, :transaction, to: :record

&nbsp;

def extension(&block)

record.class\_eval(&block)

end

&nbsp;

def method\_missing(meth, ...)

if !record.respond\_to?(meth) \|\| meth.end\_with?("?", "=") then super else

record.public\_send(meth, ...).then do \|value\|

value.respond\_to?(:each) ? value.map(&attribute\_name) : value&.public\_send(attribute\_name)

end

end

end

&nbsp;

def respond\_to\_missing?(meth, ...)

(record.respond\_to?(meth, ...) && !meth.end\_with?("?", "=")) \|\| super

end

end

&nbsp;

module Caching

def cache\_key\_with\_version

"#\{cache\_key\}-#\{cache\_version\}".tap \{ \_1.delete\_suffix!("-") \}

end

delegate :cache\_version, to: :record

&nbsp;

def cache\_key = case

when !record.cache\_versioning?

raise "ActiveRecord::AssociatedObject#cache\_key only supports \#\{record.class\}.cache\_versioning = true"

when new\_record?

"#\{model\_name.cache\_key\}/new"

else

"#\{model\_name.cache\_key\}/#\{id\}"

end

end

include Caching

&nbsp;

attr\_reader :record

delegate :id, :new\_record?, :persisted?, to: :record

delegate :updated\_at, :updated\_on, to: :record \# Helpful when passing to \`fresh\_when\`/\`stale?\`

delegate :transaction, to: :record

&nbsp;

def initialize(record)

@record = record

end

&nbsp;

def ==(other)

other.is\_a?(self.class) && id == other.id

end

end

&nbsp;

require\_relative "associated\_object/version"

require\_relative "associated\_object/railtie" if defined?(Rails::Railtie)

\`\`\`

\- From [lib/active\_record/associated\_object.rb 1.0.0](https://github.com/kaspth/active_record-associated_object/blob/v1.0.0/lib/active_record/associated_object.rb)

In another gem of mine, [Oaken](https://gem.coop/@kaspth/oaken), I’ve chosen a slightly larger scope where I’m focusing on making Rails apps dev and test data more maintainable but specifically with the concept of leveling up database seeds to do so. I could see that surface being worth up to 1000 lines of code.

Right now, however, Oaken 1.0.0 has 280 lines of code.

Over time, this experience in my gems led me to these napkin math buckets that I’m slotting gem ideas into:

<table><tbody><tr><td><p>&lt; 100</p></td><td><p>Teeny, short and sweet. Ideally a lot of bang for our code buck.</p></td></tr><tr><td><p>250-500</p></td><td><p>Medium, there’s complexity in here</p></td></tr><tr><td><p>500-1,000</p></td><td><p>Big-ish, the gem has to be really good and solve a meaty problem to warrant this.</p></td></tr><tr><td><p>1,000+</p></td><td><p>Big, the error margin is so wide here that it’s a whole other ball game.</p></td></tr></tbody></table>

Having a gem with many lines it’s fine, it’s when a gem exceeds what I think they’re worth that I get a little unsure of what’s going on in there.

\### Takeaways

> I have generally scrunched my nose at that idea \[about lines-of-code rules\], **but** hearing how you pair it with *conceptual* complexity made it click. It's not just golfing.

*\- my friend* [*Thomas Cannon*](https://practical.computer) *after listening to the Dead Code episode.*

I’ve been finding these lines of code buckets useful, both when I’m writing a gem and trying to quantify what its worth is, but also when I’m deciding whether to depend on another person’s gem.

It’s definitely pretty lossy--lines of code isn’t a 100% reliable metric: for example, they vary between different languages and even implementation styles.

On the other hand, I’ve gotten enough useful and surprising insights in practice using this method -- especially comparing testing libraries (let me know if you’re interested in a post about that) -- that I’m going to keep using it!

Still, I file this under “All models are wrong, some are useful”, and there’s been learning and clarity when I’ve applied this rough napkin math lines of code constraint.

Try using it and report back what you find,

Kasper

&nbsp;