---
title: Gerir cópias de segurança de partilha de ficheiros Azure com o Azure CLI
description: Saiba como utilizar o Azure CLI para gerir e monitorizar as ações de ficheiros Azure apoiadas pela Azure Backup.
ms.topic: conceptual
ms.date: 01/15/2020
ms.openlocfilehash: 5a8a785016845b836a102663a959e4b2f28696b6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "94566457"
---
# <a name="manage-azure-file-share-backups-with-the-azure-cli"></a>Gerir cópias de segurança de partilha de ficheiros Azure com o Azure CLI

O Azure CLI proporciona uma experiência de linha de comando para a gestão dos recursos da Azure. É uma ótima ferramenta para construir automatização personalizada para usar recursos Azure. Este artigo explica como executar tarefas de gestão e monitorização das ações de ficheiros Azure que são apoiadas pela [Azure Backup](./backup-overview.md). Também pode realizar estes passos com o [portal Azure.](https://portal.azure.com/)

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que já tem uma partilha de ficheiros Azure apoiada pela [Azure Backup](./backup-overview.md). Se não tiver um, consulte [ações de ficheiros Back up Azure com o CLI](backup-afs-cli.md) para configurar a cópia de segurança para as suas ações de ficheiro. Para este artigo, utiliza os seguintes recursos:
   -  **Grupo de recursos**: *azurefiles*
   -  **RecoveryServicesVault**: *azurefilesvault*
   -  **Conta de Armazenamento**: *afsaccount*
   -  **Partilha de Ficheiros**: *azurefiles*
  
  [!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]
   - Este tutorial requer a versão 2.0.18 ou posterior do Azure CLI. Se utilizar o Azure Cloud Shell, a versão mais recente já está instalada.

## <a name="monitor-jobs"></a>Monitorizar trabalhos

Quando aciona operações de backup ou restauro, o serviço de backup cria um trabalho para rastrear. Para monitorizar os trabalhos concluídos ou atualmente em funcionamento, utilize o cmdlet da [lista de trabalho de reserva az.](/cli/azure/backup/job#az-backup-job-list) Com o CLI, também pode [suspender um trabalho atualmente em execução](/cli/azure/backup/job#az-backup-job-stop) ou [esperar até que um trabalho termine.](/cli/azure/backup/job#az-backup-job-wait)

O exemplo a seguir mostra o estado dos trabalhos de backup para o cofre dos Serviços de Recuperação *azurefilesvault:*

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

## <a name="modify-policy"></a>Alterar a política

Pode modificar uma política de backup para alterar a frequência de backup ou o intervalo de retenção utilizando [a política de definição de produto de backup az](/cli/azure/backup/item#az-backup-item-set-policy).

Para alterar a política, defina os seguintes parâmetros:

* **--nome do contentor**: o nome da conta de armazenamento que acolhe a parte do ficheiro. Para recuperar o **nome** ou **o nome amigável** do seu recipiente, utilize o comando da lista de [contentores de reserva az.](/cli/azure/backup/container#az-backup-container-list)
* **--nome**: O nome da partilha de ficheiros para a qual pretende alterar a política. Para recuperar o **nome** ou **o nome amigável** do seu item de reserva, utilize o comando da lista de produtos de reserva [az.](/cli/azure/backup/item#az-backup-item-list)
* **--nome de política**: O nome da política de backup que pretende definir para a sua parte do ficheiro. Você pode usar [a lista de políticas de backup az](/cli/azure/backup/policy#az-backup-policy-list) para ver todas as políticas para o seu cofre.

O exemplo a seguir define a política de backup *do agendamento2* para a partilha de ficheiros *azurefiles* presente na conta de armazenamento *afsaccount.*

```azurecli-interactive
az backup item set-policy --policy-name schedule2 --name azurefiles --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --name "AzureFileShare;azurefiles" --backup-management-type azurestorage --out table
```

Também pode executar o comando anterior utilizando os nomes amigáveis para o recipiente e o item, fornecendo os seguintes dois parâmetros adicionais:

* **--tipo de gestão de backup:** *azurestorage*
* **--tipo de carga de trabalho:** *azurefileshare*

```azurecli-interactive
az backup item set-policy --policy-name schedule2 --name azurefiles --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --name azurefiles --backup-management-type azurestorage --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
fec6f004-0e35-407f-9928-10a163f123e5  azurefiles
```

O atributo **Nome** na saída corresponde ao nome do trabalho criado pelo serviço de backup para o seu funcionamento de política de mudança. Para acompanhar o estado do trabalho, use o [az backup job show](/cli/azure/backup/job#az-backup-job-show) cmdlet.

## <a name="stop-protection-on-a-file-share"></a>Parar a proteção numa partilha de ficheiros

Existem duas formas de parar a proteção de partilhas de ficheiros do Azure:

* Pare todos os futuros trabalhos de backup e *elimine* todos os pontos de recuperação.
* Parem com todos os futuros trabalhos de *apoio,* mas deixem os pontos de recuperação.

Pode haver um custo associado a deixar os pontos de recuperação armazenados, porque os instantâneos subjacentes criados pela Azure Backup serão mantidos. O benefício de deixar os pontos de recuperação é a opção de restaurar a partilha do ficheiro mais tarde, se quiser. Para obter informações sobre o custo de sair dos pontos de recuperação, consulte os [detalhes dos preços.](https://azure.microsoft.com/pricing/details/storage/files) Se optar por eliminar todos os pontos de recuperação, não poderá restaurar a partilha de ficheiros.

Para parar a proteção da partilha de ficheiros, defina os seguintes parâmetros:

* **--nome do contentor**: o nome da conta de armazenamento que acolhe a parte do ficheiro. Para recuperar o **nome** ou **o nome amigável** do seu recipiente, utilize o comando da lista de [contentores de reserva az.](/cli/azure/backup/container#az-backup-container-list)
* **--nome do item**: o nome da partilha de ficheiros para a qual pretende parar a proteção. Para recuperar o **nome** ou **o nome amigável** do seu item de reserva, utilize o comando da lista de produtos de reserva [az.](/cli/azure/backup/item#az-backup-item-list)

### <a name="stop-protection-and-retain-recovery-points"></a>Parar a proteção e reter pontos de recuperação

Para parar a proteção enquanto conserva os dados, utilize a [proteção de segurança az desative o](/cli/azure/backup/protection#az-backup-protection-disable) cmdlet.

O exemplo seguinte para a proteção para a partilha *de ficheiros azurefiles,* mas mantém todos os pontos de recuperação.

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name “AzureFileShare;azurefiles” --out table
```

Também pode executar o comando anterior utilizando o nome amigável para o recipiente e o item, fornecendo os seguintes dois parâmetros adicionais:

* **--tipo de gestão de backup:** *azurestorage*
* **--tipo de carga de trabalho:** *azurefileshare*

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --item-name azurefiles --workload-type azurefileshare --backup-management-type Azurestorage --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
fec6f004-0e35-407f-9928-10a163f123e5  azurefiles
```

O atributo **Nome** na saída corresponde ao nome do trabalho criado pelo serviço de backup para a sua operação de proteção stop. Para acompanhar o estado do trabalho, use o [az backup job show](/cli/azure/backup/job#az-backup-job-show) cmdlet.

### <a name="stop-protection-without-retaining-recovery-points"></a>Parar a proteção sem reter pontos de recuperação

Para parar a proteção sem reter pontos de recuperação, utilize a [proteção de segurança az desativar](/cli/azure/backup/protection#az-backup-protection-disable) o cmdlet com a opção **de eliminação de dados de cópia de segurança** definida como **verdadeira**.

O exemplo a seguir para a proteção da partilha *de ficheiros azurefiles* sem reter pontos de recuperação.

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name “AzureFileShare;azurefiles” --delete-backup-data true --out table
```

Também pode executar o comando anterior utilizando o nome amigável para o recipiente e o item, fornecendo os seguintes dois parâmetros adicionais:

* **--tipo de gestão de backup:** *azurestorage*
* **--tipo de carga de trabalho:** *azurefileshare*

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --item-name azurefiles --workload-type azurefileshare --backup-management-type Azurestorage --delete-backup-data true --out table
```

## <a name="resume-protection-on-a-file-share"></a>Retomar a proteção numa partilha de ficheiros

Se parasse a proteção de uma partilha de ficheiros Azure, mas mantivesse pontos de recuperação, poderá retomar a proteção mais tarde. Se não mantiver os pontos de recuperação, não pode retomar a proteção.

Para retomar a proteção da partilha de ficheiros, defina os seguintes parâmetros:

* **--nome do contentor**: o nome da conta de armazenamento que acolhe a parte do ficheiro. Para recuperar o **nome** ou **o nome amigável** do seu recipiente, utilize o comando da lista de [contentores de reserva az.](/cli/azure/backup/container#az-backup-container-list)
* **--nome do item**: o nome da partilha de ficheiros para a qual pretende retomar a proteção. Para recuperar o **nome** ou **o nome amigável** do seu item de reserva, utilize o comando da lista de produtos de reserva [az.](/cli/azure/backup/item#az-backup-item-list)
* **--nome de política**: O nome da política de backup para a qual pretende retomar a proteção para a partilha de ficheiros.

O exemplo a seguir utiliza o cmdlet de [proteção de backup az](/cli/azure/backup/protection#az-backup-protection-resume) para retomar a proteção para a partilha *de ficheiros azurefiles* utilizando a política de backup do *agenda1.*

```azurecli-interactive
az backup protection resume --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount” --item-name “AzureFileShare;azurefiles” --policy-name schedule2 --out table
```

Também pode executar o comando anterior utilizando o nome amigável para o recipiente e o item, fornecendo os seguintes dois parâmetros adicionais:

* **--tipo de gestão de backup:** *azurestorage*
* **--tipo de carga de trabalho:** *azurefileshare*

```azurecli-interactive
az backup protection resume --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --item-name azurefiles --workload-type azurefileshare --backup-management-type Azurestorage --policy-name schedule2 --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
75115ab0-43b0-4065-8698-55022a234b7f  azurefiles
```

O atributo **Nome** na saída corresponde ao nome do trabalho criado pelo serviço de backup para a sua operação de proteção de currículo. Para acompanhar o estado do trabalho, use o [az backup job show](/cli/azure/backup/job#az-backup-job-show) cmdlet.

## <a name="unregister-a-storage-account"></a>Não registar uma conta de armazenamento

Se pretender proteger as suas ações de ficheiros numa determinada conta de armazenamento utilizando um cofre diferente dos Serviços de Recuperação, [primeiro pare a proteção para todas as ações de ficheiros](#stop-protection-on-a-file-share) nessa conta de armazenamento. Em seguida, desagrega a conta do cofre dos Serviços de Recuperação atualmente utilizado para proteção.

Tem de fornecer um nome de contentor para não registar a conta de armazenamento. Para recuperar o **nome** ou o **nome amigável** do seu recipiente, utilize o comando da lista [de contentores de reserva az.](/cli/azure/backup/container#az-backup-container-list)

O exemplo a seguir não registra a conta de armazenamento *afsaccount* a partir de *azurefilesvault* utilizando o [cmdlet do recipiente de reserva az.](/cli/azure/backup/container#az-backup-container-unregister)

```azurecli-interactive
az backup container unregister --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --out table
```

Também pode executar o cmdlet anterior utilizando o nome amigável para o recipiente, fornecendo o seguinte parâmetro adicional:

* **--tipo de gestão de backup:** *azurestorage*

```azurecli-interactive
az backup container unregister --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --backup-management-type azurestorage --out table
```

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações, consulte [a Troubleshoot Azure file shares backup](troubleshoot-azure-files.md).
