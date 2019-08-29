---
title: Montar o armazenamento de arquivos do Azure em VMs Linux usando SMB | Microsoft Docs
description: Como montar o armazenamento de arquivos do Azure em VMs Linux usando SMB com o CLI do Azure
services: virtual-machines-linux
documentationcenter: virtual-machines-linux
author: cynthn
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/28/2018
ms.author: cynthn
ms.openlocfilehash: c394b013b057a78e99cafc0adde9727d0a75a87c
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70091831"
---
# <a name="mount-azure-file-storage-on-linux-vms-using-smb"></a>Montar o armazenamento de arquivos do Azure em VMs Linux usando SMB

Este artigo mostra como usar o serviço de armazenamento de arquivos do Azure em uma VM do Linux usando uma montagem SMB com o CLI do Azure. O armazenamento de arquivos do Azure oferece compartilhamentos de arquivos na nuvem usando o protocolo SMB padrão. 

O armazenamento de arquivos oferece compartilhamentos de arquivos na nuvem que usam o protocolo SMB padrão. Você pode montar um compartilhamento de arquivos de qualquer sistema operacional que ofereça suporte a SMB 3,0. Ao usar uma montagem SMB no Linux, você obtém backups fáceis para um local de armazenamento de arquivamento robusto e permanente com suporte de um SLA.

Mover arquivos de uma VM para uma montagem SMB hospedada no armazenamento de arquivos é uma ótima maneira de depurar logs. O mesmo compartilhamento SMB pode ser montado localmente em sua estação de trabalho do Mac, Linux ou Windows. O SMB não é a melhor solução para streaming de logs de aplicativos ou Linux em tempo real, porque o protocolo SMB não foi criado para lidar com essas tarefas pesadas de registro em log. Uma ferramenta de camada de registro em log dedicada, como Fluentd, seria uma opção melhor do que o SMB para coletar a saída de log do aplicativo e do Linux.

Este guia requer que você esteja executando o CLI do Azure versão 2.0.4 ou posterior. Execute **az --version** para descobrir a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli). 


## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos chamado MyResource Group no local *leste dos EUA* .

```bash
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Crie uma nova conta de armazenamento, dentro do grupo de recursos que você criou, usando [AZ Storage Account Create](/cli/azure/storage/account). Este exemplo cria uma conta de armazenamento denominada *mySTORAGEACCT\<número aleatório >* e coloca o nome dessa conta de armazenamento na variável **STORAGEACCT**. Os nomes de conta de armazenamento devem ser `$RANDOM` exclusivos, usando acrescenta um número ao final para torná-lo exclusivo.

```bash
STORAGEACCT=$(az storage account create \
    --resource-group "myResourceGroup" \
    --name "mystorageacct$RANDOM" \
    --location eastus \
    --sku Standard_LRS \
    --query "name" | tr -d '"')
```

## <a name="get-the-storage-key"></a>Obter a chave de armazenamento

Quando você cria uma conta de armazenamento, as chaves de conta são criadas em pares para que possam ser giradas sem qualquer interrupção de serviço. Ao alternar para a segunda chave do par, você cria um novo par de chaves. Novas chaves de conta de armazenamento são sempre criadas em pares, portanto, você sempre tem pelo menos uma chave de conta de armazenamento não utilizada pronta para alternar para.

Exiba as chaves da conta de armazenamento usando a [lista de chaves de conta de armazenamento AZ](/cli/azure/storage/account/keys). Este exemplo armazena o valor da chave 1 na variável **STORAGEKEY** .

```bash
STORAGEKEY=$(az storage account keys list \
    --resource-group "myResourceGroup" \
    --account-name $STORAGEACCT \
    --query "[0].value" | tr -d '"')
```

## <a name="create-a-file-share"></a>Criar uma partilha de ficheiros

Crie o compartilhamento de armazenamento de arquivos usando [AZ Storage share Create](/cli/azure/storage/share). 

Os nomes de compartilhamento precisam ter todas as letras minúsculas, números e hifens únicos, mas não podem começar com um hífen. Para obter detalhes completos sobre a nomenclatura de partilhas de ficheiros e ficheiros, veja [Naming and Referencing Shares, Directories, Files, and Metadata (Nomenclatura e Referência de Partilhas, Diretórios, Ficheiros e Metadados)](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata).

Este exemplo cria um compartilhamento chamado *MyShare* com uma cota de 10 Gib. 

```bash
az storage share create --name myshare \
    --quota 10 \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY
```

## <a name="create-a-mount-point"></a>Criar um ponto de montagem

Para montar o compartilhamento de arquivos do Azure em seu computador Linux, você precisa verificar se o pacote **CIFS-utils** está instalado. Para obter instruções de instalação, consulte [instalar o pacote CIFS-utils para sua distribuição do Linux](../../storage/files/storage-how-to-use-files-linux.md#install-cifs-utils).

Os arquivos do Azure usam o protocolo SMB, que se comunica pela porta TCP 445.  Se você estiver tendo problemas para montar o compartilhamento de arquivos do Azure, verifique se o firewall não está bloqueando a porta TCP 445.


```bash
mkdir -p /mnt/MyAzureFileShare
```

## <a name="mount-the-share"></a>Montar o compartilhamento

Monte o compartilhamento de arquivos do Azure no diretório local. 

```bash
sudo mount -t cifs //$STORAGEACCT.file.core.windows.net/myshare /mnt/MyAzureFileShare -o vers=3.0,username=$STORAGEACCT,password=$STORAGEKEY,dir_mode=0777,file_mode=0777,serverino
```

O comando acima usa o comando [Mount](https://linux.die.net/man/8/mount) para montar o compartilhamento de arquivos do Azure e as opções específicas para o [CIFS](https://linux.die.net/man/8/mount.cifs). Especificamente, as opções file_mode e dir_mode definem arquivos e diretórios como `0777`permissão. A `0777` permissão fornece permissões de leitura, gravação e execução para todos os usuários. Você pode alterar essas permissões substituindo os valores por outras [permissões de chmod](https://en.wikipedia.org/wiki/Chmod). Você também pode usar outras opções de [CIFS](https://linux.die.net/man/8/mount.cifs) , como GID ou UID. 


## <a name="persist-the-mount"></a>Persistir a montagem

Quando você reinicializa a VM do Linux, o compartilhamento SMB montado é desmontado durante o desligamento. Para remontar o compartilhamento SMB na inicialização, adicione uma linha ao/etc/fstab. do Linux O Linux usa o arquivo fstab para listar os sistemas de arquivos que precisam ser montados durante o processo de inicialização. A adição do compartilhamento SMB garante que o compartilhamento de armazenamento de arquivos seja um sistema de arquivos montado permanentemente para a VM Linux. Adicionar o compartilhamento SMB de armazenamento de arquivos a uma nova VM é possível quando você usa Cloud-init.

```bash
//myaccountname.file.core.windows.net/mystorageshare /mnt/mymountpoint cifs vers=3.0,username=mystorageaccount,password=myStorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
```
Para aumentar a segurança em ambientes de produção, você deve armazenar suas credenciais fora do fstab.

## <a name="next-steps"></a>Passos Seguintes

- [Usando Cloud-init para personalizar uma VM do Linux durante a criação](using-cloud-init.md)
- [Adicionar um disco a uma VM do Linux](add-disk.md)
- [Criptografar discos em uma VM Linux usando o CLI do Azure](encrypt-disks.md)

