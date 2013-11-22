#
# Setup
#

# load 'tasks/redis.rake'
#require 'rake/testtask'

$LOAD_PATH.unshift 'lib'
#require 'resque/tasks'

def command?(command)
  system("type #{command} > /dev/null 2>&1")
end

require 'rake'
require 'bundler/gem_tasks'
require 'rspec/core/rake_task'

desc "Tag #{Bundler::GemHelper.new.send(:version_tag)}, build and push to gemfury"
task :release_internal do |t|
  require 'gemfury'

  class ReleaseInternalGem < Bundler::GemHelper
    def release_gem
      guard_clean
      built_gem_path = build_gem
      if Bundler::VERSION =~ /1\.3\.\d/
        tag_version { git_push } unless already_tagged?
      else
        guard_already_tagged
        tag_version { git_push }
      end
      `fury push #{built_gem_path}`
      Bundler.ui.confirm "Pushed #{name} #{version} to gemfury"
    end
  end

  ReleaseInternalGem.new.release_gem
end

#
# Tests
#

task :default => :test

desc "Run the test suite"
task :test do
  rg = command?(:rg)
  Dir['test/**/*_test.rb'].each do |f|
    rg ? sh("rg #{f}") : ruby(f)
  end
end

if command? :kicker
  desc "Launch Kicker (like autotest)"
  task :kicker do
    puts "Kicking... (ctrl+c to cancel)"
    exec "kicker -e rake test lib examples"
  end
end
