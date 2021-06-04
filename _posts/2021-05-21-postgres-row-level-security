---
layout: post
title: Postgres Row Level Security
image: samsboard.png
categories: [supabase, postgresql]
---

As a followup to [last week](https://patricsteiner.github.io/samsbaord-authentication-with-supabase/), in this post we'll see how we can make use of postgresql's row level security feature (RLS) to assure that every user of [Samsboard](https://samsboard.vercel.app) can only acces their own board, and is denied access to other boards on a database level.


# Database security
In addition to user level permissions (grants) in [RDBMS](https://en.wikipedia.org/wiki/Relational_database#RDBMS), we now also have something called row level permissions (RLS), or row security policies. I've mostly been using noSQL DBs in the last couple years, so I haven't even been aware of this feature in RDBMS. 😮

The feature is now available in Postresql, and since Supabase uses Postresql as a database, we can make perfect use of it. It's quite simple: 
We can enable a table for RLS by typing `ALTER TABLE xxx ENABLE ROW LEVEL SECURITY;`

After that, the default rule is to deny all queries to that table, unless specified otherwise.

What we can do now is 

