I documented how every AI provider in an open-source tool handles your data and how long it keeps it, whether a data agreement exists, and whether it trains on it. I submitted the change as a pull request, after writing a checklist that made me plan the work and fix my own mistakes first.

MarketMirror sends your documents to whichever AI provider you choose, but its privacy note only said "that provider's terms apply." For a team under privacy laws like GDPR, that is not enough to choose a provider. I added a privacy link to every provider the tool supports, made the tool show that link when you check a provider, and wrote a sourced table covering 15 cloud-hosted providers, each row checked against the provider's own policy, with "not stated" where they say nothing.

The checklist caught two real problems before anyone else did: a missing plan file and a note about the AI tool I used that was too long. The request is now open and passing the project's automated checks. A review bot found a gap I had missed — users who log in with a provider's own tool got no privacy link and I fixed it. No maintainer has replied yet, so the follow-ups are my next step.

https://github.com/DarkWorldCoder/market-mirror/pull/195

#OpenSource #DataPrivacy #AI
