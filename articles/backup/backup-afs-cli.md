---
title: Back up Ações de ficheiros Azure com o Azure CLI
description: Saiba como usar o Azure CLI para apoiar as ações de ficheiros azure no Cofre de Serviços de Recuperação
ms.topic: conceptual
ms.date: 01/14/2020
ms.openlocfilehash: ff1d8c6245521d2d0262b0440177d65713058742
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76844046"
---
# <a name="back-up-azure-file-shares-with-cli"></a>Back up Ações de ficheiros Azure com CLI

A interface de linha de comando Azure (CLI) proporciona uma experiência de linha de comando para gerir os recursos Azure. É uma ótima ferramenta para construir automação personalizada para usar recursos Azure. Este artigo detalha como fazer o backup De ações de ficheiro sinuoso azure com o Azure CLI. Também pode realizar estes passos com o [Azure PowerShell](https://docs.microsoft.com/azure/backup/backup-azure-afs-automation) ou no [portal do Azure](backup-afs.md).

No final deste tutorial, você aprenderá a realizar abaixo as operações com o Azure CLI:

* Criar um cofre dos serviços de recuperação
* Ativar backup para ações de ficheiros Azure
* Desencadear uma cópia de segurança a pedido para ações de ficheiros

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Para instalar e utilizar a CLI localmente, tem de executar a CLI do Azure versão 2.0.18 ou posterior. Para encontrar a versão `run az --version`CLI, . Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="create-a-recovery-services-vault"></a>Criar um cofre de serviços de recuperação

Um cofre de serviço de recuperação é uma entidade que lhe dá uma visão consolidada e capacidade de gestão em todos os itens de backup. Quando a tarefa de cópia de segurança de um recurso protegido é executada, cria um ponto de recuperação dentro do cofre dos Serviços de Recuperação. Em seguida, pode utilizar um destes pontos de recuperação para restaurar dados para um determinado ponto no tempo.

Siga estes passos para criar um cofre de serviços de recuperação:

1. Um cofre é colocado num grupo de recursos. Se não tiver um grupo de recursos existente, crie um novo com grupo [Az criar](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create) . Neste tutorial, criamos o novo grupo de recursos *azurefiles* na região leste dos EUA.

    ```azurecli-interactive
    az group create --name AzureFiles --location eastus --output table
    ```

    ```output
    Location    Name
    ----------  ----------
    eastus      AzureFiles
    ```

2. Use o [cofre de reserva az criar](https://docs.microsoft.com/cli/azure/backup/vault?view=azure-cli-latest#az-backup-vault-create) cmdlet para criar o cofre. Especifique a mesma localização para o cofre que foi usado para o grupo de recursos.

    O exemplo seguinte cria um cofre de serviços de recuperação chamado *azurefilesvault* na região leste dos EUA.

    ```azurecli-interactive
    az backup vault create --resource-group azurefiles --name azurefilesvault --location eastus --output table
    ```

    ```output
    Location    Name                ResourceGroup
    ----------  ----------------    ---------------
    eastus      azurefilesvault     azurefiles
    ```

3. Especifique o tipo de redundância a utilizar para o armazenamento do cofre. Pode utilizar [armazenamento redundante localmente](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs) ou [armazenamento geo-redundante.](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs)

    O exemplo seguinte define a opção de redundância de armazenamento para *azurefilesvault* para **Georedundant** usando o conjunto de propriedades de backup do [cofre de reserva az.](https://docs.microsoft.com/cli/azure/backup/vault/backup-properties?view=azure-cli-latest#az-backup-vault-backup-properties-set)

    ```azurecli-interactive
    az backup vault backup-properties set --name azurefilesvault --resource-group azurefiles --backup-storage-redundancy Georedundant
    ```

    Para verificar se o cofre foi criado com sucesso, pode usar o cofre de [reserva az](https://docs.microsoft.com/cli/azure/backup/vault?view=azure-cli-latest#az-backup-vault-show) para obter detalhes do seu cofre. O exemplo que se segue mostra os detalhes do *cofre azurefilesque* criámos nos degraus acima.

    ```azurecli-interactive
    az backup vault show --name azurefilesvault --resource-group azurefiles --output table
    ```

    A saída será semelhante à seguinte resposta:

    ```output
    Location     Name               ResourceGroup
    ----------   ---------------    ---------------
    eastus       azurefilesvault    azurefiles
    ```

## <a name="enable-backup-for-azure-file-shares"></a>Ativar backup para ações de ficheiros Azure

Esta secção pressupõe que já tem uma partilha de ficheiros Azure para a qual pretende configurar a cópia de segurança. Se não tiver uma, crie uma partilha de ficheiros Azure utilizando a parte de [armazenamento az criar](https://docs.microsoft.com/cli/azure/storage/share?view=azure-cli-latest#az-storage-share-create) comando.

Para permitir o backup para partilhas de ficheiros, é necessário criar uma política de proteção que defina quando um trabalho de backup funciona e quanto tempo os pontos de recuperação são armazenados. Pode criar uma política de backup utilizando a política de [backup az criar](https://docs.microsoft.com/cli/azure/backup/policy?view=azure-cli-latest#az-backup-policy-create) cmdlet.

O exemplo seguinte utiliza o cmdlet de proteção de [backup az para azurefileshare](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-enable-for-azurefileshare) cmdlet para ativar a cópia de segurança para a parte de ficheiro sinuosa da conta de armazenamento *afsaccount* utilizando a política de backup *de agenda 1:* *azurefiles*

```azurecli-interactive
az backup protection enable-for-azurefileshare --vault-name azurefilesvault --resource-group  azurefiles --policy-name schedule1 --storage-account afsaccount --azure-file-share azurefiles  --output table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
0caa93f4-460b-4328-ac1d-8293521dd928  azurefiles
```

O atributo **nome** na saída corresponde ao nome do trabalho que é criado pelo serviço de backup para a sua operação **de backup ativa.** Para rastrear o estado do trabalho, use o show de [trabalho de reserva az](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) cmdlet.

## <a name="trigger-an-on-demand-backup-for-file-share"></a>Desencadear uma cópia de segurança a pedido para a partilha de ficheiros

Se quiser acionar um backup a pedido para a sua parte de ficheiro em vez de esperar que a política de backup execute o trabalho na hora programada, use o backup de backup de proteção de [reserva az agora](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-backup-now) cmdlet.

É necessário definir os seguintes parâmetros para desencadear uma cópia de segurança a pedido:

* **-nome do contentor** é o nome da conta de armazenamento que acolhe a parte do ficheiro. Para recuperar o **nome** ou **o nome amigável** do seu recipiente, utilize o comando da lista de contentores de reserva [az.](/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list)
* **-nome do item** é o nome da parte do ficheiro para a qual pretende desencadear uma cópia de segurança a pedido. Para recuperar o **nome** ou **o nome amigável** do seu item backed, utilize o comando da lista de itens de reserva [az.](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-list)
* **-retendo até** especificar a data até quando quiser manter o ponto de recuperação. O valor deve ser definido no formato de tempo UTC (dd-mm-yyyy).

O exemplo seguinte desencadeia uma cópia de segurança a pedido para a partilha de *ficheiros em arquivos em azuresfiles* na conta de armazenamento da *afsaccount* com retenção até *20-01-2020*.

```azurecli-interactive
az backup protection backup-now --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --retain-until 20-01-2020 --output table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
9f026b4f-295b-4fb8-aae0-4f058124cb12  azurefiles
```

O atributo **nome** na saída corresponde ao nome do trabalho que é criado pelo serviço de backup para a sua operação de "backup a pedido". Para acompanhar o estado de um trabalho, use o show de [trabalho de reserva az](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) cmdlet.

## <a name="next-steps"></a>Passos seguintes

* Saiba como restaurar as partilhas de [ficheiros Azure com o CLI](restore-afs-cli.md)
* Saiba como gerir ficheiros [Azure partilhar ackups com cli](manage-afs-backup-cli.md)
