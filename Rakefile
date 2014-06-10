require 'rake'
require 'bundler/gem_tasks'

desc 'Default: Run all specs.'
task :default => 'all:spec'


namespace :travis do
  
  desc 'Run tests on Travis CI'
  task :run => [:compatible_rubygems, 'all:bundle:install', 'all:spec']

  desc 'Ensure compatible Rubygems version for Ruby 1.8'
  task :compatible_rubygems do
    if RUBY_VERSION == '1.8.7'
      system "rvm rubygems latest-1.8 --force"
    end
  end

end

namespace :all do

  desc "Run specs on all spec apps"
  task :spec do
    success = true
    for_each_directory_of('spec/**/Rakefile') do |directory|
      env = "SPEC=../../#{ENV['SPEC']} " if ENV['SPEC']
      success &= system("cd #{directory} && #{env} bundle exec rake spec")
    end
    fail "Tests failed" unless success
  end


  task :bundle => 'bundle:install'

  namespace :bundle do

    desc "Bundle all spec apps"
    task :install do
      for_each_directory_of('spec/**/Gemfile') do |directory|
        Bundler.with_clean_env do
          system("cd #{directory} && bundle install --without development")
        end
      end
    end

    desc "Update all gems, or a list of gem given by the GEM environment variable"
    task :update do
      for_each_directory_of('spec/**/Gemfile') do |directory|
        Bundler.with_clean_env do
          system("cd #{directory} && bundle update #{ENV['GEM']}")
        end
      end
    end

  end

end

def for_each_directory_of(path, &block)
  Dir[path].sort.each do |rakefile|
    directory = File.dirname(rakefile)
    puts '', "\033[4;34m# #{directory}\033[0m" # blue underline

    if directory.include?('rails-2.3') and RUBY_VERSION != '1.8.7'
      puts 'Skipping - Rails 2.3 requires Ruby 1.8.7'
    elsif directory.include?('rails-4.1') and RUBY_VERSION == '1.8.7'
      puts 'Skipping - Rails 4.1 does not support Ruby 1.8'
    else
      block.call(directory)
    end
  end
end
