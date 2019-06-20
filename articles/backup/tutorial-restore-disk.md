---
title: Restaurar um disco da VM com o Azure Backup
description: Saiba como restaurar um disco e criar e recuperar uma VM no Azure com Serviços de Cópia de Segurança e de Recuperação.
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: tutorial
ms.date: 01/31/2019
ms.author: dacurwin
ms.custom: mvc
ms.openlocfilehash: 70431870027cc27d886995b0bf7f47108ad767fa
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67273934"
---
# <a name="restore-a-disk-and-create-a-recovered-vm-in-azure"></a>Restaurar um disco e criar uma VM recuperada no Azure
O Azure Backup cria pontos de recuperação que são armazenados em cofres de recuperação georredundantes. Quando restaurar a partir de um ponto de recuperação, pode restaurar a VM completa ou ficheiros individuais. Este artigo explica como restaurar uma VM completa através do CLI. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Listar e selecionar pontos de recuperação
> * Restaurar um disco a partir de um ponto de recuperação
> * Criar uma VM a partir do disco restaurado

Para obter informações sobre como utilizar o PowerShell para restaurar um disco e criar uma VM recuperada, consulte [Cópia de segurança e restauro de VMs do Azure com o PowerShell](backup-azure-vms-automation.md#restore-an-azure-vm).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar a CLI localmente, este tutorial requer a execução da versão 2.0.18 ou posterior da CLI do Azure. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure]( /cli/azure/install-azure-cli). 


## <a name="prerequisites"></a>Pré-requisitos
Este tutorial requer uma VM Linux que tenha sido protegida com o Azure Backup. Para simular um processo de recuperação e eliminação acidental da VM, crie uma VM a partir de um disco num ponto de recuperação. Se precisar de uma VM Linux que tenha sido protegida com o Azure Backup, veja [Realizar uma cópia de segurança de uma máquina virtual no Azure com a CLI](quick-backup-vm-cli.md).


## <a name="backup-overview"></a>Descrição geral da Cópia de Segurança
Quando o Azure inicia uma cópia de segurança, a extensão da cópia de segurança na VM tira um instantâneo de ponto no tempo. A extensão da cópia de segurança é instalada na VM quando a primeira cópia de segurança é pedida. O Azure Backup também pode tirar um instantâneo de armazenamento subjacente se a VM não estiver em execução quando a cópia de segurança iniciar.

Por predefinição, o Azure Backup cria uma cópia de segurança consistente com o sistema de ficheiros. Assim que o Azure Backup tira o instantâneo, os dados são transferidos para o cofre dos Serviços de Recuperação. Para maximizar a eficiência, o Azure Backup identifica e transfere apenas os blocos de dados que foram alterados desde a cópia de segurança anterior.

Quando a transferência de dados estiver concluída, o instantâneo é removido e é criado um ponto de recuperação.


## <a name="list-available-recovery-points"></a>Listar pontos de recuperação disponíveis
Para restaurar um disco, selecione um ponto de recuperação como a origem dos dados de recuperação. Uma vez que a política predefinida cria um ponto de recuperação por dia e mantêm-nos durante 30 dias, pode manter um conjunto de pontos de recuperação que lhe permite selecionar um ponto específico no tempo para a recuperação. 

Para ver uma lista dos pontos de recuperação disponíveis, utilize [az backup recoverypoint list](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-list). O **nome** do ponto de recuperação é utilizado para recuperar os discos. Neste tutorial, queremos os pontos de recuperação mais recentes disponíveis. O parâmetro `--query [0].name` seleciona o nome do ponto de recuperação mais recente da seguinte forma:

```azurecli-interactive
az backup recoverypoint list \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --container-name myVM \
    --item-name myVM \
    --query [0].name \
    --output tsv
```


## <a name="restore-a-vm-disk"></a>Restaurar um disco da VM
Para restaurar o disco a partir do ponto de recuperação, primeiro tem de criar uma conta de armazenamento do Azure. Esta conta de armazenamento é utilizada para armazenar o disco restaurado. Nos passos adicionais, o disco restaurado é utilizado para criar uma VM.

1. Para criar uma conta de armazenamento, utilize [az storage account create](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create). O nome da conta de armazenamento tem de estar todo em minúsculas e ser globalmente exclusivo. Substitua *mystorageaccount* pelo seu próprio nome exclusivo:

    ```azurecli-interactive
    az storage account create \
        --resource-group myResourceGroup \
        --name mystorageaccount \
        --sku Standard_LRS
    ```

2. Restaure o disco do ponto de recuperação com [az backup restore restore-disks](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-disks). Substitua *mystorageaccount* pelo nome da conta de armazenamento que criou no comando anterior. Substitua *myRecoveryPointName* pelo nome do ponto de recuperação que obteve na saída a partir do comando anterior [az backup recoverypoint list](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-list):

    ```azurecli-interactive
    az backup restore restore-disks \
        --resource-group myResourceGroup \
        --vault-name myRecoveryServicesVault \
        --container-name myVM \
        --item-name myVM \
        --storage-account mystorageaccount \
        --rp-name myRecoveryPointName
    ```


## <a name="monitor-the-restore-job"></a>Monitorizar a tarefa de restauro
Para monitorizar o estado da tarefa de restauro, utilize [az backup job list](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-list):

```azurecli-interactive 
az backup job list \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --output table
```

A saída é semelhante ao exemplo seguinte, o qual mostra que a tarefa de restauro está *InProgress* (Em curso):

```
Name      Operation        Status      Item Name    Start Time UTC       Duration
--------  ---------------  ----------  -----------  -------------------  --------------
7f2ad916  Restore          InProgress  myvm         2017-09-19T19:39:52  0:00:34.520850
a0a8e5e6  Backup           Completed   myvm         2017-09-19T03:09:21  0:15:26.155212
fe5d0414  ConfigureBackup  Completed   myvm         2017-09-19T03:03:57  0:00:31.191807
```

Quando o *Estado* dos relatórios da tarefa restauro é *Concluído*, o disco foi restaurado para a conta de armazenamento.


## <a name="convert-the-restored-disk-to-a-managed-disk"></a>Converter disco restaurado num Disco Gerido
A tarefa de restauro cria um disco não gerido. Para criar uma VM a partir do disco, primeiro tem de ser convertido para um disco gerido.

1. Obtenha as informações de ligação para a sua conta de armazenamento com [az storage account show-connection-string](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-show-connection-string). Substitua *storageaccountname* pelo nome da sua conta de armazenamento da seguinte forma:
    
    ```azurecli-interactive
    export AZURE_STORAGE_CONNECTION_STRING=$( az storage account show-connection-string \
        --resource-group myResourceGroup \
        --output tsv \
        --name mystorageaccount )
    ```

2. O disco não gerido está protegido na conta de armazenamento. Os seguintes comandos obtêm informações sobre o disco não gerido e criam uma variável denominada *uri* que é utilizada no próximo passo, ao criar o Disco Gerido.

    ```azurecli-interactive
    container=$(az storage container list --query [0].name -o tsv)
    blob=$(az storage blob list --container-name $container --query [0].name -o tsv)
    uri=$(az storage blob url --container-name $container --name $blob -o tsv)
    ```

3. Agora pode criar um Disco Gerido a partir do seu disco recuperado com [az disk create](https://docs.microsoft.com/cli/azure/disk?view=azure-cli-latest#az-disk-create). O *uri* variável do passo anterior é utilizado como a origem para o Disco Gerido.

    ```azurecli-interactive
    az disk create \
        --resource-group myResourceGroup \
        --name myRestoredDisk \
        --source $uri
    ```

4. Agora que tem um Disco Gerido a partir do seu disco restaurado, limpe a conta de armazenamento e o disco não gerido com [az storage account delete](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-delete). Substitua *storageaccountname* pelo nome da sua conta de armazenamento da seguinte forma:

    ```azurecli-interactive
    az storage account delete \
        --resource-group myResourceGroup \
        --name mystorageaccount
    ```


## <a name="create-a-vm-from-the-restored-disk"></a>Criar uma VM a partir do disco restaurado
O último passo é criar uma VM a partir do Disco Gerido.

1. Crie uma VM a partir do Disco Gerido com [az vm create](/cli/azure/vm?view=azure-cli-latest#az-vm-create) da seguinte forma:

    ```azurecli-interactive
    az vm create \
        --resource-group myResourceGroup \
        --name myRestoredVM \
        --attach-os-disk myRestoredDisk \
        --os-type linux
    ```

2. Para confirmar que a VM foi criada a partir do disco recuperado, liste as VMs no seu grupo de recursos com [az vm list](/cli/azure/vm?view=azure-cli-latest#az-vm-list) da seguinte forma:

    ```azurecli-interactive
    az vm list --resource-group myResourceGroup --output table
    ```


## <a name="next-steps"></a>Passos Seguintes
Neste tutorial, restaurou um disco a partir de um ponto de recuperação e, em seguida, criou uma VM a partir do disco. Aprendeu a:

> [!div class="checklist"]
> * Listar e selecionar pontos de recuperação
> * Restaurar um disco a partir de um ponto de recuperação
> * Criar uma VM a partir do disco restaurado

Avance para o próximo tutorial para saber mais sobre o restauro de ficheiros individuais a partir de um ponto de recuperação.

> [!div class="nextstepaction"]
> [Restaurar ficheiros para uma máquina virtual no Azure](tutorial-restore-files.md)

