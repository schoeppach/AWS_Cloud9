
$\color[rgb]{1,0,1} Created_by: spruehhack$


# Create-an-IPv4-enabled-VPC-and-subnets-using-the-AWS-CLI

## Schritt 1
#### VPC erstellen - benutze den Folgen Code:

    aws ec2 create-vpc --cidr-block 10.0.0.0/16 --query Vpc.VpcId --output text 

#### er wirft dir dann folgendes als Beispiel zurück:

    vpc-1234f567h89
    
### Subnet erstellen
#### Denk dran die folgene VPC ID mit deiner zu ersetzen 

        aws ec2 create-subnet --vpc-id vpc-2f09a348 --cidr-block 10.0.1.0/24
        
        aws ec2 create-subnet --vpc-id vpc-2f09a348 --cidr-block 10.0.0.0/24

## Schritt 2

#### Gateway erstellen

        aws ec2 create-internet-gateway --query InternetGateway.InternetGatewayId --output text
        
#### er wirft dir dann folgendes als Beispiel zurück:

        igw-dd44fwf
### Gateway mit dem VPC verbinden
#### Denk dran die folgene VPC ID und Gateway mit deinen zu ersetzen 
        
        aws ec2 attach-internet-gateway --vpc-id vpc-2f09a348 --internet-gateway-id igw-1ff7a07b
### Benutzerdefinierte VPC erstellen
#### Denk dran die folgene VPC ID mit deiner zu ersetzen 
        
        aws ec2 create-route-table --vpc-id vpc-2f09a348 --query RouteTable.RouteTableId --output text
        
#### er wirft dir dann folgendes als Beispiel zurück:

        rtb c1cfrhw
        
### Eine Route in der Routing-Tabelle die Datenverkehr auf 0.0.0.0/0 auf das Internet Gateway verweist:
#### Denk dran die folgene RTB und Gateway mit deiner zu ersetzen

        aws ec2 create-route --route-table-id rtb-c1c8faa6 --destination-cidr-block 0.0.0.0/0 --gateway-id igw-1ff7a07b
        
### Zum Test das sie erstellt und aktiv ist:
#### Denk dran die folgene RTB mit deiner zu ersetzen

        aws ec2 describe-route-tables --route-table-id rtb-c1c8faa6
        
 ###       
        {
            "RouteTables": [
                {
                    "Associations": [], 
                    "RouteTableId": "rtb-c1c8faa6", 
                    "VpcId": "vpc-2f09a348", 
                    "PropagatingVgws": [], 
                    "Tags": [], 
                    "Routes": [
                        {
                            "GatewayId": "local", 
                            "DestinationCidrBlock": "10.0.0.0/16", 
                            "State": "active", 
                            "Origin": "CreateRouteTable"
                        }, 
                        {
                            "GatewayId": "igw-1ff7a07b", 
                            "DestinationCidrBlock": "0.0.0.0/0", 
                            "State": "active", 
                            "Origin": "CreateRoute"
                        }
                    ]
                }
            ]
        }

#### Die Routing-Tabelle ist derzeit keinem Subnetz zugeordnet. Sie müssen es einem Subnetz in Ihrer VPC zuordnen, damit der Datenverkehr von diesem Subnetz zum Internet-Gateway geleitet wird. Verwenden Sie den folgenden Befehl "describe-subnets", um die Subnetz-IDs abzurufen. Die Option --filter beschränkt die Subnetze nur auf Ihre neue VPC, und die Option --query gibt nur die Subnetz-IDs und ihre CIDR-Blöcke zurück.

        aws ec2 describe-subnets --filters "Name=vpc-id,Values=vpc-2f09a348" --query "Subnets[*].{ID:SubnetId,CIDR:CidrBlock}"
#

        [
    {
        "CIDR": "10.0.1.0/24", 
        "ID": "subnet-b46032ec"
    }, 
    {
        "CIDR": "10.0.0.0/24", 
        "ID": "subnet-a46032fc"
    }
]

### Sie können auswählen, welches Subnetz der benutzerdefinierten Routing-Tabelle zugeordnet werden soll, z. B. subnet-b46032ec, und es mit dem Befehl Associate-Route-Table zuordnen. Dieses Subnetz ist Ihr öffentliches Subnetz.

#### Denk dran die folgene RTB und Subnet mit deiner zu ersetzen

        aws ec2 associate-route-table  --subnet-id subnet-b46032ec --route-table-id rtb-c1c8faa6
        
