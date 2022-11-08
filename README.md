Titel:      Dokumentattion von Git/Github Repository
Date:       03-11-2022
Author:     Renato Palavecino
Co Author:  Maik Schoeppach
Keywords:   Git, Github, Linux


used Linux Distribution:   https://linuxmint.com/

![images](https://user-images.githubusercontent.com/105228669/199946392-92c864f2-0e1b-47ea-850f-92cb5f4f53a9.jpeg)

# Git Repository lokal erstellen

## Projekt Umgebung vorbereiten

#### Erstellen Sie ein Vezeichnis mit dem Name Projekt1

    cd
    mkdir Projekt1

#### Dateien und Verzeichnisse fuer das Projekt "Statische Webseite"

    touch index.html style.css backend.py 
    mkdir Secret
    cd Secret
    touch Geheimnisse.txt
    cd ..
    mkdir Bilder
    cd Bilder
    touch Bild1.jpg Bild2.jpg Bild3.jpg Bild1.png Bild2.png
    cd ..
    touch .gitignore

#### Falls dass Sie sensibel Daten Verstecken wollen, schreiben Sie welche in .gitignore File und speicher die Aenderungen

    nano .gitignore

    Code-Blocke

    Secret/
    Bilder/*.png

#### Git Umgebung vorbereiten

    git config --global user.name "Name Nachname"
    git config --global user.email "valid-email"

#### Git Kontrollversionierung initialisiren

    git init

#### From Working Area nach Stagin Area nach Repositoty

    git status
    git add -A
    git commit -m "Erstes Commit"
    git status

#### SSH Keygen erstellen

    cd
    ssh-keygen
    
#### Public Key Download
    
    cat .ssh/id_rsa.pub

#### Github Connect

    ssh -T git@github.com

#### CD Projekt1

    git remote add origin "Code ssh Address in Github Repository "

#### Create a new Repository on the Command Line
    
    echo "# Github-LocalLinuxConnect" >> README.md
    git init
    git add README.md
    git commit -m "first commit"
    git branch -M master
    git remote add origin git@github.com:schoeppach/Github-LocalLinuxConnect.git
    git push -u origin master
    
#### Link a Website

    Linux Mint --https://linuxmint.com--










