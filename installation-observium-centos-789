#!/bin/bash

echo test

function goto {
    label=$1
    command=$(sed -n "/$label:/{:a;n;p;ba};" $0 | 
              grep -v ':$' | 
              sed -e 's/#.*$//' -e 's/^[^a-zA-Z0-9]*//' | 
              tail -n +2)
    eval "$command"
    exit
}

hostname=$(hostname)


# changement de nom de machine
if (whiptail --title "observium" --yesno "votre hostname est $hostname \n voulais vous changer de hostname ?" 8 78); then

    while true; do
    new_hostname=$(whiptail --inputbox "Entrez le nouveau nom d'hôte" 8 39 --title "hostname" 3>&1 1>&2 2>&3)
    exitstatus=$?
    if [ $exitstatus == 0 ]; then
        break
    fi
    done
    echo $new_hostname > /etc/hostname
    sed -i "s/127.0.0.1.*/127.0.0.1   $new_hostname $new_hostname.local $new_hostname.old localhost localhost.localdomain localhost4 localhost4.localdomain4/g" /etc/hosts
    hostnamectl set-hostname $new_hostname
    whiptail --title "observium" --msgbox "Le nom d'hôte a été modifié avec succès." 8 78
else
    whiptail --title "observium" --msgbox "Le nom d'hôte n'a pas été modifié." 8 78
fi

clear

