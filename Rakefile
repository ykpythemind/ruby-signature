require "bundler/gem_tasks"
require "rake/testtask"

Rake::TestTask.new(:test) do |t|
  t.libs << "test"
  t.libs << "lib"
  t.test_files = FileList["test/**/*_test.rb"].reject do |path|
    path =~ %r{test/stdlib/}
  end
end

task :default => [:test, :stdlib_test]

task :stdlib_test do
  sh "ruby bin/test_runner.rb"
end

rule ".rb" => ".y" do |t|
  sh "racc -v -o #{t.name} #{t.source}"
end

task :parser => "lib/ruby/signature/parser.rb"
task :test => :parser
task :stdlib_test => :parser
task :build => :parser

namespace :generate do
  task :stdlib_test, [:class] do |_task, args|
    klass = args.fetch(:class) do
      raise "Class name is necessary. e.g. rake 'generate:stdlib_test[String]'"
    end

    path = Pathname("test/stdlib/#{klass}_test.rb")
    raise "#{path} already exists!" if path.exist?

    path.write <<~RUBY
      class #{klass}Test < StdlibTest
        target #{klass}
        using hook.refinement

        # def test_method_name
        #   # Call the method
        #   method_name(arg)
        #   method_name(arg, arg2)
        # end
      end
    RUBY

    puts "Created: #{path}"
  end
end

CLEAN.include("lib/ruby/signature/parser.rb")
