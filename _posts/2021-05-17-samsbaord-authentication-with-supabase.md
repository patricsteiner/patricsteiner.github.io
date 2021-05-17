---
layout: post
title: Samsboard Authentication with Supabase
image: samsboard.png
categories: [supabase]
---

[Two weeks ago](https://patricsteiner.github.io/getting-organized-with-svelte-and-supabase/) I played around with [Svelte](https://Svelte.dev/) and [Supabase](https://supabase.io/) to create [Samsboard](https://samsboard.vercel.app), a virtual postit board that helps you to get a great overview of all the stuff that's going on in your life.

What I did _not_ do is setup authentication. Everyone with the id of a particular board had full access to that board. So theoretically you could just guess every possible [UUID](https://en.wikipedia.org/wiki/Universally_unique_identifier) out there and sometimes be lucky and find a board (it might take you a while though).

This was a fine solution to just to play around with the technology, especially when I was the only user of the app. But what if this is going to be the next Todoist? Of course we're going to need authentication! So let's see how Supabase can assist us.

# Supabase User Management
Supabase makes it very simple to manage users. A database, a table and a user endpoint are automatically setup by Supabase when we create a new project. 
So we can jump straight in out typescript code and get our hands dirty. I am still very influenced by Angular, that's why I'll put the necessary code in a `user.service.ts` file.
Notice the [ES6 destructuring assignment](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment), isn't it wonderful? I see many people still not taking advantage of newer language features like this, so start using it if you don't already!

```typescript
import { createClient, User } from "@supabase/supabase-js";

const SUPABASE_URL = "https://xxxxxxxxx.supabase.co"; // copy from Supabase website
const SUPABASE_KEY = "XXXXXXXXXX"; // copy from Supabase website

export class UserService {

    supabase = createClient(SUPABASE_URL, SUPABASE_KEY);

    currentUser(): User | null {
        return this.supabase.auth.user();
    }

    async signUp(email: string, password: string) {
        let { user, error } = await this.supabase.auth.signUp({ email, password });
        if (error) throw error;
    }

    async signIn(email: string, password: string) {
        let { user, error } = await this.supabase.auth.signIn({ email, password });
        if (error) throw error;
    }

    async signOut() {
        let { error } = await this.supabase.auth.signOut();
        if (error) throw error;
    }

}
```

The `supabase.auth.signUp` function will do some plausibility checks on email and password and then create a new user in the database. Unless we disable this option on the Supabase admin website, the email needs to be verified by the user. The email template can also be adjusted on the Supabase admin page. 

After that, `supabase.auth.signIn` can be used to sign in the user. On success, Supabase sends us a [JWT](https://en.wikipedia.org/wiki/JSON_Web_Token) authentication token that is automatically stored in the browsers localstorage.
From this point on, the supabase client created by `createClient` will send this token with every request it makes.
This is super useful, because it will allow us to make use of a very nice Postgres feature that helps us secure the database: [Row level security](https://www.postgresql.org/docs/9.5/ddl-rowsecurity.html). But that's a topic for another post. ☺

Until then: Happy coding ☕👨‍💻