function centos7() {

clear

yum update -y -q -e 0 > /dev/null | TERM=ansi whiptail --title "Mise à jour des paquets" --infobox "Cette opération peut prendre quelques minutes. Veuillez patienter." 8 78

whiptail --title "centos 7" --msgbox "installation observium centos 7" 8 40


# Liste des paquets à installer

packages="https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm http://yum.opennms.org/repofiles/opennms-repo-stable-rhel7.noarch.rpm http://rpms.remirepo.net/enterprise/remi-release-7.rpm"


# Variable pour stocker les paquets échoués

failed_packages=""


# Variables pour stocker le numéro de ligne de l'erreur et la commande qui a échoué

error_line=""

error_command=""

i=0

# Counter for the number of packages remaining
package_total=$(echo $packages | wc -w)

{ for package in $packages; do

  # Decrement the package counter
  i=$((i + 100 / $package_total))
  sleep 5
  echo $i

  yum install -y $package &> /dev/null

  yum update -y &> /dev/null


 # Vérification du code de retour pour chaque paquet

 if [ $? -ne 0 ]; then

   # Si l'installation a échoué, ajouter le paquet à la liste des paquets échoués

   failed_packages="$failed_packages $package"

   # Stocker le numéro de ligne de l'erreur et la commande qui a échoué

   error_line="$LINENO"

   error_command="apt-get install $package"

 fi

done } | whiptail --gauge "installation de paquets..." 6 50 0

# Vérification de la liste des paquets échoués

if [ -n "$failed_packages" ]; then

 # Si des paquets ont échoué, sortir du script avec un code d'erreur

 clear

 whiptail --title "erreur paquet" --msgbox "L'installation des paquet(s) suivant(s) a échoué : $failed_packages \n L'erreur s'est produite à la ligne $error_line : $error_command " 8 78 --ok-button "Fin du script"

 exit 1

fi

dnf module enable -y php:remi-7.4 &> /dev/null


alternatives --set python /usr/bin/python3 &> /dev/null

yum -y install epel-release &> /dev/null | TERM=ansi whiptail --title "paquet" --infobox "installation de epel-release" 8 78

curl -LsS -O https://downloads.mariadb.com/MariaDB/mariadb_repo_setup &> /dev/null | TERM=ansi whiptail --title "mariaDB" --infobox "Téléchargement de mariaDB" 8 78
bash mariadb_repo_setup --mariadb-server-version=10.9 &> /dev/null | TERM=ansi whiptail --title "mariaDB" --infobox "configuration de mariaDB" 8 78
masteryum makecache &> /dev/null | TERM=ansi whiptail --title "cache" --infobox "mise a jour du cache YUM" 8 78

clear


# Liste des paquets à installer

packages="wget httpd php php-opcache php-mysql php-gd php-posix php-pear.noarch cronie net-snmp net-snmp-utils fping mariadb-server mariadb MySQL-python rrdtool subversion jwhois ipmitool graphviz ImageMagick php-sodium libvirt"


# Variable pour stocker les paquets échoués

failed_packages=""


# Variables pour stocker le numéro de ligne de l'erreur et la commande qui a échoué

error_line=""

error_command=""

i=0

# Counter for the number of packages remaining
package_total=$(echo $packages | wc -w)

{ for package in $packages; do

  # Decrement the package counter
  i=$((i + 100 / $package_total))
  sleep 5
  echo $i

  yum install -y $package &> /dev/null

  yum update -y &> /dev/null


 # Vérification du code de retour pour chaque paquet

 if [ $? -ne 0 ]; then

   # Si l'installation a échoué, ajouter le paquet à la liste des paquets échoués

   failed_packages="$failed_packages $package"

   # Stocker le numéro de ligne de l'erreur et la commande qui a échoué

   error_line="$LINENO"

   error_command="apt-get install $package"

 fi

done } | whiptail --gauge "installation de paquets..." 6 50 0

# Vérification de la liste des paquets échoués

if [ -n "$failed_packages" ]; then

 # Si des paquets ont échoué, sortir du script avec un code d'erreur

 clear

 whiptail --title "erreur paquet" --msgbox "L'installation des paquet(s) suivant(s) a échoué : $failed_packages \n L'erreur s'est produite à la ligne $error_line : $error_command " 8 78 --ok-button "Fin du script"

 exit 1

fi

yum -y update &> /dev/null | TERM=ansi whiptail --title "update" --infobox "mise a jour des paquets" 8 78

systemctl enable --now mariadb &> /dev/null | TERM=ansi whiptail --title "mariadb" --infobox "activation du service MariaDB" 8 78

rootpassword=$(whiptail --passwordbox "Entrer le mot de passe root de MySQL " 8 78 --title "mot de passe MySQL" 3>&1 1>&2 2>&3)
exitstatus=$?
if [ $exitstatus == 1 ]; then
    exit 1
fi

dbpassword=$(whiptail --passwordbox "Entrer le mot de passe pour la base de donnée observium " 8 78 --title "mot de passe observium" 3>&1 1>&2 2>&3)
exitstatus=$?
if [ $exitstatus == 1 ]; then
    exit 1
fi

# Connectez-vous à MySQL en tant que root
mysql -u root -p$rootpassword <<EOF

# Create the database
CREATE DATABASE observium DEFAULT CHARACTER SET utf8 COLLATE utf8_general_ci;

# Grant privileges to the observium user on the observium database
GRANT ALL PRIVILEGES ON observium.* TO 'observium'@'localhost' IDENTIFIED BY '$dbpassword';

# Exit the MySQL command line
exit
EOF

yum -y update > /dev/null | TERM=ansi whiptail --title "Mise à jour des paquets" --infobox "Mise à jour des paquets" 8 78

#création du dossier /opt/observium
mkdir -p /opt/observium && cd /opt


URL="http://www.observium.org/observium-community-latest.tar.gz"
wget "$URL" 2>&1 | \
 stdbuf -o0 awk '/[.] +[0-9][0-9]?[0-9]?%/ { print substr($0,63,3) }' | \
 whiptail --gauge "Téléchargement d'observium" 6 100 0


tar zxvf observium-community-latest.tar.gz > /dev/null | TERM=ansi whiptail --title "extraction" --infobox "extraction d'observium" 8 78

cd observium

#copy du fichier config.php.default en config.php
cp config.php.default config.php

#ajout des configurations database dans le fichier config.php
file='/opt/observium/config.php'
search1="\$config\['db_user'\]      = 'USERNAME';"
replace1="\$config\['db_user'\]      = 'observium';"
search2="\$config\['db_pass'\]      = 'PASSWORD';"
replace2="\$config\['db_pass'\]      = '$dbpassword';"

# Sauvegarder le fichier original
cp $file $file.bak

# Remplacer les chaînes recherchées par les nouvelles chaînes
sed -i "s/$search1/$replace1/g" $file
sed -i "s/$search2/$replace2/g" $file

#lancement de la découverte pour détecter une erreur éventuelle
./discovery.php -u > /dev/null | TERM=ansi whiptail --title "découverte" --infobox "lancement de la découverte d'erreur" 8 78

# Récupération de la sortie de la commande "which fping"
FPING_PATH=$(which fping)

# Ajout de la ligne "$config['fping'] = "$FPING_PATH";" à la fin du fichier /opt/observium/config.php
echo "\$config['fping'] = \"$FPING_PATH\";" >> /opt/observium/config.php

#désactivation de SELINUX
setenforce 0

#désactivation permanente de SELINUX
SELINUX=permissive
searchSELINUX="SELINUX=targeted"
searchSELINUX2="SELINUX=enforcing"
replaceSELINUX="SELINUX=permissive"
sed -i "s/$searchSELINUX/$replaceSELINUX/g" /etc/selinux/config
sed -i "s/$searchSELINUX2/$replaceSELINUX/g" /etc/selinux/config

#création du dossier rrd et changement de propriétaire
mkdir rrd
chown apache:apache rrd

# création du fichier observium.conf
touch /etc/httpd/conf.d/observium.conf

# ajout de la configuration de virtualhost
cat << EOF > /etc/httpd/conf.d/observium.conf
<VirtualHost *:80>
   DocumentRoot /opt/observium/html/
   ServerName  observium.domain.com
   CustomLog /opt/observium/logs/access_log combined
   ErrorLog /opt/observium/logs/error_log
   <Directory "/opt/observium/html/">
     AllowOverride All
     Options FollowSymLinks MultiViews
     Require all granted
   </Directory>
</VirtualHost>
EOF


#creation du dossier /opt/obsevium/logs et changement de propriétaire
mkdir /opt/observium/logs
chown apache:apache /opt/observium/logs

cd /opt/observium


username=$(whiptail --inputbox "entre un nom d'utilisateur pour observium" 8 39 --title "username" 3>&1 1>&2 2>&3)
exitstatus=$?
if [ $exitstatus == 1 ]; then
    exit 1
fi

password=$(whiptail --passwordbox "entre le mot de passe pour l'utilisateur" 8 39 --title "password" 3>&1 1>&2 2>&3)
exitstatus=$?
if [ $exitstatus == 1 ]; then
    exit 1
fi


while true; do
  level=$(whiptail --inputbox "Entrer le level de l'utilisateur \n \n 10 = admin \n 9 = Global Secure Read Write \n 8 = Global Secure Read Limited Write \n 7 = Global Secure Read \n 6 = Global Read \n 1 = normal user \n 0 = disable" 15 42 --title "level" 3>&1 1>&2 2>&3)
  exitstatus=$?
  if [ $exitstatus -eq 0 ]; then
    if [[ $level =~ ^(10|9|8|7|6|1|0)$ ]]; then
      break
    fi
  else
    exit 1
  fi
done


# Exécutez le script adduser.php avec les informations fournies
./adduser.php $username $password $level

#lancement d'une découverte initiale
./discovery.php -h all > /dev/null | TERM=ansi whiptail --title "découverte" --infobox "lancement de la découverte initial" 8 78
./poller.php -h all > /dev/null | TERM=ansi whiptail --title "découverte" --infobox "lancement de la découverte initial" 8 78

# Créer le fichier observium.conf
touch /etc/cron.d/observium

# Ajout des tâches cron, créez un nouveau fichier /etc/cron.d/observium
cat <<EOF >/etc/cron.d/observium
 # Run a complete discovery of all devices once every 6 hours
 33  */6   * * *   root    /opt/observium/discovery.php -h all >> /dev/null 2>&1

 # Run automated discovery of newly added devices every 5 minutes
 */5 *     * * *   root    /opt/observium/discovery.php -h new >> /dev/null 2>&1

 # Run multithreaded poller wrapper every 5 minutes
 */5 *     * * *   root    /opt/observium/poller-wrapper.py >> /dev/null 2>&1

 # Run housekeeping script daily for syslog, eventlog and alert log
 13 5 * * * root /opt/observium/housekeeping.php -ysel

 # Run housekeeping script daily for rrds, ports, orphaned entries in the database and performance data
 47 4 * * * root /opt/observium/housekeeping.php -yrptb
EOF

#chargement du système cron
systemctl reload crond > /dev/null | TERM=ansi whiptail --title "cron" --infobox "rechargement du système cron" 8 78

#activation du service httpd
systemctl enable httpd > /dev/null | TERM=ansi whiptail --title "httpd" --infobox "activation du service httpd" 8 78

#lancement du service httpd
systemctl restart httpd > /dev/null | TERM=ansi whiptail --title "httpd" --infobox "lancement du service httpd" 8 78

#ajout des régle de pare feu
firewall-cmd --permanent --zone=public --add-service=http > /dev/null | TERM=ansi whiptail --title "pare feu" --infobox "ajout des régle de pare feu" 8 78
firewall-cmd --reload > /dev/null | TERM=ansi whiptail --title "httpd" --infobox "redémarrage du pare feu" 8 78

CHOICE=$(
whiptail --title "Operative Systems" --menu "Make your choice" 16 100 9 \
	"1)" "Geocode.Farm API"  \
	"2)" "Google API"  \
	"3)" "Yandex API"  \
	"4)" "MapQuest API"  \
	"5)" "OpenCage API"  \
	"6)" "LocationIQ API"  \
    "7)" "skip" 3>&2 2>&1 1>&3	
)

result=$(whoami)
case $CHOICE in
	"1)")   
		APIMAP="Lien vers le site de l'API Geocode.Farm : https://geocode.farm/"
        APITYPE="Geocode.Farm"
        APITYPEKEY="geocodefarm"
	;;
	"2)")   
	    APIMAP="Lien vers le site de l'API Google : https://developers.google.com/maps/"
        APITYPE="Google"
        APITYPEKEY="google"
	;;

	"3)")   
	    APIMAP="Lien vers le site de l'API Yandex : https://yandex.com/dev/predictor/keys/get/"
        APITYPE="Yandex"
        APITYPEKEY="geo_api"
        ;;
	"4)")   
	    APIMAP="Lien vers le site de l'API MapQuest : https://developer.mapquest.com/documentation/geocoding-api/"
        APITYPE="MapQuest"
        APITYPEKEY="mapquest"
        ;;
	"5)")   
        APIMAP="Lien vers le site de l'API OpenCage : https://opencagedata.com/api"
        APITYPE="OpenCage"
        APITYPEKEY="opencage"
        ;;
	"6)")   
		APIMAP="Lien vers le site de l'API LocationIQ : https://locationiq.com/docs-geocoding"
        APITYPE="LocationIQ"
        APITYPEKEY="locationiq"
        ;;
	"7)") goto skipapi
        ;;
