---
title: Restaurar ações de ficheiros Azure com o Azure CLI
description: Saiba como usar o CLI Azure para restaurar as ações de ficheiros Azure no cofre dos Serviços de Recuperação
ms.topic: conceptual
ms.date: 01/16/2020
ms.openlocfilehash: 2edc2281c29023bb8dfe0268f23eacfe081d413e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107768522"
---
# <a name="restore-azure-file-shares-with-the-azure-cli"></a>Restaurar ações de ficheiros Azure com o Azure CLI

O Azure CLI proporciona uma experiência de linha de comando para a gestão dos recursos da Azure. É uma ótima ferramenta para construir automatização personalizada para usar recursos Azure. Este artigo explica como restaurar uma partilha de ficheiros inteira ou ficheiros específicos a partir de um ponto de restauração criado pela [Azure Backup](./backup-overview.md) utilizando o Azure CLI. Também pode realizar estes passos com o [Azure PowerShell](./backup-azure-afs-automation.md) ou no [portal do Azure](backup-afs.md).

No final deste artigo, aprenderá a realizar as seguintes operações com o Azure CLI:

* Ver pontos de restauro para uma partilha de ficheiros Azure apoiada.
* Restaurar uma partilha completa de ficheiros Azure.
* Restaurar ficheiros ou pastas individuais.

