# anujvaishnav.github.io

Personal webpage of Anuj Vaishnav — [anujvaishnav.com](https://anujvaishnav.com).

Built by GitHub Pages with Jekyll. Pushing to `master` publishes the site.

## Writing a new blog post

1. Create a file in `_posts/` named `YYYY-MM-DD-your-post-slug.md`.
   The date and the dashes are required — Jekyll uses the filename to
   set the post date and the URL.

2. Start the file with front matter:

   ```yaml
   ---
   title: "Your Post Title"
   subtitle: "Optional one-line standfirst shown under the title"
   date: 2026-09-05
   tags: [systems, performance]
   description: >-
     Optional 1-3 sentence summary. Used for the blog index card and for
     Google / LinkedIn link previews. Falls back to the first paragraph.
   ---
   ```

3. Write the body in Markdown below the front matter. Do **not** repeat the
   title as an `# H1` — the layout renders it. Use `##` for section headings.

4. Commit and push:

   ```sh
   git add _posts/YYYY-MM-DD-your-post-slug.md
   git commit -m "Post: Your Post Title"
   git push
   ```

The post appears at `https://anujvaishnav.com/blog/your-post-slug/`, is listed
on `/blog/`, shows in the "Writing" section of the homepage (latest 2), and is
added to the RSS feed at `/feed.xml`.

### Drafts

Put work-in-progress in `_drafts/` (no date in the filename). Drafts are not
published. Preview them locally with `bundle exec jekyll serve --drafts`.

## Local preview (optional)

Not required — you can just push. Needs Ruby 2.7+.

```sh
bundle install
bundle exec jekyll serve
# http://127.0.0.1:4000
```

## Layout of the repo

| Path | Purpose |
| --- | --- |
| `index.html` | Homepage. Has Jekyll front matter so it can list recent posts. |
| `blog.html` | The `/blog/` index page. |
| `_posts/` | Blog posts, one Markdown file each. |
| `_layouts/default.html` | Page shell: head, top nav, footer, scripts. |
| `_layouts/post.html` | Post title, meta, body, prev/next nav. |
| `_layouts/page.html` | Thin wrapper for standalone pages. |
| `assets/css/site.css` | The whole stylesheet: tokens, layout, components, blog. |
| `_data/*.yml` | Publications, talks, awards, experience, projects. |
| `_config.yml` | Site settings, permalinks, plugins. |

## Editing the homepage content

The long lists are data, not markup. To add a publication, talk, award, role or
project, add an entry to the matching file in `_data/` — the homepage renders
them automatically. Author names are written plainly; the template emphasises
"A. Vaishnav" for you.

## Theme

One stylesheet, `assets/css/site.css`, built on CSS custom properties. Dark mode
follows the OS by default and can be overridden with the toggle in the nav, which
persists to `localStorage` under `av-theme`.

Images: `images/thumbs/*.png` are small renders of the full-size diagrams in
`images/*.svg` (those originals embed base64 rasters and are ~2.5 MB combined,
far too heavy to load for a 48px card).
