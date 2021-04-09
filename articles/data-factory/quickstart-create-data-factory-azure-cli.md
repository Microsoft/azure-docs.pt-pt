---
title: 'Quickstart: Criar uma fábrica de dados Azure utilizando o Azure CLI'
description: Este quickstart cria uma Azure Data Factory, incluindo um serviço ligado, conjuntos de dados e um oleoduto. Pode executar o oleoduto para fazer uma ação de cópia de ficheiro.
author: linda33wj
ms.author: jingwang
ms.service: azure-cli
ms.topic: quickstart
ms.date: 03/24/2021
ms.custom:
- template-quickstart
- devx-track-azurecli
ms.openlocfilehash: 9af5f276e49e9eb2756dc544db353c75c99bc5a9
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105938065"
---
# <a name="quickstart-create-an-azure-data-factory-using-azure-cli"></a>Quickstart: Criar uma fábrica de dados Azure utilizando o Azure CLI

Este quickstart descreve como usar o Azure CLI para criar uma Fábrica de Dados Azure. O gasoduto que cria nesta fábrica de dados copia dados de uma pasta para outra numa pasta num Azure Blob Storage. Para obter informações sobre como transformar dados usando a Azure Data Factory, consulte [os dados da Transform na Azure Data Factory](transform-data.md).

Para obter uma introdução ao serviço Azure Data Factory, veja [Introdução ao Azure Data Factory](introduction.md).

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

[!INCLUDE [azure-cli-prepare-your-environment](../../includes/azure-cli-prepare-your-environment.md)]