esac

APIKEY=$(whiptail --inputbox "$APIMAP \n entre la clé API si desous" 9 105 --title "API KEY" 3>&1 1>&2 2>&3)


echo "\$config['geocoding']['api'] = \"$APITYPE\";" >> /opt/observium/config.php
echo "\$config['geo_api']['$APITYPEKEY']['key'] = \"$APIKEY\";" >> /opt/observium/config.php

skipapi:


latitude=$(whiptail --inputbox "Entrez votre coordonnées gps par défaut \n | latitude |" 8 50 --title "Example Dialog" 3>&1 1>&2 2>&3)
longitude=$(whiptail --inputbox "Entrez votre coordonnées gps par défaut \n | longitude |" 8 50 --title "Example Dialog" 3>&1 1>&2 2>&3)

echo "\$config['geocoding']['default']['lat'] = \"$latitude\";" >> /opt/observium/config.php
echo "\$config['geocoding']['default']['lon'] = \"$longitude\";" >> /opt/observium/config.php

echo "\$config['timestamp_format'] = \"d-m-Y H:i:s\";" >> /opt/observium/config.php;
echo "\$config['page_refresh'] = \"60\";" >> /opt/observium/config.php;
echo "\$config['web_session_lifetime'] = \"0\";" >> /opt/observium/config.php;

#redémarrage du service httpd
systemctl restart httpd > /dev/null | TERM=ansi whiptail --title "httpd" --infobox "redémarrage du service httpd" 8 78


# Obtention du nom de l'interface réseau par défaut et stockage dans la variable IFACE
IFACE=$(ip route | grep default | awk '{print $5}')

# Obtention de l'adresse IP de l'interface réseau par défaut et stockage dans la variable IP
IP=$(ip -4 addr show $IFACE | grep inet | awk '{print $2}' | cut -d/ -f1)

# Affichage des instructions pour accéder à la page de configuration observium"

echo "vous pouvez maintenant vous connecter au site web \n \n http://$IP/" > connection_textbox
whiptail --textbox connection_textbox 9 54
}

