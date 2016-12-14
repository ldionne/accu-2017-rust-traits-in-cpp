## Type erasure with Hana @ ACCU Meetup December 2016

This repository contains my [reveal.js][]-based lightning talk on type erasure
using Hana for the [ACCU Meetup][] of December 2016.

## Basic usage
Go to https://ldionne.com/accu-meetup-hana-type-erasure or open `index.html`
with your browser.

## Advanced usage
From the root of the repository,
```sh
npm install
grunt serve &
```

and then connect to `localhost:8000` to view locally.

## Building the code samples

```sh
(mkdir build && cd build && cmake ..)
cmake --build build install-dependencies
cmake --build build
```

<!-- Links -->
[ACCU Meetup]: https://www.meetup.com/ACCU-Bay-Area/events/233766721/
[reveal.js]: https://github.com/hakimel/reveal.js
