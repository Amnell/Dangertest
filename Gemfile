source 'https://rubygems.org'

# The bare minimum for building, e.g. in Homebrew
group :build do
    gem 'rake', '~> 13.0'
    gem 'xcpretty'
    gem 'fastlane'

    plugins_path = File.join(File.dirname(__FILE__), 'fastlane', 'Pluginfile')
    eval_gemfile(plugins_path) if File.exist?(plugins_path)
end

# In addition to :build, for contributing
group :development do
    gem 'danger'
    gem 'danger-changelog', '~> 0.6.0'
end

