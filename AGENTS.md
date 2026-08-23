# AGENTS.md

Operating contract for this repo. Any AI agent or human editing this site reads this first.

## What this is
The public website for CHC Ventures LLC at https://chcventures.llc. Four pages of plain static HTML and one hand written stylesheet. There is no build step, no npm, no framework, and no `node_modules`. The file you edit is the file that gets served.

## Hard rules
- **No build step. No npm. No framework.** If you are about to add a `package.json`, stop. See "Why static" below.
- **No em dashes or en dashes** in any authored text, code comment, or doc. Use commas, periods, or line breaks.
- **Copy is not authored here.** All site copy has one home: `chcventures/active/site-content-canonical.md` in the owner's `ai-os` tree. Change copy there first, then bring it here verbatim. Never let the two diverge.
- **Content must be visible with JavaScript off.** There is no JavaScript on this site and there should not be. Nothing may gate visibility on scroll or on a script running.
- Do not add fake logos, testimonials, team pages, or client lists. Do not list concept stage or parked ventures.

## The shared block rule
This is the one real cost of having no build step. Four files repeat the same markup:

1. **`<head>`** block. Everything except `<title>`, `description`, `canonical`, and the three `og:` values, which are per page.
2. **`<header class="site-header">`** block. Same markup in all four files, but two things differ by page: which nav link carries `aria-current="page"`, and the link depth. Root pages use `./`, `story/`, `about/`. Pages one level down use `../`, `../story/`, `../about/`.
3. **`<footer class="site-footer">`** block. Byte for byte identical across all four files.

**Every page also carries `data-page` on its `<body>`**, one of `home`, `story`, `build`, `about`. It is not decoration. `style.css` reads it to position that page's ambient hero light. A new page with no `data-page` still renders correctly, it just falls back to the neutral light position. Add a value for any page you add, and add the matching rule in the Ambient light block of `style.css`.

Both repeated blocks are marked with a `SHARED BLOCK` HTML comment.

**When you change a shared block, change it in all four files in the same commit, then diff them to confirm they match.** Nothing enforces this. There is no template. If you change the footer in one file and not the others, the site is silently inconsistent and nobody will notice.

Quick check before committing a shared-block change:
```
for f in index.html story/index.html build/index.html about/index.html; do
  grep -A2 'class="site-footer"' $f | md5sum
done
```
All four hashes must match.

## Files
```
index.html         Home. Hero, two venture cards, the #ventures anchor section.
story/index.html   Story. First person, seven blocks.
build/index.html   Build log. Six drafts, each with stack, Proved, Ended, Carried.
about/index.html   About. Hero, two venture cards, Company Details, Founder, contact.
style.css          The entire stylesheet. Tokens at the top, then layout, then components.
favicon.svg        CHC monogram.
robots.txt         Allow all, points at the sitemap.
sitemap.xml        Four URLs. UPDATE THIS whenever a page is added or removed.
.nojekyll          Stops GitHub Pages running Jekyll over the files.
CNAME              chcventures.llc. Do not delete; it is what binds the custom domain.
```

**`CNAME` exists and holds exactly `chcventures.llc`.** The DNS cutover ran 2026-08-22 and the site serves at the apex over HTTPS with Enforce HTTPS on. Do not delete or edit that file; removing it drops the custom domain. (This section previously said no `CNAME` existed yet, which was true only during pre-cutover verification.)

## Paths are relative, on purpose
Every asset and nav link is relative, not absolute. That means the site works unchanged in all four places it needs to: served at a domain root, served from a `github.io/repo-name/` subpath during verification, and opened straight off the filesystem with no server. Do not "clean this up" to absolute `/style.css` paths. It breaks subpath serving, which is how the site gets verified before DNS is touched.

The `canonical` and `og:url` tags are the exception. They are absolute and point at `https://chcventures.llc`, on purpose, so a staging copy on `github.io` never gets indexed as a separate site.

## Structure, and what is deliberately absent
Four pages: Home, Story, Build, About. **Ventures is not a page.** It is an anchor section on Home at `#ventures`, and every venture card links there.

Per-venture pages (`/throughline/`, `/empathos/`) are deferred on purpose. Each gets a page when it has something true to put on one: a screenshot, a waitlist, a first customer. A page for a pre-revenue concept reads as vapor, which is the failure mode this site exists to avoid.

**The build log is written in first person, like Story.** It is a personal build history and it does not work in third person. That is a deliberate exception to the copy rule, recorded in the owner's canon.

**Page count is a stack decision, not a preference.** Past roughly six pages the shared block rule stops being manageable and the site should move to Hugo (single Go binary, zero npm, output is still plain HTML). Do not move to Astro or Eleventy, which reintroduce the npm surface. If you are adding the fourth or fifth page, note it. If you are adding the seventh, raise the Hugo question first.