function centos8() {

clear

yum update -y -q -e 0 > /dev/null | TERM=ansi whiptail --title "Mise à jour des paquets" --infobox "Cette opération peut prendre quelques minutes. Veuillez patienter." 8 78

whiptail --title "centos 8" --msgbox "installation observium centos 8" 8 40

clear
# Liste des paquets à installer

packages="https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm http://yum.opennms.org/repofiles/opennms-repo-stable-rhel8.noarch.rpm http://rpms.remirepo.net/enterprise/remi-release-8.rpm yum-utils"


# Variable pour stocker les paquets échoués

failed_packages=""


# Variables pour stocker le numéro de ligne de l'erreur et la commande qui a échoué

error_line=""

error_command=""

i=0

# Counter for the number of packages remaining
package_total=$(echo $packages | wc -w)

{ for package in $packages; do

  # Decrement the package counter
  i=$((i + 100 / $package_total))
  sleep 5
  echo $i

  yum install -y $package &> /dev/null

  yum update -y &> /dev/null


 # Vérification du code de retour pour chaque paquet

 if [ $? -ne 0 ]; then

   # Si l'installation a échoué, ajouter le paquet à la liste des paquets échoués

   failed_packages="$failed_packages $package"

   # Stocker le numéro de ligne de l'erreur et la commande qui a échoué

   error_line="$LINENO"

   error_command="apt-get install $package"

 fi

done } | whiptail --gauge "installation de paquets..." 6 50 0

# Vérification de la liste des paquets échoués

if [ -n "$failed_packages" ]; then

 # Si des paquets ont échoué, sortir du script avec un code d'erreur

 clear

 whiptail --title "erreur paquet" --msgbox "L'installation des paquet(s) suivant(s) a échoué : $failed_packages \n L'erreur s'est produite à la ligne $error_line : $error_command " 8 78 --ok-button "Fin du script"

 exit 1

fi

dnf module enable -y php:remi-7.4 &> /dev/null


alternatives --set python /usr/bin/python3 &> /dev/null

yum -y install epel-release &> /dev/null | TERM=ansi whiptail --title "paquet" --infobox "installation de epel-release" 8 78

curl -LsS -O https://downloads.mariadb.com/MariaDB/mariadb_repo_setup &> /dev/null | TERM=ansi whiptail --title "mariaDB" --infobox "Téléchargement de mariaDB" 8 78
bash mariadb_repo_setup --mariadb-server-version=10.9 &> /dev/null | TERM=ansi whiptail --title "mariaDB" --infobox "configuration de mariaDB" 8 78
masteryum makecache &> /dev/null | TERM=ansi whiptail --title "cache" --infobox "mise a jour du cache YUM" 8 78

clear


# Liste des paquets à installer

packages="wget httpd php php-opcache php-mysqlnd php-gd libvirt php-posix nmap git php-pear cronie net-snmp net-snmp-utils fping mariadb-server mariadb rrdtool subversion whois ipmitool graphviz ImageMagick php sodium python3 python3-mysql python3-PyMySQL"


# Variable pour stocker les paquets échoués

failed_packages=""


# Variables pour stocker le numéro de ligne de l'erreur et la commande qui a échoué

error_line=""

error_command=""

i=0

# Counter for the number of packages remaining
package_total=$(echo $packages | wc -w)

{ for package in $packages; do

  # Decrement the package counter
  i=$((i + 100 / $package_total))
  sleep 5
  echo $i

  yum install -y $package &> /dev/null

  yum update -y &> /dev/null


 # Vérification du code de retour pour chaque paquet

 if [ $? -ne 0 ]; then

   # Si l'installation a échoué, ajouter le paquet à la liste des paquets échoués

   failed_packages="$failed_packages $package"

   # Stocker le numéro de ligne de l'erreur et la commande qui a échoué

   error_line="$LINENO"

   error_command="apt-get install $package"

 fi

done } | whiptail --gauge "installation de paquets..." 6 50 0

# Vérification de la liste des paquets échoués

if [ -n "$failed_packages" ]; then

 # Si des paquets ont échoué, sortir du script avec un code d'erreur

 clear

 whiptail --title "erreur paquet" --msgbox "L'installation des paquet(s) suivant(s) a échoué : $failed_packages \n L'erreur s'est produite à la ligne $error_line : $error_command " 8 78 --ok-button "Fin du script"

 exit 1

fi

yum -y update &> /dev/null | TERM=ansi whiptail --title "update" --infobox "mise a jour des paquets" 8 78

yum install -y http://www6.atomicorp.com/channels/atomic/centos/8/x86_64/RPMS/openvas-smb-22.4.0-26623.el8.art.x86_64.rpm &> /dev/null | TERM=ansi whiptail --title "installation" --infobox "installation de openvas-smb" 8 78

ln -s /usr/bin/python3 /usr/bin/python

systemctl enable --now mariadb &> /dev/null | TERM=ansi whiptail --title "mariadb" --infobox "activation du service MariaDB" 8 78

rootpassword=$(whiptail --passwordbox "Entrer le mot de passe root de MySQL " 8 78 --title "mot de passe MySQL" 3>&1 1>&2 2>&3)
exitstatus=$?
if [ $exitstatus == 1 ]; then
    exit 1
fi

dbpassword=$(whiptail --passwordbox "Entrer le mot de passe pour la base de donnée observium " 8 78 --title "mot de passe observium" 3>&1 1>&2 2>&3)
exitstatus=$?
if [ $exitstatus == 1 ]; then
    exit 1
fi

# Connectez-vous à MySQL en tant que root
mysql -u root -p$rootpassword <<EOF

# Create the database
CREATE DATABASE observium DEFAULT CHARACTER SET utf8 COLLATE utf8_general_ci;

# Grant privileges to the observium user on the observium database
GRANT ALL PRIVILEGES ON observium.* TO 'observium'@'localhost' IDENTIFIED BY '$dbpassword';

# Exit the MySQL command line
exit
EOF

yum -y update > /dev/null | TERM=ansi whiptail --title "Mise à jour des paquets" --infobox "Mise à jour des paquets" 8 78

#création du dossier /opt/observium
mkdir -p /opt/observium && cd /opt


URL="http://www.observium.org/observium-community-latest.tar.gz"
wget "$URL" 2>&1 | \
 stdbuf -o0 awk '/[.] +[0-9][0-9]?[0-9]?%/ { print substr($0,63,3) }' | \
 whiptail --gauge "Téléchargement d'observium" 6 100 0


tar zxvf observium-community-latest.tar.gz > /dev/null | TERM=ansi whiptail --title "extraction" --infobox "extraction d'observium" 8 78

cd observium

#copy du fichier config.php.default en config.php
cp config.php.default config.php

#ajout des configurations database dans le fichier config.php
file='/opt/observium/config.php'
search1="\$config\['db_user'\]      = 'USERNAME';"
replace1="\$config\['db_user'\]      = 'observium';"
search2="\$config\['db_pass'\]      = 'PASSWORD';"
replace2="\$config\['db_pass'\]      = '$dbpassword';"

# Sauvegarder le fichier original
cp $file $file.bak

# Remplacer les chaînes recherchées par les nouvelles chaînes
sed -i "s/$search1/$replace1/g" $file
sed -i "s/$search2/$replace2/g" $file

#lancement de la découverte pour détecter une erreur éventuelle
./discovery.php -u > /dev/null | TERM=ansi whiptail --title "découverte" --infobox "lancement de la découverte d'erreur" 8 78

# Récupération de la sortie de la commande "which fping"
FPING_PATH=$(which fping)

# Ajout de la ligne "$config['fping'] = "$FPING_PATH";" à la fin du fichier /opt/observium/config.php
echo "\$config['fping'] = \"$FPING_PATH\";" >> /opt/observium/config.php

#désactivation de SELINUX
setenforce 0

#désactivation permanente de SELINUX
SELINUX=permissive
searchSELINUX="SELINUX=targeted"
searchSELINUX2="SELINUX=enforcing"
replaceSELINUX="SELINUX=permissive"
sed -i "s/$searchSELINUX/$replaceSELINUX/g" /etc/selinux/config
sed -i "s/$searchSELINUX2/$replaceSELINUX/g" /etc/selinux/config

#création du dossier rrd et changement de propriétaire
mkdir rrd
chown apache:apache rrd

# création du fichier observium.conf
touch /etc/httpd/conf.d/observium.conf

# ajout de la configuration de virtualhost
cat << EOF > /etc/httpd/conf.d/observium.conf
<VirtualHost *:80>
   DocumentRoot /opt/observium/html/
   ServerName  observium.domain.com
   CustomLog /opt/observium/logs/access_log combined
   ErrorLog /opt/observium/logs/error_log
   <Directory "/opt/observium/html/">
     AllowOverride All
     Options FollowSymLinks MultiViews
     Require all granted
   </Directory>
</VirtualHost>
EOF


#creation du dossier /opt/obsevium/logs et changement de propriétaire
mkdir /opt/observium/logs
chown apache:apache /opt/observium/logs

cd /opt/observium


username=$(whiptail --inputbox "entre un nom d'utilisateur pour observium" 8 39 --title "username" 3>&1 1>&2 2>&3)
exitstatus=$?
if [ $exitstatus == 1 ]; then
    exit 1
fi

password=$(whiptail --passwordbox "entre le mot de passe pour l'utilisateur" 8 39 --title "password" 3>&1 1>&2 2>&3)
exitstatus=$?
if [ $exitstatus == 1 ]; then
    exit 1
fi


while true; do
  level=$(whiptail --inputbox "Entrer le level de l'utilisateur \n \n 10 = admin \n 9 = Global Secure Read Write \n 8 = Global Secure Read Limited Write \n 7 = Global Secure Read \n 6 = Global Read \n 1 = normal user \n 0 = disable" 15 42 --title "level" 3>&1 1>&2 2>&3)
  exitstatus=$?
  if [ $exitstatus -eq 0 ]; then
    if [[ $level =~ ^(10|9|8|7|6|1|0)$ ]]; then
      break
    fi
  else
    exit 1
  fi
done


# Exécutez le script adduser.php avec les informations fournies
./adduser.php $username $password $level

#lancement d'une découverte initiale
./discovery.php -h all > /dev/null | TERM=ansi whiptail --title "découverte" --infobox "lancement de la découverte initial" 8 78
./poller.php -h all > /dev/null | TERM=ansi whiptail --title "découverte" --infobox "lancement de la découverte initial" 8 78

# Créer le fichier observium.conf
touch /etc/cron.d/observium

# Ajout des tâches cron, créez un nouveau fichier /etc/cron.d/observium
cat <<EOF >/etc/cron.d/observium
 # Run a complete discovery of all devices once every 6 hours
 33  */6   * * *   root    /opt/observium/discovery.php -h all >> /dev/null 2>&1

 # Run automated discovery of newly added devices every 5 minutes
 */5 *     * * *   root    /opt/observium/discovery.php -h new >> /dev/null 2>&1

 # Run multithreaded poller wrapper every 5 minutes
 */5 *     * * *   root    /opt/observium/poller-wrapper.py >> /dev/null 2>&1

 # Run housekeeping script daily for syslog, eventlog and alert log
 13 5 * * * root /opt/observium/housekeeping.php -ysel

 # Run housekeeping script daily for rrds, ports, orphaned entries in the database and performance data
 47 4 * * * root /opt/observium/housekeeping.php -yrptb
EOF

#chargement du système cron
systemctl reload crond > /dev/null | TERM=ansi whiptail --title "cron" --infobox "rechargement du système cron" 8 78

#activation du service httpd
systemctl enable httpd > /dev/null | TERM=ansi whiptail --title "httpd" --infobox "activation du service httpd" 8 78

#lancement du service httpd
systemctl restart httpd > /dev/null | TERM=ansi whiptail --title "httpd" --infobox "lancement du service httpd" 8 78

#ajout des régle de pare feu
firewall-cmd --permanent --zone=public --add-service=http > /dev/null | TERM=ansi whiptail --title "pare feu" --infobox "ajout des régle de pare feu" 8 78
firewall-cmd --reload > /dev/null | TERM=ansi whiptail --title "httpd" --infobox "redémarrage du pare feu" 8 78

CHOICE=$(
whiptail --title "Operative Systems" --menu "Make your choice" 16 100 9 \
	"1)" "Geocode.Farm API"  \
	"2)" "Google API"  \
	"3)" "Yandex API"  \
	"4)" "MapQuest API"  \
	"5)" "OpenCage API"  \
	"6)" "LocationIQ API"  \
    "7)" "skip" 3>&2 2>&1 1>&3	
)

result=$(whoami)
case $CHOICE in
	"1)")   
		APIMAP="Lien vers le site de l'API Geocode.Farm : https://geocode.farm/"
        APITYPE="Geocode.Farm"
        APITYPEKEY="geocodefarm"
	;;
	"2)")   
	    APIMAP="Lien vers le site de l'API Google : https://developers.google.com/maps/"
        APITYPE="Google"
        APITYPEKEY="google"
	;;

	"3)")   
	    APIMAP="Lien vers le site de l'API Yandex : https://yandex.com/dev/predictor/keys/get/"
        APITYPE="Yandex"
        APITYPEKEY="geo_api"
        ;;
	"4)")   
	    APIMAP="Lien vers le site de l'API MapQuest : https://developer.mapquest.com/documentation/geocoding-api/"
        APITYPE="MapQuest"
        APITYPEKEY="mapquest"
        ;;
	"5)")   
        APIMAP="Lien vers le site de l'API OpenCage : https://opencagedata.com/api"
        APITYPE="OpenCage"
        APITYPEKEY="opencage"
        ;;
	"6)")   
		APIMAP="Lien vers le site de l'API LocationIQ : https://locationiq.com/docs-geocoding"
        APITYPE="LocationIQ"
        APITYPEKEY="locationiq"
        ;;
	"7)") goto skipapi
        ;;
