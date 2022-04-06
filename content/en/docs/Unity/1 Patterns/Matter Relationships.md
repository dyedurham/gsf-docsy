---
title: "Matter Relationships"
linkTitle: "Matter Relationships"
date: 2017-01-05
weight: 3
---

'Matter Relationships' are stored in the GSF.Matters service. Each describes a link between a matter and some other entity. It is deliberately unopinionated for maximum extensibility and flexibility, and enables new features from different areas of law to be added without the GSF.Matters needing changes (S, O, D from the SOLID principles).

Each relationship has:

- From: the type and id of the source entity. This is usually a matter id
- To: the type and id of the target entity, for example another matter or a Contact from the Contact service
- Role: some metadata giving context to the relationship for example the relationship between a matter and a Contact could be 'Vendor' or 'Purchaser'.

In order to reduce duplication, the same To and From entities could be linked multiple times with different roles e.g. a lawyer might be both the 'PersonActing' and 'PersonResponsible' linked to a matter.
