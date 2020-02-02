---
title: Restaurar as ações de ficheiro sinuoso com o Azure CLI
description: Saiba como usar o Azure CLI para restaurar as ações de ficheiros Azure apoiadas no cofre dos Serviços de Recuperação
ms.topic: conceptual
ms.date: 01/16/2020
ms.openlocfilehash: 63b2be2fe24c1274ed1581b7b849de578c978842
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/01/2020
ms.locfileid: "76931039"
---
# <a name="restore-azure-file-shares-with-the-azure-cli"></a>Restaurar as ações de ficheiro sinuoso com o Azure CLI

O Azure CLI proporciona uma experiência de linha de comando para gerir os recursos azure. É uma excelente ferramenta para a criação de automação personalizada para usar os recursos do Azure. Este artigo explica como restaurar uma partilha de ficheiros inteira ou ficheiros específicos a partir de um ponto de restauro criado pela [Azure Backup](https://docs.microsoft.com/azure/backup/backup-overview) utilizando o Azure CLI. Também pode realizar estes passos com o [Azure PowerShell](https://docs.microsoft.com/azure/backup/backup-azure-afs-automation) ou no [portal do Azure](backup-afs.md).

No final deste artigo, aprenderá a realizar as seguintes operações com o Azure CLI:

* Ver pontos de restauro para uma partilha de ficheiros Azure apoiada.
* Restaurar uma parte completa do ficheiro Azure.
* Restaurar ficheiros ou pastas individuais.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Para instalar e utilizar a CLI localmente, tem de executar a CLI do Azure versão 2.0.18 ou posterior. Para encontrar a versão da CLI, execute `az --version`. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="prerequisites"></a>Pré-requisitos

Este artigo assume que já tem uma partilha de ficheiros Azure que é apoiada pela Azure Backup. Se não tiver uma, consulte as partilhas de [ficheiros Do Back Up Azure com o CLI](backup-afs-cli.md) para configurar a cópia de segurança para a sua partilha de ficheiros. Para este artigo, utiliza os seguintes recursos:

| Partilha de ficheiros  | Conta de armazenamento | Região | Detalhes                                                      |
| ----------- | --------------- | ------ | ------------------------------------------------------------ |
| *azurefiles*  | *afsaccount*      | EastUS | Fonte original apoiada usando Azure Backup                 |
| *azurefiles1* | *afaccount1*      | EastUS | Origem de destino usada para recuperação de local alternativo |

Pode utilizar uma estrutura semelhante para as suas partilhas de ficheiros para experimentar os diferentes tipos de restauros explicados neste artigo.

## <a name="fetch-recovery-points-for-the-azure-file-share"></a>Buscar pontos de recuperação para a partilha de ficheiros Azure

Use o cmdlet [AZ backup RecoveryPoint List](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-list) para listar todos os pontos de recuperação para o compartilhamento de arquivos de backup.

O exemplo seguinte indica a lista de pontos de recuperação da parte do ficheiro *azurefiles* na conta de armazenamento da *afsaccount.*

```azurecli-interactive
az backup recoverypoint list --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount” --backup-management-type azurestorage --item-name “AzureFileShare;azurefiles” --workload-type azurefileshare --out table
```

Também pode executar o cmdlet anterior utilizando o nome amigável para o recipiente e o item, fornecendo os seguintes dois parâmetros adicionais:

* **--Gerenciamento de backup-tipo**: *AzureStorage*
* **--tipo de carga de trabalho**: *azurefileshare*

```azurecli-interactive
az backup recoverypoint list --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --backup-management-type azurestorage --item-name azurefiles --workload-type azurefileshare --out table
```

O conjunto de resultados é uma lista de pontos de recuperação com tempo e detalhes de consistência para cada ponto de restauro.

```output
Name                Time                        Consistency
------------------  -------------------------   --------------------
932887541532871865  2020-01-05T07:08:23+00:00   FileSystemConsistent
932885927361238054  2020-01-05T07:08:10+00:00   FileSystemConsistent
932879614553967772  2020-01-04T21:33:04+00:00   FileSystemConsistent
```

O atributo **Name** na saída corresponde ao nome do ponto de recuperação que pode ser usado como um valor para o parâmetro **--RP-Name** em operações de recuperação.

## <a name="full-share-recovery-by-using-the-azure-cli"></a>Recuperação total de ações utilizando o Azure CLI

Você pode usar essa opção de restauração para restaurar o compartilhamento de arquivos completo no local original ou alternativo.

Defina os seguintes parâmetros para executar operações de restauração:

* **--nome do contentor**: O nome da conta de armazenamento que acolhe a parte de ficheiro original apoiada. Para recuperar o nome ou o nome amigável do seu recipiente, utilize o comando da lista de contentores de [reserva az.](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list)
* **--nome**do item : O nome da parte original do ficheiro que pretende utilizar para a operação de restauro. Para recuperar o nome ou o nome amigável do seu item backed, utilize o comando da lista de itens de [reserva az.](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-list)

### <a name="restore-a-full-share-to-the-original-location"></a>Restaurar uma parte completa para a localização original

Ao restaurar para um local original, você não precisa especificar parâmetros relacionados ao destino. Apenas o **conflito de resolução** deve ser fornecido.

O exemplo seguinte utiliza a cópia de segurança [az restaurar o restauro-azurefileshare](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurefileshare) cmdlet com o modo de restauro definido para a *localização original* para restaurar a parte de ficheiro de *ficheiros azurefiles* na localização original. Utiliza o ponto de recuperação 932883129628959823, que obteve nos pontos de [recuperação da Fetch para a partilha de ficheiros Azure:](#fetch-recovery-points-for-the-azure-file-share)

```azurecli-interactive
az backup restore restore-azurefileshare --vault-name azurefilesvault --resource-group azurefiles --rp-name 932887541532871865   --container-name "StorageContainer;Storage;AzureFiles;afsaccount” --item-name “AzureFileShare;azurefiles” --restore-mode originallocation --resolve-conflict overwrite --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
6a27cc23-9283-4310-9c27-dcfb81b7b4bb  azurefiles
```

O atributo **nome** na saída corresponde ao nome do trabalho criado pelo serviço de backup para a sua operação de restauro. Para acompanhar o status do trabalho, use o cmdlet [AZ backup Job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) .

### <a name="restore-a-full-share-to-an-alternate-location"></a>Restaurar uma parte completa para um local alternativo

Você pode usar essa opção para restaurar um compartilhamento de arquivos para um local alternativo e manter o compartilhamento de arquivos original como está. Especifique os seguintes parâmetros para recuperação de local alternativo:

* **--target-Storage-Account**: a conta de armazenamento para a qual o conteúdo de backup é restaurado. A conta de armazenamento de destino deve estar no mesmo local que o cofre.
* **--target-File-Share**: o compartilhamento de arquivos na conta de armazenamento de destino para a qual o conteúdo de backup é restaurado.
* **--pasta-de-destino**: a pasta sob o compartilhamento de arquivos para o qual os dados são restaurados. Se o conteúdo de backup for restaurado para uma pasta raiz, forneça os valores da pasta de destino como uma cadeia de caracteres vazia.
* **--resolve-Conflict**: instrução se houver um conflito com os dados restaurados. Aceita **substituir** ou **ignorar**.

O exemplo seguinte utiliza a cópia de [segurança az restaurar o restauro-azurefileshare](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurefileshare) com o modo de restauro como *alternativa de localização* para restaurar a parte de ficheiro sinuosa na conta de armazenamento da *afsaccount* para a parte de ficheiro *azurefiles1"* na conta de armazenamento *afaccount1.*

```azurecli-interactive
az backup restore restore-azurefileshare --vault-name azurefilesvault --resource-group azurefiles --rp-name 932883129628959823 --container-name "StorageContainer;Storage;AzureFiles;afsaccount” --item-name “AzureFileShare;azurefiles” --restore-mode alternatelocation --target-storage-account afaccount1 --target-file-share azurefiles1 --target-folder restoredata --resolve-conflict overwrite --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
babeb61c-d73d-4b91-9830-b8bfa83c349a  azurefiles
```

O atributo **nome** na saída corresponde ao nome do trabalho criado pelo serviço de backup para a sua operação de restauro. Para acompanhar o status do trabalho, use o cmdlet [AZ backup Job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) .

## <a name="item-level-recovery"></a>Recuperação ao nível do item

Você pode usar essa opção de restauração para restaurar arquivos ou pastas individuais no local original ou alternativo.

Defina os seguintes parâmetros para executar operações de restauração:

* **--nome do contentor**: O nome da conta de armazenamento que acolhe a parte de ficheiro original apoiada. Para recuperar o nome ou o nome amigável do seu recipiente, utilize o comando da lista de contentores de [reserva az.](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list)
* **--nome**do item : O nome da parte original do ficheiro que pretende utilizar para a operação de restauro. Para recuperar o nome ou o nome amigável do seu item backed, utilize o comando da lista de itens de [reserva az.](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-list)

Especifique os seguintes parâmetros para os itens que você deseja recuperar:

* **SourceFilePath**: o caminho absoluto do arquivo a ser restaurado no compartilhamento de arquivos, como uma cadeia de caracteres. Esse caminho é o mesmo caminho usado no comando [AZ Storage File Download](https://docs.microsoft.com/cli/azure/storage/file?view=azure-cli-latest#az-storage-file-download) ou [AZ Storage file show](https://docs.microsoft.com/cli/azure/storage/file?view=azure-cli-latest#az-storage-file-show) CLI.
* **SourceFileType**: escolha se um diretório ou um arquivo está selecionado. Aceita **diretório** ou **arquivo**.
* **ResolveConflict**: instrução se houver um conflito com os dados restaurados. Aceita **substituir** ou **ignorar**.

### <a name="restore-individual-files-or-folders-to-the-original-location"></a>Restaurar ficheiros ou pastas individuais para a localização original

Utilize a [restauração de restauro de restauro az](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurefiles) cmdlet com o modo de restauro definido para *a localização original* para restaurar ficheiros ou pastas específicos para a sua localização original.

O exemplo seguinte restaura o ficheiro *RestoreTest.txt* na sua localização original: a partilha de *ficheiros azurefiles.*

```azurecli-interactive
az backup restore restore-azurefiles --vault-name azurefilesvault --resource-group azurefiles --rp-name 932881556234035474 --container-name "StorageContainer;Storage;AzureFiles;afsaccount” --item-name “AzureFileShare;azurefiles” --restore-mode originallocation  --source-file-type file --source-file-path "Restore/RestoreTest.txt" --resolve-conflict overwrite  --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
df4d9024-0dcb-4edc-bf8c-0a3d18a25319  azurefiles
```

O atributo **nome** na saída corresponde ao nome do trabalho criado pelo serviço de backup para a sua operação de restauro. Para acompanhar o status do trabalho, use o cmdlet [AZ backup Job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) .

### <a name="restore-individual-files-or-folders-to-an-alternate-location"></a>Restaurar ficheiros ou pastas individuais para um local alternativo

Para restaurar ficheiros ou pastas específicos para um local alternativo, utilize o cmdlet de restauro de restauro de [restauro az](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurefiles) com o modo de restauro definido para *alternor* a localização alternativa e especificar os seguintes parâmetros relacionados com o alvo:

* **--target-Storage-Account**: a conta de armazenamento para a qual o conteúdo de backup é restaurado. A conta de armazenamento de destino deve estar no mesmo local que o cofre.
* **--target-File-Share**: o compartilhamento de arquivos na conta de armazenamento de destino para a qual o conteúdo de backup é restaurado.
* **--pasta-de-destino**: a pasta sob o compartilhamento de arquivos para o qual os dados são restaurados. Se o conteúdo de backup for restaurado para uma pasta raiz, forneça o valor da pasta de destino como uma cadeia de caracteres vazia.

O exemplo seguinte restaura o ficheiro *RestoreTest.txt* originalmente presente na partilha de ficheiros *azurefiles* para um local alternativo: a pasta de *dados de restauro* na partilha de ficheiros *azurefiles1* alojada na conta de armazenamento *afaccount1.*

```azurecli-interactive
az backup restore restore-azurefiles --vault-name azurefilesvault --resource-group azurefiles --rp-name 932881556234035474 --container-name "StorageContainer;Storage;AzureFiles;afsaccount” --item-name “AzureFileShare;azurefiles” --restore-mode alternatelocation --target-storage-account afaccount1 --target-file-share azurefiles1 --target-folder restoredata --resolve-conflict overwrite --source-file-type file --source-file-path "Restore/RestoreTest.txt" --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
df4d9024-0dcb-4edc-bf8c-0a3d18a25319  azurefiles
```

O atributo **nome** na saída corresponde ao nome do trabalho criado pelo serviço de backup para a sua operação de restauro. Para acompanhar o status do trabalho, use o cmdlet [AZ backup Job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) .

## <a name="next-steps"></a>Passos seguintes

Saiba como gerir os backups de [ficheiros Azure com o Azure CLI](manage-afs-backup-cli.md).
