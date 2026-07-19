# Jay's Github Pages (Jekyll Site)

This repository contains the source code for my personal GitHub Pages website built with Jekyll and the [TeXt Theme](https://github.com/kitian616/jekyll-TeXt-theme).

## Getting Started

### Prerequisites

To run this project locally, you need to have the following installed:

- **Ruby** (compatible version as per Jekyll requirements, typically 3.0+)
- **Bundler** (run `gem install bundler` if you don't have it)

### Installation

1. Install dependencies:
   ```bash
   bundle install
   ```

### Local Development

To run the development server locally, run:

```bash
bundle exec jekyll serve
```

This will boot up the Jekyll server. By default, it will be available at [http://localhost:4000](http://localhost:4000).

#### With Live Reload
If you want the site to automatically reload when you make changes, run:

```bash
bundle exec jekyll serve --livereload
```

## Structure

- `_posts/`: Markdown files for blog posts.
- `_config.yaml`: Configuration settings for the Jekyll site and the TeXt theme.
- `index.md`: Homepage content.
- `_data/`: Directory containing site data files.