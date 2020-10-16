---
title: Faça o back up ações de ficheiros Azure com a Azure CLI
description: Saiba como usar o Azure CLI para apoiar as ações de ficheiros da Azure no cofre dos Serviços de Recuperação
ms.topic: conceptual
ms.date: 01/14/2020
ms.openlocfilehash: 12d258a3242530745cc8ce31afae18f622323488
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91293293"
---
# <a name="back-up-azure-file-shares-with-cli"></a>Faça o back up ações de ficheiros Azure com o CLI

A interface de linha de comando Azure (CLI) proporciona uma experiência de linha de comando para a gestão dos recursos Azure. É uma ótima ferramenta para construir automatização personalizada para usar recursos Azure. Este artigo detalha como fazer o back up Azure file shares with Azure CLI. Também pode realizar estes passos com o [Azure PowerShell](./backup-azure-afs-automation.md) ou no [portal do Azure](backup-afs.md).

No final deste tutorial, você aprenderá a realizar as operações abaixo com Azure CLI:

* Criar um cofre dos Serviços de Recuperação 
* Ativar backup para ações de ficheiros Azure
* Desencadear uma cópia de segurança a pedido para ações de ficheiros

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Para instalar e utilizar a CLI localmente, tem de executar a CLI do Azure versão 2.0.18 ou posterior. Para encontrar a versão CLI, `run az --version` . Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos Serviços de Recuperação 

Um cofre de Serviços de Recuperação é uma entidade que lhe dá uma visão consolidada e capacidade de gestão em todos os itens de backup. Quando a tarefa de cópia de segurança de um recurso protegido é executada, cria um ponto de recuperação dentro do cofre dos Serviços de Recuperação. Em seguida, pode utilizar um destes pontos de recuperação para restaurar dados para um determinado ponto no tempo.

Siga estes passos para criar um cofre dos Serviços de Recuperação:

1. Um cofre é colocado num grupo de recursos. Se não tiver um grupo de recursos existente, crie um novo com [criação de grupo az](/cli/azure/group#az-group-create) . Neste tutorial, criamos o novo grupo de recursos *azurefiles* na região leste dos EUA.

    ```azurecli-interactive
    az group create --name AzureFiles --location eastus --output table
    ```

    ```output
    Location    Name
    ----------  ----------
    eastus      AzureFiles
    ```

1. Use o [cofre de reserva az criar](/cli/azure/backup/vault#az-backup-vault-create) cmdlet para criar o cofre. Especifique o mesmo local para o cofre que foi usado para o grupo de recursos.

    O exemplo a seguir cria um cofre dos Serviços de Recuperação chamado *azurefilesvault* na região leste dos EUA.

    ```azurecli-interactive
    az backup vault create --resource-group azurefiles --name azurefilesvault --location eastus --output table
    ```

    ```output
    Location    Name                ResourceGroup
    ----------  ----------------    ---------------
    eastus      azurefilesvault     azurefiles
    ```

## <a name="enable-backup-for-azure-file-shares"></a>Ativar backup para ações de ficheiros Azure

Esta secção pressupõe que já tem uma partilha de ficheiros Azure para a qual pretende configurar a cópia de segurança. Se não tiver um, crie uma partilha de ficheiros Azure utilizando o comando [de partilha de armazenamento az.](/cli/azure/storage/share#az-storage-share-create)

Para ativar a cópia de segurança para ações de ficheiros, é necessário criar uma política de proteção que defina quando funciona um trabalho de backup e quanto tempo os pontos de recuperação são armazenados. Pode criar uma política de backup utilizando a [política de backup az criar](/cli/azure/backup/policy#az-backup-policy-create) cmdlet.

O exemplo a seguir utiliza o cmdlet [de proteção de backup az para](/cli/azure/backup/protection#az-backup-protection-enable-for-azurefileshare) permitir a cópia de segurança para a partilha de ficheiros *azurefiles* na conta de armazenamento *afsaccount* utilizando a política de backup do *horário 1:*

```azurecli-interactive
az backup protection enable-for-azurefileshare --vault-name azurefilesvault --resource-group  azurefiles --policy-name schedule1 --storage-account afsaccount --azure-file-share azurefiles  --output table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
0caa93f4-460b-4328-ac1d-8293521dd928  azurefiles
```

O atributo **Nome** na saída corresponde ao nome do trabalho criado pelo serviço de backup para a sua operação **de backup.** Para controlar o estado do trabalho, use o cmdlet [de trabalho de reserva az.](/cli/azure/backup/job#az-backup-job-show)

## <a name="trigger-an-on-demand-backup-for-file-share"></a>Desencadear uma cópia de segurança a pedido para a partilha de ficheiros

Se pretender ativar uma cópia de segurança a pedido da sua parte do ficheiro em vez de esperar que a política de backup execute o trabalho na hora prevista, utilize o cmdlet [de backup de proteção az.backup agora.](/cli/azure/backup/protection#az-backup-protection-backup-now)

É necessário definir os seguintes parâmetros para desencadear uma cópia de segurança a pedido:

* **--nome do contentor** é o nome da conta de armazenamento que hospeda a parte do ficheiro. Para recuperar o **nome** ou **o nome amigável** do seu recipiente, utilize o comando da lista de [contentores de reserva az.](/cli/azure/backup/container#az-backup-container-list)
* **--o nome do item** é o nome da partilha de ficheiros para a qual pretende ativar uma cópia de segurança a pedido. Para recuperar o **nome** ou **o nome amigável** do seu item de reserva, utilize o comando da lista de produtos de reserva [az.](/cli/azure/backup/item#az-backup-item-list)
* **-- reter-até** especificar a data até quando pretender manter o ponto de recuperação. O valor deve ser definido no formato tempo UTC (dd-mm-yyyy).

O exemplo seguinte desencadeia uma cópia de segurança a pedido para o fileshare de *azurefiles* na conta de armazenamento *afsaccount* com retenção até *20-01-2020*.

```azurecli-interactive
az backup protection backup-now --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --retain-until 20-01-2020 --output table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
9f026b4f-295b-4fb8-aae0-4f058124cb12  azurefiles
```

O atributo **Nome** na saída corresponde ao nome do trabalho que é criado pelo serviço de backup para a sua operação de "backup on-demand". Para rastrear o estado de um trabalho, use o [az backup show](/cli/azure/backup/job#az-backup-job-show) cmdlet.

## <a name="next-steps"></a>Passos seguintes

* Saiba como [restaurar as ações de ficheiros Azure com o CLI](restore-afs-cli.md)
* Saiba como gerir o [ficheiro Azure partilhar backups com o CLI](manage-afs-backup-cli.md)
