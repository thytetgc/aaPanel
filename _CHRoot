#!/bin/bash

#+----------------GERANDO LOG-----------------------+OK"
SCRIPT=`basename $0`
LOG=`echo $SCRIPT.log | sed s/'.sh'/'.log'/g`
exec &> >(tee -a "$LOG")
echo "[`date`] ==== Inicio de rotina..."
#+----------------GERANDO LOG-----------------------+OK"

Principal() {
	cd /tmp/
	clear
     dir="Diretorio Atual		 : `pwd`"
	 hostname="Hostname			 : `hostname --fqdn`"
	 ip="IP						 : `wget -qO - icanhazip.com`"
     versaoso="Versao S.O.		 : `lsb_release -d | cut -d : -f 2- | sed 's/^[ \t]*//;s/[ \t]*$//'`"
	 release="Release			 : `lsb_release -r | cut -d : -f 2- | sed 's/^[ \t]*//;s/[ \t]*$//'`"
	 codename="Codename			 : `lsb_release -c | cut -d : -f 2- | sed 's/^[ \t]*//;s/[ \t]*$//'`"
	 kernel="Kernel				 : `uname -r`"
	 arquitetura="Arquitetura	 : `uname -m`"
	 echo
     echo "+-------------------------------------------------+"
     echo "|           Utilitario para aaPanel               |"
     echo "+-------------------------------------------------+"
     echo "| Usuarios ssh enjaulados                 v1.19   |"
     echo "+-------------------------------------------------+"
     echo "| Escrito por:                                    |"
     echo "| Thiago Castro - www.hostlp.cloud                |"
     echo "+-------------------------------------------------+"
     echo
     echo $dir
	 echo "+-------------------------------------------------+"
	 echo $hostname
	 echo "+-------------------------------------------------+"
	 echo $ip
	 echo "+-------------------------------------------------+"
	 echo $versaoso
	 echo "+-------------------------------------------------+"
	 echo $release
	 echo "+-------------------------------------------------+"
	 echo $codename
	 echo "+-------------------------------------------------+"
     echo $kernel
	 echo "+-------------------------------------------------+"
     echo $arquitetura
	 echo "+-------------------------------------------------+"
	 echo
     echo
	 echo "Aperte <ENTER> para continuar e começar..."
	 read 
	 echo "Opções:"
	 echo
     echo "1. Criar usuario"
	 echo "2. Remover usuario"
	 echo
	 echo "3. Sair"
	 echo
	 echo
     echo -n "Entre com a opcao desejada => "
     read opcao
     echo
     case $opcao in
        1) cu ;;
        2) ru ;;
        3) exit ;;
        *) "Opcao desconhecida." ; echo ; Principal ;;
     esac
}

