---
title: "3rd Piece: The Cloud-Agnostic Argument"
date: 2019-10-07T20:46:45-04:00
draft: false
---

If you've already been running your production environment in the cloud for, say, the past five-plus years or so, you're probably feeling pretty happy right where you are -- or, at least, pretty comfortable. And let's say you've migrated your non-cloud app and its infrastructure over to this cloud provider, as-is: simple VMs, maybe a load balancer. Then, over the years, you've matured this environment to take advantage of what this cloud provider has to offer: object storage, caching, auto-scaling, managed databases. Then functions. Then some other new managed service no one else seems to be able to replicate as well. Your production is all humming away beautifully, with fewer things to manage, and all under one roof (provider-wise, that is).

Now, you'd better be comfortable with your cloud provider, because you're pretty well stuck.

But that doesn't have to be the case.

There are several schools of thought when it comes to vendor lock-in, but really, in my view, there are two: embrace it with wild abandon, or figure out how not to.

I get the draw of the former case. It's got to be quite the freeing feeling to not have to think of the "what if's" that come with an undying embrace of tooling and infrastructure. Who gives a shit if GCP has X feature or Azure has Y -- we're in AWS, and we have Z, and we "always will." Stop wasting time worrying and preparing for a hypothetical doomsday and get to work.

Then there is the latter: the doomsayers. There is no lock-in. There is no barrel to be held over. No ring to rule them all. That's my camp.

I get the argument against cloud agnosticity (if that's a word). Obviously there's the cost that comes with stack portability, not only in having some form of it run within different providers, but the man-hours it takes to make it happen. There is also, of course, the unfortunate effect of not being able to leverage features -- usually fully-managed ones -- that another provider simply doesn't offer.

But isn't that all a bit akin to the days of sticking with a certain operating system or chip architecture because it's too costly to consider anything else? Or, to a less-likely degree, ignoring disaster recovery options because what you've got running now is perfect and your MSP is a never-dying vampire, so you're safe until the end of days?

Well, vampires can die, and they can also decide to kill you when they're sick of sucking your blood.

Let me tell you something about your cloud provider, and I know this first-hand: They can decide to kick you off their platform anytime they feel it's remotely warranted, and the burden of proof is on you. Imagine being told you're suspected of sending spam through one of your instances and therefore need to pack your bags for an impromptu, permanent holiday that shall commence in sixty days? You claim you're innocent and have proof, but that's not good enough for them. So, you have sixty days to leave, and you take your baggage to a new port, only they look at your baggage and don't know what the hell to do with most of it. And neither does the next one. You're fucked -- you need ALL NEW BAGGAGE!

I spoke with someone recently about this very topic, someone who works for a much larger organization than I do right now, and who has fully embraced AWS and nothing else. I brought up what seems a hypothetical situation (but is not), and his viewpoint was that it's simply too expensive to consider the alternatives when what you have is working for you just fine. So, he's obviously on the "embrace with wild abandon" camp, but he's right: it is expensive. So is standing up N+1 servers, load balancers, databases, etc.

But he did have another very good point that gave me some perspective in that "uhm duh, you're right" way: At what point do you stop going silly about redundancies? We're on one planet, so do we launch this shit onto a satellite? The moon? Mars? Probably not, unless the fate of all humanity rested on your precious app running.

While I still like the idea of seriously considering the idea of not having vendor lock-in, I need to understand that unless there's a reasonable risk the vendor is either A) going to go away suddenly, B) going to decide to jack up prices 150%, or C) find something we might do is bad enough to kick us out, the idea of stack portability might be way, way lower on the priority list.

I'll probably still continue to beat my vendor-UNlock drum for quite some time, though.