## Why static, so nobody re-litigates it
Measured 2026-08-22. No major AI crawler executes JavaScript. GPTBot, OAI-SearchBot, ChatGPT-User, ClaudeBot, and PerplexityBot fetch JavaScript files and never run them. Only Googlebot, Gemini, and AppleBot render. A React SPA ships an `index.html` of about 0.45 KB containing an empty root div, which means it is **totally invisible to ChatGPT, Claude, and Perplexity**. For a holding company whose site exists so that someone looking us up finds out who we are, that is disqualifying.

Secondary reasons: no yearly framework major to chase, and no `npm install` step, which is the only place supply chain risk lives.

The predecessor design lives at `Hafen88/CHC-Ventures-Website2` (private, Vite plus React plus Tailwind v4). It is a **design reference only**. Do not port its plumbing and do not copy its copy, which predates canon.

## Styling
`style.css` is hand written, not generated. Tokens live in `:root` at the top. Use them; do not hard code hex values.

```
Navy    #1B263B    text, hero background, buttons
Slate   #415A77    body text
Accent  #3A86FF    the one accent, links and hovers
Light   #F8FAFC    alternating section background
Surface #FFFFFF    page background, cards
```
Headings: Playfair Display. Body: Plus Jakarta Sans. Both from Google Fonts with real fallback stacks.

**Third register, added 2026-08-23.** A monospace stack via `--font-mono` carries the small factual labels only: build log dates and stack lines, definition list labels, the status eyebrow, and `.eyebrow`. Data reads as data, prose reads as prose. It is not a body or heading face. Do not widen its use.

One accent color. Text forward. No second accent, no icon sets.

**Gradient rule, with its one exception.** No decorative gradients, with a single deliberate exception: the ambient hero light, a soft radial in the accent color at low alpha, positioned per page from `data-page`. It is in the Ambient light block of `style.css` and it is intentional. Do not delete it as a "no gradients" cleanup. Any *other* gradient is still out.

## Motion, added 2026-08-23
The site has a CSS only motion system. Still zero JavaScript. It has four parts and four constraints, and the constraints are the part that matters.

**The four parts**
1. **Cross document view transitions.** `@view-transition { navigation: auto; }` plus three named elements: `site-header`, `hero-mark`, `hero-panel`. The header holds still across a navigation, the logo travels, the hero cross fades. Chromium and Safari today. Firefox falls back to a plain page load with no visual penalty, which is fine and needs no workaround.
2. **Load sequence.** The hero mark, `h1`, and paragraph rise on load, staggered.
3. **Ambient light.** One radial in the hero, parked in a different corner per page via `data-page`. Paint only, it moves no boxes.
4. **Mono register.** See Styling.

**The four constraints. Do not break these.**
1. **All text is visible with JavaScript off, CSS animation off, and no hover.** Nothing may gate visibility.
2. **Everything that moves lives inside `@media (prefers-reduced-motion: no-preference)`**, and a `prefers-reduced-motion: reduce` block neutralizes transitions and hover transforms. The reduced motion render must stay **pixel identical to the normal render at rest**. It is today. Verify by screenshot diff, not by reading the CSS.
3. **Use `animation-fill-mode: backwards`, never `both`.** This is the trap that broke constraint 2 the first time. `both` holds a transform after the animation ends, which promotes the element to a compositing layer and changes text antialiasing, so the two renders differ visibly even though no box moved. `backwards` still prevents the pre animation flash and drops the layer when the animation finishes.
4. **A `view-transition-name` must be unique per document.** Each of the three appears exactly once per page. If you add a fourth, check every page.

Density was tuned the same day. Hero, section, stack, build entry, and footer spacing all sit in the Density block at the bottom of `style.css`, which only ever reduces space and changes no color, size, or order.

## Deploy
Push to `main`. GitHub Pages serves it. There are no previews and no rollback button, so `git revert` is the undo.

## DNS, read before touching
The domain is registered at Squarespace and the zone stays there. The cutover changes five records and leaves nine untouched, and **every mail record is in the untouched nine**. `chcventures.llc` carries a Google Workspace with twelve aliases.

- Never apply Squarespace's "Defaults preset." It overwrites the Google records and takes down site and mail.
- Never touch the MX, SPF, DKIM, or verification records.
- Remove the old Google Sites domain mapping **last**, after the new site verifies, so the domain never points nowhere.

Full record inventory and the cutover order: `chcventures/references/domain-dns.md` and `chcventures/decisions/2026-08-22-site-stack-and-hosting.md` in the owner's `ai-os` tree.