cu() {

	echo -n "Digite o nome de usuario...: "
	read NEW_USER_NAME
	echo -n "Digite o site/dominio......: "
	read DOMAIN
	
export NEW_GROUP_NAME=${NEW_USER_NAME}
export HOME_DIR="/home"
export HOME_AAP="/www/wwwroot"
export USER_WEB="www"
export GROUP_WEB="www"
#++++++++++++++++++++++++++++++++++++++++++++

echo
echo "Configurando o SSH..."
echo "" >> /etc/ssh/sshd_config
echo "Match Group ${NEW_GROUP_NAME}" >> /etc/ssh/sshd_config
echo "   ChrootDirectory ${HOME_DIR}/${NEW_USER_NAME}" >> /etc/ssh/sshd_config
echo "   AuthorizedKeysFile ${HOME_DIR}/${NEW_USER_NAME}${HOME_DIR}/${NEW_USER_NAME}/.ssh/authorized_keys" >> /etc/ssh/sshd_config
	echo "+-------------------------------------------------+OK"

echo
echo "Reiniciando SSH..."
systemctl restart sshd
	echo "+-------------------------------------------------+OK"

echo
echo "Adicionando Usuario..."
useradd ${NEW_USER_NAME} 2>/dev/null
usermod -aG www,${NEW_GROUP_NAME} -d ${HOME_DIR}/${NEW_USER_NAME} ${NEW_USER_NAME}
mkdir -p ${HOME_DIR}/${NEW_USER_NAME}${HOME_AAP}/${DOMAIN}
	echo "+-------------------------------------------------+OK"

echo
echo "Digite uma senha...<--"
passwd ${NEW_USER_NAME}
	echo "+-------------------------------------------------+OK"

echo
echo "Instalando aplicativos para o usuario..."
yum --installroot=${HOME_DIR}/${NEW_USER_NAME} --releasever=7 --nogpg --disablerepo='*' --enablerepo=base install centos-release openssh-clients wget vim nano zip unzip tar findutils iputils bind-utils rsync git htop -y
\cp -v /bin/{htop,clear} ${HOME_DIR}/${NEW_USER_NAME}/bin/
	echo "+-------------------------------------------------+OK"

echo
echo "Adicionando montagem ao fstab..."
echo "" >> /etc/fstab
echo "#${NEW_USER_NAME}" >> /etc/fstab
echo "none ${HOME_DIR}/${NEW_USER_NAME}/proc proc defaults 0 0" >> /etc/fstab
echo "/dev ${HOME_DIR}/${NEW_USER_NAME}/dev none bind 0 0" >> /etc/fstab
echo "${HOME_AAP}/${DOMAIN} ${HOME_DIR}/${NEW_USER_NAME}${HOME_AAP}/${DOMAIN} none bind 0 0" >> /etc/fstab
mount -a
	echo "+-------------------------------------------------+OK"

echo
echo "Dando as permissões de uso para pasta do aaPanel..."
#chown ${NEW_USER_NAME}:${NEW_GROUP_NAME} ${HOME_DIR}/${NEW_USER_NAME}${HOME_AAP}/${DOMAIN} -R 2>/dev/null
chown ${USER_WEB}:${GROUP_WEB} ${HOME_DIR}/${NEW_USER_NAME}${HOME_AAP}/${DOMAIN} -R 2>/dev/null
chmod 775 ${HOME_DIR}/${NEW_USER_NAME}${HOME_AAP}/${DOMAIN} -R 2>/dev/null
export NEW_WEB_ID=$(id -u ${USER_WEB})
export NEW_WEB_GROUP_ID=$(id -g ${GROUP_WEB})
chroot ${HOME_DIR}/${NEW_USER_NAME} /bin/bash -c 'useradd -u '${NEW_WEB_ID}' '${USER_WEB}' -s/sbin/nologin' 2>/dev/null
chroot ${HOME_DIR}/${NEW_USER_NAME} /bin/bash -c 'groupadd -g '${NEW_WEB_GROUP_ID}' '${GROUP_WEB}'' 2>/dev/null
	echo "+-------------------------------------------------+OK"

echo
echo "Enjaulando usuario..."
export NEW_USER_ID=$(id -u ${NEW_USER_NAME})
export NEW_USER_GROUP_ID=$(id -g ${NEW_GROUP_NAME})
chroot ${HOME_DIR}/${NEW_USER_NAME} /bin/bash -c 'useradd -u '${NEW_USER_ID}' '${NEW_USER_NAME}'' 2>/dev/null
chroot ${HOME_DIR}/${NEW_USER_NAME} /bin/bash -c 'groupadd -g '${NEW_USER_GROUP_ID}' '${NEW_GROUP_NAME}'' 2>/dev/null
chroot ${HOME_DIR}/${NEW_USER_NAME} /bin/bash -c 'usermod -aG '${GROUP_WEB}' '${NEW_USER_NAME}'' 2>/dev/null
ln -s ${HOME_AAP}/${DOMAIN} ${HOME_DIR}/${NEW_USER_NAME}${HOME_DIR}/${NEW_USER_NAME}
	echo "+-------------------------------------------------+OK"

echo
echo "Configurando o SSH KEY..."
mkdir ${HOME_DIR}/${NEW_USER_NAME}${HOME_DIR}/${NEW_USER_NAME}/.ssh/
ssh-keygen -f ${HOME_DIR}/${NEW_USER_NAME}${HOME_DIR}/${NEW_USER_NAME}/.ssh/id_rsa -t ed25519 -C ${NEW_USER_NAME}@`hostname --fqdn`
cp -rf ${HOME_DIR}/${NEW_USER_NAME}${HOME_DIR}/${NEW_USER_NAME}/.ssh/id_rsa.pub ${HOME_DIR}/${NEW_USER_NAME}${HOME_DIR}/${NEW_USER_NAME}/.ssh/authorized_keys
chown ${NEW_USER_NAME}:${NEW_USER_NAME} -R ${HOME_DIR}/${NEW_USER_NAME}${HOME_DIR}/${NEW_USER_NAME}/.ssh/
echo
cat ${HOME_DIR}/${NEW_USER_NAME}${HOME_DIR}/${NEW_USER_NAME}/.ssh/id_rsa
echo
	echo "+-------------------------------------------------+OK"

echo
echo "Adicionando DNS para o usuario..."
echo "nameserver 1.1.1.1" >> ${HOME_DIR}/${NEW_USER_NAME}/etc/resolv.conf
echo "nameserver 8.8.8.8" >> ${HOME_DIR}/${NEW_USER_NAME}/etc/resolv.conf
echo "nameserver 9.9.9.9" >> ${HOME_DIR}/${NEW_USER_NAME}/etc/resolv.conf
	echo "+-------------------------------------------------+OK"

echo
echo "Criando banner de entrada..."
wget --no-check-certificate --no-cache --no-cookies -N https://raw.githubusercontent.com/thytetgc/shh/master/motd -O ${HOME_DIR}/${NEW_USER_NAME}/etc/motd 2>/dev/null
	echo "+-------------------------------------------------+OK"

	echo
	echo "Usuario --> "${NEW_USER_NAME}""
	echo "Grupo   --> "${NEW_GROUP_NAME}""
	echo 
	echo "CRIADOS"
	echo "+-------------------------------------------------+OK"
	
	echo
    echo "Pressione qualquer tecla para continuar..."
    read msg
	Principal
}

