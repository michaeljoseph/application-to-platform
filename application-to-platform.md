name: inverse
layout: true
class: center, middle, inverse
---
# Application To Platform
.footnote[[How we used Python to scale Yola](https://github.com/michaeljoseph/application-to-platform)]
---
layout: false
.large[
![avatar](https://raw.github.com/michaeljoseph/application-to-platform/gh-pages/images/avatar.png)

michaeljoseph   @github     @twitter
![yola](https://raw.github.com/michaeljoseph/application-to-platform/gh-pages/images/yola-logo.png)
]
???
.small[
# excuses

- false pretences:  backup speaker

- 4 day talk

- crappy slides: squirrel (show remark / reveal)

- we're hiring, let me convince you / swimming the recruitment and hiring manager infested nerdpool during the break
we have business cards
]
---
class: center, inverse
## this talk is about
.large[
![transformation](https://raw.github.com/michaeljoseph/application-to-platform/gh-pages/images/transformers.png)
]
???
.small[
This is the story, all about [Yola](https://yola.com) engineers transforming
a website building application into a robust, distributed platform
(for hosting and building websites).
]
---
# follow along at home

https://github.com/michaeljoseph/application-to-platform
???
If you're staring at your device while I'm talking I can pretend that you're looking at my presentation
---
.left-column[
## Other talks
]
.right-column[

- yodeploy and yoconfigurator

- yola’s hosting platform

- release automation and continuous deployment

]
???
This talk mostly concerns itself with the python API services layer, since i’m the Services Team Lead.
There are a number of other interesting talks that I have yet to convince some Yola colleagues to prepare
---
template: inverse

.left-column[
# overview
]
.right-column[

before and after

lessons

deployment / rollout patterns

roadmap
]
???
I shall attempt to:

- extract and present some patterns that worked for us

- discuss the organisational flexibility and engineering workflow that enabled us to refactor the system architecture, and

- share the medium term roadmap for our system.
---
# History
![image](https://raw.github.com/michaeljoseph/application-to-platform/gh-pages/images/2007-beta-architecture.png)
???
.small[
In the beginning, we had an application. It was called The Sitebuilder. And it was good. Well, pretty good.

We had an application that lived on two servers in 2007 (along with a php website and wordpress blog and database) and not much else.
Except smart people :)
]
---
class: inverse
# architecture goals

Simplicity is preferable to complexity

Existing solutions are preferable to entirely new ones

Open Source solutions are preferable to proprietary ones

Avoid single points of failure

???
.small[
@nbm: architecture goals, decided that soa was the way to go (it was also a buzzword back in the day, it  was a thing guys).
over the next 2 years we evolved a reasonably robust platform.

solutioneering, speakers dinner

architecture is a juggling act. balancing a series of tradeoffs

Architecture, designs, and implementations that are simple have useful properties:
  They are easy to understand
  They are easy to modify
  They are easy to replace or rewrite

Existing solutions are preferable to entirely new ones
  Use of mature existing solutions has benefits over creating new solutions:
    It generally takes less time to go from concept to production with an existing solutions
    Existing solutions tend to be of higher quality than new solutions, at least for a period of time
    Third-party solutions tend to improve their features and quality over time without internal effort
    Existing solutions generally have better documentation and

However, there are downsides that must be considered:
  It is generally harder to modify third-party solutions
  Existing solutions may not be simple, or may not serve the requirements effectively.
  Open Source solutions are preferable to proprietary ones

Avoid single points of failure
]
---
## architecture changelog 

SynthaSite v2 beta — November 2007: The SynthaSite v2 beta went live.

SynthaSite Platform 2.0 — June 2008: gh-pages step in creating a more scalable and reliable platform, introducing load balancing and failover databases and filesystems to the Site Building cluster

SynthaSite Platform 2.1 — July 2008: Adds a dedicated hosting file system and database server, with drbd failover.

SynthaSite Platform 2.2 — September 2008: Adds horizontal scalability to the Site Builder database tier, allowing additional Site Builder database servers to be added to improve capacity.
---
## architecture changelog

SynthaSite Platform 2.3 — October 2008: Adds the domain service and the payment service, allowing for the gh-pages company revenue stream.

SynthaSite Platform 2.4 — November 2008: Adds hosting web server load balancing, allowing horizontal scaling as well as high availability.

SynthaSite Platform 2.5 — December 2008: Adds the ability to have multiple hosting file servers, allowing horizontal scaling of hosting storage and I/O.
---
## architecture changelog

Yola Platform 2.6 — May 2009: Adds the ability to have multiple Site Building file servers, allowing horizontal scaling of Site Building storage and I/O.

Yola Platform 2.7 — May 2009: Adds the Resource Store, which facilitates scalable and highly-available storage of site resources.

Yola Platform 2.8 — June 2009: Adds the Checkout Service, which facilitates the provision of purchase flows in a secure fashion (it, over SSL).

Yola Platform 2.9 — August 2009: Adds Resource Store instances to the publishing environment.
---
template: inverse
## Before And After
![platform-2.9](https://raw.github.com/michaeljoseph/application-to-platform/gh-pages/images/2009-platform-2.9.png)
???
our architect created lovely architecture changelogs and when he left us for Facebook (which is becoming a damn theme at yola these days).

@vhata and devops: cheffing and automating all the things

tell them on twitter that i'm @ name dropping...

oct 2013: We have around 30 deployable applications, service apis, front-ends and automation/testing repositories.
---
# lessons

release early and often

keep the changes small and pointy

  lowers the cognitive overhead of review

  increases confidence in pushing out new code

  small surface area of introduced bugs to inspect 

bugs are shallow anyway because we have many eyeballs
---
## standardise
### applications and installables
#### applications
#### installables
???
- frontends: sb is pretty much the same application, we’ve added helper services around it to absorb the load, we now have a logged in user management site: MyYola and a CMS for brochure-ware (the main website)
- storage: from single spofs to one level of database redundancy, with automatic backups
(where are the Yola people in the audience? We should test some restores sometime guys?)
  - s3, no more NFS
- pure services: userservice, domainservice, michango, rankmonitor
- applications: myyola, checkoutservice, yolacom [+ apis]
- cookiecutter
---
template: inverse
layout: false
class: center
.left-column[
deployment / rollout patterns
]

## new services first
## switch client applications using standard demands based service clients
## cleanup

works for hardware too

???
- build out and release new service calls independent of consuming clients

- refactor apps
  - re-use standard python service clients to reduce development time

- rm old implementation / cleanup / decommission

- same deal for rotating hardware

   - spin up a fresh box
   - provision
   - test
   - load balance in, load balance out
---
##  evolving services with service-clients
![yosites-source](https://raw.github.com/michaeljoseph/application-to-platform/gh-pages/images/yosites-source.png))
---
## generated docs
![yosites](https://raw.github.com/michaeljoseph/application-to-platform/gh-pages/images/yosites.png))
???
- shittiest python code is (in only a few places, hopefully): python 2.6, django < 1.4, piston, sloppy testing practices, complicated code with production bug fixing barnacles that works

- new apis: python 2.7, djrf, django
- small api: flask
- python service clients: [`demands`](https://github.com/yola/demands). kennethreitz
- new user facing app: django 1.5, cbvs, south, asset compression, front end dependency toolchain (lessc, bower, grunt)
---
# engineering workflow

hiring

meritocracy

BDFL

pull requests

eyeballs
???
.small[
Being careful not to over engineer in advance abstraction astronaut we delayed major technology decision making

In hindsight it seems obvious: the decentralised, internet-based, just-in-time way of working that has 
risen to popularity in the wake of companies like github, facebook and valve.

A flat organisational structure and hiring smart people means that you don’t really need to worry about who does what and when,
as long as there is work to be done and some degree of priorisation, smart engineers just get the job done.

decision making and meritocracies with a BDFL

Github pull requests and code review have transformed the way the team works.

Many eyeballs make all the bugs shallower and there’s always a paper trail.
]
---
class: inverse
# yola open source

https://github.com/yola

https://github.com/yola/demands

https://github.com/yola/property-caching

https://github.com/yola/auth_tkt

https://github.com/yola/hashcache
---
# roadmap

- database succession

- feature flags

- modernise all the things

???
- database succession
- feature flags and user segmenting, better dogfooding
---
template: inverse
.left-column[
# roadmap: Monitoring
]
.large[
graphite

munin

sentry
]
???
- monitoring: we use pingdom, graphite, munin and sentry
  - stuff mainly just goes in there (unless there’s a problem. for the size of cluster we operate, these tools are generally sufficient)
  - we need a monitoring dashboard (next juicy devops project)
---
# roadmap: what was hard

- slow

- boring

- singularity imminent

???
Waiting for improvements to compound, almost at the singularity

We're hiring :)
---
name: last-page
template: inverse

## summary

Slideshow created using [remark](http://github.com/gnab/remark).
???
small steps
org support
---
# questions
---
