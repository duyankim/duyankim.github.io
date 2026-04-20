source "https://rubygems.org"

gem "jekyll-theme-chirpy", "~> 7.1"

# Chirpy 기본 설치 그룹
group :test do
  gem "html-proofer", "~> 5.0"
end

# Windows / JRuby
platforms :mingw, :x64_mingw, :mswin, :jruby do
  gem "tzinfo", ">= 1", "< 3"
  gem "tzinfo-data"
end

gem "wdm", "~> 0.1.1", :platforms => [:mingw, :x64_mingw, :mswin]

gem "http_parser.rb", "~> 0.6.0", :platforms => [:jruby]
