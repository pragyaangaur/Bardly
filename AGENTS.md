# Bardly

Context for anyone, human or AI, picking this up cold. Written 4 September 2026.

## What it is

A URL expander, the inverse of a URL shortener. You paste a link and get back a longer one whose path is a slugified line of Shakespeare. It is a joke with a real security argument underneath it.

- Repo: https://github.com/pragyaangaur/Bardly
- Live: GitHub Pages off `main`
- Language: plain HTML, CSS and vanilla JavaScript. No build step, no dependencies, no server.

## Status

**Update, 23 September 2026.** The history is now six commits on GitHub, including a LICENSE and an ignore rule for the local `.claude` folder. The code is unchanged since the first commit.

Finished and shipped. One commit, `13e4050 Bardly`, on 12 August 2026. Nothing is in progress. The working tree only has a stray `.DS_Store`.

## Layout

| File | Job |
| --- | --- |
| `index.html` | The generator. Validates the URL, picks a quote, builds the link. |
| `404.html` | The resolver. GitHub Pages serves this for any unmatched path, which is how arbitrary quote paths work with no server. |
| `quotes.js` | 261 quotes as a plain array, plus the canonical `slugify()`. |
| `style.css` | Quarto and first folio styling. |

## The design decisions that matter

Do not undo these without reading the reasoning first, because each one is deliberate.

- **The destination rides in the URL fragment.** Browsers never send the fragment in the HTTP request, so even the host serving the page does not learn where a link points. There is nothing stored and nothing to look up.
- **Percent-encoded plaintext, not base64.** Two reasons. It makes the URL longer, which is the entire point of the project. And it keeps the destination readable, so nobody can be tricked about where a link goes.
- **The interstitial never auto-redirects.** `404.html` always shows the quote, the destination hostname spelled out, and the full URL. You continue by clicking. Auto-bouncing would turn Bardly into a convenient open redirector.
- **Validation is client side at generation time.** Scheme-less input is normalised to `https://`, and only `http:` and `https:` are accepted afterwards.

## Working on it

Open `index.html` in any static server. Editing a file and reloading is the whole loop. `slugify()` in `quotes.js` is canonical and both pages must agree on it, so change it in one place only.
