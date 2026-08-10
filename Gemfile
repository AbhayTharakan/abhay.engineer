source "https://rubygems.org"

# Built and deployed by .github/workflows/pages.yml rather than the native
# GitHub Pages builder, so we're free to run current Jekyll instead of the
# `github-pages` gem's pinned (and Ruby 3.2+ incompatible) 3.9 stack.
gem "jekyll", "~> 4.4"

group :jekyll_plugins do
  gem "jekyll-seo-tag", "~> 2.8"
  gem "jekyll-sitemap", "~> 1.4"
end

# No longer default gems on modern Ruby.
gem "webrick", "~> 1.8"
gem "csv"
gem "base64"
gem "bigdecimal"
