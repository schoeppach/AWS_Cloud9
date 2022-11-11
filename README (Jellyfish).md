
# Jellyfish install

## Terminal Befehl

	sudo docker run -d \
	--name jellyfin \
	--restart=unless-stopped \
	-p 8096:8096 \
	-v $HOME/docker/jellyfin/config:/config \
	-v /media:/media \
	-e UID=1000 -e GID=100 \
	jellyfin/jellyfin:latest
	
## Website aufrufen	
	
	http:localhost:8096
