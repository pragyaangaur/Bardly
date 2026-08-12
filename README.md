# Bardly

A URL expander — the inverse of a URL shortener. Paste a link, get back a
**longer** one whose path is a slugified line of Shakespeare.

```
https://username.github.io/bardly/to-be-or-not-to-be-that-is-the-question#https%3A%2F%2Freddit.com
```

## How it works

There is no server and no database. The destination rides along **in the
fragment** of the generated link, so there is nothing to store and nothing to
look up.

- `index.html` — the generator. Validates your URL, picks a quote, builds the link.
- `404.html` — the resolver. GitHub Pages serves this for any path that doesn't
  match a real file, which is how arbitrary quote paths work without a server.
  It reads the slug from `location.pathname` and the destination from
  `location.hash`, then renders an interstitial.
- `quotes.js` — 261 quotes as a plain JS array, plus the canonical `slugify()`.
- `style.css` — quarto / first folio styling.

The fragment is never sent to any server — browsers don't put it in the HTTP
request — so even the host serving the page does not learn where a given link
points.

### Why percent-encoded plaintext, not base64

Two reasons. It makes the URL longer, which is the entire point of the project.
And it keeps the destination human-readable, so nobody can be tricked about
where a link goes — you can read the target straight out of the address bar
before you click.

### The interstitial never auto-redirects

`404.html` always shows you the quote, the destination hostname spelled out
plainly, and the full URL. You continue by clicking. This is deliberate: an
expanded link is an opaque-looking URL from an untrusted party, and silently
bouncing people onward would make Bardly a convenient open redirector.

## Validation

At generation time, client side:

- Scheme-less input is normalized to `https://` (`//host` becomes `https://host`).
- Only `http:` and `https:` are accepted after normalization.
- `javascript:`, `data:`, `file:` and `vbscript:` are explicitly blocked, checked
  against the raw input with whitespace stripped so `java\nscript:` can't sneak through.
- Links pointing back at Bardly's own origin are rejected, to prevent loops.
- Input is capped at 2048 characters.
- The hostname must contain a dot, which rules out `https://reddit`.

The resolver re-validates independently. The fragment is attacker-controlled, so
`404.html` re-parses it and refuses to put anything but an `http(s)` URL into the
Continue link. All rendering uses `textContent`, never `innerHTML`.

Malformed links — missing fragment, broken percent-encoding, non-http scheme —
get a friendly "this link is malformed" page rather than an exception.

## The corpus

261 quotes across the tragedies, comedies, histories and sonnets. Each is 4–12
words so the slugs stay readable, and punctuation-heavy lines were avoided
because they slugify badly. Every entry cites its play and act/scene (or sonnet
number); quotes whose citation could not be stated with confidence were left out
rather than approximated.

Slugs are decorative, not keys — the destination lives entirely in the fragment,
so collisions are harmless and no collision handling exists. Two links to
different places may legitimately share a quote.

To add quotes, append to the array in `quotes.js`. Nothing needs rebuilding.