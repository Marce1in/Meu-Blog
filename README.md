# Meu Blog

This repository contains the source code for my personal blog, built with Jekyll and hosted on Cloudflare Pages.

## Technologies

The blog is built using [Jekyll](https://jekyllrb.com/), a static site generator. Key technologies and plugins include:

*   **Theme:** [jekyll-theme-hacker](https://github.com/pages-themes/hacker) with gruvbox colors is used for the visual style.
*   **Localization:** The site is bilingual (Portuguese and English), managed by the [jekyll-polyglot](https://github.com/untra/polyglot) plugin.
    *   UI strings (like the site title and description) are stored in the `_data/[lang]/strings.yml` files.
    *   Blog posts are created with separate files for each language (e.g., `YYYY-MM-DD-title-pt-BR.md` and `YYYY-MM-DD-title-en.md`) in the `_posts` directory.
    *   The default language is Portuguese (`pt-BR`).
*   **Other Plugins:**
    *   `jekyll-feed`: Generates an Atom feed for posts.
    *   `jekyll-date-localization`: Displays dates in the appropriate language.
    *   `jekyll-minifier`: Minifies HTML and CSS to improve performance.

## Local Development

To run the project locally, follow these steps:

1.  **Install Dependencies:** Ensure you have Ruby and Bundler installed. Then, run:
    ```bash
    bundle install
    ```

2.  **Start the Server:**
    ```bash
    bundle exec jekyll serve
    ```
    The site will be accessible at `http://localhost:4000`.

## Deployment

The blog is deployed automatically by [Cloudflare Pages](https://pages.cloudflare.com/).
