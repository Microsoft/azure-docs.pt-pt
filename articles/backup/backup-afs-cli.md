---
title: Fazer backup de compartilhamentos de arquivos do Azure com CLI do Azure
description: Saiba como usar CLI do Azure para fazer backup de compartilhamentos de arquivos do Azure no cofre dos serviços de recuperação
ms.topic: conceptual
ms.date: 01/14/2020
ms.openlocfilehash: ff1d8c6245521d2d0262b0440177d65713058742
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76844046"
---
# <a name="back-up-azure-file-shares-with-cli"></a>Fazer backup de compartilhamentos de arquivos do Azure com a CLI

A CLI (interface de linha de comando) do Azure fornece uma experiência de linha de comando para gerenciar recursos do Azure. É uma excelente ferramenta para a criação de automação personalizada para usar os recursos do Azure. Este artigo fornece detalhes sobre como fazer backup de compartilhamentos de arquivos do Azure com CLI do Azure. Também pode realizar estes passos com o [Azure PowerShell](https://docs.microsoft.com/azure/backup/backup-azure-afs-automation) ou no [portal do Azure](backup-afs.md).

Ao final deste tutorial, você aprenderá a executar operações abaixo com CLI do Azure:

* Criar um cofre dos serviços de recuperação
* Habilitar backup para compartilhamentos de arquivos do Azure
* Disparar um backup sob demanda para compartilhamentos de arquivos

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Para instalar e utilizar a CLI localmente, tem de executar a CLI do Azure versão 2.0.18 ou posterior. Para localizar a versão da CLI, `run az --version`. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos serviços de recuperação

Um cofre do serviço de recuperação é uma entidade que fornece uma funcionalidade consolidada de exibição e gerenciamento em todos os itens de backup. Quando a tarefa de cópia de segurança de um recurso protegido é executada, cria um ponto de recuperação dentro do cofre dos Serviços de Recuperação. Em seguida, pode utilizar um destes pontos de recuperação para restaurar dados para um determinado ponto no tempo.

Siga estas etapas para criar um cofre dos serviços de recuperação:

1. Um cofre é colocado em um grupo de recursos. Se você não tiver um grupo de recursos existente, crie um novo com [AZ Group Create](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create) . Neste tutorial, criamos o novo grupo de recursos *azurefiles* na região leste dos EUA.

    ```azurecli-interactive
    az group create --name AzureFiles --location eastus --output table
    ```

    ```output
    Location    Name
    ----------  ----------
    eastus      AzureFiles
    ```

2. Use o cmdlet [AZ backup Vault Create](https://docs.microsoft.com/cli/azure/backup/vault?view=azure-cli-latest#az-backup-vault-create) para criar o cofre. Especifique o mesmo local para o cofre que foi usado para o grupo de recursos.

    O exemplo a seguir cria um cofre dos serviços de recuperação chamado *azurefilesvault* na região leste dos EUA.

    ```azurecli-interactive
    az backup vault create --resource-group azurefiles --name azurefilesvault --location eastus --output table
    ```

    ```output
    Location    Name                ResourceGroup
    ----------  ----------------    ---------------
    eastus      azurefilesvault     azurefiles
    ```

3. Especifique o tipo de redundância a ser usado para o armazenamento do cofre. Você pode usar o [armazenamento com redundância local](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs) ou o [armazenamento com redundância geográfica](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs).

    O exemplo a seguir define a opção de redundância de armazenamento para *azurefilesvault* como **georedundância** usando o cmdlet [AZ backup Vault backup-Properties Set](https://docs.microsoft.com/cli/azure/backup/vault/backup-properties?view=azure-cli-latest#az-backup-vault-backup-properties-set) .

    ```azurecli-interactive
    az backup vault backup-properties set --name azurefilesvault --resource-group azurefiles --backup-storage-redundancy Georedundant
    ```

    Para verificar se o cofre foi criado com êxito, você pode usar o cmdlet [AZ backup Vault show](https://docs.microsoft.com/cli/azure/backup/vault?view=azure-cli-latest#az-backup-vault-show) para obter detalhes do cofre. O exemplo a seguir exibe os detalhes do *azurefilesvault* que criamos nas etapas acima.

    ```azurecli-interactive
    az backup vault show --name azurefilesvault --resource-group azurefiles --output table
    ```

    A saída será semelhante à seguinte resposta:

    ```output
    Location     Name               ResourceGroup
    ----------   ---------------    ---------------
    eastus       azurefilesvault    azurefiles
    ```

## <a name="enable-backup-for-azure-file-shares"></a>Habilitar backup para compartilhamentos de arquivos do Azure

Esta seção pressupõe que você já tenha um compartilhamento de arquivos do Azure para o qual deseja configurar o backup. Se você não tiver uma, crie um compartilhamento de arquivos do Azure usando o comando [AZ Storage share Create](https://docs.microsoft.com/cli/azure/storage/share?view=azure-cli-latest#az-storage-share-create) .

Para habilitar o backup de compartilhamentos de arquivos, você precisa criar uma política de proteção que define quando um trabalho de backup é executado e por quanto tempo os pontos de recuperação são armazenados. Você pode criar uma política de backup usando o cmdlet [AZ backup Policy Create](https://docs.microsoft.com/cli/azure/backup/policy?view=azure-cli-latest#az-backup-policy-create) .

O exemplo a seguir usa o cmdlet [AZ backup Protection enable-for-azurefileshare](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-enable-for-azurefileshare) para habilitar o backup para o compartilhamento de arquivos *azurefiles* na conta de armazenamento *afsaccount* usando a política de backup do *Schedule 1* :

```azurecli-interactive
az backup protection enable-for-azurefileshare --vault-name azurefilesvault --resource-group  azurefiles --policy-name schedule1 --storage-account afsaccount --azure-file-share azurefiles  --output table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
0caa93f4-460b-4328-ac1d-8293521dd928  azurefiles
```

O atributo **Name** na saída corresponde ao nome do trabalho que é criado pelo serviço de backup para a operação de **habilitar backup** . Para acompanhar o status do trabalho, use o cmdlet [AZ backup Job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) .

## <a name="trigger-an-on-demand-backup-for-file-share"></a>Disparar um backup sob demanda para compartilhamento de arquivos

Se você quiser disparar um backup sob demanda para seu compartilhamento de arquivos em vez de aguardar que a política de backup execute o trabalho no horário agendado, use o cmdlet [AZ backup Protection Backup-Now](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-backup-now) .

Você precisa definir os seguintes parâmetros para disparar um backup sob demanda:

* **--container-Name** é o nome da conta de armazenamento que hospeda o compartilhamento de arquivos. Para recuperar o **nome** ou **nome amigável** do seu contêiner, use o comando [AZ backup container List](/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list) .
* **--Item-Name** é o nome do compartilhamento de arquivos para o qual você deseja disparar um backup sob demanda. Para recuperar o **nome** ou **nome amigável** de seu item de backup, use o comando [AZ backup item List](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-list) .
* **--Retain-until** especifica a data até o momento em que você deseja manter o ponto de recuperação. O valor deve ser definido no formato de hora UTC (dd-mm-aaaa).

O exemplo a seguir dispara um backup sob demanda para o FileShare *azuresfiles* na conta de armazenamento *afsaccount* com retenção até *20-01-2020*.

```azurecli-interactive
az backup protection backup-now --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --retain-until 20-01-2020 --output table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
9f026b4f-295b-4fb8-aae0-4f058124cb12  azurefiles
```

O atributo **Name** na saída corresponde ao nome do trabalho que é criado pelo serviço de backup para a operação de "backup sob demanda". Para acompanhar o status de um trabalho, use o cmdlet [AZ backup Job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) .

## <a name="next-steps"></a>Passos seguintes

* Saiba como [restaurar compartilhamentos de arquivos do Azure com a CLI](restore-afs-cli.md)
* Saiba como [gerenciar o compartilhamento de arquivos do Azure ackups com a CLI](manage-afs-backup-cli.md)
