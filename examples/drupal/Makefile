ifndef $(APP_NAME)
	APP_NAME = drupal
endif
ifndef $(APP_ENV)
	APP_ENV = local
endif
ifndef $(MYSQL_ENDPOINT)
	MYSQL_ENDPOINT = mysql
endif
ifndef $(MYSQL_USERNAME)
	MYSQL_USERNAME = root
endif
ifndef $(MYSQL_PASSWORD)
	MYSQL_PASSWORD = password
endif
ifndef $(MYSQL_DATABASE)
	MYSQL_DATABASE = $(APP_ENV)_$(APP_NAME)
endif

APP_VARS = APP_NAME APP_ENV MYSQL_ENDPOINT MYSQL_USERNAME MYSQL_PASSWORD MYSQL_DATABASE

DOCKER_ENV_VARS = $(foreach name,$(APP_VARS), -e $(name)=$($(name)))
DOCKER_MNT_OPTS = -v $(PWD):/var/www/html

DOCKER_PORT_OPTS = -p 8000:80
DOCKER_LINK_OPTS = --link $(APP_NAME)_mysql:mysql

DOCKER_RUN_OPTS = --name $(APP_NAME) -d $(DOCKER_ENV_VARS) $(DOCKER_PORT_OPTS) $(DOCKER_MNT_OPTS) $(DOCKER_LINK_OPTS)

APACHE_ENV_VARS = $(foreach name,$(APP_VARS),$(name)=$($(name)))
local: clean
	docker run --name $(APP_NAME)_mysql -e MYSQL_ROOT_PASSWORD=$(MYSQL_PASSWORD) -d mysql
	docker ps
	docker build -t $(APP_NAME) .
	docker run $(DOCKER_RUN_OPTS) $(APP_NAME)
	@docker ps
	@docker logs -f $(APP_NAME)

clean:
	-@docker ps | grep $(APP_NAME) | awk '{ print $$1 }' | xargs docker kill > /dev/null
	-@docker ps -a | grep $(APP_NAME) | awk '{ print $$1 }' | xargs docker rm > /dev/null

clean-all:
	-@docker images | grep $(APP_NAME) | awk '{ print $$3 }' | xargs docker rmi


configure:
	@sed -i 's|/var/www/html|/var/www/html/docroot|' /etc/apache2/sites-available/000-default.conf
	@for var in $(APACHE_ENV_VARS); do \
		echo export $$var >> /etc/apache2/envvars; \
	done
	@cp config/php.ini /etc/php5/

install:
	@mysql -u$(MYSQL_USERNAME) -h$(MYSQL_ENDPOINT) -p$(MYSQL_PASSWORD) \
		-e "CREATE DATABASE IF NOT EXISTS $(MYSQL_DATABASE);"
	@cat $(PWD)/config/base.sql | mysql -u$(MYSQL_USERNAME) -h$(MYSQL_ENDPOINT) -p$(MYSQL_PASSWORD) $(MYSQL_DATABASE)

drupal: configure
	service apache2 restart
	nohup tail -f /var/log/apache2/error.log > /dev/stderr
	tail -f /var/log/nginx/access.log

deploy:
	curl -X POST -H "Content-Type: application/json" http://ec2-54-152-80-238.compute-1.amazonaws.com:8080/v2/apps -d@app.json
