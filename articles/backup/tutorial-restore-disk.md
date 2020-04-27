---
title: Tutorial - Restaurar um disco VM com backup Azure
description: Saiba como restaurar um disco e criar e recuperar uma VM no Azure com Serviços de Cópia de Segurança e de Recuperação.
ms.topic: tutorial
ms.date: 01/31/2019
ms.custom: mvc
ms.openlocfilehash: 56410b5302611d5de3d72f727e1a4c36bd49ca7e
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/26/2020
ms.locfileid: "82160943"
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
    --backup-management-type AzureIaasVM
    --container-name myVM \
    --item-name myVM \
    --query [0].name \
    --output tsv
```

## <a name="restore-a-vm-disk"></a>Restaurar um disco da VM

> [!IMPORTANT]
> É fortemente recomendado usar a versão Az CLI 2.0.74 ou mais tarde para obter todos os benefícios de uma restauração rápida, incluindo a restauração do disco gerido. É melhor que o utilizador utilize sempre a versão mais recente.

### <a name="managed-disk-restore"></a>Restauro de disco gerido

Se o VM apoiado tiver gerido discos e se a intenção é restaurar os discos geridos a partir do ponto de recuperação, fornece primeiro uma conta de armazenamento Azure. Esta conta de armazenamento é usada para armazenar a configuração VM e o modelo de implementação que pode ser usado mais tarde para implantar o VM a partir dos discos restaurados. Em seguida, também fornece um grupo de recursos-alvo para que os discos geridos sejam restaurados.

1. Para criar uma conta de armazenamento, utilize [az storage account create](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create). O nome da conta de armazenamento tem de estar todo em minúsculas e ser globalmente exclusivo. Substitua *mystorageaccount* pelo seu próprio nome exclusivo:

    ```azurecli-interactive
    az storage account create \
        --resource-group myResourceGroup \
        --name mystorageaccount \
        --sku Standard_LRS
    ```

2. Restaure o disco do ponto de recuperação com [az backup restore restore-disks](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-disks). Substitua *mystorageaccount* pelo nome da conta de armazenamento que criou no comando anterior. Substitua o *myRecoveryPointName* pelo nome do ponto de recuperação obtido na saída do comando da lista de pontos de recuperação de cópias de [segurança az](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-list) anteriores. Forneça também o grupo de ***recursos-alvo para o qual os discos geridos são restaurados em***.

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
    > Se o grupo de recursos-alvo não for fornecido, os discos geridos serão restaurados como discos não geridos na conta de armazenamento dada. Isto terá consequências significativas para o tempo de reinstalação, uma vez que o tempo de reinstalação dos discos depende inteiramente da conta de armazenamento dada. Os clientes só terão o benefício da restauração instantânea quando o parâmetro do grupo de recursos-alvo for dado. Se a intenção é restaurar os discos geridos como não geridos, não forneça o parâmetro do grupo de recursos-alvo e, em vez disso, forneça o parâmetro de ristímetro de restabelecimento de parâmetros como mostrado abaixo. Este parâmetro está disponível a partir de az 3.4.0.

    ```azurecli-interactive
    az backup restore restore-disks \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --container-name myVM \
    --item-name myVM \
    --storage-account mystorageaccount \
    --rp-name myRecoveryPointName
    --restore-as-unmanaged-disk
    ```

Isto irá restaurar os discos geridos como discos não geridos para a conta de armazenamento dada e não irá aproveitar a funcionalidade de restauro 'instantânea'. Em futuras versões do CLI, será obrigatório fornecer o parâmetro do grupo de recursos-alvo ou o parâmetro "restaurar como não gerido".

### <a name="unmanaged-disks-restore"></a>Ris não geridos restabelecem

Se o VM de reserva tiver discos não geridos e se a intenção é restaurar os discos do ponto de recuperação, fornece primeiro uma conta de armazenamento Azure. Esta conta de armazenamento é usada para armazenar a configuração VM e o modelo de implementação que pode ser usado mais tarde para implantar o VM a partir dos discos restaurados. Por padrão, os discos não geridos serão restaurados nas suas contas de armazenamento originais. Se o utilizador quiser restaurar todos os discos não geridos num único local, então a conta de armazenamento dada também pode ser usada como local de paragem para esses discos.

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

Como mencionado acima, os discos não geridos serão restaurados na sua conta de armazenamento original. Isto proporciona o melhor desempenho de restauro. Mas se todos os discos não geridos precisarem de ser restaurados na conta de armazenamento, então use a bandeira relevante como mostrado abaixo.

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

Quando o *Estado* dos relatórios de trabalho *restaurados concluídos,* as informações necessárias (configuração VM e o modelo de implementação) foram restauradas na conta de armazenamento.

## <a name="create-a-vm-from-the-restored-disk"></a>Criar uma VM a partir do disco restaurado

O passo final é criar um VM a partir dos discos restaurados. Pode utilizar o modelo de implementação descarregado para a conta de armazenamento dada para criar o VM.

### <a name="fetch-the-job-details"></a>Buscar os detalhes do Trabalho

Os detalhes de trabalho resultantes dão o modelo URI que pode ser consultado e implantado. Use o comando do show de trabalho para obter mais detalhes para o trabalho restaurado desencadeado.

```azurecli-interactive
az backup job show \
    -v myRecoveryServicesVault \
    -g myResourceGroup \
    -n 1fc2d55d-f0dc-4ca6-ad48-aca0fe5d0414
```

A saída desta consulta dará todos os detalhes, mas estamos interessados apenas no conteúdo da conta de armazenamento. Podemos usar a capacidade de [consulta](https://docs.microsoft.com/cli/azure/query-azure-cli?view=azure-cli-latest) do Azure CLI para obter os detalhes relevantes

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

### <a name="fetch-the-deployment-template"></a>Buscar o modelo de implementação

O modelo não é diretamente acessível, uma vez que se encontra sob a conta de armazenamento de um cliente e o recipiente dado. Precisamos do URL completo (juntamente com um token SAS temporário) para aceder a este modelo.

Primeiro, extrair o modelo blob Uri de detalhes de trabalho

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

Então, o nome do modelo ```azuredeploy1fc2d55d-f0dc-4ca6-ad48-aca0519c0232.json``` do exemplo acima será e o nome do recipiente é```myVM-daa1931199fd4a22ae601f46d8812276```

Agora obtenha o símbolo SAS para este recipiente e modelo como detalhado [aqui](https://docs.microsoft.com/azure/azure-resource-manager/templates/secure-template-with-sas-token?tabs=azure-cli#provide-sas-token-during-deployment)

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

### <a name="deploy-the-template-to-create-the-vm"></a>Implementar o modelo para criar o VM

Agora implemente o modelo para criar o VM, como explicado [aqui](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-cli).

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
