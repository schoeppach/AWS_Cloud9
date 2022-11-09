
# Docker auf ArchoLinux installieren, Docker erstellen

## Vorraussetzung ist eine Maschiene mit ssh Verbindung


### Docker installieren, Version checken, starten

	sudo yum install docker -y (sudo pacman -S docker)

	docker --version

	sudo systemctl enable docker

	sudo systemctl start docker

	sudo systemctl status docker
	
### Wordpress installieren

	ip addr
	
	sudo docker run hello-world
	
	sudo ufw status
	
	sudo docker run --name schoeppach-wordpress -p 8080:80 -d wordpress
	
	sudo docker ps
	
	http://localhost:8080

### Netdata installieren
	
	sudo docker ps -a
	
	sudo nano /etc/ssh/sshd_config (# vor Port 22 entfernen speichern)
	
	sudo systemctl restart sshd
	
	touch netdata.txt
	
	nano netdata.txt
	
	sudo docker ps
	
		docker run -d --name=netdata \
		-p 19999:19999 \
		-v /etc/passwd:/host/etc/passwd:ro \
		-v /etc/group:/host/etc/group:ro \
		-v /proc:/host/proc:ro \
		-v /sys:/host/sys:ro \
		-v /var/run/docker.sock:/var/run/docker.sock:ro \
		--cap-add SYS_PTRACE \
		--security-opt apparmor=unconfined \
		netdata/netdata
		
### Netdata installieren im Terminal eingeben und durchlaufen lassen	

		sudo docker run -d --name=netdata \
		-p 19999:19999 \
		-v /etc/passwd:/host/etc/passwd:ro \
		-v /etc/group:/host/etc/group:ro \
		-v /proc:/host/proc:ro \
		-v /sys:/host/sys:ro \
		-v /var/run/docker.sock:/var/run/docker.sock:ro \
		--cap-add SYS_PTRACE \
		--security-opt apparmor=unconfined \
		netdata/netdata
		
	sudo docker ps
	http://localhost 19999

### Docker stoppen starten mit Docker ID

	sudo docker stop efwdsk326z48
	sudo docker start efwdsk326z48

### Docker File standort anzeigem

	sudo systemctl docker.service
	which docker

