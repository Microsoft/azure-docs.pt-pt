---
title: Instalar o MySQL numa VM OpenSUSE no Azure | Documentos da Microsoft
description: Aprenda a instalar o MySQL numa máquina Virtual do OpenSUSE Linux no Azure.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 1594e10e-c314-455a-9efb-a89441de364b
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/11/2018
ms.author: za-rhoads
ms.openlocfilehash: 19f53449e21b39eb212f94b100eaf5d26ca4cf13
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58006660"
---
# <a name="install-mysql-on-a-virtual-machine-running-opensuse-linux-in-azure"></a>Instalar o MySQL numa máquina virtual com o OpenSUSE Linux no Azure

[MySQL](https://www.mysql.com) é uma base de dados do SQL popular, código-fonte aberto. Este tutorial mostra-lhe como criar uma máquina virtual OpenSUSE Linux, em seguida, instalar o MySQL.


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar a CLI localmente, precisa da versão 2.0 ou posterior da CLI do Azure. Para localizar a versão, execute `az --version`. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).

## <a name="create-a-virtual-machine-running-opensuse-linux"></a>Criar uma máquina virtual OpenSUSE Linux

Primeiro, crie um grupo de recursos. Neste exemplo, o grupo de recursos é designado *mySQSUSEResourceGroup* e é criado no *E.U.A. Leste* região.

```azurecli-interactive
az group create --name mySQLSUSEResourceGroup --location eastus
```

Crie a VM. Neste exemplo, a VM é o nome *myVM* e o tamanho da VM é *Standard_D2s_v3*, mas deve escolher o [tamanho da VM](sizes.md) acha que é mais adequada para a sua carga de trabalho.

```azurecli-interactive
az vm create --resource-group mySQLSUSEResourceGroup \
   --name myVM \
   --image openSUSE-Leap \
   --size Standard_D2s_v3 \
   --generate-ssh-keys
```

Também terá de adicionar uma regra para o grupo de segurança de rede para permitir o tráfego através da porta 3306 para MySQL.

```azurecli-interactive
az vm open-port --port 3306 --resource-group mySQLSUSEResourceGroup --name myVM
```

## <a name="connect-to-the-vm"></a>Ligar à VM

Irá utilizar o SSH para ligar à VM. Neste exemplo, é o endereço IP público da VM *10.111.112.113*. Pode ver o endereço IP na saída quando criou a VM.

```azurecli-interactive  
ssh 10.111.112.113
```

 
## <a name="update-the-vm"></a>Atualize a VM
 
Quando estiver ligado à VM, instale as atualizações do sistema e patches. 
   
```bash
sudo zypper update
```

Siga as instruções para atualizar a sua VM.

## <a name="install-mysql"></a>Instalar MySQL 


Instale o MySQL na VM através de SSH. Responda a pedidos conforme apropriado.

```bash
sudo zypper install mysql
```
 
Definir o MySQL para iniciar quando o sistema for inicializado. 

```bash
sudo systemctl enable mysql
```
Certifique-se de que o MySQL está ativado.

```bash
systemctl is-enabled mysql
```

Isto deverá devolver: ativado.

Reinicie o servidor.

```bash
sudo reboot
```


## <a name="mysql-password"></a>Palavra-passe do MySQL

Após a instalação, a palavra-passe de raiz do MySQL é vazia por padrão. Executar o **mysql\_segura\_instalação** script para proteger o MySQL. O script pede-lhe para alterar a palavra-passe de raiz do MySQL, remova as contas de utilizador anónimo, desativar o início de sessão remoto raiz, remover bases de dados de teste e recarregar a tabela de privilégios. 

Assim que o servidor ser reiniciado, o ssh à VM novamente.

```azurecli-interactive  
ssh 10.111.112.113
```



```bash
mysql_secure_installation
```

## <a name="sign-in-to-mysql"></a>Inicie sessão no MySQL

Pode agora iniciar sessão e introduza a linha de comandos do MySQL.

```bash  
mysql -u root -p
```
Esse procedimento alterna para a linha de comandos do MySQL em que pode emitir instruções SQL para interagir com a base de dados.

Agora, crie um novo utilizador do MySQL.

```sql
CREATE USER 'mysqluser'@'localhost' IDENTIFIED BY 'password';
```
   
A ponto e vírgula (;) no final da linha é crucial para terminar o comando.


## <a name="create-a-database"></a>Criar uma base de dados


Criar uma base de dados e conceder a `mysqluser` permissões de utilizador.

```sql
CREATE DATABASE testdatabase;
GRANT ALL ON testdatabase.* TO 'mysqluser'@'localhost' IDENTIFIED BY 'password';
```
   
Os nomes de utilizador de base de dados e de palavras-passe só são utilizadas pelos scripts ligar à base de dados.  Os nomes de conta de utilizador de base de dados não representa necessariamente contas de utilizador reais no sistema.

Ative o início de sessão de outro computador. Neste exemplo, é o endereço IP do computador para permitir o início de sessão *10.112.113.114*.

```sql
GRANT ALL ON testdatabase.* TO 'mysqluser'@'10.112.113.114' IDENTIFIED BY 'password';
```
   
Para sair do utilitário de administração de banco de dados MySQL, escreva:

```    
quit
```


## <a name="next-steps"></a>Passos Seguintes
Para obter detalhes sobre o MySQL, consulte a [MySQL documentação](https://dev.mysql.com/doc).




