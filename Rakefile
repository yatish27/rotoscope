# frozen_string_literal: true
# ==========================================================
# Packaging
# ==========================================================
GEMSPEC = Gem::Specification.load('rotoscope.gemspec')

require 'bundler/gem_tasks'
require 'rubygems/package_task'
require 'English'

Gem::PackageTask.new(GEMSPEC) do |pkg|
end

# ==========================================================
# Ruby Extension
# ==========================================================

require 'rake/extensiontask'
Rake::ExtensionTask.new('rotoscope', GEMSPEC) do |ext|
  ext.lib_dir = 'lib/rotoscope'
end

task build: :compile

task install: [:build] do |_t|
  sh "gem build rotoscope.gemspec && gem install rotoscope-*.gem"
end

# ==========================================================
# Testing
# ==========================================================

require 'rake/testtask'
Rake::TestTask.new 'test' do |t|
  t.test_files = FileList['test/*_test.rb']
end
task test: :build

task :rubocop do
  require 'rubocop/rake_task'
  RuboCop::RakeTask.new
end

namespace :lint do
  task ruby: :rubocop

  task :c do
    diff_output = ''.dup
    Dir['ext/rotoscope/*.[ch]'].each do |filename|
      diff_output << `bash -c 'diff -u #{filename.inspect} <(clang-format -style=file #{filename.inspect})'`
      abort "clang-format diff failed" if $CHILD_STATUS.exitstatus > 1
    end
    unless diff_output.empty?
      STDERR.puts "Using clang-format version: #{`clang-format --version`}"
      STDERR.puts "C format changes are needed:"
      puts '```diff'
      STDERR.puts diff_output
      puts '```'
      abort "Please run bin/fmt to make these format changes"
    end
  end
end
task lint: ['lint:ruby', 'lint:c']

task default: [:test, :lint]