esac

APIKEY=$(whiptail --inputbox "$APIMAP \n entre la clé API si desous" 9 105 --title "API KEY" 3>&1 1>&2 2>&3)


echo "\$config['geocoding']['api'] = \"$APITYPE\";" >> /opt/observium/config.php
echo "\$config['geo_api']['$APITYPEKEY']['key'] = \"$APIKEY\";" >> /opt/observium/config.php

skipapi:


latitude=$(whiptail --inputbox "Entrez votre coordonnées gps par défaut \n | latitude |" 8 50 --title "Example Dialog" 3>&1 1>&2 2>&3)
longitude=$(whiptail --inputbox "Entrez votre coordonnées gps par défaut \n | longitude |" 8 50 --title "Example Dialog" 3>&1 1>&2 2>&3)

echo "\$config['geocoding']['default']['lat'] = \"$latitude\";" >> /opt/observium/config.php
echo "\$config['geocoding']['default']['lon'] = \"$longitude\";" >> /opt/observium/config.php

echo "\$config['timestamp_format'] = \"d-m-Y H:i:s\";" >> /opt/observium/config.php;
echo "\$config['page_refresh'] = \"60\";" >> /opt/observium/config.php;
echo "\$config['web_session_lifetime'] = \"0\";" >> /opt/observium/config.php;

