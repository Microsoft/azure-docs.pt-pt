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
ms.openlocfilehash: 21ad3f9baf4b8e117f881d9a36fc606af04e17a5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66158446"
---
# <a name="how-to-install-mysql-on-azure"></a>Como instalar o MySQL no Azure
Neste artigo, aprenderá como instalar e configurar o MySQL numa máquina virtual do Azure em execução no Linux.


> [!NOTE]
> Já tem de ter uma máquina de virtual do Microsoft Azure em execução no Linux para concluir este tutorial. Consulte a [tutorial de VM Linux do Azure](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) para criar e configurar uma VM do Linux com `mysqlnode` como o nome da VM e `azureuser` como utilizador antes de continuar.
> 
> 

Neste caso, utilize a porta de 3306 como a porta MySQL.  

Nós usaremos o pacote do repositório para instalar MySQL5.6 como um exemplo deste artigo. Na verdade, MySQL5.6 tem mais de melhoria no desempenho do que MySQL5.5.  Obter mais informações [aqui](http://www.mysqlperformanceblog.com/2013/02/18/is-mysql-5-6-slower-than-mysql-5-5/).

## <a name="install-mysql56-on-ubuntu"></a>Instalar MySQL5.6 no Ubuntu
Utilizamos uma VM do Linux com o Ubuntu.


### <a name="install-mysql"></a>Instalar MySQL

Instalar MySQL 5.6 do servidor ao mudar para o `root` utilizador:

```bash  
sudo su -
```

Instale o servidor mysql 5.6:

```bash  
apt-get update
apt-get -y install mysql-server-5.6
```

  
Durante a instalação, verá uma janela de caixa de diálogo apresentada perguntar-lhe definir senha de raiz do MySQL abaixo e que tem de definir a palavra-passe aqui.
  
![image](./media/mysql-install/virtual-machines-linux-install-mysql-p1.png)

Introduza a palavra-passe novamente para confirmar.

![image](./media/mysql-install/virtual-machines-linux-install-mysql-p2.png)

### <a name="sign-in"></a>Iniciar sessão
  
Quando terminar a instalação do servidor MySQL, o serviço de MySQL será iniciado automaticamente. Pode iniciar sessão no servidor MySQL com o `root` utilizador e a palavra-passe.

```bash  
mysql -uroot -p
```


### <a name="manage-the-mysql-service"></a>Gerir o serviço de MySQL

Obter estado do serviço de MySQL

```bash   
service mysql status
```
  
Iniciar o serviço de MySQL

```bash  
service mysql start
```
  
Parar o serviço de MySQL

```bash  
service mysql stop
```
  
Reinicie o serviço de MySQL

```bash  
service mysql restart
```

## <a name="install-mysql-on-red-hat-os-centos-oracle-linux"></a>Instalar o MySQL no sistema operacional de Red Hat, CentOS, Oracle Linux
Nós usaremos VM do Linux com o CentOS ou Oracle Linux aqui.

### <a name="add-the-mysql-yum-repository"></a>Adicione o repositório de yum do MySQL
    
Mude para `root` utilizador:

```bash  
sudo su -
```

Transfira e instale o pacote de versão do MySQL:

```bash  
wget https://repo.mysql.com/mysql-community-release-el6-5.noarch.rpm
yum localinstall -y mysql-community-release-el6-5.noarch.rpm
```

### <a name="enable-the-mysql-repository"></a>Ativar o repositório de MySQL
Edite abaixo ficheiro para ativar o repositório do MySQL para o download do pacote de MySQL5.6.

```bash  
vim /etc/yum.repos.d/mysql-community.repo
```

  
Atualize cada valor deste ficheiro para abaixo:

```  
\# *Enable to use MySQL 5.6*
  
[mysql56-community]
name=MySQL 5.6 Community Server
  
baseurl=http://repo.mysql.com/yum/mysql-5.6-community/el/6/$basearch/
  
enabled=1
  
gpgcheck=1
  
gpgkey=file:/etc/pki/rpm-gpg/RPM-GPG-KEY-mysql
```

### <a name="install-mysql"></a>Instalar MySQL 

Instale o MySQL a partir do repositório.

```bash  
yum install mysql-community-server
```
  
Serão instalados o pacote de RPM do MySQL e todos os pacotes relacionados.


## <a name="manage-the-mysql-service"></a>Gerir o serviço de MySQL
  
Verificar o estado do serviço do servidor MySQL:

```bash  
service mysqld status\
```
  
Verifique se o servidor de porta do MySQL predefinido está em execução:

```bash  
netstat  –tunlp|grep 3306
```

Inicie o servidor MySQL:

```bash
service mysqld start
```

Pare o servidor MySQL:

```bash
service mysqld stop
```

Definir o MySQL para iniciar quando o arranque de sistema-up:

```bash
chkconfig mysqld on
```

## <a name="install-mysql-on-suse-linux"></a>Instalar o MySQL no SUSE Linux

Nós usaremos VM do Linux com o OpenSUSE aqui.

### <a name="download-and-install-mysql-server"></a>Transferir e instalar o servidor MySQL
  
Mude para `root` utilizador através do comando abaixo:  

```bash  
sudo su -
```
  
Transfira e instale o pacote do MySQL:

```bash  
zypper update
zypper install mysql-server mysql-devel mysql
```

### <a name="manage-the-mysql-service"></a>Gerir o serviço de MySQL
  
Verifique o estado do servidor MySQL:

```bash  
rcmysql status
```
  
Verifique se a porta predefinida do servidor MySQL:

```bash  
netstat  –tunlp|grep 3306
```

Inicie o servidor MySQL:

```bash
rcmysql start
```

Pare o servidor MySQL:

```bash
rcmysql stop
```

Definir o MySQL para iniciar quando o arranque de sistema-up:

```bash
insserv mysql
```

## <a name="next-step"></a>Passo seguinte
Para obter mais informações, consulte a [MySQL](https://www.mysql.com/) Web site.

