load 'deploy' if respond_to?(:namespace) # cap2 differentiator
 
# Standard configuration
set :user, "adrianrf"
set :password, "password"
set :application, "Hello-World"
 
# I like to deploy the code in /var/apps
# and then link it to the webserver directory
set :deploy_to, "/var/apps/#{application}"
 
# SCM Stuff configure to taste, just remember the repository
# here I used github as main repository
set :repository,  "git@github.com:adrianrf/Hello-World.git"
set :scm, :git
set :branch, "master"
set :repository_cache, "git_master"
set :deploy_via, :remote_cache
set :scm_verbose,  true
 
# Two servers, double fun
# You really don't need app, web and db here,
# but I used all of them just to be sure.
# Usually only web is ok.
role :app, "server8.websitehostserver.net", :primary => true
role :web, "server8.websitehostserver.net", :primary => true
role :db, "server8.websitehostserver.net", :primary => true
 
after 'deploy:setup', 'drupal:setup' # Here we setup the shared files directory
after 'deploy:symlink', 'drupal:symlink' # After symlinking the code we symlink the shared dirs
 
# Before restarting the webserver we fix all the 
# permissions and then symlink it to production
before 'deploy:restart', 'mikamai:permissions:fix', 'mikamai:symlink:application'
 
 
namespace :drupal do
  # shared directories
  task :setup, :except => { :no_release => true } do
    sudo "mkdir -p #{shared_path}/files"
    sudo "chown -R #{user}:#{user} #{deploy_to}"
  end
 
  # symlink shared directories
  task :symlink, :except => { :no_release => true } do
    sudo "ln -s #{shared_path}/files #{latest_release}"
  end
end
 
namespace :deploy do
  # adjusted finalize_update, removed non rails stuff
  task :finalize_update, :except => { :no_release => true } do
    sudo "chmod -R g+w #{latest_release}" if fetch(:group_writable, true)
  end
 
  task :restart do
    # nothing to do here since we're on mod-php
  end
end
 
namespace :mikamai do
  # symlinking to production
  namespace :symlink do
    task :application, :except => { :no_release => true } do
      sudo "rm -rf /var/www/montalbano"
      sudo "ln -s #{latest_release} /var/www/montalbano"
    end
  end
 
  # change ownership
  namespace :permissions do
    task :fix, :except => { :no_release => true } do
      sudo "chown -R www-data:www-data #{latest_release}"
    end
  end
 
end