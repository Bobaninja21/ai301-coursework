I documented what each AI provider retains and where, in an open-source command-line tool. I opened a pull request with the change after writing the checklist that made me write the plan and fix my own mistakes first.

MarketMirror sends your documents to whichever AI provider you configure, but its privacy note only said "that provider's terms apply." I added a privacy link to every provider in the tool's catalog, made the provider check command show it, and wrote a sourced data-handling table covering 15 hosted providers — each row checked against the provider's own pages, with "not stated" where they say nothing. Teams choosing a provider under privacy rules need those facts before they send real documents.

The checklist caught two real problems: a missing plan file and a disclosure sentence that was too long. The request is now open and passing the project's automated checks. A review bot found a gap I had missed — users of the login gateways got no privacy link — and I fixed that in a follow-up commit. No maintainer has replied yet, so the follow-ups are my next step.

https://github.com/DarkWorldCoder/market-mirror/pull/195

#OpenSource #Python #Privacy