#++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++#

ru() {

	echo -n "Digite o nome de usuario...: "
	read NEW_USER_NAME
	echo -n "Digite o site/dominio......: "
	read DOMAIN
	
NEW_GROUP_NAME=${NEW_USER_NAME}
HOME_DIR="/home"
HOME_AAP="/www/wwwroot"
#++++++++++++++++++++++++++++++++++++++++++++

echo
echo "Desmontando unidades do usuario..."
umount ${HOME_DIR}/${NEW_USER_NAME}/proc
umount ${HOME_DIR}/${NEW_USER_NAME}/dev
umount ${HOME_DIR}/${NEW_USER_NAME}${HOME_AAP}/${DOMAIN}
	echo "+-------------------------------------------------+OK"

echo
echo "Removendo entradas do usuario no fstab..."
sed -i '/'${NEW_USER_NAME}'/d' /etc/fstab
sed -i '/^$/d' /etc/fstab
	echo "+-------------------------------------------------+OK"

echo
echo "Removendo entradas do usuario no ssh..."
sed -i '/'${NEW_USER_NAME}'/d' /etc/ssh/sshd_config
sed -i '/^$/d' /etc/ssh/sshd_config
	echo "+-------------------------------------------------+OK"

echo
echo "Movendo pasta home do usuario..."
\mv -f ${HOME_DIR}/${NEW_USER_NAME} ${HOME_DIR}/${NEW_USER_NAME}.$(date "+%H:%M-%Y-%m-%d")
	echo "+-------------------------------------------------+OK"

echo
echo "Removendo usuario e grupo..."
userdel ${NEW_USER_NAME}
groupdel ${NEW_GROUP_NAME}
	echo "+-------------------------------------------------+OK"

	echo
	echo "Usuario --> "${NEW_USER_NAME}""
	echo "Grupo   --> "${NEW_GROUP_NAME}""
	echo 
	echo "REMOVIDOS"
	echo "+-------------------------------------------------+OK"

	echo
    echo "Pressione qualquer tecla para continuar..."
    read msg
	Principal
}

Principal

echo "[`date`] ==== Fim da rotina..."

#https://serverfault.com/questions/699346/a-proper-way-to-create-a-chrooted-ssh-on-centos-7
