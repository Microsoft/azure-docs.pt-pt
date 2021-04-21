---
title: Tutorial - Restaurar um VM com Azure CLI
description: Saiba como restaurar um disco e criar e recuperar uma VM no Azure com Serviços de Cópia de Segurança e de Recuperação.
ms.topic: tutorial
ms.date: 01/31/2019
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 7f4d70f43f76c3a72cd8e53037d06d32e61c3cdb
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107768515"
---
# <a name="restore-a-vm-with-azure-cli"></a>Restaurar uma VM com a CLI do Azure

O Azure Backup cria pontos de recuperação que são armazenados em cofres de recuperação georredundantes. Quando restaurar a partir de um ponto de recuperação, pode restaurar a VM completa ou ficheiros individuais. Este artigo explica como restaurar uma VM completa através do CLI. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
>
> * Listar e selecionar pontos de recuperação
> * Restaurar um disco a partir de um ponto de recuperação
> * Criar uma VM a partir do disco restaurado

Para obter informações sobre como utilizar o PowerShell para restaurar um disco e criar uma VM recuperada, consulte [Cópia de segurança e restauro de VMs do Azure com o PowerShell](backup-azure-vms-automation.md#restore-an-azure-vm).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

 - Este tutorial requer a versão 2.0.18 ou posterior do Azure CLI. Se utilizar o Azure Cloud Shell, a versão mais recente já está instalada.

 - Este tutorial requer uma VM Linux que tenha sido protegida com o Azure Backup. Para simular um processo de recuperação e eliminação acidental da VM, crie uma VM a partir de um disco num ponto de recuperação. Se precisar de uma VM Linux que tenha sido protegida com o Azure Backup, veja [Realizar uma cópia de segurança de uma máquina virtual no Azure com a CLI](quick-backup-vm-cli.md).

## <a name="backup-overview"></a>Descrição geral da Cópia de Segurança

Quando o Azure inicia uma cópia de segurança, a extensão da cópia de segurança na VM tira um instantâneo de ponto no tempo. A extensão da cópia de segurança é instalada na VM quando a primeira cópia de segurança é pedida. O Azure Backup também pode tirar uma foto do armazenamento subjacente se o VM não estiver a funcionar quando a cópia de segurança ocorrer.

Por predefinição, o Azure Backup cria uma cópia de segurança consistente com o sistema de ficheiros. Assim que o Azure Backup tira o instantâneo, os dados são transferidos para o cofre dos Serviços de Recuperação. Para maximizar a eficiência, o Azure Backup identifica e transfere apenas os blocos de dados que foram alterados desde a cópia de segurança anterior.

Quando a transferência de dados estiver concluída, o instantâneo é removido e é criado um ponto de recuperação.

## <a name="list-available-recovery-points"></a>Listar pontos de recuperação disponíveis

Para restaurar um disco, selecione um ponto de recuperação como a origem dos dados de recuperação. Uma vez que a política predefinida cria um ponto de recuperação por dia e mantêm-nos durante 30 dias, pode manter um conjunto de pontos de recuperação que lhe permite selecionar um ponto específico no tempo para a recuperação.

Para ver uma lista dos pontos de recuperação disponíveis, utilize [az backup recoverypoint list](/cli/azure/backup/recoverypoint#az_backup_recoverypoint_list). O **nome** do ponto de recuperação é utilizado para recuperar os discos. Neste tutorial, queremos os pontos de recuperação mais recentes disponíveis. O parâmetro `--query [0].name` seleciona o nome do ponto de recuperação mais recente da seguinte forma:

```azurecli-interactive
az backup recoverypoint list \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --backup-management-type AzureIaasVM \
    --container-name myVM \
    --item-name myVM \
    --query [0].name \
    --output tsv
```

## <a name="restore-a-vm-disk"></a>Restaurar um disco da VM

> [!IMPORTANT]
> É muito fortemente recomendado usar a versão 2.0.74 do Az CLI ou mais tarde para obter todos os benefícios de uma restauração rápida, incluindo a restauração do disco gerido. É melhor usar sempre a versão mais recente.

### <a name="managed-disk-restore"></a>Restauro do disco gerido

Se o VM de back-up tiver gerido discos e se a intenção é restaurar os discos geridos a partir do ponto de recuperação, primeiro fornece uma conta de armazenamento Azure. Esta conta de armazenamento é usada para armazenar a configuração VM e o modelo de implementação que pode ser posteriormente utilizado para implantar o VM a partir dos discos restaurados. Em seguida, também fornece um grupo de recursos-alvo para que os discos geridos sejam restaurados.

1. Para criar uma conta de armazenamento, utilize [az storage account create](/cli/azure/storage/account#az_storage_account_create). O nome da conta de armazenamento tem de estar todo em minúsculas e ser globalmente exclusivo. Substitua *mystorageaccount* pelo seu próprio nome exclusivo:

    ```azurecli-interactive
    az storage account create \
        --resource-group myResourceGroup \
        --name mystorageaccount \
        --sku Standard_LRS
    ```

2. Restaure o disco do ponto de recuperação com [az backup restore restore-disks](/cli/azure/backup/restore#az_backup_restore_restore_disks). Substitua *mystorageaccount* pelo nome da conta de armazenamento que criou no comando anterior. Substitua *o meu NomeRecoveryPoint por* o nome do ponto de recuperação obtido na saída do comando anterior da lista de pontos de recuperação de backup [az.](/cli/azure/backup/recoverypoint#az_backup_recoverypoint_list) ***Forneça também o grupo de recursos-alvo ao qual os discos geridos são restaurados em***.

    ```azurecli-interactive
    az backup restore restore-disks \
        --resource-group myResourceGroup \
        --vault-name myRecoveryServicesVault \
        --container-name myVM \
        --item-name myVM \
        --storage-account mystorageaccount \
        --rp-name myRecoveryPointName \
        --target-resource-group targetRG
    ```

    > [!WARNING]
    > Se o **grupo de recursos-alvo** não for fornecido, então os discos geridos serão restaurados como discos não geridos para a conta de armazenamento dada. Isto terá consequências significativas para o tempo de restauro, uma vez que o tempo necessário para restaurar os discos depende inteiramente da conta de armazenamento dada. Só terá o benefício da restauração instantânea quando o parâmetro do grupo de recursos-alvo for dado. Se a intenção é restaurar os discos geridos como não geridos, então não forneça o parâmetro **do grupo de recursos-alvo** e, em vez disso, forneça o parâmetro de restaurar o parâmetro como não **gerido-disco,** como mostrado abaixo. Este parâmetro está disponível a partir de az 3.4.0 em diante.

    ```azurecli-interactive
    az backup restore restore-disks \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --container-name myVM \
    --item-name myVM \
    --storage-account mystorageaccount \
    --rp-name myRecoveryPointName \
    --restore-as-unmanaged-disk
    ```

Isto irá restaurar os discos geridos como discos não geridos para a conta de armazenamento dada e não estará aproveitando a funcionalidade de restauro 'instantâneo'. Em futuras versões do CLI, será obrigatório fornecer o parâmetro do **grupo de recursos-alvo** ou o parâmetro **de disco restaurador como não gerido.**

### <a name="unmanaged-disks-restore"></a>Discos não geridos restauram

Se o VM de back-up tiver discos não geridos e se a intenção for restaurar os discos do ponto de recuperação, primeiro fornece uma conta de armazenamento Azure. Esta conta de armazenamento é usada para armazenar a configuração VM e o modelo de implementação que pode ser posteriormente utilizado para implantar o VM a partir dos discos restaurados. Por predefinição, os discos não geridos serão restaurados nas suas contas de armazenamento originais. Se desejar restaurar todos os discos não geridos num único local, então a conta de armazenamento dada também pode ser usada como um local de preparação para esses discos também.

Nos passos adicionais, o disco restaurado é utilizado para criar uma VM.

1. Para criar uma conta de armazenamento, utilize [az storage account create](/cli/azure/storage/account#az_storage_account_create). O nome da conta de armazenamento tem de estar todo em minúsculas e ser globalmente exclusivo. Substitua *mystorageaccount* pelo seu próprio nome exclusivo:

    ```azurecli-interactive
    az storage account create \
        --resource-group myResourceGroup \
        --name mystorageaccount \
        --sku Standard_LRS
    ```

2. Restaure o disco do ponto de recuperação com [az backup restore restore-disks](/cli/azure/backup/restore#az_backup_restore_restore_disks). Substitua *mystorageaccount* pelo nome da conta de armazenamento que criou no comando anterior. Substitua *myRecoveryPointName* pelo nome do ponto de recuperação que obteve na saída a partir do comando anterior [az backup recoverypoint list](/cli/azure/backup/recoverypoint#az_backup_recoverypoint_list):

    ```azurecli-interactive
    az backup restore restore-disks \
        --resource-group myResourceGroup \
        --vault-name myRecoveryServicesVault \
        --container-name myVM \
        --item-name myVM \
        --storage-account mystorageaccount \
        --rp-name myRecoveryPointName
    ```

Como mencionado acima, os discos não geridos serão restaurados na sua conta de armazenamento original. Isto proporciona o melhor desempenho de restauro. Mas se todos os discos não geridos precisarem de ser restaurados para uma conta de armazenamento dada, então use a bandeira relevante como mostrado abaixo.

```azurecli-interactive
    az backup restore restore-disks \
        --resource-group myResourceGroup \
        --vault-name myRecoveryServicesVault \
        --container-name myVM \
        --item-name myVM \
        --storage-account mystorageaccount \
        --rp-name myRecoveryPointName \
        --restore-to-staging-storage-account
    ```

## Monitor the restore job

To monitor the status of restore job, use [az backup job list](/cli/azure/backup/job#az_backup_job_list):

```azurecli-interactive
az backup job list \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --output table
```

A saída é semelhante ao exemplo seguinte, o qual mostra que a tarefa de restauro está *InProgress* (Em curso):

```output
Name      Operation        Status      Item Name    Start Time UTC       Duration
--------  ---------------  ----------  -----------  -------------------  --------------
7f2ad916  Restore          InProgress  myvm         2017-09-19T19:39:52  0:00:34.520850
a0a8e5e6  Backup           Completed   myvm         2017-09-19T03:09:21  0:15:26.155212
fe5d0414  ConfigureBackup  Completed   myvm         2017-09-19T03:03:57  0:00:31.191807
```

Quando o *Estado* do local de trabalho de restauração *concluído,* as informações necessárias (configuração VM e o modelo de implantação) foram restauradas na conta de armazenamento.

## <a name="create-a-vm-from-the-restored-disk"></a>Criar uma VM a partir do disco restaurado

O passo final é criar um VM a partir dos discos restaurados. Pode utilizar o modelo de implementação descarregado na conta de armazenamento dada para criar o VM.

### <a name="fetch-the-job-details"></a>Pegue os detalhes do Trabalho

Os detalhes do trabalho resultantes dão ao modelo URI que pode ser consultado e implementado. Use o comando do show de trabalho para obter mais detalhes para o trabalho restaurado desencadeado.

```azurecli-interactive
az backup job show \
    -v myRecoveryServicesVault \
    -g myResourceGroup \
    -n 1fc2d55d-f0dc-4ca6-ad48-aca0fe5d0414
```

A produção desta consulta dará todos os detalhes, mas estamos interessados apenas no conteúdo da conta de armazenamento. Podemos usar a capacidade de [consulta](/cli/azure/query-azure-cli) do Azure CLI para obter os detalhes relevantes

```azurecli-interactive
az backup job show \
    -v myRecoveryServicesVault \
    -g myResourceGroup \
    -n 1fc2d55d-f0dc-4ca6-ad48-aca0fe5d0414 \
    --query properties.extendedInfo.propertyBag

{
  "Config Blob Container Name": "myVM-daa1931199fd4a22ae601f46d8812276",
  "Config Blob Name": "config-myVM-1fc2d55d-f0dc-4ca6-ad48-aca0fe5d0414.json",
  "Config Blob Uri": "https://mystorageaccount.blob.core.windows.net/myVM-daa1931199fd4a22ae601f46d8812276/config-appvm8-1fc2d55d-f0dc-4ca6-ad48-aca0519c0232.json",
  "Job Type": "Recover disks",
  "Recovery point time ": "12/25/2019 10:07:11 PM",
  "Target Storage Account Name": "mystorageaccount",
  "Target resource group": "mystorageaccountRG",
  "Template Blob Uri": "https://mystorageaccount.blob.core.windows.net/myVM-daa1931199fd4a22ae601f46d8812276/azuredeploy1fc2d55d-f0dc-4ca6-ad48-aca0519c0232.json"
}
```

### <a name="fetch-the-deployment-template"></a>Pegue o modelo de implementação

O modelo não é diretamente acessível, uma vez que está sob a conta de armazenamento de um cliente e o recipiente dado. Precisamos do URL completo (juntamente com um token SAS temporário) para aceder a este modelo.

Primeiro, extraia o modelo blob Uri dos detalhes do trabalho

```azurecli-interactive
az backup job show \
    -v myRecoveryServicesVault \
    -g myResourceGroup \
    -n 1fc2d55d-f0dc-4ca6-ad48-aca0fe5d0414 \
    --query properties.extendedInfo.propertyBag."""Template Blob Uri"""

"https://mystorageaccount.blob.core.windows.net/myVM-daa1931199fd4a22ae601f46d8812276/azuredeploy1fc2d55d-f0dc-4ca6-ad48-aca0519c0232.json"
```

O modelo blob Uri será deste formato e extrairá o nome do modelo

```https
https://<storageAccountName.blob.core.windows.net>/<containerName>/<templateName>
```

Assim, o nome do modelo do exemplo acima será ```azuredeploy1fc2d55d-f0dc-4ca6-ad48-aca0519c0232.json``` e o nome do recipiente é ```myVM-daa1931199fd4a22ae601f46d8812276```

Agora obtenha o símbolo SAS para este recipiente e modelo como detalhado [aqui](../azure-resource-manager/templates/secure-template-with-sas-token.md?tabs=azure-cli#provide-sas-token-during-deployment)

```azurecli-interactive
expiretime=$(date -u -d '30 minutes' +%Y-%m-%dT%H:%MZ)
connection=$(az storage account show-connection-string \
    --resource-group mystorageaccountRG \
    --name mystorageaccount \
    --query connectionString)
token=$(az storage blob generate-sas \
    --container-name myVM-daa1931199fd4a22ae601f46d8812276 \
    --name azuredeploy1fc2d55d-f0dc-4ca6-ad48-aca0519c0232.json \
    --expiry $expiretime \
    --permissions r \
    --output tsv \
    --connection-string $connection)
url=$(az storage blob url \
   --container-name myVM-daa1931199fd4a22ae601f46d8812276 \
    --name azuredeploy1fc2d55d-f0dc-4ca6-ad48-aca0519c0232.json \
    --output tsv \
    --connection-string $connection)
```

### <a name="deploy-the-template-to-create-the-vm"></a>Implemente o modelo para criar o VM

Agora implemente o modelo para criar o VM como explicado [aqui](../azure-resource-manager/templates/deploy-cli.md).

```azurecli-interactive
az deployment group create \
  --resource-group ExampleGroup \
  --template-uri $url?$token
```

Para confirmar que a VM foi criada a partir do disco recuperado, liste as VMs no seu grupo de recursos com [az vm list](/cli/azure/vm#az_vm_list) da seguinte forma:

```azurecli-interactive
az vm list --resource-group myResourceGroup --output table
```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, restaurou um disco a partir de um ponto de recuperação e, em seguida, criou uma VM a partir do disco. Aprendeu a:

> [!div class="checklist"]
>
> * Listar e selecionar pontos de recuperação
> * Restaurar um disco a partir de um ponto de recuperação
> * Criar uma VM a partir do disco restaurado

Avance para o próximo tutorial para saber mais sobre o restauro de ficheiros individuais a partir de um ponto de recuperação.

> [!div class="nextstepaction"]
> [Restaurar ficheiros para uma máquina virtual no Azure](tutorial-restore-files.md)
