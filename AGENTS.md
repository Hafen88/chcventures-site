# AGENTS.md

Operating contract for this repo. Any AI agent or human editing this site reads this first.

## What this is
The public website for CHC Ventures LLC at https://chcventures.llc. Three pages of plain static HTML and one hand written stylesheet. There is no build step, no npm, no framework, and no `node_modules`. The file you edit is the file that gets served.

## Hard rules
- **No build step. No npm. No framework.** If you are about to add a `package.json`, stop. See "Why static" below.
- **No em dashes or en dashes** in any authored text, code comment, or doc. Use commas, periods, or line breaks.
- **Copy is not authored here.** All site copy has one home: `chcventures/active/site-content-canonical.md` in the owner's `ai-os` tree. Change copy there first, then bring it here verbatim. Never let the two diverge.
- **Content must be visible with JavaScript off.** There is no JavaScript on this site and there should not be. Nothing may gate visibility on scroll or on a script running.
- Do not add fake logos, testimonials, team pages, or client lists. Do not list concept stage or parked ventures.

## The shared block rule
This is the one real cost of having no build step. Three files repeat the same markup:

1. **`<head>`** block. Everything except `<title>`, `description`, `canonical`, and the three `og:` values, which are per page.
2. **`<header class="site-header">`** block. Same markup in all three files, but two things differ by page: which nav link carries `aria-current="page"`, and the link depth. Root pages use `./`, `story/`, `about/`. Pages one level down use `../`, `../story/`, `../about/`.
3. **`<footer class="site-footer">`** block. Byte for byte identical across all three files.

Both repeated blocks are marked with a `SHARED BLOCK` HTML comment.

**When you change a shared block, change it in all three files in the same commit, then diff them to confirm they match.** Nothing enforces this. There is no template. If you change the footer in one file and not the others, the site is silently inconsistent and nobody will notice.

Quick check before committing a shared-block change:
```
for f in index.html story/index.html about/index.html; do
  grep -A2 'class="site-footer"' $f | md5sum
done
```
All three hashes must match.

## Files
```
index.html         Home. Hero, two venture cards, the #ventures anchor section.
story/index.html   Story. First person, seven blocks.
about/index.html   About. Hero, two venture cards, Company Details, Founder, contact.
style.css          The entire stylesheet. Tokens at the top, then layout, then components.
favicon.svg        CHC monogram.
robots.txt         Allow all, points at the sitemap.
sitemap.xml        Three URLs. UPDATE THIS whenever a page is added or removed.
.nojekyll          Stops GitHub Pages running Jekyll over the files.
```

**There is no `CNAME` file yet, deliberately.** The site is being verified on the free `github.io` URL before the custom domain is attached. When the host is ratified and the DNS cutover happens, add a one line `CNAME` file at the repo root containing exactly `chcventures.llc`, and read the DNS section below first.

## Paths are relative, on purpose
Every asset and nav link is relative, not absolute. That means the site works unchanged in all three places it needs to: served at a domain root, served from a `github.io/repo-name/` subpath during verification, and opened straight off the filesystem with no server. Do not "clean this up" to absolute `/style.css` paths. It breaks subpath serving, which is how the site gets verified before DNS is touched.

The `canonical` and `og:url` tags are the exception. They are absolute and point at `https://chcventures.llc`, on purpose, so a staging copy on `github.io` never gets indexed as a separate site.

## Structure, and what is deliberately absent
Three pages: Home, Story, About. **Ventures is not a page.** It is an anchor section on Home at `#ventures`, and every venture card links there.

Per-venture pages (`/throughline/`, `/empathos/`) are deferred on purpose. Each gets a page when it has something true to put on one: a screenshot, a waitlist, a first customer. A page for a pre-revenue concept reads as vapor, which is the failure mode this site exists to avoid.

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

One accent color. Text forward. No second accent, no gradients, no icon sets.

## Deploy
Push to `main`. GitHub Pages serves it. There are no previews and no rollback button, so `git revert` is the undo.

## DNS, read before touching
The domain is registered at Squarespace and the zone stays there. The cutover changes five records and leaves nine untouched, and **every mail record is in the untouched nine**. `chcventures.llc` carries a Google Workspace with twelve aliases.

- Never apply Squarespace's "Defaults preset." It overwrites the Google records and takes down site and mail.
- Never touch the MX, SPF, DKIM, or verification records.
- Remove the old Google Sites domain mapping **last**, after the new site verifies, so the domain never points nowhere.

Full record inventory and the cutover order: `chcventures/references/domain-dns.md` and `chcventures/decisions/2026-08-22-site-stack-and-hosting.md` in the owner's `ai-os` tree.
