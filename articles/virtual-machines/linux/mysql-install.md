---
title: Configurar o MySQL numa VM do Linux no Azure | Documentos da Microsoft
description: Saiba como instalar a pilha de MySQL numa máquina virtual do Linux (Ubuntu ou Red Hat família de SO) no Azure
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: 153bae7c-897b-46b3-bd86-192a6efb94fa
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/01/2016
ms.author: cynthn
ms.openlocfilehash: f9e0582a1338bcae7b330c7ece7c3d8cc8593cfa
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58004511"
---
# <a name="how-to-install-mysql-on-azure"></a>Como instalar o MySQL no Azure
Neste artigo, aprenderá como instalar e configurar o MySQL numa máquina virtual do Azure em execução no Linux.

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="install-mysql-on-your-virtual-machine"></a>Instalar o MySQL na sua máquina virtual
> [!NOTE]
> Já tem de ter uma máquina de virtual do Microsoft Azure em execução no Linux para concluir este tutorial. Consulte a [tutorial de VM Linux do Azure](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) para criar e configurar uma VM do Linux com `mysqlnode` como o nome da VM e `azureuser` como utilizador antes de continuar.
> 
> 

Neste caso, utilize a porta de 3306 como a porta MySQL.  

Ligue-se para o Linux VM criada por meio do putty. Se esta for a primeira vez que utilizar VM do Linux do Azure, veja como utilizar o putty ligar a uma VM do Linux [aqui](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Nós usaremos o pacote do repositório para instalar MySQL5.6 como um exemplo deste artigo. Na verdade, MySQL5.6 tem mais de melhoria no desempenho do que MySQL5.5.  Obter mais informações [aqui](http://www.mysqlperformanceblog.com/2013/02/18/is-mysql-5-6-slower-than-mysql-5-5/).

### <a name="how-to-install-mysql56-on-ubuntu"></a>Como instalar MySQL5.6 no Ubuntu
Nós usaremos VM do Linux com o Ubuntu do Azure aqui.

* Passo 1: Instalar MySQL Server 5.6 comutador para `root` utilizador:
  
            #[azureuser@mysqlnode:~]sudo su -
  
    Instale o servidor mysql 5.6:
  
            #[root@mysqlnode ~]# apt-get update
            #[root@mysqlnode ~]# apt-get -y install mysql-server-5.6
  
    Durante a instalação, verá uma janela de caixa de diálogo apresentada perguntar-lhe definir senha de raiz do MySQL abaixo e que tem de definir a palavra-passe aqui.
  
    ![image](./media/mysql-install/virtual-machines-linux-install-mysql-p1.png)

    Introduza a palavra-passe novamente para confirmar.

    ![image](./media/mysql-install/virtual-machines-linux-install-mysql-p2.png)

* Passo 2: Servidor MySQL de início de sessão
  
    Quando terminar a instalação do servidor MySQL, o serviço de MySQL será iniciado automaticamente. Pode iniciar sessão com o servidor MySQL `root` utilizador.
    Utilize o comando a palavra-passe de início de sessão e de entrada abaixo.
  
             #[root@mysqlnode ~]# mysql -uroot -p
* Passo 3: Gerir o serviço de MySQL em execução
  
    (a) a obter estado do serviço de MySQL
  
             #[root@mysqlnode ~]# service mysql status
  
    (b) iniciar o serviço de MySQL
  
             #[root@mysqlnode ~]# service mysql start
  
    (c) parar o serviço de MySQL
  
             #[root@mysqlnode ~]# service mysql stop
  
    (d) reinicie o serviço de MySQL
  
             #[root@mysqlnode ~]# service mysql restart

### <a name="how-to-install-mysql-on-red-hat-os-family-like-centos-oracle-linux"></a>Como instalar o MySQL numa família de SO do Red Hat, como CentOS, Oracle Linux
Nós usaremos VM do Linux com o CentOS ou Oracle Linux aqui.

* Passo 1: Adicione o repositório de MySQL Yum comutador para `root` utilizador:
  
            #[azureuser@mysqlnode:~]sudo su -
  
    Transfira e instale o pacote de versão do MySQL:
  
            #[root@mysqlnode ~]# wget https://repo.mysql.com/mysql-community-release-el6-5.noarch.rpm
            #[root@mysqlnode ~]# yum localinstall -y mysql-community-release-el6-5.noarch.rpm
* Passo 2: Edite abaixo ficheiro para ativar o repositório do MySQL para o download do pacote de MySQL5.6.
  
            #[root@mysqlnode ~]# vim /etc/yum.repos.d/mysql-community.repo
  
    Atualize cada valor deste ficheiro para abaixo:
  
        \# *Enable to use MySQL 5.6*
  
        [mysql56-community]
        name=MySQL 5.6 Community Server
  
        baseurl=http://repo.mysql.com/yum/mysql-5.6-community/el/6/$basearch/
  
        enabled=1
  
        gpgcheck=1
  
        gpgkey=file:/etc/pki/rpm-gpg/RPM-GPG-KEY-mysql
* Passo 3: Instale o MySQL a partir do repositório de MySQL instalar MySQL:
  
           #[root@mysqlnode ~]#yum install mysql-community-server
  
    Pacote de RPM do MySQL e todos os pacotes relacionados serão instalados.
* Passo 4: Gerir o serviço de MySQL em execução
  
    (a) verificar o estado do serviço do servidor MySQL:
  
           #[root@mysqlnode ~]#service mysqld status
  
    (b) verifique se o servidor de porta do MySQL predefinido está em execução:
  
           #[root@mysqlnode ~]#netstat  –tunlp|grep 3306

    (c) inicie o servidor MySQL:

           #[root@mysqlnode ~]#service mysqld start

    (d) pare o servidor MySQL:

           #[root@mysqlnode ~]#service mysqld stop

    (e) MySQL de conjunto para iniciar quando o arranque de sistema-up:

           #[root@mysqlnode ~]#chkconfig mysqld on


### <a name="how-to-install-mysql-on-suse-linux"></a>Como instalar o MySQL no SUSE Linux
Nós usaremos VM do Linux com o OpenSUSE aqui.

* Passo 1: Transferir e instalar o servidor MySQL
  
    Mude para `root` utilizador através do comando abaixo:  
  
           #sudo su -
  
    Transfira e instale o pacote do MySQL:
  
           #[root@mysqlnode ~]# zypper update
  
           #[root@mysqlnode ~]# zypper install mysql-server mysql-devel mysql
* Passo 2: Gerir o serviço de MySQL em execução
  
    (a) verificar o estado do servidor MySQL:
  
           #[root@mysqlnode ~]# rcmysql status
  
    (b) verifique se a porta predefinida do servidor MySQL:
  
           #[root@mysqlnode ~]# netstat  –tunlp|grep 3306

    (c) inicie o servidor MySQL:

           #[root@mysqlnode ~]# rcmysql start

    (d) pare o servidor MySQL:

           #[root@mysqlnode ~]# rcmysql stop

    (e) MySQL de conjunto para iniciar quando o arranque de sistema-up:

           #[root@mysqlnode ~]# insserv mysql

### <a name="next-step"></a>Passo seguinte
Encontrar mais informações sobre MySQL e utilização [aqui](https://www.mysql.com/).

