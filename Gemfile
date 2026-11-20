# frozen_string_literal: true

source "https://rubygems.org"

group :jekyll_plugins do
  gem "jekyll-theme-chirpy", "~> 7.6"
  # Chirpy theme dependencies (not installed by jekyll-remote-theme)
  gem "jekyll-paginate"
  gem "jekyll-redirect-from"
  gem "jekyll-seo-tag"
  gem "jekyll-archives"
  gem "jekyll-sitemap"
  gem "jekyll-include-cache"
  gem "jekyll-feed"
end

# No longer default gems since Ruby 4.0
gem "logger"

gem "html-proofer", "~> 5.2.2", group: :test

platforms :windows, :jruby do
  gem "tzinfo", ">= 1", "< 3"
  gem "tzinfo-data"
end

gem "wdm", "~> 0.2.0", :platforms => [:windows]
