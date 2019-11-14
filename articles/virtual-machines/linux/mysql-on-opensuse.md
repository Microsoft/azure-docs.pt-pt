---
title: Instalar o MySQL em uma VM OpenSUSE no Azure
description: Saiba como instalar o MySQL em uma máquina virtual OpenSUSE Linux no Azure.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 1594e10e-c314-455a-9efb-a89441de364b
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 07/11/2018
ms.author: cynthn
ms.openlocfilehash: d5f2ef2d82cbcced6202ad2c09f23dd734d373b3
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74035589"
---
# <a name="install-mysql-on-a-virtual-machine-running-opensuse-linux-in-azure"></a>Instalar o MySQL numa máquina virtual com o OpenSUSE Linux no Azure

O [MySQL](https://www.mysql.com) é um popular banco de dados SQL de software livre. Este tutorial mostra como criar uma máquina virtual executando o OpenSUSE Linux e, em seguida, instalar o MySQL.


Se optar por instalar e utilizar a CLI localmente, precisa da versão 2.0 ou posterior da CLI do Azure. Para localizar a versão, execute `az --version`. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).

## <a name="create-a-virtual-machine-running-opensuse-linux"></a>Criar uma máquina virtual executando o OpenSUSE Linux

Primeiro, crie um grupo de recursos. Neste exemplo, o grupo de recursos é denominado *mySQSUSEResourceGroup* e é criado na região *leste dos EUA* .

```azurecli-interactive
az group create --name mySQLSUSEResourceGroup --location eastus
```

Crie a VM. Neste exemplo, a VM é denominada *myVM* e o tamanho da vm é *Standard_D2s_v3*, mas você deve escolher o [tamanho da VM](sizes.md) que você considera mais apropriado para sua carga de trabalho.

```azurecli-interactive
az vm create --resource-group mySQLSUSEResourceGroup \
   --name myVM \
   --image openSUSE-Leap \
   --size Standard_D2s_v3 \
   --generate-ssh-keys
```

Você também precisa adicionar uma regra ao grupo de segurança de rede para permitir o tráfego pela porta 3306 para MySQL.

```azurecli-interactive
az vm open-port --port 3306 --resource-group mySQLSUSEResourceGroup --name myVM
```

## <a name="connect-to-the-vm"></a>Ligar à VM

Você usará o SSH para se conectar à VM. Neste exemplo, o endereço IP público da VM é *10.111.112.113*. Você pode ver o endereço IP na saída quando criou a VM.

```azurecli-interactive  
ssh 10.111.112.113
```

 
## <a name="update-the-vm"></a>Atualizar a VM
 
Depois que você estiver conectado à VM, instale atualizações e patches do sistema. 
   
```bash
sudo zypper update
```

Siga os prompts para atualizar sua VM.

## <a name="install-mysql"></a>Instalar MySQL 


Instale o MySQL na VM por SSH. Responda a prompts conforme apropriado.

```bash
sudo zypper install mysql
```
 
Defina MySQL para iniciar quando o sistema for inicializado. 

```bash
sudo systemctl enable mysql
```
Verifique se o MySQL está habilitado.

```bash
systemctl is-enabled mysql
```

Isso deve retornar: habilitado.

Reinicie o servidor.

```bash
sudo reboot
```


## <a name="mysql-password"></a>Senha do MySQL

Após a instalação, a senha raiz do MySQL fica vazia por padrão. Execute o script de **instalação do mysql\_secure\_** para proteger o MySQL. O script solicita que você altere a senha raiz do MySQL, remova as contas de usuário anônimos, desabilite a entrada raiz remota, remova os bancos de dados de teste e recarregue a tabela de privilégios. 

Depois que o servidor for reinicializado, use o ssh para a VM novamente.

```azurecli-interactive  
ssh 10.111.112.113
```



```bash
mysql_secure_installation
```

## <a name="sign-in-to-mysql"></a>Entrar no MySQL

Agora você pode entrar e entrar no prompt do MySQL.

```bash  
mysql -u root -p
```
Isso o alterna para o prompt do MySQL, onde você pode emitir instruções SQL para interagir com o banco de dados.

Agora, crie um novo usuário do MySQL.

```sql
CREATE USER 'mysqluser'@'localhost' IDENTIFIED BY 'password';
```
   
O ponto-e-vírgula (;) no final da linha, é crucial para encerrar o comando.


## <a name="create-a-database"></a>Criar uma base de dados


Crie um banco de dados e conceda as permissões de usuário do `mysqluser`.

```sql
CREATE DATABASE testdatabase;
GRANT ALL ON testdatabase.* TO 'mysqluser'@'localhost' IDENTIFIED BY 'password';
```
   
Nomes de usuário e senhas de banco de dados são usados somente por scripts que se conectam ao banco de dados.  Os nomes de conta de usuário do banco de dados não representam necessariamente contas de usuário reais no sistema.

Habilite a entrada de outro computador. Neste exemplo, o endereço IP do computador para permitir a entrada é *10.112.113.114*.

```sql
GRANT ALL ON testdatabase.* TO 'mysqluser'@'10.112.113.114' IDENTIFIED BY 'password';
```
   
Para sair do utilitário de administração do banco de dados MySQL, digite:

```    
quit
```


## <a name="next-steps"></a>Passos seguintes
Para obter detalhes sobre o MySQL, consulte a [documentação do MySQL](https://dev.mysql.com/doc).




