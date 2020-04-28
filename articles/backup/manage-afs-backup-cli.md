---
title: Gerir cópias de segurança de partilha de ficheiros Azure com o Azure CLI
description: Aprenda a utilizar o Azure CLI para gerir e monitorizar as ações de ficheiros Azure apoiadas pela Azure Backup.
ms.topic: conceptual
ms.date: 01/15/2020
ms.openlocfilehash: 06e1f29874085c3943a5207f36eff313dc670e88
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82184117"
---
# <a name="manage-azure-file-share-backups-with-the-azure-cli"></a>Gerir cópias de segurança de partilha de ficheiros Azure com o Azure CLI

O Azure CLI proporciona uma experiência de linha de comando para gerir os recursos azure. É uma ótima ferramenta para construir automação personalizada para usar recursos Azure. Este artigo explica como executar tarefas de gestão e monitorização das ações de ficheiro supérno do Azure que são apoiadas por [Azure Backup](https://docs.microsoft.com/azure/backup/backup-overview). Também pode realizar estes passos com o [portal Azure.](https://portal.azure.com/)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Para instalar e utilizar o CLI localmente, tem de executar a versão Azure CLI 2.0.18 ou posterior. Para encontrar a versão da CLI, execute `az --version`. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="prerequisites"></a>Pré-requisitos

Este artigo assume que já tem uma partilha de ficheiros Azure apoiada pela [Azure Backup](https://docs.microsoft.com/azure/backup/backup-overview). Se não tiver uma, consulte as partilhas de [ficheiros Do Back Up Azure com o CLI](backup-afs-cli.md) para configurar a cópia de segurança para as suas ações de ficheiro. Para este artigo, utiliza os seguintes recursos:

* **Grupo de recursos**: *ficheiros azurefiles*
* **RecoveryServicesVault**: *azurefilesvault*
* **Conta de Armazenamento**: *afsaccount*
* **Partilha de Ficheiros**: *ficheiros azurefiles*

## <a name="monitor-jobs"></a>Monitorizar trabalhos

Quando aciona as operações de backup ou restaura, o serviço de backup cria um trabalho de rastreio. Para monitorizar os trabalhos concluídos ou atualmente em funcionamento, utilize a lista de trabalho de [backup az](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-list) cmdlet. Com o CLI, também pode [suspender um trabalho atualmente em funcionamento](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-stop) ou [esperar até que um trabalho termine.](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-wait)

O exemplo seguinte mostra o estado dos trabalhos de backup para o cofre dos Serviços de Recuperação do cofre dos Serviços de Recuperação do cofre do *Azurefilesvault* Recovery Services:

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

## <a name="modify-policy"></a>Modificar a política

Pode modificar uma política de backup para alterar a frequência de backup ou o intervalo de retenção utilizando a [política de definição de itens](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-set-policy)de backup az .

Para alterar a política, defina os seguintes parâmetros:

* **--nome do contentor**: O nome da conta de armazenamento que acolhe a parte do ficheiro. Para recuperar o **nome** ou **o nome amigável** do seu recipiente, utilize o comando da lista de contentores de reserva [az.](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list)
* **-name**: O nome da parte do ficheiro para a qual pretende alterar a apólice. Para recuperar o **nome** ou **o nome amigável** do seu item backed, utilize o comando da lista de itens de reserva [az.](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-list)
* **--nome da política**: O nome da política de backup que pretende definir para a sua parte de ficheiro. Pode usar a lista de política de [backup da AZ](https://docs.microsoft.com/cli/azure/backup/policy?view=azure-cli-latest#az-backup-policy-list) para ver todas as políticas para o seu cofre.

O exemplo seguinte define a política de backup *do calendário2* para a partilha de ficheiros *azurefiles* presente na conta de armazenamento da *afsaccount.*

```azurecli-interactive
az backup item set-policy --policy-name schedule2 --name azurefiles --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --name "AzureFileShare;azurefiles" --backup-management-type azurestorage --out table
```

Também pode executar o comando anterior utilizando os nomes amigáveis para o recipiente e o item, fornecendo os seguintes dois parâmetros adicionais:

* **--backup-gestão tipo:** *armazenamento em azurestorage*
* **--tipo de carga de trabalho**: *azurefileshare*

```azurecli-interactive
az backup item set-policy --policy-name schedule2 --name azurefiles --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --name azurefiles --backup-management-type azurestorage --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
fec6f004-0e35-407f-9928-10a163f123e5  azurefiles
```

O **atributo nome** na saída corresponde ao nome do trabalho criado pelo serviço de backup para a sua operação de política de mudança. Para acompanhar o estado do trabalho, use o show de [trabalho de reserva az](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) cmdlet.

## <a name="stop-protection-on-a-file-share"></a>Parar a proteção numa partilha de ficheiros

Existem duas formas de parar a proteção de partilhas de ficheiros do Azure:

* Pare todos os futuros trabalhos de backup e *elimine* todos os pontos de recuperação.
* Pare todos os futuros trabalhos de reserva, mas *deixe* os pontos de recuperação.

Pode haver um custo associado à saída dos pontos de recuperação no armazenamento, porque os instantâneos subjacentes criados pela Azure Backup serão mantidos. O benefício de deixar os pontos de recuperação é a opção de restaurar a parte do ficheiro mais tarde, se quiser. Para obter informações sobre o custo de saída dos pontos de recuperação, consulte os detalhes dos [preços.](https://azure.microsoft.com/pricing/details/storage/files) Se optar por eliminar todos os pontos de recuperação, não pode restaurar a parte do ficheiro.

Para parar a proteção da partilha de ficheiros, defina os seguintes parâmetros:

* **--nome do contentor**: O nome da conta de armazenamento que acolhe a parte do ficheiro. Para recuperar o **nome** ou **o nome amigável** do seu recipiente, utilize o comando da lista de contentores de reserva [az.](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list)
* **--nome**do item : O nome da parte do ficheiro para a qual pretende parar a proteção. Para recuperar o **nome** ou **o nome amigável** do seu item backed, utilize o comando da lista de itens de reserva [az.](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-list)

### <a name="stop-protection-and-retain-recovery-points"></a>Parar a proteção e reter pontos de recuperação

Para parar a proteção enquanto retém dados, utilize a proteção de [reserva az desativar](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-disable) cmdlet.

O exemplo seguinte impede a proteção da partilha de *ficheiros azurefiles,* mas mantém todos os pontos de recuperação.

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name “AzureFileShare;azurefiles” --out table
```

Também pode executar o comando anterior utilizando o nome amigável para o recipiente e o item, fornecendo os seguintes dois parâmetros adicionais:

* **--backup-gestão tipo:** *armazenamento em azurestorage*
* **--tipo de carga de trabalho**: *azurefileshare*

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --item-name azurefiles --workload-type azurefileshare --backup-management-type Azurestorage --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
fec6f004-0e35-407f-9928-10a163f123e5  azurefiles
```

O atributo **nome** na saída corresponde ao nome do trabalho criado pelo serviço de backup para a sua operação de proteção de paragens. Para acompanhar o estado do trabalho, use o show de [trabalho de reserva az](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) cmdlet.

### <a name="stop-protection-without-retaining-recovery-points"></a>Parar a proteção sem reter pontos de recuperação

Para parar a proteção sem reter pontos de recuperação, utilize a proteção de [reserva az desativar](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-disable) cmdlet com a opção **de dados de eliminação de dados de cópia** de segurança definida como **verdadeira**.

O exemplo seguinte impede a proteção da partilha de *ficheiros azurefiles* sem reter pontos de recuperação.

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name “AzureFileShare;azurefiles” --delete-backup-data true --out table
```

Também pode executar o comando anterior utilizando o nome amigável para o recipiente e o item, fornecendo os seguintes dois parâmetros adicionais:

* **--backup-gestão tipo:** *armazenamento em azurestorage*
* **--tipo de carga de trabalho**: *azurefileshare*

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --item-name azurefiles --workload-type azurefileshare --backup-management-type Azurestorage --delete-backup-data true --out table
```

## <a name="resume-protection-on-a-file-share"></a>Retomar a proteção numa partilha de ficheiros

Se parou a proteção para uma partilha de ficheiros Azure, mas manteve pontos de recuperação, pode retomar a proteção mais tarde. Se não mantiver os pontos de recuperação, não pode retomar a proteção.

Para retomar a proteção da partilha de ficheiros, defina os seguintes parâmetros:

* **--nome do contentor**: O nome da conta de armazenamento que acolhe a parte do ficheiro. Para recuperar o **nome** ou **o nome amigável** do seu recipiente, utilize o comando da lista de contentores de reserva [az.](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list)
* **--nome**do item : O nome da parte do ficheiro para a qual pretende retomar a proteção. Para recuperar o **nome** ou **o nome amigável** do seu item backed, utilize o comando da lista de itens de reserva [az.](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-list)
* **--nome da política**: O nome da política de backup para a qual pretende retomar a proteção da parte do ficheiro.

O exemplo seguinte utiliza o currículo de [proteção de reserva az](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-resume) cmdlet para retomar a proteção para a partilha de *ficheiros azurefiles* utilizando a política de backup *schedule1.*

```azurecli-interactive
az backup protection resume --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount” --item-name “AzureFileShare;azurefiles” --policy-name schedule2 --out table
```

Também pode executar o comando anterior utilizando o nome amigável para o recipiente e o item, fornecendo os seguintes dois parâmetros adicionais:

* **--backup-gestão tipo:** *armazenamento em azurestorage*
* **--tipo de carga de trabalho**: *azurefileshare*

```azurecli-interactive
az backup protection resume --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --item-name azurefiles --workload-type azurefileshare --backup-management-type Azurestorage --policy-name schedule2 --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
75115ab0-43b0-4065-8698-55022a234b7f  azurefiles
```

O atributo **nome** na saída corresponde ao nome do trabalho criado pelo serviço de backup para a sua operação de proteção de currículo. Para acompanhar o estado do trabalho, use o show de [trabalho de reserva az](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) cmdlet.

## <a name="unregister-a-storage-account"></a>Desregistar uma conta de armazenamento

Se quiser proteger as suas ações de ficheiro numa determinada conta de armazenamento utilizando um cofre diferente dos Serviços de Recuperação, a primeira [paragem de proteção para todas as ações](#stop-protection-on-a-file-share) de ficheiro supor nessa conta de armazenamento. Em seguida, desregilhe a conta do cofre dos Serviços de Recuperação atualmente utilizado para proteção.

É necessário fornecer um nome de contentor para desregistar a conta de armazenamento. Para recuperar o **nome** ou o **nome amigável** do seu recipiente, utilize o comando da lista de contentores de [reserva Az.](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list)

O exemplo seguinte não regista a conta de armazenamento da *afsaccount* a partir do *cofre azurefilesvault* utilizando o recipiente de [reserva az não registrar](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-unregister) cmdlet.

```azurecli-interactive
az backup container unregister --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --out table
```

Também pode executar o cmdlet anterior utilizando o nome amigável para o recipiente, fornecendo o seguinte parâmetro adicional:

* **--backup-gestão tipo:** *armazenamento em azurestorage*

```azurecli-interactive
az backup container unregister --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --backup-management-type azurestorage --out table
```

## <a name="next-steps"></a>Passos seguintes

Para mais informações, consulte [o ficheiro Troubleshoot Azure partilha backup](troubleshoot-azure-files.md).
