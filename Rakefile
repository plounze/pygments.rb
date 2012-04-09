task :default => :test

# ==========================================================
# Packaging
# ==========================================================

GEMSPEC = eval(File.read('pygments.rb.gemspec'))

require 'rake/gempackagetask'
Rake::GemPackageTask.new(GEMSPEC) do |pkg|
end

# ==========================================================
# Ruby Extension
# ==========================================================

require 'rake/extensiontask'
Rake::ExtensionTask.new('pygments_ext', GEMSPEC) do |ext|
  ext.ext_dir = 'ext'
end
task :build => :compile

# ==========================================================
# Testing
# ==========================================================

require 'rake/testtask'
Rake::TestTask.new 'test' do |t|
  t.test_files = FileList['test/test_*.rb']
  t.ruby_opts = ['-rubygems']
end
task :test => :build

# ==========================================================
# Vendor
# ==========================================================

namespace :vendor do
  file 'vendor/pygments-main' do |f|
    sh "hg clone https://bitbucket.org/birkenfeld/pygments-main #{f.name}"
    sh "hg --repository #{f.name} identify --id > #{f.name}/REVISION"
    rm_rf Dir["#{f.name}/.hg*"]
  end

  task :clobber do
    rm_rf 'vendor/pygments-main'
  end

  # Load all the custom lexers in the `vendor/custom_lexers` folder
  # and stick them in our custom Pygments vendor
  task :load_lexers do
    LEXERS_DIR = 'vendor/pygments-main/pygments/lexers'
    lexers = FileList['vendor/custom_lexers/*.py']
    lexers.each { |l| FileUtils.copy l, LEXERS_DIR }
    FileUtils.cd(LEXERS_DIR) { sh "python _mapping.py" }
  end

  task :update => [:clobber, 'vendor/pygments-main', :load_lexers]
end
