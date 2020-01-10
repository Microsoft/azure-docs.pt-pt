---
title: Tutorial – restaurar um disco de VM com o backup do Azure
description: Saiba como restaurar um disco e criar e recuperar uma VM no Azure com Serviços de Cópia de Segurança e de Recuperação.
ms.topic: tutorial
ms.date: 01/31/2019
ms.custom: mvc
ms.openlocfilehash: f0300930d4dbfb7745f0837eb5fa9605a2e766d7
ms.sourcegitcommit: a100e3d8b0697768e15cbec11242e3f4b0e156d3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/06/2020
ms.locfileid: "75680580"
---
# <a name="restore-a-disk-and-create-a-recovered-vm-in-azure"></a>Restaurar um disco e criar uma VM recuperada no Azure

O Azure Backup cria pontos de recuperação que são armazenados em cofres de recuperação georredundantes. Quando restaurar a partir de um ponto de recuperação, pode restaurar a VM completa ou ficheiros individuais. Este artigo explica como restaurar uma VM completa através do CLI. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
>
> * Listar e selecionar pontos de recuperação
> * Restaurar um disco a partir de um ponto de recuperação
> * Criar uma VM a partir do disco restaurado

Para obter informações sobre como utilizar o PowerShell para restaurar um disco e criar uma VM recuperada, consulte [Cópia de segurança e restauro de VMs do Azure com o PowerShell](backup-azure-vms-automation.md#restore-an-azure-vm).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar a CLI localmente, este tutorial requer a execução da versão 2.0.18 ou posterior da CLI do Azure. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial requer uma VM Linux que tenha sido protegida com o Azure Backup. Para simular um processo de recuperação e eliminação acidental da VM, crie uma VM a partir de um disco num ponto de recuperação. Se precisar de uma VM Linux que tenha sido protegida com o Azure Backup, veja [Realizar uma cópia de segurança de uma máquina virtual no Azure com a CLI](quick-backup-vm-cli.md).

## <a name="backup-overview"></a>Descrição geral da cópia de segurança

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

> [!IMPORTANT]
> É altamente recomendável usar AZ CLI versão 2.0.74 ou posterior para obter todos os benefícios de uma restauração rápida, incluindo restauração de disco gerenciado. É melhor se o usuário sempre usar a versão mais recente.

### <a name="managed-disk-restore"></a>Restauração de disco gerenciado

Se a VM de backup tiver discos gerenciados e se a intenção for restaurar discos gerenciados do ponto de recuperação, você primeiro fornecerá uma conta de armazenamento do Azure. Essa conta de armazenamento é usada para armazenar a configuração da VM e o modelo de implantação que pode ser usado posteriormente para implantar a VM dos discos restaurados. Em seguida, você também fornece um grupo de recursos de destino para os discos gerenciados a serem restaurados.

1. Para criar uma conta de armazenamento, utilize [az storage account create](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create). O nome da conta de armazenamento tem de estar todo em minúsculas e ser globalmente exclusivo. Substitua *mystorageaccount* pelo seu próprio nome exclusivo:

    ```azurecli-interactive
    az storage account create \
        --resource-group myResourceGroup \
        --name mystorageaccount \
        --sku Standard_LRS
    ```

2. Restaure o disco do ponto de recuperação com [az backup restore restore-disks](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-disks). Substitua *mystorageaccount* pelo nome da conta de armazenamento que criou no comando anterior. Substitua *myrecoverypointname pelo* pelo nome do ponto de recuperação obtido na saída do comando [AZ backup RecoveryPoint List](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-list) anterior. ***Forneça também o grupo de recursos de destino para o qual os discos gerenciados são restaurados***.

    ```azurecli-interactive
    az backup restore restore-disks \
        --resource-group myResourceGroup \
        --vault-name myRecoveryServicesVault \
        --container-name myVM \
        --item-name myVM \
        --storage-account mystorageaccount \
        --rp-name myRecoveryPointName
        --target-resource-group targetRG
    ```

> [!WARNING]
> Se target-Resource-Group não for fornecido, os discos gerenciados serão restaurados como discos não gerenciados para a conta de armazenamento determinada. Isso terá consequências significativas no tempo de restauração, pois o tempo necessário para restaurar os discos depende totalmente da conta de armazenamento determinada.

### <a name="unmanaged-disks-restore"></a>Restauração de discos não gerenciados

Se a VM de backup tiver discos não gerenciados e se a intenção for restaurar discos do ponto de recuperação, você primeiro fornecerá uma conta de armazenamento do Azure. Essa conta de armazenamento é usada para armazenar a configuração da VM e o modelo de implantação que pode ser usado posteriormente para implantar a VM dos discos restaurados. Por padrão, os discos não gerenciados serão restaurados para suas contas de armazenamento originais. Se o usuário quiser restaurar todos os discos não gerenciados para um único local, a conta de armazenamento fornecida também poderá ser usada como um local de preparo para esses discos.

Nos passos adicionais, o disco restaurado é utilizado para criar uma VM.

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

Conforme mencionado acima, os discos não gerenciados serão restaurados para sua conta de armazenamento original. Isso fornece o melhor desempenho de restauração. Mas se todos os discos não gerenciados precisarem ser restaurados para determinada conta de armazenamento, use o sinalizador relevante, conforme mostrado abaixo.

```azurecli-interactive
    az backup restore restore-disks \
        --resource-group myResourceGroup \
        --vault-name myRecoveryServicesVault \
        --container-name myVM \
        --item-name myVM \
        --storage-account mystorageaccount \
        --rp-name myRecoveryPointName
        --restore-to-staging-storage-account
    ```

## Monitor the restore job

To monitor the status of restore job, use [az backup job list](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-list):

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

Quando o *status* dos relatórios do trabalho de restauração for *concluído*, as informações necessárias (configuração da VM e o modelo de implantação) foram restauradas para a conta de armazenamento.

## <a name="create-a-vm-from-the-restored-disk"></a>Criar uma VM a partir do disco restaurado

A etapa final é criar uma VM com base nos discos restaurados. Você pode usar o modelo de implantação baixado para a conta de armazenamento determinada para criar a VM.

### <a name="fetch-the-job-details"></a>Buscar os detalhes do trabalho

Os detalhes do trabalho resultante fornecem o URI do modelo que pode ser consultado e implantado. Use o comando trabalho show para obter mais detalhes sobre o trabalho restaurado disparado.

```azurecli-interactive
az backup job show \
    -v myRecoveryServicesVault \
    -g myResourceGroup \
    -n 1fc2d55d-f0dc-4ca6-ad48-aca0fe5d0414
```

A saída dessa consulta fornecerá todos os detalhes, mas estamos interessados apenas no conteúdo da conta de armazenamento. Podemos usar o [recurso de consulta](https://docs.microsoft.com/cli/azure/query-azure-cli?view=azure-cli-latest) de CLI do Azure para buscar os detalhes relevantes

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

### <a name="fetch-the-deployment-template"></a>Buscar o modelo de implantação

O modelo não é acessível diretamente, pois está sob a conta de armazenamento de um cliente e o contêiner fornecido. Precisamos da URL completa (junto com um token SAS temporário) para acessar esse modelo.

Primeiro, extraia o URI do blob de modelo dos detalhes do trabalho

```azurecli-interactive
az backup job show \
    -v myRecoveryServicesVault \
    -g myResourceGroup \
    -n 1fc2d55d-f0dc-4ca6-ad48-aca0fe5d0414 \
    --query properties.extendedInfo.propertyBag."""Template Blob Uri"""

"https://mystorageaccount.blob.core.windows.net/myVM-daa1931199fd4a22ae601f46d8812276/azuredeploy1fc2d55d-f0dc-4ca6-ad48-aca0519c0232.json"
```

O URI do blob de modelo será desse formato e extrairá o nome do modelo

```https
https://<storageAccountName.blob.core.windows.net>/<containerName>/<templateName>
```

Portanto, o nome do modelo do exemplo acima será ```azuredeploy1fc2d55d-f0dc-4ca6-ad48-aca0519c0232.json``` e o nome do contêiner será ```myVM-daa1931199fd4a22ae601f46d8812276```

Agora obtenha o token SAS para este contêiner e modelo, conforme detalhado [aqui](https://docs.microsoft.com/azure/azure-resource-manager/templates/secure-template-with-sas-token?tabs=azure-cli#provide-sas-token-during-deployment)

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

### <a name="deploy-the-template-to-create-the-vm"></a>Implantar o modelo para criar a VM

Agora, implante o modelo para criar a VM, conforme explicado [aqui](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-cli).

```azurecli-interactive
az group deployment create \
  --resource-group ExampleGroup \
  --template-uri $url?$token
```

Para confirmar que a VM foi criada a partir do disco recuperado, liste as VMs no seu grupo de recursos com [az vm list](/cli/azure/vm?view=azure-cli-latest#az-vm-list) da seguinte forma:

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
