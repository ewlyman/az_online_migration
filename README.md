# az_online_migration
Provides migrations from the custom built Arizona Online marketing site to Quickstart 2

Abbreviated setup instructions

	⁃	terminus env:wake online-arizona-edu.live 

	⁃	lando drush cset az_migration.settings allow_archived_paragraphs true

	⁃	terminus backup:create online-arizona-edu.live --element=db

	⁃	terminus backup:get online-arizona-edu.live --element=db | xargs wget -O database.sql.gz

	⁃	lando mysql -h database -uroot -e "CREATE DATABASE IF NOT EXISTS migrate; GRANT ALL PRIVILEGES ON migrate.* TO 'drupal10'@'%' IDENTIFIED by 'drupal10';"

	⁃	lando ssh -u root

	⁃	printf "\$databases['migrate']['default'] = [\n  'driver' => 'mysql',\n  'namespace' => 'Drupal\Core\Database\Driver\mysql',\n  'database' => 'migrate',\n  'username' => 'drupal10',\n  'password' => 'drupal10',\n  'port' => '3306',\n  'host' => 'database',\n  'prefix' => '',\n];" >> /app/web/sites/default/settings.php

	⁃	exit

	⁃	gunzip < database.sql.gz | lando mysql -u drupal10 -pdrupal10 migrate

	-   lando drush en az_online_migration

	⁃	lando drush ms --group=azo_migration

	Migration process (includes extra steps/feeds imports)

	- lando drush mim azo_user
	(migration will fail for currently-existing users)

    - download and unzip copy of latest files backup from https://dashboard.pantheon.io/sites/9b45d0c3-3903-4029-a4fc-8a81d7c56050#live/backups/backup-log

	- rsync unzipped files backup folder with /web/sites/default/files

	- lando drush mim azo_files

	- lando drush mim azo_media_remote_video

	- lando drush mim azo_media_image
	(migration will fail for currently-existing images)

	- lando drush mim azo_taxonomy_career_insights_skills,azo_taxonomy_careerlevel,azo_taxonomy_college,azo_taxonomy_degree_interests,azo_taxonomy_degree_tags,azo_taxonomy_degree

	- lando drush mim azo_taxonomy_news_categories,azo_taxonomy_tags

	- lando drush mim azo_paragraph_course

	- lando drush mim azo_node_degree_program

	- lando drush mim azo_node_news

	(Need to add additional paragraph migrations for Degree/Program - some of the entity/entity-revision references are populated by feeds.)


