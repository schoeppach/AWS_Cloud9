# Apache Webserver auf einer EC2 in AWS installieren

--------------------------------------------------------------------------

## Usefull Links

#### Anleitung online
	
(https://towardsaws.com/how-to-use-aws-cli-to-launch-an-ec2-web-server-with-apache-9c20d07e07be)

#### in AWS Anmelden 
	
[Link](https://us-east-1.console.aws.amazon.com/iamv2/home?region=eu-north-1#/home)

#### in AWS Anmelden 
-----------------------------------------------------------------

## Vorbereitungen mit Cloud9 verbinden und ein Projekt erstellen

#### 1. Projektverzeichnis erstellen
	
	mkdir AWS_CLI

#### 2. Textdatei für Ressourcen
	
	toch ressources.txt

#### 3. Sicherheitsupdates installieren
	
	sudo yum update -y

#### 4. Überprüfung der installierten Version
	
	aws --version

#### 5. AWS CLI konfigurieren
	
	aws configure

#### 6. Liste der konfigurierten Dateien	
  	
	aws configure list
	
#### 7. AMI Nummer suchen mit der die EC2 Instance gebaut wurde
	
	aws ec2 describe-images --owners amazon --filters "Name=name, Values=amzn2-ami-hvm-2.0.????????.?
    	-x86_64-gp2" "Name=state, Values=available" --query "reverse(sort_by(Images, &Name))
	[:1].ImageId" --output text
	
#### 8. den Instance Typ ermitteln
	
	Now go to the AWS console and search for EC2. To the left of the screen, 
  	from Instances/Instance Types — choose t2.mirco as this is the free tier.
		
#### 9. Key Pair erstellen
	
	aws ec2 create-key-pair --key-name IHR-KEY --query 'KeyMaterial' --output text > IHR-KEY.pem

#### 10. Key Pair anzeigen
	
	aws ec2 describe-key-pairs --key-name IHR-KEY

#### 11. Security Group erstellen
  	
	aws ec2 create-security-group --group-name IHRE-SECURE-GROUP --description "Allows SSH and HTTP connections for the Web Server"

#### 12. Regeln für den Netzwerkverkehr für TCP port 22, TCP port 80 und TCP port 433
	
	aws ec2 authorize-security-group-ingress --group-id sg-08cd0d53e3574cc4a --protocol tcp --port 22 --cidr 0.0.0.0/0

	aws ec2 authorize-security-group-ingress --group-id sg-08cd0d53e3574cc4a --protocol tcp --port 80 --cidr 0.0.0.0/0
	
	aws ec2 authorize-security-group-ingress --group-id sg-08cd0d53e3574cc4a --protocol tcp --port 433 --cidr 0.0.0.0/0

#### 13. Regeln der Firewall überprüfen
	
	aws ec2 describe-security-groups --group-ids sg-08cd0d53e3574cc4a

----------------------------------------------------------------------

##  Erstellen einer EC2 Instance und Apache mitthilfe des Scripts

#### 14. Apache script ertsellen mit nano — webscript.sh in C://Users/ <your user name> inhalt eifügen und speichern
    
	#!/bin/bash
	yum update -y
	yum install httpd -y
	systemctl start httpd
	systemctl enable httpd

#### 15. dieses Script erstellt die EC2 Instance
	
	aws ec2 run-instances --image-id ami-070b208e993b59cea --count 
	--instance-type t2.micro --key-name IHR-KEY 
	--security-group-ids sg-08cd0d53e3574cc4a 
	--user-data file://webscript.sh
	
#### 16. Überprüfen der EC2 Instance und dem Apache Webserver
	
	aws ec2 describe-instances

#### 17. Login als EC2-user mit dem ssh Kommando
	
	ssh -i IHR-USERNAME ec2-user@3.121.209.21
    	     oder
	ssh -i IHR-USERNAME ec2-user@ec2-54-93-34-191.eu-central-1.compute.amazonaws.com
	
#### 18. Netzwerk Verbindung checken über HTTP
	
	sudo systemctl status httpd
	
Finish
