---
title: Instale o MySQL num OpenSUSE VM em Azure
description: Aprenda a instalar o MySQL numa máquina virtual OpenSUSE Linux virtual em Azure.
services: virtual-machines-linux
author: cynthn
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 07/11/2018
ms.author: cynthn
ms.openlocfilehash: 0d3f0a61da3654c31c99cfac43c86b081876f700
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78944581"
---
# <a name="install-mysql-on-a-virtual-machine-running-opensuse-linux-in-azure"></a>Instale o MySQL numa máquina virtual que executa o OpenSUSE Linux em Azure

[MySQL](https://www.mysql.com) é uma base de dados SQL popular e de código aberto. Este tutorial mostra-lhe como criar uma máquina virtual que executa o OpenSUSE Linux e, em seguida, instalar o MySQL.


Se optar por instalar e utilizar a CLI localmente, precisa da versão 2.0 ou posterior da CLI do Azure. Para localizar a versão, execute `az --version`. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)]( /cli/azure/install-azure-cli).

## <a name="create-a-virtual-machine-running-opensuse-linux"></a>Crie uma máquina virtual que execute o OpenSUSE Linux

Primeiro, criar um grupo de recursos. Neste exemplo, o grupo de recursos é nomeado *mySQSUSEResourceGroup* e é criado na região *leste dos EUA.*

```azurecli-interactive
az group create --name mySQLSUSEResourceGroup --location eastus
```

Crie o VM. Neste exemplo, o VM é nomeado *myVM* e o tamanho VM é *Standard_D2s_v3*, mas você deve escolher o [tamanho VM](sizes.md) que você acha mais adequado para a sua carga de trabalho.

```azurecli-interactive
az vm create --resource-group mySQLSUSEResourceGroup \
   --name myVM \
   --image openSUSE-Leap \
   --size Standard_D2s_v3 \
   --generate-ssh-keys
```

Também precisa adicionar uma regra ao grupo de segurança da rede para permitir o tráfego sobre o porto 3306 para o MySQL.

```azurecli-interactive
az vm open-port --port 3306 --resource-group mySQLSUSEResourceGroup --name myVM
```

## <a name="connect-to-the-vm"></a>Ligar à VM

Usará sSH para ligar ao VM. Neste exemplo, o endereço IP público do VM é *10.111.112.113*. Pode ver o endereço IP na saída quando criou o VM.

```azurecli-interactive  
ssh 10.111.112.113
```

 
## <a name="update-the-vm"></a>Atualizar o VM
 
Depois de estar ligado ao VM, instale atualizações e patches do sistema. 
   
```bash
sudo zypper update
```

Siga as instruções para atualizar o seu VM.

## <a name="install-mysql"></a>Instale o MySQL 


Instale o MySQL no VM sobre SSH. Responda às solicitações conforme apropriado.

```bash
sudo zypper install mysql
```
 
Desloque o MySQL para começar quando o sistema arrancar. 

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

Após a instalação, a palavra-passe raiz MySQL está vazia por padrão. Executar o script de **instalação seguro\_\_mysql** para proteger o MySQL. O script pede-lhe que altere a palavra-passe da raiz MySQL, remova contas de utilizador anónimas, desative o sinal de raiz remota, remova as bases de dados de teste e recarregue a tabela de privilégios. 

Assim que o servidor reiniciar, sSH para o VM novamente.

```azurecli-interactive  
ssh 10.111.112.113
```



```bash
mysql_secure_installation
```

## <a name="sign-in-to-mysql"></a>Inscreva-se no MySQL

Agora pode iniciar sessão e introduzir o aviso MySQL.

```bash  
mysql -u root -p
```
Isto muda-o para o aviso MySQL onde pode emitir declarações SQL para interagir com a base de dados.

Agora, crie um novo utilizador MySQL.

```sql
CREATE USER 'mysqluser'@'localhost' IDENTIFIED BY 'password';
```
   
O ponto-e-vírgula (;) no final da linha é crucial para acabar com o comando.


## <a name="create-a-database"></a>Criar uma base de dados


Crie uma base `mysqluser` de dados e conceda permissões ao utilizador.

```sql
CREATE DATABASE testdatabase;
GRANT ALL ON testdatabase.* TO 'mysqluser'@'localhost' IDENTIFIED BY 'password';
```
   
Os nomes e palavras-passe dos utilizadores da base de dados são utilizados apenas por scripts que se ligam à base de dados.  Os nomes da conta de utilizador da base de dados não representam necessariamente contas reais de utilizador no sistema.

Ativar o sinal de outro computador. Neste exemplo, o endereço IP do computador para permitir o início é *de 10.112.113.114*.

```sql
GRANT ALL ON testdatabase.* TO 'mysqluser'@'10.112.113.114' IDENTIFIED BY 'password';
```
   
Para sair do utilitário de administração da base de dados MySQL, escreva:

```    
quit
```


## <a name="next-steps"></a>Passos seguintes
Para mais detalhes sobre o MySQL, consulte a [Documentação MySQL](https://dev.mysql.com/doc).




