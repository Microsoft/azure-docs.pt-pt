---
title: Faça o back up ações de ficheiros Azure com a Azure CLI
description: Saiba como usar o Azure CLI para apoiar as ações de ficheiros da Azure no Cofre dos Serviços de Recuperação
ms.topic: conceptual
ms.date: 01/14/2020
ms.openlocfilehash: ee83d4df5a857f0ae5b554514ecda0c257a829ae
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85391099"
---
# <a name="back-up-azure-file-shares-with-cli"></a>Faça o back up ações de ficheiros Azure com o CLI

A interface de linha de comando Azure (CLI) proporciona uma experiência de linha de comando para a gestão dos recursos Azure. É uma ótima ferramenta para construir automatização personalizada para usar recursos Azure. Este artigo detalha como fazer o back up Azure file shares with Azure CLI. Também pode realizar estes passos com o [Azure PowerShell](https://docs.microsoft.com/azure/backup/backup-azure-afs-automation) ou no [portal do Azure](backup-afs.md).

No final deste tutorial, você aprenderá a executar abaixo operações com Azure CLI:

* Criar um cofre dos serviços de recuperação
* Ativar backup para ações de ficheiros Azure
* Desencadear uma cópia de segurança a pedido para ações de ficheiros

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Para instalar e utilizar a CLI localmente, tem de executar a CLI do Azure versão 2.0.18 ou posterior. Para encontrar a versão CLI, `run az --version` . Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="create-a-recovery-services-vault"></a>Criar um cofre de serviços de recuperação

Um cofre de serviço de recuperação é uma entidade que lhe dá uma visão consolidada e capacidade de gestão em todos os itens de backup. Quando a tarefa de cópia de segurança de um recurso protegido é executada, cria um ponto de recuperação dentro do cofre dos Serviços de Recuperação. Em seguida, pode utilizar um destes pontos de recuperação para restaurar dados para um determinado ponto no tempo.

Siga estes passos para criar um cofre de serviços de recuperação:

1. Um cofre é colocado num grupo de recursos. Se não tiver um grupo de recursos existente, crie um novo com [criação de grupo az](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create) . Neste tutorial, criamos o novo grupo de recursos *azurefiles* na região leste dos EUA.

    ```azurecli-interactive
    az group create --name AzureFiles --location eastus --output table
    ```

    ```output
    Location    Name
    ----------  ----------
    eastus      AzureFiles
    ```

1. Use o [cofre de reserva az criar](https://docs.microsoft.com/cli/azure/backup/vault?view=azure-cli-latest#az-backup-vault-create) cmdlet para criar o cofre. Especifique o mesmo local para o cofre que foi usado para o grupo de recursos.

    O exemplo a seguir cria um cofre de serviços de recuperação chamado *azurefilesvault* na região leste dos EUA.

    ```azurecli-interactive
    az backup vault create --resource-group azurefiles --name azurefilesvault --location eastus --output table
    ```

    ```output
    Location    Name                ResourceGroup
    ----------  ----------------    ---------------
    eastus      azurefilesvault     azurefiles
    ```

## <a name="enable-backup-for-azure-file-shares"></a>Ativar backup para ações de ficheiros Azure

Esta secção pressupõe que já tem uma partilha de ficheiros Azure para a qual pretende configurar a cópia de segurança. Se não tiver um, crie uma partilha de ficheiros Azure utilizando o comando [de partilha de armazenamento az.](https://docs.microsoft.com/cli/azure/storage/share?view=azure-cli-latest#az-storage-share-create)

Para ativar a cópia de segurança para ações de ficheiros, é necessário criar uma política de proteção que defina quando funciona um trabalho de backup e quanto tempo os pontos de recuperação são armazenados. Pode criar uma política de backup utilizando a [política de backup az criar](https://docs.microsoft.com/cli/azure/backup/policy?view=azure-cli-latest#az-backup-policy-create) cmdlet.

O exemplo a seguir utiliza o cmdlet [de proteção de backup az para](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-enable-for-azurefileshare) permitir a cópia de segurança para a partilha de ficheiros *azurefiles* na conta de armazenamento *afsaccount* utilizando a política de backup do *horário 1:*

```azurecli-interactive
az backup protection enable-for-azurefileshare --vault-name azurefilesvault --resource-group  azurefiles --policy-name schedule1 --storage-account afsaccount --azure-file-share azurefiles  --output table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
0caa93f4-460b-4328-ac1d-8293521dd928  azurefiles
```

O atributo **Nome** na saída corresponde ao nome do trabalho que é criado pelo serviço de backup para a sua operação **de backup.** Para controlar o estado do trabalho, use o cmdlet [de trabalho de reserva az.](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show)

## <a name="trigger-an-on-demand-backup-for-file-share"></a>Desencadear uma cópia de segurança a pedido para a partilha de ficheiros

Se pretender ativar uma cópia de segurança a pedido da sua parte do ficheiro em vez de esperar que a política de backup execute o trabalho na hora prevista, utilize o cmdlet [de backup de proteção az.backup agora.](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-backup-now)

É necessário definir os seguintes parâmetros para desencadear uma cópia de segurança a pedido:

* **--nome do contentor** é o nome da conta de armazenamento que hospeda a parte do ficheiro. Para recuperar o **nome** ou **o nome amigável** do seu recipiente, utilize o comando da lista de [contentores de reserva az.](/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list)
* **--o nome do item** é o nome da partilha de ficheiros para a qual pretende ativar uma cópia de segurança a pedido. Para recuperar o **nome** ou **o nome amigável** do seu item de reserva, utilize o comando da lista de produtos de reserva [az.](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-list)
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

O atributo **Nome** na saída corresponde ao nome do trabalho que é criado pelo serviço de backup para a sua operação de "backup on-demand". Para rastrear o estado de um trabalho, use o [az backup show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) cmdlet.

## <a name="next-steps"></a>Próximos passos

* Saiba como [restaurar as ações de ficheiros Azure com o CLI](restore-afs-cli.md)
* Saiba como gerir o [ficheiro Azure partilhar backups com o CLI](manage-afs-backup-cli.md)
