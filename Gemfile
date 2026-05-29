source "https://rubygems.org"

# Use modern Jekyll instead of github-pages gem
# Site will be built via GitHub Actions
gem "jekyll", "~> 4.3"

gem "tzinfo-data"
gem "wdm", "~> 0.1.0" if Gem.win_platform?

# Theme
gem "minimal-mistakes-jekyll"

# Pin: sass-embedded 1.100.0 fails to build (requires json >= 2.20 for JSON::Fragment).
# Drop this pin once upstream releases a fix.
gem "sass-embedded", "< 1.100"

# Plugins
group :jekyll_plugins do
  gem "jekyll-paginate"
  gem "jekyll-sitemap"
  gem "jekyll-gist"
  gem "jekyll-feed"
  gem "jemoji"
  gem "jekyll-include-cache"
  gem "jekyll-algolia"
  gem "jekyll-scholar", "~> 7.2"
  gem "jekyll-seo-tag"
end

gem "webrick", "~> 1.8"
gem "csv"
gem "base64"
