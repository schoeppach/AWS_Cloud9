# Docker_Portainer (Wordpress)

### Vorraussetzung für Docker Portainer
  
### Linux (ArchoLinux) mit Docker Installation

### Portainer Ordner

	mkdir portainer

	cd portainer
	
	------------------------------------------------------------------

		nano docker-compose.yaml

			version: "3.3"
			services:
				portainer:
					image: portainer/portainer-ce:latest
					container_name: portainer
					restart: always
					privileged: true
					volumes:
						- ./data:/data:Z
						- /var/run/docker.sock:/var/run/docker.sock:Z
					ports:
						- 9443:9443

			sudo docker-compose up -d

			sudo docker-compose down --remove-orphans

			sudo docker-compose up -de
			
	-------------------------------------------------------------------		

### Wordpress Ordner

	mkdir wordpress

	cd wordpress

	nano docker-compose.yaml

--------------------------------------------------------------------------

### nano docker-compose.yaml Datei Inhalt
	 
		version: '2.3'
		services:
		  nginx-proxy:
			image: jwilder/nginx-proxy
			restart: always
			volumes:
			  - ./nginx-proxy/nginx-proxy-certs:/etc/nginx/certs:ro
			  - ./nginx-proxy/nginx-proxy-vhost:/etc/nginx/vhost.d
			  - /usr/share/nginx/html
			  - /var/run/docker.sock:/tmp/docker.sock:ro
			ports:
			  - 80:80
			  - 443:443
			labels:
			  - "com.github.jrcs.letsencrypt_nginx_proxy_companion.nginx_proxy"
			networks:
			  wp-network:
		  nginx-letsencrypt:
			image: jrcs/letsencrypt-nginx-proxy-companion
			restart: always
			volumes:
			  - ./nginx-proxy/nginx-proxy-certs:/etc/nginx/certs:rw
			  - /var/run/docker.sock:/var/run/docker.sock:ro
			volumes_from:
			  - nginx-proxy
			networks:
			  wp-network:
		  wpdb:
			image: mariadb:10.5.4
			restart: always
			environment:
			  - MYSQL_ROOT_PASSWORD=db_passwd                     # change this
			  - MYSQL_DATABASE=wordpress                          # change this
			  - MYSQL_USER=wpuser                                 # change this
			  - MYSQL_PASSWORD=wp_passwd                          # change this
			volumes:
			  - ./wordpress-db/wpdb-data:/var/lib/mysql
			networks:
			  wp-network:
		  wp:
			depends_on:
			  - wpdb
			image: wordpress:latest
			volumes:
			  - ./wordpress/wp-data:/var/www/html/wordpress
			ports:
			  - "8080:80"
			restart: always
			environment:
			  - WORDPRESS_DB_HOST=wpdb:3306
			  - WORDPRESS_DB_USER=wpuser                          # change this
			  - WORDPRESS_DB_PASSWORD=wp_passwd                   # change this
			  - WORDPRESS_DB_NAME=wordpress                       # change this
			  - VIRTUAL_HOST=www.example.com                      # change this
			  - VIRTUAL_PORT=80
			  - VIRTUAL_PROTO=http
			  - LETSENCRYPT_HOST=10.50.103.24                  # change this
			  - LETSENCRYPT_EMAIL=your-mail@example.com           # change this
			  - RESOLVE_TO_PROXY_IP=true
			networks:
			  wp-network:
		networks:
		  wp-network:
			driver: bridge
		volumes:
		  nginx-proxy-certs:
		  nginx-proxy-vhost:
		  nginx-proxy-certs:
		  wpdb-data:
		  wp-data:
		 
----------------------------------------------------------------------------------
 
### Dateiinhalt prüfen

	nano docker-compose.yaml

	ip addr 10.50.103.24
	
	einfügen LETSENCRYPT_HOST=10.50.103.24 
	
	
### Wordpress stoppen und Docker-Compose starten

	sudo docker ps (ID kopieren)
	
	sudo docker stop 433c5289cc71
	
	sudo docker-compose up -d
	
	http://10.50.103.24:8080/wp-admin/install.php
	




