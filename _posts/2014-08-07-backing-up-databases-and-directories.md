---
layout: post
title: Backing up Databases and Directories
description: "Keep it backed-up, Keep it safe, Keep it anywhere you like"
headline: "Need Backup"
categories: programming
tags: 
  - backup
  - database
imagefeature: "website-speed.jpg"
imagecredit: spreadeffect.com
imagecreditlink: "http://www.spreadeffect.com/blog/improve-website-speed/"
comments: false
mathjax: null
featured: true
published: true
---

Backup your Database OR any of your personal stuff like directories with "backup":https://github.com/meskyanichi/backup

*Backup's Components*

  * Archives and Databases
  * Compressors and Encryptors
  * Storages
  * Syncers
  * Notifiers

Its a handy backup tool with plenty of storage options (for example: S3, Dropbox, FTP, SCP and many more)


For installation(Dependencies should be installed if you are working with shell environment)

bc.. gem install backup

backup dependencies --install net-ssh

backup dependencies --install net-scp

backup dependencies --install excon

backup dependencies --install fog

bq. Now execute the @backup generate@ command

bc. backup generate:model -t backup [-c <path_to_configuration_file>] --archives \
 --storages='local,s3' --compressors='gzip' --notifiers='mail,twitter'

This will create two files a configuration file @~/Backup/config.rb@ and Backup model @~/Backup/models/backup.rb@

Copy the @backup.rb@ to you @/config@ directory (if path not specified while generation)

"a sample backup model":https://github.com/meskyanichi/backup/wiki/Getting-Started#generating-your-first-backup-model

More complex generators can also be used per your requirement.

To use s3 as your backup location you need some extra configurations to load the S3 credentials and database.yml settings(in case used)

bc.. Backup::Model.new(:backup, 'BackUp To S3') do

  split_into_chunks_of 250

  database_yml = File.expand_path('../database.yml',  __FILE__)
  s3_yml    = File.expand_path('../amazon_s3.yml',  __FILE__)
  RAILS_ENV = ENV['RAILS_ENV'] || 'development'

  require 'yaml'
  config = YAML.load_file(database_yml)

  storage = YAML.load_file(s3_yml)[RAILS_ENV]

  # PostgreSQL [Database]

  database PostgreSQL do |db|
  db.name             = config[RAILS_ENV]["database"]
  db.username         = config[RAILS_ENV]["username"]
  db.password         = config[RAILS_ENV]["password"]
  db.host             = config[RAILS_ENV]["host"]
  db.skip_tables      = []
  end

  store_with S3 do |s3|
  s3.access_key_id  = storage['connection'][:access_key_id]
  s3.secret_access_key = storage['connection'][:secret_access_key]
  s3.region         = "ap-southeast-1"
  s3.bucket         = storage['bucket']
  s3.path           = "/daily-backup"
  s3.keep           = 50
  end

  # Gzip [Compressor]

  compress_with Gzip

  # Mail [Notifier]

  notify_by Mail do |mail|
  mail.on_success         = true
  mail.on_warning         = true
  mail.on_failure         = true

  mail.from               = "from@example.com"
  mail.to                 = "recepient@example.com"
  mail.address            = "<smtp_settings>"
  mail.port               = 587
  mail.domain             = "example.com"
  mail.user_name          = "<user_name>"
  mail.password           = "<password>"
  mail.authentication     = "plain"
  mail.encryption = true
  end

end

bq. Now to run the backup execute following command on rails root

bc. backup perform --trigger backup --config_file config/backup.rb \
 --data-path db --log-path log --tmp-path tmp

How about scheduling the backup command ?
The backup should be triggered automatically and needs to be scheduled

Use "whenever":https://github.com/javan/whenever OR "rufus-scheduler":/rufus-scheduler

With whenever:

bc.. every 1.day, at: Time.zone.parse('01:00') do
  command "cd #{path} && RAILS_ENV=#{environment} backup perform --trigger backup \
 --config_file config/backup.rb --data-path db --log-path log --tmp-path tmp"
end

bq. A rake task would be awesome here, "backup-task":https://github.com/edebill/backup-task
This is not up-to-date but can be easily customized.

So you can just do

bc.. every 1.day, at: Time.zone.parse('01:00') do
  rake 'db:backup'
end

bq. This would be good to go for scheduled backup.

PS Note: Restore is missing worth to look at "backup2s3":https://github.com/aricwalker/backup2s3
Recommended "s3db-backup":https://github.com/mmarschall/s3db-backup but has limitation on mysql can't be used with PG