#redémarrage du service httpd
systemctl restart httpd > /dev/null | TERM=ansi whiptail --title "httpd" --infobox "redémarrage du service httpd" 8 78


# Obtention du nom de l'interface réseau par défaut et stockage dans la variable IFACE
IFACE=$(ip route | grep default | awk '{print $5}')

# Obtention de l'adresse IP de l'interface réseau par défaut et stockage dans la variable IP
IP=$(ip -4 addr show $IFACE | grep inet | awk '{print $2}' | cut -d/ -f1)

# Affichage des instructions pour accéder à la page de configuration observium"

echo "vous pouvez maintenant vous connecter au site web \n \n http://$IP/" > connection_textbox
whiptail --textbox connection_textbox 9 54

}

function centos9() {

clear

yum update -y -q -e 0 > /dev/null | TERM=ansi whiptail --title "Mise à jour des paquets" --infobox "Cette opération peut prendre quelques minutes. Veuillez patienter." 8 78


whiptail --title "centos 9" --msgbox "installation observium centos 9" 8 40

clear

dnf config-manager --set-enabled crb

clear


# Liste des paquets à installer

packages="https://dl.fedoraproject.org/pub/epel/epel-release-latest-9.noarch.rpm https://dl.fedoraproject.org/pub/epel/epel-next-release-latest-9.noarch.rpm dnf-utils http://rpms.remirepo.net/enterprise/remi-release-9.rpm"

# Variable pour stocker les paquets échoués

failed_packages=""


# Variables pour stocker le numéro de ligne de l'erreur et la commande qui a échoué

error_line=""

error_command=""

i=0

# Counter for the number of packages remaining
package_total=$(echo $packages | wc -w)

{ for package in $packages; do

  # Decrement the package counter
  i=$((i + 100 / $package_total))
  sleep 5
  echo $i

  yum install -y $package &> /dev/null

  yum update -y &> /dev/null


 # Vérification du code de retour pour chaque paquet

 if [ $? -ne 0 ]; then

   # Si l'installation a échoué, ajouter le paquet à la liste des paquets échoués

   failed_packages="$failed_packages $package"

   # Stocker le numéro de ligne de l'erreur et la commande qui a échoué

   error_line="$LINENO"

   error_command="apt-get install $package"

 fi

done } | whiptail --gauge "installation de paquets..." 6 50 0

# Vérification de la liste des paquets échoués

if [ -n "$failed_packages" ]; then

 # Si des paquets ont échoué, sortir du script avec un code d'erreur

 clear

 whiptail --title "erreur paquet" --msgbox "L'installation des paquet(s) suivant(s) a échoué : $failed_packages \n L'erreur s'est produite à la ligne $error_line : $error_command " 8 78 --ok-button "Fin du script"

 exit 1

fi

dnf module enable php:remi-8.2 -y &> /dev/null


yum -y install epel-release &> /dev/null | TERM=ansi whiptail --title "paquet" --infobox "installation de epel-release" 8 78

masteryum makecache &> /dev/null | TERM=ansi whiptail --title "cache" --infobox "mise a jour du cache YUM" 8 78

clear


# Liste des paquets à installer

packages="wget httpd php php-opcache php-mysqlnd php-gd php-posix php-pear cronie net-snmp net-snmp-utils fping mariadb-server mariadb rrdtool subversion whois ipmitool graphviz ImageMagick php-sodium python3 python3-PyMySQL libvirt"


# Variable pour stocker les paquets échoués

failed_packages=""


# Variables pour stocker le numéro de ligne de l'erreur et la commande qui a échoué

error_line=""

error_command=""

i=0

# Counter for the number of packages remaining
package_total=$(echo $packages | wc -w)

{ for package in $packages; do

  # Decrement the package counter
  i=$((i + 100 / $package_total))
  sleep 5
  echo $i

  yum install -y $package &> /dev/null

  yum update -y &> /dev/null


 # Vérification du code de retour pour chaque paquet

 if [ $? -ne 0 ]; then

   # Si l'installation a échoué, ajouter le paquet à la liste des paquets échoués

   failed_packages="$failed_packages $package"

   # Stocker le numéro de ligne de l'erreur et la commande qui a échoué

   error_line="$LINENO"

   error_command="apt-get install $package"

 fi

done } | whiptail --gauge "installation de paquets..." 6 50 0

# Vérification de la liste des paquets échoués

if [ -n "$failed_packages" ]; then

 # Si des paquets ont échoué, sortir du script avec un code d'erreur

 clear

 whiptail --title "erreur paquet" --msgbox "L'installation des paquet(s) suivant(s) a échoué : $failed_packages \n L'erreur s'est produite à la ligne $error_line : $error_command " 8 78 --ok-button "Fin du script"

 exit 1

fi

yum -y update &> /dev/null | TERM=ansi whiptail --title "update" --infobox "mise a jour des paquets" 8 78

systemctl enable --now mariadb &> /dev/null | TERM=ansi whiptail --title "mariadb" --infobox "activation du service MariaDB" 8 78

rootpassword=$(whiptail --passwordbox "Entrer le mot de passe root de MySQL " 8 78 --title "mot de passe MySQL" 3>&1 1>&2 2>&3)
exitstatus=$?
if [ $exitstatus == 1 ]; then
    exit 1
fi

dbpassword=$(whiptail --passwordbox "Entrer le mot de passe pour la base de donnée observium " 8 78 --title "mot de passe observium" 3>&1 1>&2 2>&3)
exitstatus=$?
if [ $exitstatus == 1 ]; then
    exit 1
fi

# Connectez-vous à MySQL en tant que root
mysql -u root -p$rootpassword <<EOF

# Create the database
CREATE DATABASE observium DEFAULT CHARACTER SET utf8 COLLATE utf8_general_ci;

# Grant privileges to the observium user on the observium database
GRANT ALL PRIVILEGES ON observium.* TO 'observium'@'localhost' IDENTIFIED BY '$dbpassword';

# Exit the MySQL command line
exit
EOF

yum -y update > /dev/null | TERM=ansi whiptail --title "Mise à jour des paquets" --infobox "Mise à jour des paquets" 8 78

#création du dossier /opt/observium
mkdir -p /opt/observium && cd /opt


URL="http://www.observium.org/observium-community-latest.tar.gz"
wget "$URL" 2>&1 | \
 stdbuf -o0 awk '/[.] +[0-9][0-9]?[0-9]?%/ { print substr($0,63,3) }' | \
 whiptail --gauge "Téléchargement d'observium" 6 100 0


tar zxvf observium-community-latest.tar.gz > /dev/null | TERM=ansi whiptail --title "extraction" --infobox "extraction d'observium" 8 78

cd observium

#copy du fichier config.php.default en config.php
cp config.php.default config.php

#ajout des configurations database dans le fichier config.php
file='/opt/observium/config.php'
search1="\$config\['db_user'\]      = 'USERNAME';"
replace1="\$config\['db_user'\]      = 'observium';"
search2="\$config\['db_pass'\]      = 'PASSWORD';"
replace2="\$config\['db_pass'\]      = '$dbpassword';"

# Sauvegarder le fichier original
cp $file $file.bak

# Remplacer les chaînes recherchées par les nouvelles chaînes
sed -i "s/$search1/$replace1/g" $file
sed -i "s/$search2/$replace2/g" $file

#lancement de la découverte pour détecter une erreur éventuelle
./discovery.php -u > /dev/null | TERM=ansi whiptail --title "découverte" --infobox "lancement de la découverte d'erreur" 8 78

# Récupération de la sortie de la commande "which fping"
FPING_PATH=$(which fping)

# Ajout de la ligne "$config['fping'] = "$FPING_PATH";" à la fin du fichier /opt/observium/config.php
echo "\$config['fping'] = \"$FPING_PATH\";" >> /opt/observium/config.php

#désactivation de SELINUX
setenforce 0

#désactivation permanente de SELINUX
SELINUX=permissive
searchSELINUX="SELINUX=targeted"
searchSELINUX2="SELINUX=enforcing"
replaceSELINUX="SELINUX=permissive"
sed -i "s/$searchSELINUX/$replaceSELINUX/g" /etc/selinux/config
sed -i "s/$searchSELINUX2/$replaceSELINUX/g" /etc/selinux/config

#création du dossier rrd et changement de propriétaire
mkdir rrd
chown apache:apache rrd

# création du fichier observium.conf
touch /etc/httpd/conf.d/observium.conf

# ajout de la configuration de virtualhost
cat << EOF > /etc/httpd/conf.d/observium.conf
<VirtualHost *>
   DocumentRoot /opt/observium/html/
   ServerName  observium.local
   CustomLog /opt/observium/logs/access_log combined
   ErrorLog /opt/observium/logs/error_log
   <Directory "/opt/observium/html/">
     AllowOverride All
     Options FollowSymLinks MultiViews
     Require all granted
   </Directory>
</VirtualHost>
EOF


#creation du dossier /opt/obsevium/logs et changement de propriétaire
mkdir /opt/observium/logs
chown apache:apache /opt/observium/logs

cd /opt/observium


username=$(whiptail --inputbox "entre un nom d'utilisateur pour observium" 8 39 --title "username" 3>&1 1>&2 2>&3)
exitstatus=$?
if [ $exitstatus == 1 ]; then
    exit 1
fi

password=$(whiptail --passwordbox "entre le mot de passe pour l'utilisateur" 8 39 --title "password" 3>&1 1>&2 2>&3)
exitstatus=$?
if [ $exitstatus == 1 ]; then
    exit 1
fi


while true; do
  level=$(whiptail --inputbox "Entrer le level de l'utilisateur \n \n 10 = admin \n 9 = Global Secure Read Write \n 8 = Global Secure Read Limited Write \n 7 = Global Secure Read \n 6 = Global Read \n 1 = normal user \n 0 = disable" 15 42 --title "level" 3>&1 1>&2 2>&3)
  exitstatus=$?
  if [ $exitstatus -eq 0 ]; then
    if [[ $level =~ ^(10|9|8|7|6|1|0)$ ]]; then
      break
    fi
  else
    exit 1
  fi
done


# Exécutez le script adduser.php avec les informations fournies
./adduser.php $username $password $level

#lancement d'une découverte initiale
./discovery.php -h all > /dev/null | TERM=ansi whiptail --title "découverte" --infobox "lancement de la découverte initial" 8 78
./poller.php -h all > /dev/null | TERM=ansi whiptail --title "découverte" --infobox "lancement de la découverte initial" 8 78

# Créer le fichier observium.conf
touch /etc/cron.d/observium

# Ajout des tâches cron, créez un nouveau fichier /etc/cron.d/observium
cat <<EOF >/etc/cron.d/observium
 # Run a complete discovery of all devices once every 6 hours
 33  */6   * * *   root    /opt/observium/discovery.php -h all >> /dev/null 2>&1

 # Run automated discovery of newly added devices every 5 minutes
 */5 *     * * *   root    /opt/observium/discovery.php -h new >> /dev/null 2>&1

 # Run multithreaded poller wrapper every 5 minutes
 */5 *     * * *   root    /opt/observium/poller-wrapper.py >> /dev/null 2>&1

 # Run housekeeping script daily for syslog, eventlog and alert log
 13 5 * * * root /opt/observium/housekeeping.php -ysel

 # Run housekeeping script daily for rrds, ports, orphaned entries in the database and performance data
 47 4 * * * root /opt/observium/housekeeping.php -yrptb
EOF

#chargement du système cron
systemctl reload crond > /dev/null | TERM=ansi whiptail --title "cron" --infobox "rechargement du système cron" 8 78

#activation du service httpd
systemctl enable httpd > /dev/null | TERM=ansi whiptail --title "httpd" --infobox "activation du service httpd" 8 78

#lancement du service httpd
systemctl restart httpd > /dev/null | TERM=ansi whiptail --title "httpd" --infobox "lancement du service httpd" 8 78

#ajout des régle de pare feu
firewall-cmd --permanent --zone=public --add-service=http > /dev/null | TERM=ansi whiptail --title "pare feu" --infobox "ajout des régle de pare feu" 8 78
firewall-cmd --reload > /dev/null | TERM=ansi whiptail --title "httpd" --infobox "redémarrage du pare feu" 8 78

CHOICE=$(
whiptail --title "Operative Systems" --menu "Make your choice" 16 100 9 \
	"1)" "Geocode.Farm API"  \
	"2)" "Google API"  \
	"3)" "Yandex API"  \
	"4)" "MapQuest API"  \
	"5)" "OpenCage API"  \
	"6)" "LocationIQ API"  \
    "7)" "skip" 3>&2 2>&1 1>&3	
)

result=$(whoami)
case $CHOICE in
	"1)")   
		APIMAP="Lien vers le site de l'API Geocode.Farm : https://geocode.farm/"
        APITYPE="Geocode.Farm"
        APITYPEKEY="geocodefarm"
	;;
	"2)")   
	    APIMAP="Lien vers le site de l'API Google : https://developers.google.com/maps/"
        APITYPE="Google"
        APITYPEKEY="google"
	;;

	"3)")   
	    APIMAP="Lien vers le site de l'API Yandex : https://yandex.com/dev/predictor/keys/get/"
        APITYPE="Yandex"
        APITYPEKEY="geo_api"
        ;;
	"4)")   
	    APIMAP="Lien vers le site de l'API MapQuest : https://developer.mapquest.com/documentation/geocoding-api/"
        APITYPE="MapQuest"
        APITYPEKEY="mapquest"
        ;;
	"5)")   
        APIMAP="Lien vers le site de l'API OpenCage : https://opencagedata.com/api"
        APITYPE="OpenCage"
        APITYPEKEY="opencage"
        ;;
	"6)")   
		APIMAP="Lien vers le site de l'API LocationIQ : https://locationiq.com/docs-geocoding"
        APITYPE="LocationIQ"
        APITYPEKEY="locationiq"
        ;;
	"7)") goto skipapi
        ;;
