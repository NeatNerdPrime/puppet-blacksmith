require 'rake'
require 'rake/clean'
require 'rubygems'
require 'bundler/gem_tasks'
require 'rspec/core/rake_task'
require 'cucumber'
require 'cucumber/rake/task'
require 'fileutils'

CLEAN.include("pkg/", "tmp/")
CLOBBER.include("Gemfile.lock")

$LOAD_PATH.unshift(File.expand_path('../lib', __FILE__))
require 'puppet_blacksmith/version'

task :default => [:clean, :spec, :cucumber, :build]

RSpec::Core::RakeTask.new(:spec) do |t|
  t.rspec_opts = "--tag ~live"
end

Cucumber::Rake::Task.new(:cucumber) do |t|
end

task :bump do
  v = Gem::Version.new("#{Blacksmith::VERSION}.0")
  fail("Unable to increase prerelease version #{Blacksmith::VERSION}") if v.prerelease?
  s = <<-EOS
module Blacksmith
  VERSION = #{v.bump.to_s}
end
EOS

  File.open("lib/puppet_blacksmith/version.rb", "w") do |file|
    file.print s
  end
  sh "git add version"
  sh "git commit -m 'Bump version'"
end

begin
  require 'rubygems'
  require 'github_changelog_generator/task'
rescue LoadError
else
  GitHubChangelogGenerator::RakeTask.new :changelog do |config|
    config.exclude_labels = %w{duplicate question invalid wontfix wont-fix skip-changelog}
    config.user = 'voxpupuli'
    config.project = 'puppet-blacksmith'
    gem_version = Gem::Specification.load("#{config.project}.gemspec").version
    config.future_release = gem_version
  end
end
