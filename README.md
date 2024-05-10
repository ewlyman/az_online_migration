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

	⁃	lando drush ms --group=azo_migration
