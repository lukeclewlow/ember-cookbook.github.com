# This file is taken from
# https://github.com/jcrawford/jcrawford.github.com/blob/dev/Rakefile
#
# Big thanks for helping me make this work with Github Pages


# prompt user for a commit message; default: HEAD commit 1-liner
def commit_message
  last_commit = `git log -1 --pretty=format:"%s"`.chomp.strip
  last_commit = 'Publishing developer content to GitHub pages.' if last_commit == ''

  print "Enter a commit message (default: '#{last_commit}'): "
  STDOUT.flush
  mesg = STDIN.gets.chomp.strip

  mesg = last_commit if mesg == ''
  mesg.gsub(/'/, '') # to allow this to be handed off via -m '#{message}'
end

desc "Deploy to http://ember-cookbook.github.com"
task :deploy do
  mesg = commit_message

  FileUtils.rm_r('_site') if File.exist?('_site')

  sh "jekyll"

  ENV['GIT_DIR'] = File.expand_path(`git rev-parse --git-dir`.chomp)
  old_sha = `git rev-parse refs/remotes/origin/master`.chomp
  Dir.chdir('_site') do
    ENV['GIT_INDEX_FILE'] = gif = '/tmp/dev.gh.i'
    ENV['GIT_WORK_TREE'] = Dir.pwd
    File.unlink(gif) if File.file?(gif)
    `git add -A`
    tsha = `git write-tree`.strip
    puts "Created tree   #{tsha}"
    if old_sha.size == 40
      csha = `git commit-tree #{tsha} -p #{old_sha} -m '#{mesg}'`.strip
    else
      csha = `git commit-tree #{tsha} -m '#{mesg}'`.strip
    end
    puts "Created commit #{csha}"
    puts `git show #{csha} --stat`
    puts "Updating master from #{old_sha}"
    `git update-ref refs/heads/master #{csha}`
    `git push origin master`
  end
end

