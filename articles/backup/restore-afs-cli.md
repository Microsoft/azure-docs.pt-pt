---
title: Restaurar compartilhamentos de arquivos do Azure com CLI do Azure
description: Saiba como usar CLI do Azure para restaurar compartilhamentos de arquivos do Azure com backup no cofre dos serviços de recuperação
ms.topic: conceptual
ms.date: 01/16/2020
ms.openlocfilehash: 8d91031ff9ee0d4f32cc74ff7031d39ddab3e2b4
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/21/2020
ms.locfileid: "76294412"
---
# <a name="restore-azure-file-shares-with-cli"></a>Restaurar compartilhamentos de arquivos do Azure com a CLI

A CLI (interface de linha de comando) do Azure fornece uma experiência de linha de comando para gerenciar recursos do Azure. É uma excelente ferramenta para a criação de automação personalizada para usar os recursos do Azure. Este artigo explica como restaurar um compartilhamento de arquivos inteiro ou arquivos específicos de um ponto de restauração criado pelo serviço de [backup do Azure](https://docs.microsoft.com/azure/backup/backup-overview) usando CLI do Azure. Também pode realizar estes passos com o [Azure PowerShell](https://docs.microsoft.com/azure/backup/backup-azure-afs-automation) ou no [portal do Azure](backup-afs.md).

Ao final deste tutorial, você aprenderá a executar operações abaixo com CLI do Azure:

* Exibir pontos de restauração para um compartilhamento de arquivos do Azure com backup
* Restaurar um compartilhamento de arquivos completo do Azure
* Restaurar arquivos ou pastas individuais

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Para instalar e utilizar a CLI localmente, tem de executar a CLI do Azure versão 2.0.18 ou posterior. Para localizar a versão da CLI, `run az --version`. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que você já tenha um compartilhamento de arquivos do Azure cujo backup é feito pelo serviço de backup do Azure. Se você não tiver um, consulte [fazer backup de compartilhamentos de arquivos do Azure com a CLI](backup-afs-cli.md) para configurar o backup de seu compartilhamento de arquivos. Neste artigo, usaremos os seguintes recursos:

| Partilha de ficheiros  | Conta de Armazenamento | Região | Detalhes                                                      |
| ----------- | --------------- | ------ | ------------------------------------------------------------ |
| *azurefiles*  | *afsaccount*      | EastUS | Backup original de origem usando o backup do Azure                 |
| *azurefiles1* | *afaccount1*      | EastUS | Origem de destino usada para recuperação de local alternativo |

Você pode usar uma estrutura semelhante para seus compartilhamentos de arquivos para experimentar os diferentes tipos de restaurações explicados neste documento.

## <a name="fetch-recovery-points-for-the-azure-file-share"></a>Buscar pontos de recuperação para o compartilhamento de arquivos do Azure

Use o cmdlet [AZ backup RecoveryPoint List](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-list) para listar todos os pontos de recuperação para o compartilhamento de arquivos de backup.

O exemplo a seguir busca a lista de pontos de recuperação para o compartilhamento de arquivos *azurefiles* na conta de armazenamento *afsaccount* .

```azurecli-interactive
az backup recoverypoint list --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount” --backup-management-type azurestorage --item-name “AzureFileShare;azurefiles” --workload-type azurefileshare --out table
```

Você também pode executar o cmdlet acima usando o "nome amigável" para o contêiner e o item fornecendo os dois parâmetros adicionais a seguir:

* **--Gerenciamento de backup-tipo**: *AzureStorage*
* **--tipo de carga de trabalho**: *azurefileshare*

```azurecli-interactive
az backup recoverypoint list --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --backup-management-type azurestorage --item-name azurefiles --workload-type azurefileshare --out table
```

O conjunto de resultados será uma lista de pontos de recuperação com detalhes de tempo e consistência para cada ponto de restauração.

```output
Name                Time                        Consistency
------------------  -------------------------   --------------------
932887541532871865  2020-01-05T07:08:23+00:00   FileSystemConsistent
932885927361238054  2020-01-05T07:08:10+00:00   FileSystemConsistent
932879614553967772  2020-01-04T21:33:04+00:00   FileSystemConsistent
```

O atributo **Name** na saída corresponde ao nome do ponto de recuperação que pode ser usado como um valor para o parâmetro **--RP-Name** em operações de recuperação.

## <a name="full-share-recovery-using-azure-cli"></a>Recuperação de compartilhamento completo usando o CLI do Azure

Você pode usar essa opção de restauração para restaurar o compartilhamento de arquivos completo no local original ou alternativo.

Defina os seguintes parâmetros para executar operações de restauração:

* **--container-Name** é o nome da conta de armazenamento que hospeda o compartilhamento de arquivos original de backup. Para recuperar o **nome** ou **nome amigável** do seu contêiner, use o comando [AZ backup container List](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list) .
* **--Item-Name** é o nome do compartilhamento de arquivos original de backup que você deseja usar para a operação de restauração. Para recuperar o **nome** ou **nome amigável** de seu item de backup, use o comando [AZ backup item List](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-list) .

### <a name="restore-full-share-to-the-original-location"></a>Restaurar compartilhamento completo para o local original

Ao restaurar para um local original, você não precisa especificar parâmetros relacionados ao destino. Apenas o **conflito de resolução** deve ser fornecido.

O exemplo a seguir usa o cmdlet [AZ backup restore-azurefileshare](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurefileshare) com o modo de restauração definido como *originallocation* para restaurar o compartilhamento de arquivos *azurefiles* no local original, usando o ponto de recuperação 932883129628959823, que obtivemos com [buscar pontos de recuperação para o compartilhamento de arquivos do Azure](#fetch-recovery-points-for-the-azure-file-share):

```azurecli-interactive
az backup restore restore-azurefileshare --vault-name azurefilesvault --resource-group azurefiles --rp-name 932887541532871865   --container-name "StorageContainer;Storage;AzureFiles;afsaccount” --item-name “AzureFileShare;azurefiles” --restore-mode originallocation --resolve-conflict overwrite --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
6a27cc23-9283-4310-9c27-dcfb81b7b4bb  azurefiles
```

O atributo **Name** na saída corresponde ao nome do trabalho que é criado pelo serviço de backup para a operação de "restauração". Para acompanhar o status do trabalho, use o cmdlet [AZ backup Job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) .

### <a name="restore-full-share-to-an-alternate-location"></a>Restaurar compartilhamento completo para um local alternativo

Você pode usar essa opção para restaurar um compartilhamento de arquivos para um local alternativo e manter o compartilhamento de arquivos original como está. Especifique os seguintes parâmetros para recuperação de local alternativo:

* **--target-Storage-Account**: a conta de armazenamento para a qual o conteúdo de backup é restaurado. A conta de armazenamento de destino deve estar no mesmo local que o cofre.
* **--target-File-Share**: o compartilhamento de arquivos na conta de armazenamento de destino para a qual o conteúdo de backup é restaurado.
* **--pasta-de-destino**: a pasta sob o compartilhamento de arquivos para o qual os dados são restaurados. Se o conteúdo de backup for restaurado para uma pasta raiz, forneça os valores da pasta de destino como uma cadeia de caracteres vazia.
* **--resolve-Conflict**: instrução se houver um conflito com os dados restaurados. Aceita **substituir** ou **ignorar**.

O exemplo a seguir usa [AZ Backup Restore Restore-azurefileshare](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurefileshare) com o modo de restauração como *alternatelocation* para restaurar o compartilhamento de arquivos *azurefiles* na conta de armazenamento *afsaccount* para *azurefiles1 "* compartilhamento de arquivos na conta de armazenamento *afaccount1* .

```azurecli-interactive
az backup restore restore-azurefileshare --vault-name azurefilesvault --resource-group azurefiles --rp-name 932883129628959823 --container-name "StorageContainer;Storage;AzureFiles;afsaccount” --item-name “AzureFileShare;azurefiles” --restore-mode alternatelocation --target-storage-account afaccount1 --target-file-share azurefiles1 --target-folder restoredata --resolve-conflict overwrite --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
babeb61c-d73d-4b91-9830-b8bfa83c349a  azurefiles
```

O atributo **Name** na saída corresponde ao nome do trabalho que é criado pelo serviço de backup para a operação de "restauração". Para acompanhar o status do trabalho, use o cmdlet [AZ backup Job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) .

## <a name="item-level-recovery"></a>Recuperação em nível de item

Você pode usar essa opção de restauração para restaurar arquivos ou pastas individuais no local original ou alternativo.

Defina os seguintes parâmetros para executar operações de restauração:

* **--container-Name** é o nome da conta de armazenamento que hospeda o compartilhamento de arquivos original de backup. Para recuperar o **nome** ou **nome amigável** do seu contêiner, use o comando [AZ backup container List](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list) .
* **--Item-Name** é o nome do compartilhamento de arquivos original de backup que você deseja usar para a operação de restauração. Para recuperar o **nome** ou **nome amigável** de seu item de backup, use o comando [AZ backup item List](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-list) .

Especifique os seguintes parâmetros para os itens que você deseja recuperar:

* **SourceFilePath**: o caminho absoluto do arquivo a ser restaurado no compartilhamento de arquivos, como uma cadeia de caracteres. Esse caminho é o mesmo caminho usado no comando [AZ Storage File Download](https://docs.microsoft.com/cli/azure/storage/file?view=azure-cli-latest#az-storage-file-download) ou [AZ Storage file show](https://docs.microsoft.com/cli/azure/storage/file?view=azure-cli-latest#az-storage-file-show) CLI.
* **SourceFileType**: escolha se um diretório ou um arquivo está selecionado. Aceita **diretório** ou **arquivo**.
* **ResolveConflict**: instrução se houver um conflito com os dados restaurados. Aceita **substituir** ou **ignorar**.

### <a name="restore-individual-filesfolders-to-the-original-location"></a>Restaurar arquivos/pastas individuais para o local original

Use o cmdlet [AZ backup restore-azurefiles](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurefiles) com o modo de restauração definido como *originallocation* para restaurar arquivos/pastas específicos para seu local original.

O exemplo a seguir restaura o arquivo *"RestoreTest. txt"* no local original: o FileShare *azurefiles* .

```azurecli-interactive
az backup restore restore-azurefiles --vault-name azurefilesvault --resource-group azurefiles --rp-name 932881556234035474 --container-name "StorageContainer;Storage;AzureFiles;afsaccount” --item-name “AzureFileShare;azurefiles” --restore-mode originallocation  --source-file-type file --source-file-path "Restore/RestoreTest.txt" --resolve-conflict overwrite  --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
df4d9024-0dcb-4edc-bf8c-0a3d18a25319  azurefiles
```

O atributo **Name** na saída corresponde ao nome do trabalho que é criado pelo serviço de backup para a operação de "restauração". Para acompanhar o status do trabalho, use o cmdlet [AZ backup Job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) .

### <a name="restore-individual-filesfolders-to-an-alternate-location"></a>Restaurar arquivos/pastas individuais para um local alternativo

Para restaurar arquivos/pastas específicos para um local alternativo, use o cmdlet [AZ backup restore-azurefiles](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurefiles) com o modo de restauração definido como *alternatelocation* e especifique os seguintes parâmetros relacionados ao destino:

* **--target-Storage-Account**: a conta de armazenamento para a qual o conteúdo de backup é restaurado. A conta de armazenamento de destino deve estar no mesmo local que o cofre.
* **--target-File-Share**: o compartilhamento de arquivos na conta de armazenamento de destino para a qual o conteúdo de backup é restaurado.
* **--pasta-de-destino**: a pasta sob o compartilhamento de arquivos para o qual os dados são restaurados. Se o conteúdo de backup for restaurado para uma pasta raiz, forneça o valor da pasta de destino como uma cadeia de caracteres vazia.

O exemplo a seguir restaura o arquivo *RestoreTest. txt* originalmente presente no FileShare *azurefiles* para um local alternativo: a pasta *restoredata* no compartilhamento de *azurefiles1* hospedado na conta de armazenamento *afaccount1* .

```azurecli-interactive
az backup restore restore-azurefiles --vault-name azurefilesvault --resource-group azurefiles --rp-name 932881556234035474 --container-name "StorageContainer;Storage;AzureFiles;afsaccount” --item-name “AzureFileShare;azurefiles” --restore-mode alternatelocation --target-storage-account afaccount1 --target-file-share azurefiles1 --target-folder restoredata --resolve-conflict overwrite --source-file-type file --source-file-path "Restore/RestoreTest.txt" --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
df4d9024-0dcb-4edc-bf8c-0a3d18a25319  azurefiles
```

O atributo **Name** na saída corresponde ao nome do trabalho que é criado pelo serviço de backup para a operação de "restauração". Para acompanhar o status do trabalho, use o cmdlet [AZ backup Job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) .

## <a name="next-steps"></a>Passos seguintes

Saiba como [gerenciar backups de compartilhamento de arquivos do Azure com o CLI do Azure](manage-afs-backup-cli.md)