### Sie können das öffentliche IP-Adressierungsverhalten Ihres Subnetzes so ändern, dass eine im Subnetz gestartete Instanz automatisch eine öffentliche IP-Adresse erhält, indem Sie den folgenden Befehl modify-subnet-attribute verwenden. Andernfalls ordnen Sie Ihrer Instance nach dem Start eine Elastic IP-Adresse zu, damit die Instance über das Internet erreichbar ist. 

#### Denk dran die folgene Subnet mit deiner zu ersetzen

      aws ec2 modify-subnet-attribute --subnet-id subnet-b46032ec --map-public-ip-on-launch  
      
## Schritt 3 Instance in Subnet starten

### Erstellen Sie ein Schlüsselpaar und verwenden Sie die Option --query und die Option --output text, um Ihren privaten Schlüssel direkt in eine Datei mit der Erweiterung .pem zu leiten.

        aws ec2 create-key-pair --key-name MyKeyPair --query "KeyMaterial" --output text > MyKeyPair.pem
        
### In diesem Beispiel starten Sie eine Amazon Linux-Instance. Wenn Sie einen SSH-Client auf einem Linux- oder Mac OS X-Betriebssystem verwenden, um eine Verbindung zu Ihrer Instance herzustellen, verwenden Sie den folgenden Befehl, um die Berechtigungen Ihrer privaten Schlüsseldatei so festzulegen, dass nur Sie sie lesen können.        
        chmod 400 MyKeyPair.pem

### Sicherheitsgruppe erstellen
#### Denk dran die folgene VPC mit deiner zu ersetzen

        aws ec2 create-security-group --group-name SSHAccess --description "Security group for SSH access" --vpc-id vpc-2f09a348
        
#### Wirft dir dann folgendes Beispiel raus:

        {
            "GroupId": "sg-e1fb8c9a"
        }
### Fügen Sie mit dem Befehl authorize-security-group-ingress eine Regel hinzu, die den SSH-Zugriff von überall zulässt.        
#### Denk dran die folgende SG mit deiner zu ersetzen

        aws ec2 authorize-security-group-ingress --group-id sg-e1fb8c9a --protocol tcp --port 22 --cidr 0.0.0.0/0
        
### Starten Sie eine Instanz in Ihrem öffentlichen Subnetz, indem Sie die von Ihnen erstellte Sicherheitsgruppe und das Schlüsselpaar verwenden. Notieren Sie sich in der Ausgabe die Instanz-ID für Ihre Instanz.

### Aktuelle AMI aus dem AMI-Katalog auswählen, SG mit deiner ersetzen und Subnet ersetzen

        aws ec2 run-instances --image-id ami-a4827dc9 --count 1 --instance-type t2.micro --key-name MyKeyPair --security-group-ids sg-e1fb8c9a --subnet-id subnet-             b46032ec
        
### Ihre Instanz muss sich im Zustand „Running“ befinden, um eine Verbindung zu ihr herzustellen. Verwenden Sie den folgenden Befehl, um den Status und die IP-Adresse Ihrer Instance zu beschreiben.
#### Die Instance ID kannst du aus dem was dir der vorherige Befehl wiedergegeben hat auslesen, die aus dem Befehl funktioniert nicht bei dir und muss ersetzt werden

        aws ec2 describe-instances --instance-id i-0146854b7443af453 --query "Reservations[*].Instances[*].{State:State.Name,Address:PublicIpAddress}"
        
### Das sollte als Beispiel dastehen:
        [
            [
                {
                    "State": "running",
                    "Address": "52.87.168.235" 
                }
            ]
        ]
### Wenn Ihre Instanz ausgeführt wird, können Sie mit einem SSH-Client auf einem Linux- oder Mac OS X-Computer eine Verbindung zu ihr herstellen, indem Sie den folgenden Befehl verwenden:
#### Denk dran die Adresse mit deiner zu ersetzen
        ssh -i "MyKeyPair.pem" ec2-user@52.87.168.235
        
## Schritt 4 Alles wieder löschen, toll.

### Delete your security group:

        aws ec2 delete-security-group --group-id sg-e1fb8c9a
### Delete your subnets:

        aws ec2 delete-subnet --subnet-id subnet-b46032ec
        aws ec2 delete-subnet --subnet-id subnet-a46032fc
### Delete your custom route table:

        aws ec2 delete-route-table --route-table-id rtb-c1c8faa6
### Detach your internet gateway from your VPC:

        aws ec2 detach-internet-gateway --internet-gateway-id igw-1ff7a07b --vpc-id vpc-2f09a348
### Delete your internet gateway:

        aws ec2 delete-internet-gateway --internet-gateway-id igw-1ff7a07b
### Delete your VPC:

        aws ec2 delete-vpc --vpc-id vpc-2f09a348