>[!NOTE]
> O Azure Backup suporta agora restaurar vários ficheiros ou pastas para a localização original ou alternativa utilizando o Azure CLI. Consulte a [secção de localização de vários ficheiros ou pastas para obter informações sobre os ficheiros ou pastas originais ou alternativos](#restore-multiple-files-or-folders-to-original-or-alternate-location) deste documento para saber mais.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo assume que já tem uma partilha de ficheiros Azure que é apoiada pela Azure Backup. Se não tiver um, consulte [as ações de ficheiros do Back up Azure com o CLI](backup-afs-cli.md) para configurar a cópia de segurança para a sua parte do ficheiro. Para este artigo, utiliza os seguintes recursos:

| Partilha de ficheiros | Conta de armazenamento | Region | Detalhes |
|---|---|---|---|
| *azurefiles* | *afsaccount* | Rio Eastus | Fonte original apoiada usando Azure Backup |
| *azurefiles1* | *afaccount1* | Rio Eastus | Fonte de destino utilizada para recuperação alternativa da localização |

Pode utilizar uma estrutura semelhante para as suas ações de ficheiros para experimentar os diferentes tipos de restauros explicados neste artigo.

[!INCLUDE [azure-cli-prepare-your-environment-h3.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

 - Este tutorial requer a versão 2.0.18 ou posterior do Azure CLI. Se utilizar o Azure Cloud Shell, a versão mais recente já está instalada.

## <a name="fetch-recovery-points-for-the-azure-file-share"></a>Procure pontos de recuperação para a partilha de ficheiros Azure

Utilize o cmdlet da [lista de recuperação de backup az](/cli/azure/backup/recoverypoint#az_backup_recoverypoint_list) para listar todos os pontos de recuperação para a partilha de ficheiros apoiado.

O exemplo a seguir requer a lista de pontos de recuperação para a participação de ficheiros *azurefiles* na conta de armazenamento *afsaccount.*

```azurecli-interactive
az backup recoverypoint list --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --backup-management-type azurestorage --item-name "AzureFileShare;azurefiles" --workload-type azurefileshare --out table
```

Também pode executar o cmdlet anterior utilizando o nome amigável para o recipiente e o item, fornecendo os seguintes dois parâmetros adicionais:

* **--tipo de gestão de backup:** *azurestorage*
* **--tipo de carga de trabalho:** *azurefileshare*

```azurecli-interactive
az backup recoverypoint list --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --backup-management-type azurestorage --item-name azurefiles --workload-type azurefileshare --out table
```

O conjunto de resultados é uma lista de pontos de recuperação com detalhes de tempo e consistência para cada ponto de restauro.

```output
Name                Time                        Consistency
------------------  -------------------------   --------------------
932887541532871865  2020-01-05T07:08:23+00:00   FileSystemConsistent
932885927361238054  2020-01-05T07:08:10+00:00   FileSystemConsistent
932879614553967772  2020-01-04T21:33:04+00:00   FileSystemConsistent
```

O atributo **Nome** na saída corresponde ao nome do ponto de recuperação que pode ser usado como um valor para o parâmetro **do nome rp** em operações de recuperação.

## <a name="full-share-recovery-by-using-the-azure-cli"></a>Recuperação total de ações utilizando o CLI Azure

Pode utilizar esta opção de restauro para restaurar a partilha completa de ficheiros no local original ou alternativo.

Definir os seguintes parâmetros para executar operações de restauro:

* **--nome do contentor**: o nome da conta de armazenamento que acolhe a parte original do ficheiro. Para recuperar o nome ou o nome amigável do seu recipiente, utilize o comando [da lista de contentores de reserva az.](/cli/azure/backup/container#az_backup_container_list)
* **--nome do item**: O nome da partilha de ficheiros original com ressarçamento que pretende utilizar para a operação de restauro. Para recuperar o nome ou o nome amigável do seu item de reserva, utilize o comando [da lista de produtos de reserva az.](/cli/azure/backup/item#az_backup_item_list)

### <a name="restore-a-full-share-to-the-original-location"></a>Restaurar uma parte completa para a localização original

Quando restaura para uma localização original, não precisa de especificar parâmetros relacionados com o alvo. Só deve ser providenciado **o Conflito de Resolução.**

O exemplo a seguir utiliza o cmdlet [restore-azurefileshare de backback az](/cli/azure/backup/restore#az_backup_restore_restore_azurefileshare) com modo de restauro definido para *originalidade* para restaurar a partilha *de ficheiros azurefiles* na localização original. Utilize o ponto de recuperação 932883129628959823, que obteve em [pontos de recuperação da Fetch para a partilha de ficheiros Azure:](#fetch-recovery-points-for-the-azure-file-share)

```azurecli-interactive
az backup restore restore-azurefileshare --vault-name azurefilesvault --resource-group azurefiles --rp-name 932887541532871865   --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --restore-mode originallocation --resolve-conflict overwrite --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
6a27cc23-9283-4310-9c27-dcfb81b7b4bb  azurefiles
```

O atributo **Nome** na saída corresponde ao nome do trabalho que é criado pelo serviço de backup para a sua operação de restauro. Para acompanhar o estado do trabalho, use o [az backup job show](/cli/azure/backup/job#az_backup_job_show) cmdlet.

### <a name="restore-a-full-share-to-an-alternate-location"></a>Restaurar uma parte completa para um local alternativo

Pode utilizar esta opção para restaurar uma partilha de ficheiros para uma localização alternativa e manter a partilha original do ficheiro tal como está. Especificar os seguintes parâmetros para a recuperação alternativa da localização:

* **--conta-alvo-armazenamento**: a conta de armazenamento à qual o conteúdo de ressarimento é restaurado. A conta de armazenamento do alvo deve estar no mesmo local que o cofre.
* **--target-file-share**: A parte do ficheiro na conta de armazenamento-alvo para a qual o conteúdo de back-up é restaurado.
* **--pasta-alvo**: A pasta sob a partilha de ficheiros para a qual os dados são restaurados. Se o conteúdo de apoio for restaurado para uma pasta de raiz, dê os valores da pasta-alvo como uma corda vazia.
* **--resolver conflito:** instrução se houver um conflito com os dados restaurados. Aceita **Overwrite** ou **Skip**.

O exemplo a seguir utiliza [a az backup restaurar restaurar o restauro-azurefileshare](/cli/azure/backup/restore#az_backup_restore_restore_azurefileshare) com modo de *restauração* como alternativa para restaurar a partilha de ficheiros *azurefiles* na conta de armazenamento *afsaccount* para a parte de *ficheiros azurefiles1"* na conta de armazenamento *afaccount1.*

```azurecli-interactive
az backup restore restore-azurefileshare --vault-name azurefilesvault --resource-group azurefiles --rp-name 932883129628959823 --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --restore-mode alternatelocation --target-storage-account afaccount1 --target-file-share azurefiles1 --target-folder restoredata --resolve-conflict overwrite --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
babeb61c-d73d-4b91-9830-b8bfa83c349a  azurefiles
```

O atributo **Nome** na saída corresponde ao nome do trabalho que é criado pelo serviço de backup para a sua operação de restauro. Para acompanhar o estado do trabalho, use o [az backup job show](/cli/azure/backup/job#az_backup_job_show) cmdlet.

## <a name="item-level-recovery"></a>Recuperação ao nível do item

Pode utilizar esta opção de restauro para restaurar ficheiros ou pastas individuais no local original ou alternativo.

Definir os seguintes parâmetros para executar operações de restauro:

* **--nome do contentor**: o nome da conta de armazenamento que acolhe a parte original do ficheiro. Para recuperar o nome ou o nome amigável do seu recipiente, utilize o comando [da lista de contentores de reserva az.](/cli/azure/backup/container#az_backup_container_list)
* **--nome do item**: O nome da partilha de ficheiros original com ressarçamento que pretende utilizar para a operação de restauro. Para recuperar o nome ou o nome amigável do seu item de reserva, utilize o comando [da lista de produtos de reserva az.](/cli/azure/backup/item#az_backup_item_list)

Especifique os seguintes parâmetros para os itens que pretende recuperar:

* **SourceFilePath**: O caminho absoluto do ficheiro, a restaurar dentro da partilha de ficheiros, como uma cadeia. Este caminho é o mesmo caminho usado no download de [ficheiros de armazenamento az](/cli/azure/storage/file#az_storage_file_download) ou [az armazenamento de arquivos](/cli/azure/storage/file#az_storage_file_show) CLI comandos.
* **SourceFileType**: Escolha se um diretório ou um ficheiro são selecionados. Aceita **Diretório** ou **Arquivo.**
* **ResolveConflict**: Instrução se houver um conflito com os dados restaurados. Aceita **Overwrite** ou **Skip**.

### <a name="restore-individual-files-or-folders-to-the-original-location"></a>Restaurar ficheiros ou pastas individuais para a localização original

Utilize o cmdlet restaurar o [restauro-azurefiles az](/cli/azure/backup/restore#az_backup_restore_restore_azurefiles) com modo de restauro definido para *a instalação original* para restaurar ficheiros ou pastas específicos para a sua localização original.

O exemplo a seguir restaura o ficheiro *RestoreTest.txt* na sua localização original: a partilha *de ficheiros azurefiles.*

```azurecli-interactive
az backup restore restore-azurefiles --vault-name azurefilesvault --resource-group azurefiles --rp-name 932881556234035474 --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --restore-mode originallocation  --source-file-type file --source-file-path "Restore/RestoreTest.txt" --resolve-conflict overwrite  --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
df4d9024-0dcb-4edc-bf8c-0a3d18a25319  azurefiles
```

O atributo **Nome** na saída corresponde ao nome do trabalho que é criado pelo serviço de backup para a sua operação de restauro. Para acompanhar o estado do trabalho, use o [az backup job show](/cli/azure/backup/job#az_backup_job_show) cmdlet.

### <a name="restore-individual-files-or-folders-to-an-alternate-location"></a>Restaurar ficheiros ou pastas individuais para uma localização alternativa

Para restaurar ficheiros ou pastas específicos para uma localização alternativa, utilize o cmdlet [restaurar azurefiles de restauro az](/cli/azure/backup/restore#az_backup_restore_restore_azurefiles) com modo de restauro definido para *alternar* e especificar os seguintes parâmetros relacionados com o alvo:

* **--conta-alvo-armazenamento**: a conta de armazenamento à qual o conteúdo de ressarimento é restaurado. A conta de armazenamento do alvo deve estar no mesmo local que o cofre.
* **--target-file-share**: A parte do ficheiro na conta de armazenamento-alvo para a qual o conteúdo de back-up é restaurado.
* **--pasta-alvo**: A pasta sob a partilha de ficheiros para a qual os dados são restaurados. Se o conteúdo de apoio for restaurado para uma pasta de raiz, dê o valor da pasta-alvo como uma corda vazia.

O exemplo a seguir restaura o ficheiro *RestoreTest.txt* originalmente presente na partilha de ficheiros *azurefiles* para uma localização alternativa: a pasta *restoredata* na partilha *de ficheiros azurefiles1* hospedada na conta de armazenamento *afaccount1.*

```azurecli-interactive
az backup restore restore-azurefiles --vault-name azurefilesvault --resource-group azurefiles --rp-name 932881556234035474 --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --restore-mode alternatelocation --target-storage-account afaccount1 --target-file-share azurefiles1 --target-folder restoredata --resolve-conflict overwrite --source-file-type file --source-file-path "Restore/RestoreTest.txt" --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
df4d9024-0dcb-4edc-bf8c-0a3d18a25319  azurefiles
```

O atributo **Nome** na saída corresponde ao nome do trabalho que é criado pelo serviço de backup para a sua operação de restauro. Para acompanhar o estado do trabalho, use o [az backup job show](/cli/azure/backup/job#az_backup_job_show) cmdlet.

## <a name="restore-multiple-files-or-folders-to-original-or-alternate-location"></a>Restaurar vários ficheiros ou pastas para localização original ou alternativa

Para executar a restauração de vários itens, passe o valor para o parâmetro de **caminho de ficheiro de origem** como caminhos **separados** de espaço de todos os ficheiros ou pastas que pretende restaurar.

O exemplo a seguir restaura os *ficheiros de Report.docx* *de testesRestore.txt* e AFS na sua localização original.

```azurecli-interactive
az backup restore restore-azurefiles --vault-name azurefilesvault --resource-group azurefiles --rp-name 932889937058317910 --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --restore-mode originallocation  --source-file-type file --source-file-path "Restore Test.txt" "AFS Testing Report.docx" --resolve-conflict overwrite  --out table
```

O resultado será semelhante ao seguinte:

```output
Name                                          ResourceGroup
------------------------------------          ---------------
649b0c14-4a94-4945-995a-19e2aace0305          azurefiles
```

O atributo **Nome** na saída corresponde ao nome do trabalho que é criado pelo serviço de backup para a sua operação de restauro. Para acompanhar o estado do trabalho, use o [az backup job show](/cli/azure/backup/job#az_backup_job_show) cmdlet.

Se pretender restaurar vários itens para uma localização alternativa, utilize o comando acima especificando parâmetros relacionados com o alvo, conforme explicado nos [ficheiros ou pastas individuais do Restore para uma secção de localização alternativa.](#restore-individual-files-or-folders-to-an-alternate-location)

## <a name="next-steps"></a>Passos seguintes

Saiba como gerir o [ficheiro Azure partilhar backups com o Azure CLI](manage-afs-backup-cli.md).
