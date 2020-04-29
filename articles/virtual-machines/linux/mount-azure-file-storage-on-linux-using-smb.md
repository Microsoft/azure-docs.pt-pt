---
title: Armazenamento de ficheiros Mount Azure em VMs Linux usando SMB
description: Como montar o armazenamento de ficheiros Azure em VMs Linux utilizando SMB com o Azure CLI
author: cynthn
ms.service: virtual-machines-linux
ms.topic: article
ms.workload: infrastructure
ms.date: 06/28/2018
ms.author: cynthn
ms.openlocfilehash: 0314095a053087a7d490926c41c6ae386c304919
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80066638"
---
# <a name="mount-azure-file-storage-on-linux-vms-using-smb"></a>Armazenamento de ficheiros Mount Azure em VMs Linux usando SMB

Este artigo mostra-lhe como utilizar o serviço de armazenamento de ficheiros Azure num VM Linux utilizando uma montagem SMB com o Azure CLI. O armazenamento de ficheiros Azure oferece partilhas de ficheiros na nuvem utilizando o protocolo Padrão SMB. 

O armazenamento de ficheiros oferece partilhas de ficheiros na nuvem que utilizam o protocolo Padrão SMB. Pode montar uma partilha de ficheiros de qualquer SISTEMA que suporte o SMB 3.0. Quando você usa uma montagem SMB em Linux, você obtém backups fáceis para um local de armazenamento robusto e permanente de armazenamento que é suportado por um SLA.

Mover ficheiros de um VM para uma montagem SMB que está hospedada no armazenamento de ficheiros é uma ótima maneira de depurar registos. A mesma quota SMB pode ser montada localmente para o seu Mac, Linux ou Windows. A SMB não é a melhor solução para o streaming de registos linux ou de aplicações em tempo real, porque o protocolo SMB não foi construído para lidar com tarefas de exploração tão pesadas. Uma ferramenta dedicada e unificada de camada seletiva, como fluente, seria uma escolha melhor do que a SMB para recolher o Linux e a saída de registo de aplicações.

Este guia requer que esteja a executar a versão Azure CLI 2.0.4 ou posterior. Execute **az --version** para descobrir a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli). 


## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos chamado *myResourceGroup* na localização *dos EUA Orientais.*

```azurecli
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Crie uma nova conta de armazenamento, dentro do grupo de recursos que criou, utilizando a conta de [armazenamento az criar](/cli/azure/storage/account). Este exemplo cria uma conta de armazenamento chamada *mySTORAGEACCT\<random number>* e coloca o nome dessa conta de armazenamento na variável **STORAGEACCT**. Os nomes das contas `$RANDOM` de armazenamento devem ser únicos, utilizando anexações um número até ao fim para torná-lo único.

```azurecli
STORAGEACCT=$(az storage account create \
    --resource-group "myResourceGroup" \
    --name "mystorageacct$RANDOM" \
    --location eastus \
    --sku Standard_LRS \
    --query "name" | tr -d '"')
```

## <a name="get-the-storage-key"></a>Obtenha a chave de armazenamento

Quando cria uma conta de armazenamento, as chaves da conta são criadas em pares para que possam ser rodadas sem qualquer interrupção de serviço. Quando mudas para a segunda chave do par, crias um novo par de chaves. As novas chaves da conta de armazenamento são sempre criadas em pares, por isso tem sempre pelo menos uma chave de conta de armazenamento não utilizada pronta a mudar.

Ver as chaves da conta de armazenamento utilizando a lista de [chaves da conta de armazenamento az](/cli/azure/storage/account/keys). Este exemplo armazena o valor da chave 1 na variável **STORAGEKEY.**

```azurecli
STORAGEKEY=$(az storage account keys list \
    --resource-group "myResourceGroup" \
    --account-name $STORAGEACCT \
    --query "[0].value" | tr -d '"')
```

## <a name="create-a-file-share"></a>Criar uma partilha de ficheiros

Crie a partilha de armazenamento de ficheiros utilizando a criação de partilha de [armazenamento az](/cli/azure/storage/share). 

Os nomes de partilha têm de ser letras minúsculas, números e hífens individuais, mas não podem começar com um hífen. Para obter detalhes completos sobre a nomenclatura de partilhas de ficheiros e ficheiros, veja [Naming and Referencing Shares, Directories, Files, and Metadata (Nomenclatura e Referência de Partilhas, Diretórios, Ficheiros e Metadados)](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata).

Este exemplo cria uma quota chamada *myshare* com uma quota de 10 GiB. 

```azurecli
az storage share create --name myshare \
    --quota 10 \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY
```

## <a name="create-a-mount-point"></a>Criar um ponto de montagem

Para montar a partilha de ficheiros Azure no seu computador Linux, tem de se certificar de que tem o pacote **cifs-utils** instalado. Para obter instruções de instalação, consulte [Instale o pacote cifs-utils para a sua distribuição Linux](../../storage/files/storage-how-to-use-files-linux.md#install-cifs-utils).

O Azure Files utiliza o protocolo SMB, que comunica através da porta TCP 445.  Se tiver problemas em montar a sua parte de ficheiroS Azure, certifique-se de que a sua firewall não está a bloquear a porta TCP 445.


```bash
mkdir -p /mnt/MyAzureFileShare
```

## <a name="mount-the-share"></a>Monte a parte

Monte o arquivo Azure partilha para o diretório local. 

```bash
sudo mount -t cifs //$STORAGEACCT.file.core.windows.net/myshare /mnt/MyAzureFileShare -o vers=3.0,username=$STORAGEACCT,password=$STORAGEKEY,dir_mode=0777,file_mode=0777,serverino
```

O comando acima utiliza o comando de [montagem](https://linux.die.net/man/8/mount) para montar a partilha de ficheiros Azure e opções específicas aos [cifs](https://linux.die.net/man/8/mount.cifs). Especificamente, as opções de file_mode e `0777`dir_mode definir ficheiros e diretórios para permissão . A `0777` permissão dá leitura, escrita e executa permissões a todos os utilizadores. Pode alterar estas permissões substituindo os valores por [outras permissões chmod](https://en.wikipedia.org/wiki/Chmod). Também pode utilizar outras opções [cifs,](https://linux.die.net/man/8/mount.cifs) tais como gid ou uid. 


## <a name="persist-the-mount"></a>Persistir no monte

Quando reiniciar o VM Linux, a parte SMB montada é desmontada durante a paragem. Para remontar a parte SMB na bota, adicione uma linha ao Linux /etc/fstab. O Linux usa o ficheiro fstab para listar os sistemas de ficheiros que precisa de montar durante o processo de arranque. A adição da parte SMB garante que a parte de armazenamento de ficheiros é um sistema de ficheiros montado permanentemente para o VM Linux. Adicionar a partilha de SMB de armazenamento de ficheiros a um novo VM é possível quando utilizar o cloud-init.

```bash
//myaccountname.file.core.windows.net/mystorageshare /mnt/mymountpoint cifs vers=3.0,username=mystorageaccount,password=myStorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
```

Para uma maior segurança em ambientes de produção, deve armazenar as suas credenciais fora da fstab.

## <a name="next-steps"></a>Passos seguintes

- [Usando cloud-init para personalizar um VM Linux durante a criação](using-cloud-init.md)
- [Adicionar um disco a uma VM do Linux](add-disk.md)
- [Encriptação de disco azure para VMs Linux](disk-encryption-overview.md)

