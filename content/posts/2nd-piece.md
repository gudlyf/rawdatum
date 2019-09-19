---
title: "2nd Piece: Misunderstandings and Misfortunes of AWS ELBs"
date: 2019-09-19T09:25:05-04:00
draft: false
---

Well, this is embarrassing.

I've been working with AWS for many years now, but I will likely never claim that I know everything about any particular service and, most especially, every service offering that's available. The velocity of change their and, really, with any modern cloud provider is crazy, and keeping up with it can be tough. But, that doesn't excuse my not fully understanding the basic features of a fundamental piece of infrastructure there.

Case in point: [Elastic Load Balancing (ELB) connection draining](https://aws.amazon.com/blogs/aws/elb-connection-draining-remove-instances-from-service-with-care/).

This is a simple feature that was introduced over five years ago, though I haven't until recently had to concern myself with it. In short, it provides the ability for an instance on a load-balancer's Target Group (TG) to "drain" connections from it before removing it from the TG. From the above documentation:

> When Connection Draining is enabled and configured, the process of deregistering an instance from an Elastic Load Balancer gains an additional step. For the duration of the configured timeout, the load balancer will allow existing, in-flight requests made to an instance to complete, but it will not send any new requests to the instance. During this time, the API will report the status of the instance as **InService**, along with a message stating that “Instance deregistration currently in progress.” Once the timeout is reached, any remaining connections will be forcibly closed.

Okay, pretty simple. Here's where my misunderstanding comes in, which is partly a language issue, partly (mostly) an issue with lack of personal testing. When the documentation says **"will allow existing, in-flight requests made to an instance to complete,"** this does not mean that once those requests are completed, the instance can go on ahead and deregister itself _even if the timeout hasn't been reached yet_. If you set the draining value to 300 seconds, it's going to wait that long to degregister the instance, no matter what.

Well, sorry, but that sucks. But I get it -- it is what it is.

Because of this, we had to find that "sweet spot" for deregistration when creating our code push-live process (one that we're unfortunately stuck with -- it's a long story). We can't deregister too soon, and we can't wait too long. Ideally, the AWS API would either deregister upon lack of open connections to the instance (and then forcibly deregister after the set timeout maximum), and/or allow a command to the API to forcibly deregister the instance once our application sees no lingering connections -- let us control it.

I'd been used to how haproxy worked in the dregistration process and stupidly assumed AWS ELBs worked the same. I've since learned that assumptions make an ass ... you know the saying.