esac

APIKEY=$(whiptail --inputbox "$APIMAP \n entre la clé API si desous" 9 105 --title "API KEY" 3>&1 1>&2 2>&3)


echo "\$config['geocoding']['api'] = \"$APITYPE\";" >> /opt/observium/config.php
echo "\$config['geo_api']['$APITYPEKEY']['key'] = \"$APIKEY\";" >> /opt/observium/config.php

skipapi:


latitude=$(whiptail --inputbox "Entrez votre coordonnées gps par défaut \n | latitude |" 8 50 --title "Example Dialog" 3>&1 1>&2 2>&3)
longitude=$(whiptail --inputbox "Entrez votre coordonnées gps par défaut \n | longitude |" 8 50 --title "Example Dialog" 3>&1 1>&2 2>&3)

echo "\$config['geocoding']['default']['lat'] = \"$latitude\";" >> /opt/observium/config.php
echo "\$config['geocoding']['default']['lon'] = \"$longitude\";" >> /opt/observium/config.php

echo "\$config['timestamp_format'] = \"d-m-Y H:i:s\";" >> /opt/observium/config.php;
echo "\$config['page_refresh'] = \"60\";" >> /opt/observium/config.php;
echo "\$config['web_session_lifetime'] = \"0\";" >> /opt/observium/config.php;

