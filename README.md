# Caching and iframes

We’re doing some testing around browser caching and assets requested by the “host” page as well as multiple iframes within that page, so I figured we may as well bring enough share with the rest of the class. More data to come.

## Total Potential Requests (8):

* index.html _(340 bytes)_
    * jquery-1.10.2.js _(266KB)_
    * frame1.html _(226 bytes)_
        * jquery-1.10.2.js _(266KB)_
    * frame2.html _(226 bytes)_
        * jquery-1.10.2.js _(266KB)_
    * frame3.html _(226 bytes)_
        * jquery-1.10.2.js _(266KB)_

## Results:

<table>
    <thead>
        <tr>
            <th></th>
            <th scope="col">Empty Browser Cache</th>
            <th scope="col">Primed Browser Cache</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <th scope="row">Chrome (Current)</th>
            <td>8 requests, 268KB transferred</td>
            <td>6 requests, 223 bytes transferred</td>
        </tr>
        <tr>
            <th scope="row">Safari 6.1</th>
            <td>8 requests, 1068KB transferred</td>
            <td>6 requests, 268K transferred</td>
        </tr>
        <tr>
            <th scope="row">iOS7 Safari</th>
            <td>8 requests, 1068KB transferred</td>
            <td>6 requests, 535KB transferred</td>
        </tr>
    </tbody>
</table>

These totals show WebKit-based browsers—iOS most notably—does not fetch the iframes’ contents from the cache on the first page load, despite having loaded the same file in the head of the index.html.

Given that Firefox is somewhat unique in how it reports requests, we’ll do a more granular comparison between Chrome and iOS WebKit here.

### Chrome (Current)

#### Empty Cache

##### Totals
8 requests
268KB transferred

##### Request Details

* *index.html* _(340 bytes)_ 
    * *jquery-1.10.2.js* _(266KB)_
    * *frame1.html* _(226 bytes)_
        * jquery-1.10.2.js _(from cache)_
    * *frame2.html* _(226 bytes)_
        * jquery-1.10.2.js _(from cache)_
    * *frame3.html* _(226 bytes)_
        * jquery-1.10.2.js _(from cache)_

On an empty cache, Chrome fetches the index page and `iframe` contents as expected. Chrome immediately caches the script file linked from the index page and makes it available from the cache when requested by the `iframes`’ contents.

#### Primed Cache

##### Total Transfer
223 bytes transferred

##### Request Details

* *index.html* _(223 bytes)_ 
    * *jquery-1.10.2.js* _(from cache)_
    * *frame1.html* _(from cache)_
        * jquery-1.10.2.js _(from cache)_
    * *frame2.html* _(from cache)_
        * jquery-1.10.2.js _(from cache)_
    * *frame3.html* _(from cache)_
        * jquery-1.10.2.js _(from cache)_

On an primed cache, Chrome returns “304: Not modified” for the index page. Data transferred while checking for “modified” status is the likely reason for the transfer side being smaller than the page itself, as it seems the entire page doesn’t need to be parsed in order to perform said check. All other assets are served from the cache, as expected.

### iOS7 Safari

#### Empty Cache

##### Total Transfer
1068KB transferred

##### Request Details

* *index.html* _(340 bytes)_ 
    * *jquery-1.10.2.js* _(266KB)_
    * *frame1.html* _(226 bytes)_
        * jquery-1.10.2.js _(266KB)_
    * *frame2.html* _(226 bytes)_
        * jquery-1.10.2.js _(266KB)_
    * *frame3.html* _(226 bytes)_
        * jquery-1.10.2.js _(266KB)_

iOS Safari does not make the external asset available from the cache during subsequent requests within the `iframe`s on the index page, resulting in a quadrupled transfer size.

 As Chrome is known to be especially aggressive about caching, this should be regarded as a systemic issue and not a browser quirk unique to iOS. For the purposes of these results iOS Safari stands in for the vast majority of browsers, particularly mobile ones.

#### Primed Cache

##### Total Transfer
535KB transferred *

##### Request Details

* *index.html* _(340 bytes)_ 
    * *jquery-1.10.2.js* _(266KB)_
    * *frame1.html* _(226 bytes)_
        * jquery-1.10.2.js _(from cache)_
    * *frame2.html* _(226 bytes)_
        * jquery-1.10.2.js _(from cache)_
    * *frame3.html* _(226 bytes)_
        * jquery-1.10.2.js _(from cache)_

On a primed cache, iOS7 Safari makes a fresh request for the index page and `iframe` pages, but fetches each linked asset from the cache. The cache requests are only reported in the total request count as two: one for the index page, and one for all of the `iframe`s. The transfer size is reported as 535KB based on these two asset requests, despite being fetched from the cache.



