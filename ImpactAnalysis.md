# Introduction #
This page describes some impact analysis over the performance of an application, before and after usage of wro4j.

# Details #
We take as an example a typical medium size web application. We will test more pages. For each page, there will be a row containing results with typical css & js usage and with bold - using wro4j.

| Page No | Total Requests |  Total size| Time | YSlow Mark |
|:--------|:---------------|:-----------|:-----|:-----------|
| 1  | 30 | **15** |  350.1KB | **61KB** | 3.82 | **1.15** | F (47) | **C (70)** |
| 2 | 33 | **21** | 339.1KB | **75.9KB** | 3.1 | **1.7** | F (47) | **D (68)** |

## Interpretation ##
After integrating wro4j into my sample project:
  1. The number of request dropped with 50%
  1. The total size of downloaded resources dropped with almost 6 times
  1. The YSlow Mark improved from ~50 to ~70

When using a fast internet connection, the benefits may be not so evident. In case of a very slow connection, like dial-up, the difference is much greater. There is a firefox plugin (https://addons.mozilla.org/en-US/firefox/addon/5917/) which can help to simulate a slow internet connection and to see how your application behaves with and without wro4j.