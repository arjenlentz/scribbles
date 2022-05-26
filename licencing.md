# Open source licencing

Always been a hot topic...

## Overview

Licences can be broadly categorised in two groups:
 * BSD: the recipient can do what they like including incorporate in to closed source software. So downstream recipients may not see any aggregate.
 * GPL: the recipient can do what they like, however if they distribute (and in AGPL's case offer as a service), they have to offer any downstream recipients the option to receive the source code.

### Discussion

Now, the argument is made that BSD provides more freedom - that is somewhat true, but only for the first level recipient. Others may not benefit. A right for one that at the same time denies the same freedom to others?

Contrarily, GPL is seen by some as restrictive, as it forces developers to make source code available to downstream recipients. However, it does mean that everyone has equal rights. Hmm.

### Commercialisation Models

It is important to understand that either formula prevents someone from selling the software, the difference is in what rights downstream recipients receive (on access to the source code) along with the binary code.

GPL and AGPL do not prevent a company from commercialising a product. Even if it does not fully own the source code (which would enable it to dual licence, proprietary for a fee, for those who want to incorporate it into a proprietary product without providing downstream with access to the source), historically there are an abundance of examples where companies have done exceedingly well due to their expertise.

MySQL (before its acquisition by Sun) was intending to shift from dual licencing to services and never quite did, with 50% of its turnover remaining in dual licensing, and the other half split about evenly between training and support services.

Other companies have successfully provided services around MySQL also, and some continue to do so. Open Query operated profitably in Australia and New Zealand for over 11 years, and Percona has existed for over 15 years already and is highly respected. Neither owned or owns the code or could do dual licensing, they have relied solely on their expertise, providing a range of support services, and training.

### Business Source License

I do not believe in the "Business Source License", which is (by its own admission) not actually an open source licence, but merely promises a pathway for the source to become available at some future date. It has been called (by others) "BS licence". That may be harsh, but exactly because the licencing space is regarded as confusing, adding a licence such as this one does not improve matters. It is, in a way, no better than the early Microsoft "shared source" licence which was so restrictive as to be useless - and it was thoroughly criticised as such at the time.

## Conclusion

Based on the available historical evidence, a clear case to be made for using either licence family, depending on the type of library, tool or software package your building, and the desired business model (if any).

The author generally prefers the GPL model, since it does provide the same rights to all downstream recipients. In the case of the Affero GPL (AGPL), this includes SaaS users. I.e., with AGPL a company X can build a product, company Y can offer it as a service with modifications, however those modifications have to be available to their clients. I find this is a useful and fair safeguard that prevents company Y to simple take company X's work and run with it.

For any product that can potentially be used in a SaaS-like structure, I would recommend AGPL. It is very important that the developing company, very early on, considers what its business (and revenue!) model is going to be. People can respect a choice, but customers do not take kindly to moving goalposts.

Similarly, many companies launch something without any revenue model whatsoever, in order to gain market share. This is distinctly unwise, as the price differential from nil to anything is infinite. It is a very hard sell. Why not structure things for a modest cost from the start.

For further ideas and business advice, see also https://upstarta.com.au/

[eof]
