---
title: Armazenamento de ficheiros do Azure de montagem em VMs do Linux com o SMB | Documentos da Microsoft
description: Como montar o armazenamento de ficheiros do Azure em VMs do Linux através de SMB com a CLI do Azure
services: virtual-machines-linux
documentationcenter: virtual-machines-linux
author: cynthn
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/28/2018
ms.author: cynthn
ms.openlocfilehash: bc4de2d406ea2c1814825daf89d5e6a576c94036
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57764817"
---
# <a name="mount-azure-file-storage-on-linux-vms-using-smb"></a>Armazenamento de ficheiros do Azure de montagem em VMs do Linux através de SMB

Este artigo mostra-lhe como utilizar o serviço de armazenamento de ficheiros do Azure numa VM do Linux através de uma montagem SMB com a CLI do Azure. Armazenamento de ficheiros do Azure oferece partilhas de ficheiros na cloud utilizando o protocolo SMB padrão. 

Armazenamento de ficheiros oferece partilhas de ficheiros na cloud que utilizam o protocolo SMB padrão. Pode montar uma partilha de ficheiros de qualquer sistema operacional que suporta o SMB 3.0. Quando utiliza uma montagem SMB no Linux, obtém as cópias de segurança fácil para um robusto e permanente arquivamento local de armazenamento que é suportado por um SLA.

Mover ficheiros a partir de uma VM para uma montagem SMB que está alojada no armazenamento de ficheiros é uma excelente forma de registos de depuração. A mesma partilha SMB pode ser montada localmente para a estação de trabalho do Mac, Linux ou Windows. SMB não é a melhor solução para Linux de transmissão em fluxo ou aplicativo registra em log em tempo real, como o protocolo SMB não foi criado para lidar com tais obrigações de registo pesada. Uma ferramenta de camada de Registro em log dedicado de unificação como Fluentd seria uma opção melhor do que o SMB para a recolha de Linux e aplicação de registo de saída.

Este guia requer que está a executar a CLI do Azure versão 2.0.4 ou posterior. Execute **az --version** para descobrir a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli). 


## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos chamado *myResourceGroup* no *E.U.A. Leste* localização.

```bash
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Criar uma nova conta de armazenamento, no grupo de recursos que criou, utilizando [criar conta de armazenamento az](/cli/azure/storage/account). Este exemplo cria uma conta de armazenamento com o nome *mySTORAGEACCT<random number>*  e coloca o nome dessa conta de armazenamento na variável **STORAGEACCT**. Nomes de conta de armazenamento tem de ser exclusivos, usando `$RANDOM` associa um número ao final para o tornar único.

```bash
STORAGEACCT=$(az storage account create \
    --resource-group "myResourceGroup" \
    --name "mystorageacct$RANDOM" \
    --location eastus \
    --sku Standard_LRS \
    --query "name" | tr -d '"')
```

## <a name="get-the-storage-key"></a>Obter a chave de armazenamento

Quando cria uma conta de armazenamento, as chaves de conta são criadas em pares, de modo a que pode ser girados sem qualquer interrupção do serviço. Quando mudar para a segunda chave no par, criar um novo par de chaves. Novas chaves de conta de armazenamento são sempre criadas em pares, para que sempre tenha, pelo menos, uma chave de conta de armazenamento não utilizados pronta para mudar para o.

Exibir as chaves de conta de armazenamento usando [lista de chaves de conta de armazenamento az](/cli/azure/storage/account/keys). Neste exemplo armazena o valor da chave 1 no **STORAGEKEY** variável.

```bash
STORAGEKEY=$(az storage account keys list \
    --resource-group "myResourceGroup" \
    --account-name $STORAGEACCT \
    --query "[0].value" | tr -d '"')
```

## <a name="create-a-file-share"></a>Criar uma partilha de ficheiros

Criar a partilha de ficheiros armazenamento com [criar partilha de armazenamento az](/cli/azure/storage/share). 

Nomes de partilha têm de estar todas as letras minúsculas, números e hífenes, mas não podem começar com um hífen. Para obter detalhes completos sobre a nomenclatura de partilhas de ficheiros e ficheiros, veja [Naming and Referencing Shares, Directories, Files, and Metadata (Nomenclatura e Referência de Partilhas, Diretórios, Ficheiros e Metadados)](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata).

Este exemplo cria uma partilha denominada *myshare* com uma quota de 10 GiB. 

```bash
az storage share create --name myshare \
    --quota 10 \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY
```

## <a name="create-a-mount-point"></a>Criar um ponto de montagem

Para montar a partilha de ficheiros do Azure no seu computador Linux, tem de certificar-se de que tem o **utils do cifs** pacote instalado. Para obter instruções de instalação, consulte [instalar o pacote de utils do cifs para a distribuição de Linux](../../storage/files/storage-how-to-use-files-linux.md#install-cifs-utils).

Os ficheiros do Azure utiliza o protocolo SMB, que comunica através da porta TCP 445.  Se estiver a ter problemas ao montar a partilha de ficheiros do Azure, certifique-se de que a firewall não está a bloquear a porta TCP 445.


```bash
mkdir -p /mnt/MyAzureFileShare
```

## <a name="mount-the-share"></a>Montar a partilha

Monte a partilha de ficheiros do Azure para o diretório local. 

```bash
sudo mount -t cifs //$STORAGEACCT.file.core.windows.net/myshare /mnt/MyAzureFileShare -o vers=3.0,username=$STORAGEACCT,password=$STORAGEKEY,dir_mode=0777,file_mode=0777,serverino
```

O comando acima utiliza a [montar](https://linux.die.net/man/8/mount) comandos para montar a partilha de ficheiros do Azure e as opções específicas [cifs](https://linux.die.net/man/8/mount.cifs). Especificamente, o file_mode dir_mode opções e conjunto de arquivos e diretórios à permissão `0777`. O `0777` dá permissão para ler, escrever e permissões a todos os utilizadores de execução. Pode alterar estas permissões, substituindo os valores com os outros [chmod permissões](https://en.wikipedia.org/wiki/Chmod). Também pode usar outro [cifs](https://linux.die.net/man/8/mount.cifs) opções, como gid ou uid. 


## <a name="persist-the-mount"></a>Manter a montagem

Quando reiniciar a VM do Linux, a partilha montada do SMB for desmontada durante o encerramento. Para voltar a montar a partilha SMB no arranque, adicione uma linha para o /etc/fstab. de Linux. Linux utiliza o ficheiro fstab para listar os sistemas de arquivos que ele precisa de montagem durante o processo de inicialização. Adicionar a partilha SMB garante que a partilha de armazenamento de ficheiros é um sistema de ficheiro permanentemente instalado para a VM do Linux. É possível adicionar o armazenamento de ficheiros de partilha do SMB para uma nova VM ao utilizar o cloud-init.

```bash
//myaccountname.file.core.windows.net/mystorageshare /mnt/mymountpoint cifs vers=3.0,username=mystorageaccount,password=myStorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
```
Para maior segurança em ambientes de produção, deve armazenar as suas credenciais fora fstab.

## <a name="next-steps"></a>Passos Seguintes

- [Utilizar o cloud-init para personalizar uma VM do Linux durante a criação](using-cloud-init.md)
- [Adicionar um disco a uma VM do Linux](add-disk.md)
- [Encriptar discos numa VM do Linux ao utilizar a CLI do Azure](encrypt-disks.md)

