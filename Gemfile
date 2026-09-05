source "https://rubygems.org"

# Matches the exact Jekyll + plugin versions GitHub Pages runs server-side,
# so a local preview behaves the same as the deployed site.
# Local preview is optional -- pushing to master builds the site regardless.
gem "github-pages", group: :jekyll_plugins

# jekyll-feed, jekyll-seo-tag and jekyll-sitemap are already pinned by
# github-pages; they are listed in _config.yml `plugins:` and need no entry here.

# Ruby 3.0 dropped webrick from stdlib and Jekyll 3.x does not depend on it.
gem "webrick", "~> 1.8" 

# Windows / JRuby support
gem "tzinfo-data", platforms: [:mingw, :mswin, :x64_mingw, :jruby]
gem "wdm", "~> 0.1.0", platforms: [:mingw, :mswin, :x64_mingw]
