require 'bundler/setup'

begin
  require 'rspec/core/rake_task'

  APP_RAKEFILE = File.expand_path("../spec/manageiq/Rakefile", __FILE__)

  require 'active_record'
  if ActiveRecord::Base.methods.include?(:walk_configs)
    puts;puts;puts;puts "WE INCLUDED!?!?!?";puts;puts;puts
    ActiveRecord::Base # autoload (hopefully)

    puts
    puts "Before:"
    begin
      ActiveRecord::Base.walk_configs(ENV["RAILS_ENV"], "before", "url" => "postgresql:///not_existing_db?host=/var/lib/postgresql")
    rescue => e
      puts e.message
    end
    puts

    module ActiveRecord
      class Base
        def self.walk_configs(env_name, spec_name, config)
          puts "  .walk_configs(#{env_name.inspect}, #{spec_name.inspect}, #{config.inspect})"
          if config["database"] || config["url"] || config["adapter"]
            DatabaseConfig.new(env_name, spec_name, config)
          else
            config.each_pair.map do |sub_spec_name, sub_config|
              walk_configs(env_name, sub_spec_name, sub_config)
            end
          end
        end
      end
    end

    puts
    puts "After:"
    ActiveRecord::Base.walk_configs(ENV["RAILS_ENV"], "before", "url" => "postgresql:///not_existing_db?host=/var/lib/postgresql")
    puts

  end

  load 'rails/tasks/engine.rake'
  load 'rails/tasks/statistics.rake'
rescue LoadError
end

require 'bundler/gem_tasks'

FileList['lib/tasks_private/**/*.rake'].each { |r| load r }

task :default => :spec
