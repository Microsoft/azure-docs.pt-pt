---
title: Armazenamento de arquivos Mount Azure em LMs Linux usando SMB
description: Como montar o armazenamento de ficheiros Azure em LMs Linux usando SMB com o Azure CLI
author: cynthn
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.workload: infrastructure
ms.date: 06/28/2018
ms.author: cynthn
ms.openlocfilehash: 45042f474f39b3f64d45913905765016fae3cb26
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102551798"
---
# <a name="mount-azure-file-storage-on-linux-vms-using-smb"></a>Armazenamento de arquivos Mount Azure em LMs Linux usando SMB

Este artigo mostra-lhe como utilizar o serviço de armazenamento de ficheiros Azure num VM Linux utilizando um suporte SMB com o Azure CLI. O armazenamento de ficheiros Azure oferece ações de ficheiros na nuvem utilizando o protocolo SMB padrão. 

O armazenamento de ficheiros oferece ações de ficheiros na nuvem que utilizam o protocolo SMB padrão. Pode montar uma partilha de ficheiros a partir de qualquer SISTEMA que suporte SMB 3.0. Quando utiliza um suporte SMB no Linux, obtém-se cópias de segurança fáceis para um local de armazenamento robusto e permanente que é suportado por um SLA.

Mover ficheiros de um VM para um suporte SMB que está hospedado no armazenamento de ficheiros é uma ótima maneira de depurar registos. A mesma partilha SMB pode ser montada localmente na sua estação de trabalho Mac, Linux ou Windows. A SMB não é a melhor solução para o streaming de registos linux ou de aplicação em tempo real, porque o protocolo SMB não é construído para lidar com tarefas de exploração tão pesadas. Uma ferramenta de camada de registo unificada e dedicada, como fluente, seria uma escolha melhor do que a SMB para recolher o Linux e a produção de registo de aplicações.

Este guia requer que esteja a executar a versão 2.0.4 ou mais tarde do Azure CLI. Execute **az --version** para descobrir a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli). 


## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos chamado *myResourceGroup* na localização *leste dos EUA.*

```azurecli
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Crie uma nova conta de armazenamento, dentro do grupo de recursos que criou, utilizando [a conta de armazenamento AZ.](/cli/azure/storage/account) Este exemplo cria uma conta de armazenamento chamada *mySTORAGEACCT \<random number>* e coloca o nome dessa conta de armazenamento no **STORAGEACCT** variável. Os nomes das contas de armazenamento devem ser únicos, utilizando `$RANDOM` um número até ao fim para torná-lo único.

```azurecli
STORAGEACCT=$(az storage account create \
    --resource-group "myResourceGroup" \
    --name "mystorageacct$RANDOM" \
    --location eastus \
    --sku Standard_LRS \
    --query "name" | tr -d '"')
```

## <a name="get-the-storage-key"></a>Obtenha a chave de armazenamento

Quando cria uma conta de armazenamento, as chaves da conta são criadas em pares para que possam ser rodadas sem qualquer interrupção de serviço. Quando mudas para a segunda tecla do par, crias um novo par de chaves. As novas chaves da conta de armazenamento são sempre criadas em pares, por isso tens sempre pelo menos uma chave de conta de armazenamento não uusada pronta para mudar.

Ver as chaves da conta de armazenamento utilizando [a lista de chaves da conta de armazenamento Az](/cli/azure/storage/account/keys). Este exemplo armazena o valor da chave 1 na variável **STORAGEKEY.**

```azurecli
STORAGEKEY=$(az storage account keys list \
    --resource-group "myResourceGroup" \
    --account-name $STORAGEACCT \
    --query "[0].value" | tr -d '"')
```

## <a name="create-a-file-share"></a>Criar uma partilha de ficheiros

Crie a parte de armazenamento de ficheiro usando [a az armazenamento criar.](/cli/azure/storage/share) 

Os nomes de partilha precisam de ser todas as letras minúsculas, números e hífens simples, mas não podem começar com um hífen. Para obter detalhes completos sobre a nomenclatura de partilhas de ficheiros e ficheiros, veja [Naming and Referencing Shares, Directories, Files, and Metadata (Nomenclatura e Referência de Partilhas, Diretórios, Ficheiros e Metadados)](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata).

Este exemplo cria uma quota chamada *myshare* com uma quota de 10 GiB. 

```azurecli
az storage share create --name myshare \
    --quota 10 \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY
```

## <a name="create-a-mount-point"></a>Criar um ponto de montagem

Para montar a partilha de ficheiros Azure no seu computador Linux, tem de se certificar de que tem o pacote **cifs-utils** instalado. Para obter instruções de instalação, consulte [instalar a embalagem cifs-utils para a sua distribuição Linux](../../storage/files/storage-how-to-use-files-linux.md#install-cifs-utils).

A Azure Files utiliza o protocolo SMB, que comunica através da porta TCP 445.  Se tiver problemas em montar a sua partilha de ficheiros Azure, certifique-se de que a sua firewall não está a bloquear a porta 445 da TCP.


```bash
mkdir -p /mnt/MyAzureFileShare
```

## <a name="mount-the-share"></a>Monte a parte

Monte a partilha de arquivos Azure para o diretório local. 

```bash
sudo mount -t cifs //$STORAGEACCT.file.core.windows.net/myshare /mnt/MyAzureFileShare -o vers=3.0,username=$STORAGEACCT,password=$STORAGEKEY,dir_mode=0777,file_mode=0777,serverino
```

O comando acima utiliza o comando [de montagem](https://linux.die.net/man/8/mount) para montar a partilha de ficheiros Azure e opções específicas para [cifs](https://linux.die.net/man/8/mount.cifs). Especificamente, as opções file_mode e dir_mode definem ficheiros e diretórios para permissão `0777` . A `0777` permissão dá a leitura, a escrita e a execução de permissões a todos os utilizadores. Pode alterar estas permissões substituindo os valores por [outras permissões de chmod](https://en.wikipedia.org/wiki/Chmod). Também pode utilizar outras opções [cifs,](https://linux.die.net/man/8/mount.cifs) tais como gid ou uid. 


## <a name="persist-the-mount"></a>Persistir o monte

Quando reinicia o LM Linux, a parte SMB montada é desmontada durante o encerramento. Para voltar a montar a parte SMB no arranque, adicione uma linha ao Linux /etc/fstab. O Linux utiliza o ficheiro fstab para listar os sistemas de ficheiros que precisa de montar durante o processo de arranque. A adição da quota SMB garante que a parte de armazenamento de Ficheiros é um sistema de ficheiros montado permanentemente para o Linux VM. A adição da partilha SMB de armazenamento de ficheiros a um novo VM é possível quando se utiliza cloud-init.

```bash
//myaccountname.file.core.windows.net/mystorageshare /mnt/mymountpoint cifs vers=3.0,username=mystorageaccount,password=myStorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
```

Para uma maior segurança em ambientes de produção, deve armazenar as suas credenciais fora do fstab.

## <a name="next-steps"></a>Passos seguintes

- [Usando cloud-init para personalizar um Linux VM durante a criação](using-cloud-init.md)
- [Adicionar um disco a uma VM do Linux](add-disk.md)
- [Azure Disk Encryption para VMs do Linux](disk-encryption-overview.md)
