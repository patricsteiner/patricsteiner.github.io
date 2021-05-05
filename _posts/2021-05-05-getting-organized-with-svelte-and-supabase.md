---
layout: post
title: Getting organized with Svelte.js and Supabase
image: postits.jpg
categories: [svelte, supabase]
---

I have many things going on in my life that I want to keep track of. To get organized, I could obviously just use a whiteboard, an agenda or any of the bazillion tools and apps that exist for this exact purpose. But I could also increase the app count on the market to a bazillion + 1 🤔

A great opportunity to try out some new technology that I've had in the back of my head for a while!
The first is [Svelte](https://Svelte.dev/). Svelte is a frontend component framework that precompiles your code, as opposed to Angular, react and vue, which use runtimes. It claims to be very simple and use very little code.
The second technology is [Supabase](https://supabase.io/), an open-source alternative to [Firebase](https://Firebase.google.com/) (Firebase is an amazing product, but the biggest downside IMO is that its not open-source and owned by google...).

# The goal
A minimal webapp where I can put posits with stuff that I want to keep track of. I'll call the app Samsboard, because I shamelessly stole the idea from my friend sam who does the same thing, but on paper 😜.

# Svelte frontend
Setting up Svelte is super easy, we just copy the template project using degit:

```
npx degit Sveltejs/template samsboard
cd samsboard
node scripts/setupTypeScript.js
npm i
npm run dev
```

Typescript? Yes please! We can then jump straight into the App.svelte file and start coding. `.svelte` files almost look like normal html files, with  `<script>` and `<style>` tags for JS/TS and CSS respectively. But in addition to that, there is a special Svelte syntax we can use in the html template. Check out this example ov a `.svelte` file:

```
<script>
	let name = 'world';
</script>

<h1>Hello {name}!</h1>
```

## Data model
I would like to have colored postits positioned in kanban-like lanes. The most simple model I can think of for this purpose looks like this:

```
export interface Postit {
    id: number;
    title: string;
    lane: string;
    color: string;
};
```
A board is then simply a collection of postits.

## Dummy data
To quickly get started, we will just use some mock data that we will later replace with a datasource on Supabase:

```
<script lang="ts">
    const postits = [
        { id: 0, title: 'hello', lane: 'foo',  color: 'yellow' },
        { id: 1, title: 'world', lane: 'foo', color: 'green' },
        { id: 2, title: 'i like trains', lane: 'bar', color: 'yellow' },
    ];
    const lanes = [...new Set(postits.map((e) => e.lane))]; // keep a list of all lanes so we can easily loop through
    const boardId = '1'; // we could later generate a uuid or something like that
</script>
```

## Auxiliary functions
Just some basic javascript/typescript, nothing special here:

```
<script lang="ts">
	function addPostit(lane: string) {
		const title = prompt();
		if (!title) return;
		const id = postits.map((e) => e.id).sort((a, b) => b - a)[0] + 1 || 0; // pragmatic way to get new id
		postits = [...postits, { id, lane, title, color: 'yellow' }];
		savePostits();
	}

    function savePostits() {
        // TODO: save to Supabase backend
    }
</script>
```

## Layout
Again, I will just go with the most simple html layout that does the job. See how easy that Svelte syntax is? 😃 (`<style>` omitted for brevity).

```
<div id="board">
    {#each lanes as lane}
        <div class="lane">
            <h3>{lane}</h3>
            <button class="add-postit" on:click={() => addPostit(lane)}>+</button>
            {#each postits.filter((e) => e.lane === lane) as postit}
                <div
                    class="postit"
                    style="background:{postit.color}"
                    on:click={() => rotateColor(postit)}
                >
                    {postit.title}
                </div>
            {/each}
        </div>
    {/each}
</div>
```

# Supabase backend
Supabase is a backend-as-a-service, like google's Firebase. It offers a database, authentication, storage etc. out of the box.
I am a big Firebase fan, but the one big downside of Firebase is that it's closed-source and owned by google, which is a big vendor lock in...
So I am very happy to see that Supabase, an open-source alternative to Firebase, is getting some traction!
Supabase uses [PostgREST](https://postgrest.org/en) under the hood and makes it extremely easy for developers to query the underlying postres database via REST.

## Setting up Supabase
We can setup an account and database directly on the Supabase website. I created a table `board` with columns `id` (uuid) and `postits` (json). Since we're not doing anything fancy, I'll again just be pragmatic and store all postits as one big json array.
We run `npm install @supabase/supabase-js` and can then add some initialization code in our Svelte project:

```
import { createClient } from "@supabase/supabase-js";

const SUPABASE_URL = "https://xxxxxxxxx.supabase.co"; // copy from Supabase website
const SUPABASE_KEY = "XXXXXXXXXX"; // copy from Supabase website

const supabase = createClient(SUPABASE_URL, SUPABASE_KEY);
```

## Querying the database
Supabase has an API documentation and examples that auto-adjust to your database! So you can really just go and copy-paste a code snippet and already be at a great starting point. Only some minor adjustments are necessary to implement the `savePostits()` function we defined previously:

```
function savePostits() {
    const { data, error } = await this.supabase
            .from("board")
            .upsert([{ id: boardId, postits }])
            .eq("id", boardId);
    if (error) throw error;
}
```

And quess what, retrieving data is just as easy:
```
async findBoard(boardId: string): Promise<Postit[]> {
    const { data, error } = await this.supabase
        .from("board")
        .select("postits")
        .eq("id", boardId);
    if (error) throw error;
    return data.length > 0 ? data[0].postits : [];
}
```

# Conclusion

## Svelte
I am used to develop frontends with Angular, so using plain Svelte was a big adjustment for me. At first it felt like a lot is missing, but only then I noticed that Svelte is just a component framework, while Angular obviously is a very mature app framework with everything included. [SvelteKit](https://kit.svelte.dev/) is looking to fill that gap, but it is still in development and I haven't tried it yet. 
In the end my impression of svlete is really good, it feels very clean and intuitive and it's much less noisy than Angular.

## Supabase
Even though Supabase is still in beta, it already feels very polished. It really just works. There is a relational database behind it with user management and everything. Firebase has Firestore (and Realtime DB), which are document oriented DBs. Depending on the use case, that might be an important criteria, but for "normal" apps it doesn't really matter IMO.
What _doesn't_ work yet are Supabase realtime DB subscriptions in frontends (because they haven't yet implemented proper security mechanisms) and functions are still not available at the time of writing this blogpost.
Verdict: Supabase is not (yet) a replacement for Firebase, but as long as you don't need specific Firebase features (analytics, ML and other GCP stuff), I think you'll be quite happy with Supabase 😊

## The product: Samsboard
[Here it is](https://samsboard.vercel.app), a minimal viable product that does exactly what I want it to do and nothing else: A postit board that replaces my whiteboard (finally i can actually use my whiteboard to draw etc.).

![Samsboard](/samsboard.png)

**Time taken for this project:**
- Learning Svelte basics: 1h
- Building a very simple frontend (while still learning Svelte): 5h
- Learning Supabase basics and integrating it in my app: 2h
- Trying to find out how HTML5 drag&drop works (together with Svelte): longer than it should have
- First time using Vercel for deployment: 10m (omg it's so easy!)
- Not understanding how Supabase RLS (row level security) compares to firestore rules: 1h, still unsolved
- Writing Blogpost: 1h
- **TOTAL**: ~10h