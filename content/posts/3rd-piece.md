---
title: "3rd Piece: The Cloud-Agnostic Argument"
date: 2019-09-28T23:46:45-04:00
draft: true
---

If you’ve already been running your production environment in the cloud for, say, the past five-plus years or so, you’re probably feeling pretty happy right where you are -- or, at least, pretty comfortable. And let’s say you’ve migrated your non-cloud app and its infrastructure over to this cloud provider, as-is: simple VMs, maybe a load balancer. Then, over the years, you’ve matured this environment to take advantage of what this cloud provider has to offer: object storage, caching, auto-scaling, managed databases. Then functions. Then some other new managed service no one else seems to be able to replicate as well. Your production is all humming away beautifully, with fewer things to manage, and all under one roof (provider-wise, that is).

Now, you’d better be comfortable with your cloud provider, because you’re pretty well stuck.

But that doesn't have to be the case.

There are several schools of thought when it comes to vendor lock-in, but really, in my view, there are two: embrace it with wild abandon, or figure out how not to.

I get the draw of the former case. It's got to be quite the freeing feeling to not have to think of the "what if's" that come with an undying embrace of tooling and infrastructure. Who gives a shit if GCP has X feature or Azure has Y -- we're in AWS, and we have Z, and we "always will." Stop wasting time worrying and preparing for a hypothetican doomsday and get to work.

Then there is the latter: the doomsayers. There is no lock-in. There is no barrel to be held over. No ring to rule them all. That's my camp.

I get the argument against cloud agnosticity (if that's a word). Obviously there's the cost that comes with stack portability, not only in having some form of it run within different providers, but the man-hours it takes to make it happen. There is also, of course, the unfortunate effect of not being able to leverage features -- usually fully-managed ones -- that another provider simply doesn't offer.

But isn't that all a bit akin to the days of sticking with a certain operating system or chip architecture because it's too costly to consider anything else? Or, to a less-likely degree, ignoring disaster recovery options because what you've got running now is perfect and your MSP is a never-dying vampire, so you're safe until the end of days?

Well, vampires can die, and they can also decide to kill you when you're of no interest to them anymore.