> [!NOTE]
> Para criar instâncias do Data Factory, a conta de utilizador que utiliza para iniciar sessão no Azure tem de ser membro das funções contribuidor ou proprietário ou administrador da subscrição do Azure. Para mais informações, consulte [os papéis de Azure.](quickstart-create-data-factory-powershell.md#azure-roles)

## <a name="prepare-a-container-and-test-file"></a>Preparar um recipiente e um ficheiro de teste

Este quickstart utiliza uma conta de Armazenamento Azure, que inclui um recipiente com um ficheiro.

1. Para criar um grupo de recursos chamado `ADFQuickStartRG` , utilize o comando de [criação do grupo az:](/cli/azure/group#az_group_create)

   ```azurecli
   az group create --name ADFQuickStartRG --location eastus
   ```

1. Crie uma conta de armazenamento utilizando a [conta de armazenamento az criar](/cli/azure/storage/container#az_storage_container_create) comando:

   ```azurecli
   az storage account create --resource-group ADFQuickStartRG \
       --name adfquickstartstorage --location eastus
   ```

1. Criar um recipiente nomeado `adftutorial` utilizando o recipiente de armazenamento [az criar](/cli/azure/storage/container#az_storage_container_create) comando:

   ```azurecli
   az storage container create --resource-group ADFQuickStartRG --name adftutorial \
       --account-name adfquickstartstorage --auth-mode key
   ```

1. No diretório local, crie um ficheiro com o nome `emp.txt` de upload. Se estiver a trabalhar na Azure Cloud Shell, pode encontrar o diretório atual usando o `echo $PWD` comando Bash. Pode utilizar comandos Bash padrão, `cat` como, para criar um ficheiro:

   ```console
   cat > emp.txt
   This is text.
   ```

   Utilize **ctrl+D** para guardar o seu novo ficheiro.

1. Para enviar o novo ficheiro para o seu recipiente de armazenamento Azure, utilize o comando [de upload de blob de armazenamento az:](/cli/azure/storage/blob#az_storage_blob_upload)

   ```azurecli
   az storage blob upload --account-name adfquickstartstorage --name input/emp.txt \
       --container-name adftutorial --file emp.txt --auth-mode key
   ```

   Este comando envia para uma nova pasta chamada `input` .

## <a name="create-a-data-factory"></a>Criar uma fábrica de dados

Para criar uma fábrica de dados Azure, executar a [fábrica de datafactory az criar](/cli/azure/ext/datafactory/datafactory/factory#ext_datafactory_az_datafactory_factory_create) comando:

```azurecli
az datafactory factory create --resource-group ADFQuickStartRG \
    --factory-name ADFTutorialFactory
```

> [!IMPORTANT]
> Substitua `ADFTutorialFactory` por um nome de fábrica de dados globalmente único, por exemplo, ADFTutorialFactorySP1127.

Pode ver a fábrica de dados que criou utilizando o comando de demonstração de [fábrica de dados az:](/cli/azure/ext/datafactory/datafactory/factory#ext_datafactory_az_datafactory_factory_show)

```azurecli
az datafactory factory show --resource-group ADFQuickStartRG \
    --factory-name ADFTutorialFactory
```

## <a name="create-a-linked-service-and-datasets"></a>Criar um serviço e conjuntos de dados ligados

Em seguida, crie um serviço ligado e dois conjuntos de dados.

1. Obtenha o fio de ligação para a sua conta de armazenamento utilizando o comando [de série de ligação de ligação da conta az:](/cli/azure/ext/datafactory/datafactory/factory#ext_datafactory_az_datafactory_factory_show)

   ```azurecli
   az storage account show-connection-string --resource-group ADFQuickStartRG \
       --name adfquickstartstorage --key primary
   ```

1. No seu diretório de trabalho, crie um ficheiro JSON com este conteúdo, que inclui a sua própria cadeia de ligação do passo anterior. Nomeie o `AzureStorageLinkedService.json` ficheiro:

   ```json
   {
       "type":"AzureStorage",
           "typeProperties":{
           "connectionString":{
           "type": "SecureString",
           "value":"DefaultEndpointsProtocol=https;AccountName=adfquickstartstorage;AccountKey=K9F4Xk/EhYrMBIR98rtgJ0HRSIDU4eWQILLh2iXo05Xnr145+syIKNczQfORkQ3QIOZAd/eSDsvED19dAwW/tw==;EndpointSuffix=core.windows.net"
           }
       }
   }
   ```

1. Criar um serviço ligado, `AzureStorageLinkedService` denominado, utilizando o [comando de criação de serviço de ligação az datafactory:](/cli/azure/ext/datafactory/datafactory/linked-service#ext_datafactory_az_datafactory_linked_service_create)

   ```azurecli
   az datafactory linked-service create --resource-group ADFQuickStartRG \
       --factory-name ADFTutorialFactory --linked-service-name AzureStorageLinkedService \
       --properties @AzureStorageLinkedService.json
   ```

1. No seu diretório de trabalho, crie um ficheiro JSON com este conteúdo, denominado `InputDataset.json` :

   ```json
   {
       "type": 
           "AzureBlob",
           "linkedServiceName": {
               "type":"LinkedServiceReference",
               "referenceName":"AzureStorageLinkedService"
               },
           "annotations": [],
           "type": "Binary",
           "typeProperties": {
               "location": {
                   "type": "AzureBlobStorageLocation",
                   "fileName": "emp.txt",
                   "folderPath": "input",
                   "container": "adftutorial"
           }
       }
   }
   ```

1. Crie um conjunto de dados de entrada nomeado `InputDataset` utilizando o conjunto de [dados az-factory criar](/cli/azure/ext/datafactory/datafactory/dataset#ext_datafactory_az_datafactory_dataset_create) comando:

   ```azurecli
   az datafactory dataset create --resource-group ADFQuickStartRG \
       --dataset-name InputDataset --factory-name ADFQuickStartFactory \
       --properties @InputDataset.json
   ```

1. No seu diretório de trabalho, crie um ficheiro JSON com este conteúdo, denominado `OutputDataset.json` :

   ```json
   {
       "type": 
           "AzureBlob",
           "linkedServiceName": {
               "type":"LinkedServiceReference",
               "referenceName":"AzureStorageLinkedService"
               },
           "annotations": [],
           "type": "Binary",
           "typeProperties": {
               "location": {
                   "type": "AzureBlobStorageLocation",
                   "fileName": "emp.txt",
                   "folderPath": "output",
                   "container": "adftutorial"
           }
       }
   }
   ```

1. Criar um conjunto de dados de saída nomeado `OutputDataset` utilizando o conjunto de [dados az criar](/cli/azure/ext/datafactory/datafactory/dataset#ext_datafactory_az_datafactory_dataset_create) comando:

   ```azurecli
   az datafactory dataset create --resource-group ADFQuickStartRG \
       --dataset-name OutputDataset --factory-name ADFQuickStartFactory \
       --properties @OutputDataset.json
   ```

## <a name="create-and-run-the-pipeline"></a>Criar e executar o oleoduto

Finalmente, criar e executar o oleoduto.

1. No seu diretório de trabalho, crie um ficheiro JSON com este conteúdo denominado `Adfv2QuickStartPipeline.json` :

   ```json
   {
       "name": "Adfv2QuickStartPipeline",
       "properties": {
           "activities": [
               {
                   "name": "CopyFromBlobToBlob",
                   "type": "Copy",
                   "dependsOn": [],
                   "policy": {
                       "timeout": "7.00:00:00",
                       "retry": 0,
                       "retryIntervalInSeconds": 30,
                       "secureOutput": false,
                       "secureInput": false
                   },
                   "userProperties": [],
                   "typeProperties": {
                          "source": {
                           "type": "BinarySource",
                           "storeSettings": {
                               "type": "AzureBlobStorageReadSettings",
                               "recursive": true
                           }
                       },
                       "sink": {
                           "type": "BinarySink",
                           "storeSettings": {
                               "type": "AzureBlobStorageWriteSettings"
                           }
                       },
                       "enableStaging": false
                   },
                   "inputs": [
                       {
                           "referenceName": "InputDataset",
                           "type": "DatasetReference"
                       }
                   ],
                   "outputs": [
                       {
                           "referenceName": "OutputDataset",
                           "type": "DatasetReference"
                       }
                   ]
               }
           ],
           "annotations": []
       }
   }
   ```

1. Criar um gasoduto nomeado `Adfv2QuickStartPipeline` utilizando o [gasoduto az datafactory criar](/cli/azure/ext/datafactory/datafactory/pipeline#ext_datafactory_az_datafactory_pipeline_create) comando:

   ```azurecli
   az datafactory pipeline create --resource-group ADFQuickStartRG \
       --factory-name ADFTutorialFactory --name Adfv2QuickStartPipeline \
       --pipeline @Adfv2QuickStartPipeline.json
   ```

1. Executar o gasoduto utilizando o comando [de criação de gasoduto az:](/cli/azure/ext/datafactory/datafactory/pipeline#ext_datafactory_az_datafactory_pipeline_create_run)

   ```azurecli
   az datafactory pipeline create-run --resource-group ADFQuickStartRG \
       --name Adfv2QuickStartPipeline --factory-name ADFTutorialFactory
   ```

   Este comando devolve uma identificação de execução. Copie-o para utilização no comando seguinte.

1. Verifique se o gasoduto foi bem sucedido utilizando o comando [de espetáculo de gasoduto azfactory:](/cli/azure/ext/datafactory/datafactory/pipeline-run#ext_datafactory_az_datafactory_pipeline_run_show)

   ```azurecli
   az datafactory pipeline-run show --resource-group ADFQuickStartRG \
       --factory-name ADFTutorialFactory --run-id 00000000-0000-0000-0000-000000000000
   ```

Também pode verificar se o seu gasoduto funciona como esperado através do [portal Azure](https://portal.azure.com/). Para mais informações, consulte [os recursos utilizados pela Revisão.](quickstart-create-data-factory-powershell.md#review-deployed-resources)

## <a name="clean-up-resources"></a>Limpar os recursos

Todos os recursos neste quickstart fazem parte do mesmo grupo de recursos. Para removê-los todos, utilize o comando de eliminação do [grupo AZ:](/cli/azure/group#az_group_delete)

```azurecli
az group delete --name ADFQuickStartRG
```

Se estiver a usar este grupo de recursos para qualquer outra coisa, em vez disso, elimine os recursos individuais. Por exemplo, para remover o serviço ligado, utilize o comando [de eliminação de serviços de eliminação de dados az.](/cli/azure/ext/datafactory/datafactory/linked-service#ext_datafactory_az_datafactory_linked_service_delete)

Neste arranque rápido, criou os seguintes ficheiros JSON:

- AzureStorageLinkedService.jsem
- InputDataset.jsem
- OutputDataset.jsem
- Adfv2QuickStartPipeline.jsem

Elimine-os utilizando comandos Bash padrão.

## <a name="next-steps"></a>Passos seguintes

- [Pipelines e atividades no Azure Data Factory](concepts-pipelines-activities.md)
- [Serviços ligados no Azure Data Factory](concepts-linked-services.md)
- [Conjuntos de dados no Azure Data Factory](concepts-datasets-linked-services.md)
- [Transformar dados no Azure Data Factory](transform-data.md)
