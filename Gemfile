source "https://rubygems.org"

# --- Local preview on this machine (Homebrew Ruby 4.x) ---
# The `github-pages` gem pins Jekyll 3.x, which does NOT run on Ruby 4.x, so we use
# modern Jekyll for local preview. GitHub Pages builds the live site server-side with
# its own environment, so this Gemfile only affects local `bundle exec jekyll serve`;
# the deployed site is unaffected.
# (On a Ruby 3.x machine you could instead use: gem "github-pages", group: :jekyll_plugins)
gem "jekyll", "~> 4.3"
gem "jekyll-sitemap"
gem "webrick", "~> 1.8"
