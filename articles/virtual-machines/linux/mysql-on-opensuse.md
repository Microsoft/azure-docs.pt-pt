---
title: Instale o MySQL num VM OpenSUSE em Azure
description: Aprenda a instalar o MySQL numa máquina virtual OpenSUSE Linux em Azure.
services: virtual-machines-linux
author: cynthn
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: how-to
ms.date: 07/11/2018
ms.author: cynthn
ms.openlocfilehash: 608cfca3343b02101ac5e08acf0bec62900d1443
ms.sourcegitcommit: 48e5379c373f8bd98bc6de439482248cd07ae883
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/12/2021
ms.locfileid: "98108658"
---
# <a name="install-mysql-on-a-virtual-machine-running-opensuse-linux-in-azure"></a>Instale o MySQL numa máquina virtual em execução OpenSUSE Linux em Azure

[MySQL](https://www.mysql.com) é uma base de dados SQL de código aberto popular. Este tutorial mostra-lhe como criar uma máquina virtual em execução OpenSUSE Linux e, em seguida, instalar o MySQL.


Se optar por instalar e utilizar a CLI localmente, precisa da versão 2.0 ou posterior da CLI do Azure. Para localizar a versão, execute `az --version`. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)]( /cli/azure/install-azure-cli).

## <a name="create-a-virtual-machine-running-opensuse-linux"></a>Criar uma máquina virtual em execução OpenSUSE Linux

Primeiro, criar um grupo de recursos. Neste exemplo, o grupo de recursos é nomeado *mySQSUSEResourceGroup* e é criado na região *leste dos EUA.*

```azurecli-interactive
az group create --name mySQLSUSEResourceGroup --location eastus
```

Criar o VM. Neste exemplo, o VM é nomeado *myVM* e o tamanho VM é *Standard_D2s_v3*, mas você deve escolher o [tamanho VM](../sizes.md) que você acha mais adequado para a sua carga de trabalho.

```azurecli-interactive
az vm create --resource-group mySQLSUSEResourceGroup \
   --name myVM \
   --image SUSE:openSUSE-Leap:15-2:latest \
   --size Standard_D2s_v3 \
   --generate-ssh-keys
```

Também precisa adicionar uma regra ao grupo de segurança da rede para permitir o tráfego sobre o porto 3306 para o MySQL.

```azurecli-interactive
az vm open-port --port 3306 --resource-group mySQLSUSEResourceGroup --name myVM
```

## <a name="connect-to-the-vm"></a>Ligar à VM

Vais usar o SSH para ligar ao VM. Neste exemplo, o endereço IP público do VM é *10.111.112.113*. Pode ver o endereço IP na saída quando criou o VM.

```azurecli-interactive  
ssh 10.111.112.113
```

 
## <a name="update-the-vm"></a>Atualizar o VM
 
Depois de estar ligado ao VM, instale atualizações e patches do sistema. 
   
```bash
sudo zypper update
```

Siga as instruções para atualizar o seu VM.

## <a name="install-mysql"></a>Instalar o MySQL 


Instale o MySQL no VM sobre SSH. Responda às solicitações conforme apropriado.

```bash
sudo zypper install mysql
```
 
Desa estalade o MySQL para começar quando o sistema arrancar. 

```bash
sudo systemctl enable mysql
```
Verifique se o MySQL está ativado.

```bash
systemctl is-enabled mysql
```

Isto deve voltar: ativado.

Reinicie o servidor.

```bash
sudo reboot
```


## <a name="mysql-password"></a>Senha MySQL

Após a instalação, a palavra-passe de raiz MySQL está vazia por defeito. Executar o script de **\_ \_ instalação mysql seguro** para proteger o MySQL. O script solicita-lhe que altere a palavra-passe de raiz do MySQL, remova contas de utilizador anónimas, desative o registo remoto da raiz, remova bases de dados de teste e recarregue a tabela de privilégios. 

Assim que o servidor reiniciar, volte a ser o VM.

```azurecli-interactive  
ssh 10.111.112.113
```



```bash
mysql_secure_installation
```

## <a name="sign-in-to-mysql"></a>Inscreva-se no MySQL

Agora pode iniciar sôms e inserir o pedido mySQL.

```bash  
sudo mysql -u root -p
```
Isto muda-o para a solicitação do MySQL onde pode emitir declarações SQL para interagir com a base de dados.

Agora, crie um novo utilizador MySQL.

```sql
CREATE USER 'mysqluser'@'localhost' IDENTIFIED BY 'password';
```
   
O ponto e vírgula (;) no final da linha é crucial para terminar o comando.


## <a name="create-a-database"></a>Criar uma base de dados


Crie uma base de dados e conceda permissões ao `mysqluser` utilizador.

```sql
CREATE DATABASE testdatabase;
GRANT ALL ON testdatabase.* TO 'mysqluser'@'localhost' IDENTIFIED BY 'password';
```
   
Os nomes de utilizadores e palavras-passe da base de dados são utilizados apenas por scripts que se ligam à base de dados.  Os nomes das contas dos utilizadores da base de dados não representam necessariamente contas reais de utilizadores no sistema.

Ativar o sinal de outro computador. Neste exemplo, o endereço IP do computador para permitir a inscrição é *10.112.113.114*.

```sql
GRANT ALL ON testdatabase.* TO 'mysqluser'@'10.112.113.114' IDENTIFIED BY 'password';
```
   
Para sair do utilitário de administração da base de dados MySQL, escreva:

```    
quit
```


## <a name="next-steps"></a>Passos seguintes
Para mais informações sobre o MySQL, consulte a [Documentação MySQL](https://dev.mysql.com/doc).
