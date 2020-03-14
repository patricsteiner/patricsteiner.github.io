---
layout: post
title: Scraping Supermarket Promotions with Puppeteer
image: beers.jpg
---

Finally a quite day, it's 10pm and I've basically done everything I wanted to do this day. So it would be reasonable to go to bed now. 
BUT... I could also start working on this idea that's been in my head for a while...

Every couple of weeks or months, our local supermarket promotes certain beers for a ridiculously cheap price.
But there is no fixed schedule or notification for that promotion, so you usually end up either getting lucky 
and finding out about the promotion by chance, or you miss it and find out a day after when it's already too late.

So what if there was some way of being notified as soon as the promotion is available, so that everyone can immediately 
go buy tasty-tasty beers for little money?

And that's exactly the idea that's been in my head. I want to build an app/website, that let's you find out about current (beer) promotions.
So let's get started.

## Getting the data
So what we want is quite clear: An up to date list of all beer promotions in the supermarket.

### Approach 1 - API: ❌
The easiest way would be to use an API of our supermarket and request the current promotions. Unfortunately, there is no API.

### Approach 2 - Fetch and parse HTML: ❌
On the supermarkets website there is a [page](https://www.coop.ch/de/einkaufen/supermarkt/aktionen.html) where we can find all promotions - great!
So we can just [`fetch()`](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) the complete page, do some parsing, and we have all the data we need, right?
Unfortunately, no. The content of the page is paginated and dynamically loaded, the least we need to do to get a complete list of all promotions
is to manually press the "Alle Aktionen laden" ("load all promotions" in german) button on the promotion page.

### Approach 3 - Browser automation: ✔️
So since the website does not let us programmatically get all its content, what we can do instead is mimic the behaviour from a real user that wants to get the information.
There are various browser automation tools out there that can do this, such as for example [puppeteer](https://github.com/GoogleChrome/puppeteer).

What can it do?
> Most things that you can do manually in the browser can be done using Puppeteer!

And most importantly:
> Crawl a SPA (Single-Page Application) and generate pre-rendered content (i.e. "SSR" (Server-Side Rendering)).

[source](https://github.com/GoogleChrome/puppeteer)

So let's write down the exact actions a user needs to do to get the desired information:
1. Navigate to https://www.coop.ch/de/einkaufen/supermarkt/aktionen.html
2. Wait until the page is fully loaded
3. Click the "Alle Aktionen laden" button
4. Wait until the page is fully loaded
5. Done!

Now let's translate that into code. We will be using TypeScript for that. First, the scaffold:

```typescript
import * as puppeteer from 'puppeteer';

async function scrapePromotions(): Promise<any> {
    const url = 'https://www.coop.ch/de/einkaufen/supermarkt/aktionen.html';
    const browser = await puppeteer.launch({ headless: true, args: ['--no-sandbox'] });
    const page = await browser.newPage();
    await page.goto(url);

    // TODO: click "Alle Aktionen laden"

    const promotions = // TODO somehow get the promotions

    await browser.close();
    return promotions;
}
```

We can use our browsers devtools (press F12) and navigate to the "Alle Aktionen laden" button to see how it is rendered in the DOM. It looks like this:

```html
<div class="KBK-014-link">
  <a href="" title="Alle Aktionen laden" class="load-all">Alle Aktionen laden [114/122]</a>
</div>
```

There is a simple `<a>` tag with class `load-all`. Now that we can identify the button in the DOM, let's use puppeteer to click it:

```typescript
await page.waitForSelector('a.load-all');
await page.click('a.load-all');
await page.waitFor(5000); // wait for 5 seconds to assure everything is loaded
 ```

Now we simulated the first user interaction and the DOM should now contain all promotions. Next up: get all the promotions and store them in an array. Let's first analyze the HTML code of a single promotion, once again using the browser devtools. That's the result we get:

 ```html
<div data-kachel-id="6.336.920" class="KBK-066-A-kachel-generisch has-klapp-details active">
    <a href="#" title="Anker Lagerbier, 2 x 15 x 33 cl (100 cl = 1.21)" target="_blank">
        <div class="coopProductImage">
            <img src="//contentimages.coop.ch/aktionenimages/images/6.336.920_Anker_Lager_Bier_15x33cl_ZTGWPS_252943_XL_DE.png">
        </div>
        <div class="coopProductOffer">
            <div class="coopProductOffer__pricebox fadeoutWrapper">
                <div class="coopProductOffer__newPriceRow">
                    <span class="coopProductOffer__price">11.95</span>
                </div>
                <div class="coopProductOffer__oldPriceRow">
                    statt <span class="coopProductOffer__oldPrice">23.90</span>
                </div>
            </div>
        </div>
        <div class="coopProductDescr">
            <h2 class="coopProductDescr__title">Anker Lagerbier, 2 x 15 x 33 cl (100 cl = 1.21)</h2>
        </div>
    </a>
</div>
 ```

Awesome! So let's extract the selectors we're interested in:
- Product container: `div.KBK-066-A-kachel-generisch`
- Product image: `.coopProductImage img`
- Old price: `.coopProductOffer__oldPrice`
- New price: `.coopProductOffer__price`
- Product title: `h2.coopProductDescr__title`

And the corresponding actions in puppeteer:

```typescript
const promotions = await page.evaluate(() => {
    const products = document.querySelectorAll('div.KBK-066-A-kachel-generisch');
    const results = []
    const textContent = (elem: any) => elem ? elem.textContent.trim() : ''; // helper function
    products.forEach(product => results.push(
        {
            title: textContent(product.querySelector('h2.coopProductDescr__title')),
            price: textContent(product.querySelector('.coopProductOffer__price')),
            oldPrice: textContent(product.querySelector('.coopProductOffer__oldPrice')),
            imageUrl: product.querySelector('.coopProductImage img')!.getAttribute('src')!
        }
    ))
    return results;
    })
```

And we're done! Let's run it and see what we get:

```json
[
    {
        "imageUrl": "//contentimages.coop.ch/aktionenimages/images/6.336.920_Anker_Lager_Bier_15x33cl_ZTGWPS_252943_XL_DE.png",
        "oldPrice": "23.90",
        "price": "11.95",
        "title": "Anker Lagerbier, 2 x 15 x 33 cl (100 cl = 1.21)"
    },
    // ...
]
```

Hooray 🍻🍻🍻! We now have a function that returns an array containing all current promotions of the supermarket. Enough for today.
The next steps will be to actually make this information useful by publishing it to a website where users can subscribe and be notified
about the promotions he is interested in (e.g. beer). [Read on here!](/web-scraping-with-firebase-cloud-functions-and-cloud-firestore)

## Full code
```typescript
import * as puppeteer from 'puppeteer';

export async function scrapeCoopPromotions(): Promise<CoopPromotion[]> {
    const url = 'https://www.coop.ch/de/einkaufen/supermarkt/aktionen.html';
    const browser = await puppeteer.launch({ headless: true, args: ['--no-sandbox'] });
    const page = await browser.newPage();
    await page.goto(url);
    await page.waitForSelector('a.load-all');
    await page.click('a.load-all');
    await page.waitFor(5000);

    const promotions = await page.evaluate(() => {
        const products = document.querySelectorAll('div.KBK-066-A-kachel-generisch');
        const results: CoopPromotion[] = []
        const textContent = (elem: any) => elem ? elem.textContent.trim() : '';
        products.forEach(product => results.push(
            {
                title: textContent(product.querySelector('h2.coopProductDescr__title')),
                price: textContent(product.querySelector('.coopProductOffer__price')),
                oldPrice: textContent(product.querySelector('.coopProductOffer__oldPrice')),
                imageUrl: product.querySelector('.coopProductImage img')!.getAttribute('src')!
            }
        ))
        return results;
    })
    await browser.close();
    return promotions;
}
```
