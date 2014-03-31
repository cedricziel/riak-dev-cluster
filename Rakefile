RIAK_VERSION      = "2.0.0pre20"
RIAK_DOWNLOAD_URL = "http://s3.amazonaws.com/downloads.basho.com/riak/#{RIAK_VERSION[0..2]}/#{RIAK_VERSION}/riak-#{RIAK_VERSION}.tar.gz"
RIAKNOSTIC_DOWNLOAD_URL = "https://github.com/downloads/basho/riaknostic/riaknostic-LATEST.tar.gz"
NUM_NODES = 5

task :default => :help

task :help do
  sh %{rake -T}
end

desc "install, start and join riak nodes"
task :bootstrap => [:install, :start, :join]

desc "start all riak nodes"
task :start do
  (1..NUM_NODES).each do |n|
    sh %{ulimit -n 4096; ./riak#{n}/bin/riak start}
  end
  puts "======================================="
  puts "Riak Dev Cluster started"
  puts
  puts "HTTP API: http://127.0.0.1:11098"
  puts
  puts "Admin UI: http://127.0.0.1:11098/admin"
  puts "======================================="
end

desc "stop all riak nodes"
task :stop do
  (1..NUM_NODES).each do |n|
    sh %{ulimit -n 4096; ./riak#{n}/bin/riak stop} rescue "not running"
  end
end

desc "restart all riak nodes"
task :restart => [:stop, :start]

desc "join riak nodes (only needed once)"
task :join do
  (2..NUM_NODES).each do |n|
    sh %{./riak#{n}/bin/riak-admin join -f riak1@127.0.0.1} rescue "already joined"
  end
end

desc "clear data from all riak nodes"
task :clear => :stop do
  (1..NUM_NODES).each do |n|
    sh %{rm -rf riak#{n}}
    sh %{git checkout riak#{n}}
  end
end

desc "install riak"
task :install => [:fetch_riak, :copy_riak]

desc "install riaknostic"
task :install_riaknostic => [:fetch_riaknostic, :copy_riaknostic]

desc "ping all riak nodes"
task :ping do
  (1..NUM_NODES).each do |n|
    sh %{riak#{n}/bin/riak ping}
  end
end

task :fetch_riak do
  sh "curl -L #{RIAK_DOWNLOAD_URL} | tar xz" unless File.exist? "riak-#{RIAK_VERSION}"
  sh "cd riak-#{RIAK_VERSION} && make rel"
end

task :fetch_riaknostic do
  sh "curl -L -k #{RIAKNOSTIC_DOWNLOAD_URL} > riaknostic-LATEST.tar.gz"
  sh "tar xf riaknostic-LATEST.tar.gz" unless File.exist? "riaknostic"
end

task :copy_riak do
  (1..NUM_NODES).each do |n|
    system %{cp -nr riak-#{RIAK_VERSION}/rel/riak/* riak#{n}}
  end
end

task :copy_riaknostic do
  (1..NUM_NODES).each do |n|
    system %{cp -nr riaknostic riak#{n}/lib/}
  end
end