#redémarrage du service httpd
systemctl restart httpd > /dev/null | TERM=ansi whiptail --title "httpd" --infobox "redémarrage du service httpd" 8 78


# Obtention du nom de l'interface réseau par défaut et stockage dans la variable IFACE
IFACE=$(ip route | grep default | awk '{print $5}')

# Obtention de l'adresse IP de l'interface réseau par défaut et stockage dans la variable IP
IP=$(ip -4 addr show $IFACE | grep inet | awk '{print $2}' | cut -d/ -f1)

# Affichage des instructions pour accéder à la page de configuration observium"

echo "vous pouvez maintenant vous connecter au site web \n \n http://$IP/" > connection_textbox
whiptail --textbox connection_textbox 9 54


}

centos_version=0

if [ -f /etc/redhat-release ]; then
    # Récupérer le contenu du fichier /etc/redhat-release
    redhat_release=$(cat /etc/redhat-release)
    
    # Extraire la version de CentOS à partir du contenu du fichier
    centos_version=$(echo "$redhat_release" | sed 's/.*release \([0-9]\).*/\1/')
    
    echo "$centos_version"
else
    whiptail --title "erreur" --msgbox "Le fichier /etc/redhat-release n'existe pas. Ce système n'est peut-être pas CentOS." 8 90
fi

result=$(whoami)
case $centos_version in
  "7")
  centos7
  ;;
  "8")
  centos8
  ;;
  "9")
  centos9
  ;;
esac
