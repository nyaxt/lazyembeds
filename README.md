# LazyEmbeds: Explainer

## Authors
* [@nyaxt](https://github.com/nyaxt)

## Participate
* [github.com/nyaxt/lazyembeds/issues](https://github.com/nyaxt/lazyembeds/issues)

## Introduction

Third-party embeds enable sites to integrate engaging web experiences hosted by third-party origins on a first-party site - this includes video players, social media content and widgets. While such embeds can supercharge a site's experience, they also come with a range of performance and privacy caveats.

For example, it’s not uncommon for an embed to request and execute large amounts of script, which can have a surprising impact on the performance of the parent page - from resource contention to delaying interaction readiness. Similarly, embeds can often include tracking capabilities that may not be obvious to embedders.

We (Google Chrome team) are currently exploring if perceived page load speed can be improved by introducing a different loading schedule to third-party embeds. Here, we draw attention to cross-origin <iframe>s, which is widely used to integrate a website with such 3rd party SaaS.

## Goals
User agent explores different loading schedules for cross-origin <iframe>s. This is done by inserting arbitrary delays before cross-origin iframe to start.

We aim to selectively apply the delay using heuristics gathered from signals such as:
- Whether <iframe> is in the viewport or not
- Size of the iframe
- The origin of the iframe
- etc.

## Non-goals
We currently do not plan to apply the delay to same-origin iframes.

## The monkey patch to HTML spec
The [*will lazy load element steps*](https://html.spec.whatwg.org/#will-lazy-load-element-steps) is modified to take `request` as an additional parameter. Also, we insert the following steps before step 3:

3. If _element_’s lazy loading attribute is set and is in the Eager state, then return false.

4. If _element_’s document’s URL is not same origin with [_request_](https://fetch.spec.whatwg.org/#concept-request)’s URL’s origin, optionally, the user agent may choose to return true, if it believes doing so would improve the loading experience.
