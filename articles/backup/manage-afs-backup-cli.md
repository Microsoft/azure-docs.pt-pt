---
title: Gerenciar backups de compartilhamento de arquivos do Azure com a CLI
description: Saiba como usar CLI do Azure para gerenciar e monitorar compartilhamentos de arquivos do Azure com backup feito pelo serviço de backup do Azure.
ms.topic: conceptual
ms.date: 01/15/2020
ms.openlocfilehash: bf824b1868ad7c9e4df2cceeca101d82272e18d6
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/21/2020
ms.locfileid: "76294477"
---
# <a name="manage-azure-file-share-backups-with-azure-cli"></a>Gerenciar backups de compartilhamento de arquivos do Azure com o CLI do Azure

A CLI (interface de linha de comando) do Azure fornece uma experiência de linha de comando para gerenciar recursos do Azure. É uma excelente ferramenta para a criação de automação personalizada para usar os recursos do Azure. Este artigo explica como executar as tarefas abaixo para gerenciar e monitorar os compartilhamentos de arquivos do Azure cujo backup é feito pelo [serviço de backup do Azure](https://docs.microsoft.com/azure/backup/backup-overview). Você também pode executar essas etapas com o [portal do Azure](https://portal.azure.com/).

* [Monitorar trabalhos](#monitor-jobs)
* [Modificar política](#modify-policy)
* [Interromper a proteção em um compartilhamento de arquivos](#stop-protection-on-a-file-share)
* [Retomar a proteção em um compartilhamento de arquivos](#resume-protection-on-a-file-share)
* [Cancelar o registro de uma conta de armazenamento](#unregister-a-storage-account)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Para instalar e utilizar a CLI localmente, tem de executar a CLI do Azure versão 2.0.18 ou posterior. Para encontrar a versão da CLI, execute `az --version`. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial pressupõe que você já tenha um compartilhamento de arquivos do Azure cujo backup foi feito pelo serviço de [backup do Azure](https://docs.microsoft.com/azure/backup/backup-overview) . Se você não tiver um, consulte [fazer backup de compartilhamentos de arquivos do Azure com a CLI](backup-afs-cli.md) para configurar o backup de seus compartilhamentos de arquivos. Para este artigo, vamos usar os seguintes recursos:

* **Grupo de recursos**: *azurefiles*
* **RecoveryServicesVault**: *azurefilesvault*
* **Conta de armazenamento**: *afsaccount*
* **Compartilhamento de arquivos**: *azurefiles*

## <a name="monitor-jobs"></a>Monitorizar Tarefas

Quando você dispara operações de backup ou restauração, o serviço de backup cria um trabalho para acompanhamento. Para monitorar trabalhos concluídos ou em execução no momento, use o cmdlet [AZ backup Job List](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-list) . A CLI também permite [suspender um trabalho em execução no momento](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-stop) ou [aguardar até que um trabalho seja concluído](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-wait).

O exemplo a seguir exibe o status dos trabalhos de backup para o cofre dos serviços de recuperação do *azurefilesvault* :

```azurecli-interactive
az backup job list --resource-group azurefiles --vault-name azurefilesvault
```

```output
[
  {
    "eTag": null,
    "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupJobs/d477dfb6-b292-4f24-bb43-6b14e9d06ab5",
    "location": null,
    "name": "d477dfb6-b292-4f24-bb43-6b14e9d06ab5",
    "properties": {
      "actionsInfo": null,
      "activityId": "3cef43ed-0af4-43e2-b9cb-1322c496ccb4",
      "backupManagementType": "AzureStorage",
      "duration": "0:00:29.718011",
      "endTime": "2020-01-13T08:05:29.180606+00:00",
      "entityFriendlyName": "azurefiles",
      "errorDetails": null,
      "extendedInfo": null,
      "jobType": "AzureStorageJob",
      "operation": "Backup",
      "startTime": "2020-01-13T08:04:59.462595+00:00",
      "status": "Completed",
      "storageAccountName": "afsaccount",
      "storageAccountVersion": "MicrosoftStorage"
    },
    "resourceGroup": "azurefiles",
    "tags": null,
    "type": "Microsoft.RecoveryServices/vaults/backupJobs"
  },
  {
    "eTag": null,
    "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupJobs/1b9399bf-c23c-4caa-933a-5fc2bf884519",
    "location": null,
    "name": "1b9399bf-c23c-4caa-933a-5fc2bf884519",
    "properties": {
      "actionsInfo": null,
      "activityId": "2663449c-94f1-4735-aaf9-5bb991e7e00c",
      "backupManagementType": "AzureStorage",
      "duration": "0:00:28.145216",
      "endTime": "2020-01-13T08:05:27.519826+00:00",
      "entityFriendlyName": "azurefilesresource",
      "errorDetails": null,
      "extendedInfo": null,
      "jobType": "AzureStorageJob",
      "operation": "Backup",
      "startTime": "2020-01-13T08:04:59.374610+00:00",
      "status": "Completed",
      "storageAccountName": "afsaccount",
      "storageAccountVersion": "MicrosoftStorage"
    },
    "resourceGroup": "azurefiles",
    "tags": null,
    "type": "Microsoft.RecoveryServices/vaults/backupJobs"
  }
]
```

## <a name="modify-policy"></a>Modificar Política

Você pode modificar uma política de backup para alterar a frequência de backup ou o período de retenção usando [AZ backup Item Set-Policy](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-set-policy).

Para alterar a política, defina os seguintes parâmetros:

* **--container-Name** é o nome da conta de armazenamento que hospeda o compartilhamento de arquivos. Para recuperar o **nome** ou **nome amigável** do seu contêiner, use o comando [AZ backup container List](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list) .
* **--Name** é o nome do compartilhamento de arquivos para o qual você deseja alterar a política. Para recuperar o **nome** ou **nome amigável** de seu item de backup, use o comando [AZ backup item List](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-list) .
* **--Policy-Name** é o nome da política de backup que você deseja definir para o compartilhamento de arquivos. Você pode usar a [lista de políticas de backup AZ](https://docs.microsoft.com/cli/azure/backup/policy?view=azure-cli-latest#az-backup-policy-list) para exibir todas as políticas para seu cofre.

O exemplo a seguir define a política de backup *schedule2* para o compartilhamento de arquivos *azurefiles* presente na conta de armazenamento *afsaccount* .

```azurecli-interactive
az backup item set-policy --policy-name schedule2 --name azurefiles --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --name "AzureFileShare;azurefiles" --backup-management-type azurestorage --out table
```

Você também pode executar o comando acima usando os "nomes amigáveis" para o contêiner e o item fornecendo os dois parâmetros adicionais a seguir:

* **--Gerenciamento de backup-tipo**: *AzureStorage*
* **--tipo de carga de trabalho**: *azurefileshare*

```azurecli-interactive
az backup item set-policy --policy-name schedule2 --name azurefiles --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --name azurefiles --backup-management-type azurestorage --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
fec6f004-0e35-407f-9928-10a163f123e5  azurefiles
```

O atributo de **nome** na saída corresponde ao nome do trabalho que é criado pelo serviço de backup para a operação "alterar política". Para acompanhar o status do trabalho, use o cmdlet [AZ backup Job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) .

## <a name="stop-protection-on-a-file-share"></a>Interromper a proteção em um compartilhamento de arquivos

Existem duas formas de parar a proteção de partilhas de ficheiros do Azure:

* Parar todos os trabalhos de backup futuros e *excluir* todos os pontos de recuperação
* Parar todos os trabalhos de backup futuros, mas *deixar* os pontos de recuperação

Pode haver um custo associado à saída dos pontos de recuperação no armazenamento, pois os instantâneos subjacentes criados pelo backup do Azure serão mantidos. No entanto, o benefício de deixar os pontos de recuperação é a opção de restaurar o compartilhamento de arquivos posteriormente, se desejado. Para obter informações sobre o custo de deixar os pontos de recuperação, consulte os [detalhes de preços](https://azure.microsoft.com/pricing/details/storage/files). Se optar por eliminar todos os pontos de recuperação, não pode restaurar a Partilha de ficheiros.

Para interromper a proteção do compartilhamento de arquivos, defina os seguintes parâmetros:

* **--container-Name** é o nome da conta de armazenamento que hospeda o compartilhamento de arquivos. Para recuperar o **nome** ou **nome amigável** do seu contêiner, use o comando [AZ backup container List](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list) .
* **--Item-Name** é o nome do compartilhamento de arquivos para o qual você deseja interromper a proteção. Para recuperar o **nome** ou **nome amigável** de seu item de backup, use o comando [AZ backup item List](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-list) .

### <a name="stop-protection-and-retain-recovery-points"></a>Interromper a proteção e manter os pontos de recuperação

Para interromper a proteção enquanto mantém os dados, use o cmdlet [AZ backup Protection Disable](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-disable) .

O exemplo a seguir interrompe a proteção para o compartilhamento de arquivos *azurefiles* , mas retém todos os pontos de recuperação.

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name “AzureFileShare;azurefiles” --out table
```

Você também pode executar o comando acima usando o "nome amigável" para o contêiner e o item fornecendo os dois parâmetros adicionais a seguir:

* **--Gerenciamento de backup-tipo**: *AzureStorage*
* **--tipo de carga de trabalho**: *azurefileshare*

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --item-name azurefiles --workload-type azurefileshare --backup-management-type Azurestorage --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
fec6f004-0e35-407f-9928-10a163f123e5  azurefiles
```

O atributo **Name** na saída corresponde ao nome do trabalho que é criado pelo serviço de backup para a operação "parar proteção". Para acompanhar o status do trabalho, use o cmdlet [AZ backup Job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) .

### <a name="stop-protection-without-retaining-recovery-points"></a>Interromper a proteção sem reter pontos de recuperação

Para interromper a proteção sem reter pontos de recuperação, use a opção [AZ backup Protection Disable](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-disable) com th e **DELETE-backup-data** definido como **true**.

O exemplo a seguir interrompe a proteção para o compartilhamento de arquivos *azurefiles* sem reter os pontos de recuperação:

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name “AzureFileShare;azurefiles” --delete-backup-data true --out table
```

Você também pode executar o comando acima usando o "nome amigável" para o contêiner e o item fornecendo os dois parâmetros adicionais a seguir:

* **--Gerenciamento de backup-tipo**: *AzureStorage*
* **--tipo de carga de trabalho**: *azurefileshare*

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --item-name azurefiles --workload-type azurefileshare --backup-management-type Azurestorage --delete-backup-data true --out table
```

## <a name="resume-protection-on-a-file-share"></a>Retomar a proteção em um compartilhamento de arquivos

Se você interrompeu a proteção para um compartilhamento de arquivos do Azure, mas manteve pontos de recuperação, você pode retomar a proteção mais tarde. Se você não mantiver os pontos de recuperação, não poderá retomar a proteção.

Para retomar a proteção para o compartilhamento de arquivos, defina os seguintes parâmetros:

* **--container-Name** é o nome da conta de armazenamento que hospeda o compartilhamento de arquivos. Para recuperar o **nome** ou **nome amigável** do seu contêiner, use o comando [AZ backup container List](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list) .
* **--Item-Name** é o nome do compartilhamento de arquivos para o qual você deseja retomar a proteção. Para recuperar o **nome** ou **nome amigável** de seu item de backup, use o comando [AZ backup item List](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-list) .
* **--Policy-Name** é o nome da política de backup para a qual você deseja retomar a proteção para o compartilhamento de arquivos.

O exemplo a seguir usa o cmdlet [AZ backup Protection resume](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-resume) para retomar a proteção para o compartilhamento de arquivos *azurefiles* usando a política de backup *schedule1* .

```azurecli-interactive
az backup protection resume --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount” --item-name “AzureFileShare;azurefiles” --policy-name schedule2 --out table
```

Você também pode executar o comando acima usando o "nome amigável" para o contêiner e o item fornecendo os dois parâmetros adicionais a seguir:

* **--Gerenciamento de backup-tipo**: *AzureStorage*
* **--tipo de carga de trabalho**: *azurefileshare*

```azurecli-interactive
az backup protection resume --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --item-name azurefiles --workload-type azurefileshare --backup-management-type Azurestorage --policy-name schedule2 --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
75115ab0-43b0-4065-8698-55022a234b7f  azurefiles
```

O atributo **Name** na saída corresponde ao nome do trabalho que é criado pelo serviço de backup para a operação "retomar proteção". Para acompanhar o status do trabalho, use o cmdlet [AZ backup Job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) .

## <a name="unregister-a-storage-account"></a>Cancelar o registro de uma conta de armazenamento

Se você quiser proteger seus compartilhamentos de arquivos em uma conta de armazenamento específica usando um cofre de serviços de recuperação diferente, primeiro [interrompa a proteção para todos os compartilhamentos de arquivos](#stop-protection-on-a-file-share) nessa conta de armazenamento. Em seguida, cancele o registro da conta do cofre dos serviços de recuperação usado atualmente para proteção.

Você precisa fornecer um nome de contêiner para cancelar o registro da conta de armazenamento. Para recuperar o **nome** ou o **nome amigável** do contêiner, use o comando [AZ backup container List](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list) .

O exemplo a seguir cancela o registro da conta de armazenamento *afsaccount* do *azurefilesvault* usando o cmdlet [AZ backup container Unregister](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-unregister) .

```azurecli-interactive
az backup container unregister --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --out table
```

Você também pode executar o cmdlet acima usando o "nome amigável" para o contêiner, fornecendo o seguinte parâmetro adicional:

* **--Gerenciamento de backup-tipo**: *AzureStorage*

```azurecli-interactive
az backup container unregister --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --backup-management-type azurestorage --out table
```

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações, consulte [solucionar problemas de falhas de backup/restauração para compartilhamentos de arquivos do Azure](troubleshoot-azure-files.md)
