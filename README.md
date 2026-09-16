# api-python-tp-m2i
tp pour deploy une api sur ec2 aws

#Etape 1 
Créer une instance EC2
-Lancer une instance
-choisir l'AMI amazon linux
-t3.micro
-créer une pair de clé pour la connexion ssh
Dans le groupe sécurité
-le créer si ce n'est pas déjà fait.
-activer l'adresse ip public
-ouvrir le port ssh http et un tcp personnalisé port 5000
lancer instance

#Etape 2
Tester la connexion ssh 
ouvrir une console dans le dossier contenant la clé
ssh -i "mon-fichier-cles.pem" ec2-user@dnspublic-de-mon-instance-ec2
ces informations sont renseigné dans 
->EC2->instances->mon instance-> se connecter -> dans le client ssh

#Etape3
copier le projet dans l'instance
-ouvrir une console dans le dossier avec la clé et le zip du projet
Envoyer le zip avec cette commande 
l'adresse ip public est présent 
-scp -i CHEMIN_VERS_LA_CLE.pem CHEMIN_VERS_LE_FICHIER.zip ec2-user@ADRESSE_IP_PUBLIQUE:/home/ec2-user/

#Etape4
lancer l'api
se connecter à l'instance
installer unzip
sudo dnf install unzip -y
unzip api-python.zip
-python3-venv
-python3 -m venv venv
-source venv/bin/activate
-pip install -r requirements.txt
-python3 app.py
Sur un navigateur sur notre poste vérifier
-http://ip-public-ec2:5000

#Etape5
Permettre à l'api de redémarer en cas de reboot de l'instance
-sudo nano /etc/systemd/system/mon-service.service
-copier le service suivant
[Unit]
Description=API Python Flask sur EC2
After=network.target

[Service]
Type=simple
User=ec2-user
WorkingDirectory=/home/ec2-user/mon-api-python
ExecStart=/home/ec2-user/mon-api-python/venv/bin/python /home/ec2-user/mon-api-python/app.py
Restart=always
RestartSec=3

[Install]
WantedBy=multi-user.target

-sudo systemctl daemon-reload
-sudo systemctl start mon-service
-sudo systemctl enable mon-service.service
