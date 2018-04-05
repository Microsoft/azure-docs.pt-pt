---
title: Início Rápido do Azure – Preparação CNTK com o Batch AI - CLI do Azure | Microsoft Docs
description: Aprenda rapidamente a executar uma tarefa de preparação CNTK com o Batch AI com a CLI do Azure
services: batch-ai
documentationcenter: na
author: AlexanderYukhanov
manager: Vaman.Bedekar
editor: tysonn
ms.assetid: ''
ms.custom: ''
ms.service: batch-ai
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: CLI
ms.topic: quickstart
ms.date: 10/06/2017
ms.author: Alexander.Yukhanov
ms.openlocfilehash: 82e3885021a2f2309dfed456d472e7027b8d6cf2
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/09/2018
---
# <a name="run-a-cntk-training-job-using-the-azure-cli"></a>Executar uma tarefa de preparação CNTK com a CLI do Azure

Este início rápido detalha a utilização da interface de linha de comandos (CLI) do Azure para executar uma tarefa Microsoft Cognitive Toolkit (CNTK) com o serviço Batch AI. A CLI do Azure é utilizada para criar e gerir recursos do Azure a partir da linha de comandos ou em scripts.

Neste exemplo, utiliza a base de dados MNIST de imagens manuscritas para preparar uma rede neural convolucional (CNN) num cluster GPU de nó único gerido pelo Batch AI. 

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

Este início rápido requer a execução da versão mais recente da CLI do Azure. Se precisar de instalar ou atualizar, veja [instalar o Azure CLI 2.0]( /cli/azure/install-azure-cli).

Os fornecedores de recursos do Batch AI também têm de estar registados na sua subscrição através do Azure Cloud Shell ou da CLI do Azure. Um registo de fornecedor pode demorar até 15 minutos.

```azurecli
az provider register -n Microsoft.BatchAI
az provider register -n Microsoft.Batch
```


## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Os clusters e as tarefas do Batch AI são recursos do Azure e têm de ser colocados num grupo de recursos do Azure.

Crie um grupo de recursos com o comando [az group create](/cli/azure/group#az_group_create).

O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroup* na localização *eastus*. Em seguida, utiliza o comando [az configure](/cli/azure/reference-index#az_configure) para configurar este grupo de recursos e a localização como a predefinição.

```azurecli
az group create --name myResourceGroup --location eastus

az configure --defaults group=myResourceGroup

az configure --defaults location=eastus
```

>[!NOTE]
>Estabelecer os valores predefinidos para o comando `az` é um passo opcional. Pode optar por não estabelecer os valores predefinidos. Se optar por estabelecer as predefinições, deve remover as predefinições depois de concluir o tutorial. Remova as predefinições com os seguintes comandos:
>
>```azurecli
>az configure --defaults group=''
>
>az configure --defaults location=''
>```
>

## <a name="create-a-storage-account"></a>Criar uma conta do Storage

Este início rápido utiliza uma conta de armazenamento do Azure para alojar dados e scripts para a tarefa de preparação. Crie uma conta de armazenamento com o comando [az storage account create](/cli/azure/storage/account#az_storage_account_create).

```azurecli
az storage account create --name mystorageaccount --sku Standard_LRS
```

>[!NOTE]
>Cada conta de armazenamento tem de ter um nome exclusivo. No comando `az` anterior e noutros comandos semelhantes neste tutorial, substitua o valor da definição `mystorageaccount` pelo nome da conta de armazenamento.

## <a name="prepare-azure-file-share"></a>Preparar a partilha de ficheiros do Azure

Para efeitos de ilustração, este início rápido utiliza uma partilha de ficheiros do Azure para alojar os dados e os scripts de preparação da tarefa de aprendizagem.

1. Crie uma partilha de ficheiros com o nome *batchaiquickstart* com o comando [az storage share create](/cli/azure/storage/share#az_storage_share_create).

  ```azurecli
  az storage share create --account-name mystorageaccount --name batchaiquickstart
  ```
2. Crie um diretório na partilha denominado *mnistcntksample* com o comando [az storage directory create](/cli/azure/storage/directory#az_storage_directory_create).

  ```azurecli
  az storage directory create --share-name batchaiquickstart  --name mnistcntksample
  ```

3. Transfira o [pacote de exemplo](https://batchaisamples.blob.core.windows.net/samples/BatchAIQuickStart.zip?st=2017-09-29T18%3A29%3A00Z&se=2099-12-31T08%3A00%3A00Z&sp=rl&sv=2016-05-31&sr=b&sig=hrAZfbZC%2BQ%2FKccFQZ7OC4b%2FXSzCF5Myi4Cj%2BW3sVZDo%3D) e deszipe-o. Carregue o conteúdo para o diretório com o comando [az storage file upload](/cli/azure/storage/file#az_storage_file_upload):

  ```azurecli
  az storage file upload --share-name batchaiquickstart --source Train-28x28_cntk_text.txt --path mnistcntksample

  az storage file upload --share-name batchaiquickstart --source Test-28x28_cntk_text.txt --path mnistcntksample

  az storage file upload --share-name batchaiquickstart --source ConvNet_MNIST.py --path mnistcntksample
  ```


## <a name="create-gpu-cluster"></a>Criar cluster de GPU
Utilize o comando [az batchai cluster create](/cli/azure/batchai/cluster#az_batchai_cluster_create) para criar um cluster do Batch AI constituído por um único nó de VM GPU. Neste exemplo, a VM executa a imagem de Ubuntu LTS predefinida. Especifique `image UbuntuDSVM` para executar o Microsoft Deep Learning Virtual Machine, que suporta estruturas de preparação adicionais. O tamanho NC6 tem um GPU K80 NVIDIA. Monte a partilha de ficheiros numa pasta denominada *azurefileshare*. O caminho completo desta pasta no nó de computação do GPU é $AZ_BATCHAI_MOUNT_ROOT/azurefileshare.


```azurecli
az batchai cluster create --name mycluster --vm-size STANDARD_NC6 --image UbuntuLTS --min 1 --max 1 --storage-account-name mystorageaccount --afs-name batchaiquickstart --afs-mount-path azurefileshare --user-name <admin_username> --password <admin_password>
```


Após a criação do cluster, o resultado é semelhante ao seguinte:

```azurecli
{
  "allocationState": "resizing",
  "allocationStateTransitionTime": "2017-10-05T02:09:03.194000+00:00",
  "creationTime": "2017-10-05T02:09:01.998000+00:00",
  "currentNodeCount": 0,
  "errors": null,
  "id": "/subscriptions/10d0b7c6-9243-4713-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.BatchAI/clusters/mycluster",
  "location": "eastus",
  "name": "mycluster",
  "nodeSetup": {
    "mountVolumes": {
      "azureBlobFileSystems": null,
      "azureFileShares": [
        {
          "accountName": "batchaisamples",
          "azureFileUrl": "https://batchaisamples.file.core.windows.net/batchaiquickstart",
          "credentialsInfo": {
            "accountKey": null,
            "accountKeySecretUrl": null
          },
          "directoryMode": "0777",
          "fileMode": "0777",
          "relativeMountPath": "azurefileshare"
        }
      ],
      "fileServers": null,
      "unmanagedFileSystems": null
    },
    "setupTask": null
  },
  "nodeStateCounts": {
    "idleNodeCount": 0,
    "leavingNodeCount": 0,
    "preparingNodeCount": 0,
    "runningNodeCount": 0,
    "unusableNodeCount": 0
  },
  "provisioningState": "succeeded",
  "provisioningStateTransitionTime": "2017-10-05T02:09:02.857000+00:00",
  "resourceGroup": "myresourcegroup",
  "scaleSettings": {
    "autoScale": null,
    "manual": {
      "nodeDeallocationOption": "requeue",
      "targetNodeCount": 1
    }
  },
  "subnet": {
    "id": null
  },
  "tags": null,
  "type": "Microsoft.BatchAI/Clusters",
  "userAccountSettings": {
    "adminUserName": "demoUser",
    "adminUserPassword": null,
    "adminUserSshPublicKey": null
  },
  "virtualMachineConfiguration": {
    "imageReference": {
      "offer": "UbuntuServer",
      "publisher": "Canonical",
      "sku": "16.04-LTS",
      "version": "latest"
    }
  },
  "vmPriority": "dedicated",
  "vmSize": "STANDARD_NC6"
```
## <a name="get-cluster-status"></a>Obter estado do cluster

Para obter uma descrição geral do estado do cluster, execute o comando [az batchai cluster list](/cli/azure/batchai/cluster#az_batchai_cluster_list):

```azurecli
az batchai cluster list -o table
```

O resultado é semelhante ao seguinte:

```azurecli
Name        Resource Group    VM Size        State     Idle    Running    Preparing    Unusable    Leaving
---------   ----------------  -------------  -------   ------  ---------  -----------  ----------  --------
mycluster   myresourcegroup   STANDARD_NC6   steady    1       0          0            0            0
```

Para obter mais detalhes, execute o comando [az batchai cluster show](/cli/azure/batchai/cluster#az_batchai_cluster_show). Devolve todas as propriedades do cluster mostradas após a criação do mesmo.

O cluster está pronto quando os nós forem alocados e a preparação estiver terminada (veja o atributo `nodeStateCounts`). Se ocorrer um problema, o atributo `errors` contém a descrição do erro.

## <a name="create-training-job"></a>Criar tarefa de preparação

Depois de o cluster estar pronto, configure e submeta a tarefa de aprendizagem.

1. Crie um ficheiro de modelo JSON para a criação da tarefa com o nome job.json:

  ```JSON
  {
    "properties": {
        "stdOutErrPathPrefix": "$AZ_BATCHAI_MOUNT_ROOT/azurefileshare",
       "inputDirectories": [{
            "id": "SAMPLE",
            "path": "$AZ_BATCHAI_MOUNT_ROOT/azurefileshare/mnistcntksample"
        }],
        "outputDirectories": [{
            "id": "MODEL",
            "pathPrefix": "$AZ_BATCHAI_MOUNT_ROOT/azurefileshare",
            "pathSuffix": "model",
            "type": "custom"
        }],
        "containerSettings": {
            "imageSourceRegistry": {
                "image": "microsoft/cntk:2.1-gpu-python3.5-cuda8.0-cudnn6.0"
            }
        },
        "nodeCount": 1,
        "cntkSettings": {
            "pythonScriptFilePath": "$AZ_BATCHAI_INPUT_SAMPLE/ConvNet_MNIST.py",
            "commandLineArgs": "$AZ_BATCHAI_INPUT_SAMPLE $AZ_BATCHAI_OUTPUT_MODEL"
        }
    }
  }
  ```
2. Crie uma tarefa com o nome *myjob* a executar no cluster com o comando [az batchai job create](/cli/azure/batchai/job#az_batchai_job_create):

  ```azurecli
  az batchai job create --name myjob --cluster-name mycluster --config job.json
  ```

O resultado é semelhante ao seguinte:

```azurecli
{
  "caffeSettings": null,
  "chainerSettings": null,
  "cluster": {
    "id": "/subscriptions/10d0b7c6-9243-4713-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.BatchAI/clusters/mycluster",
    "resourceGroup": "myresourcegroup"
  },
  "cntkSettings": {
    "commandLineArgs": "$AZ_BATCHAI_INPUT_SAMPLE $AZ_BATCHAI_OUTPUT_MODEL",
    "configFilePath": null,
    "languageType": "Python",
    "processCount": 1,
    "pythonInterpreterPath": null,
    "pythonScriptFilePath": "$AZ_BATCHAI_INPUT_SAMPLE/ConvNet_MNIST.py"
  },
  "constraints": {
    "maxTaskRetryCount": null,
    "maxWallClockTime": "7 days, 0:00:00"
  },
  "containerSettings": {
    "imageSourceRegistry": {
      "credentials": null,
      "image": "microsoft/cntk:2.1-gpu-python3.5-cuda8.0-cudnn6.0",
      "serverUrl": null
    }
  },
  "creationTime": "2017-10-05T06:41:42.163000+00:00",
  "customToolkitSettings": null,
  "environmentVariables": null,
  "executionInfo": {
    "endTime": null,
    "errors": null,
    "exitCode": null,
    "lastRetryTime": null,
    "retryCount": null,
    "startTime": "2017-10-05T06:41:44.392000+00:00"
  },
  "executionState": "running",
  "executionStateTransitionTime": "2017-10-05T06:41:44.953000+00:00",
  "experimentName": null,
  "id": "/subscriptions/10d0b7c6-9243-4713-xxxx-xxxxxxxxxxxx/resourceGroups/demo/providers/Microsoft.BatchAI/jobs/myjob",
  "inputDirectories": [
    {
      "id": "SAMPLE",
      "path": "$AZ_BATCHAI_MOUNT_ROOT/azurefileshare/mnistcntksample"
    }
  ],
  "jobPreparation": null,
  "location": null,
  "name": "cntk_job",
  "nodeCount": 1,
  "outputDirectories": [
    {
      "createNew": true,
      "id": "MODEL",
      "pathPrefix": "$AZ_BATCHAI_MOUNT_ROOT/azurefileshare",
      "pathSuffix": "model",
      "type": "Custom"
    }
  ],
  "priority": 0,
  "provisioningState": "succeeded",
  "provisioningStateTransitionTime": "2017-10-05T06:41:44.238000+00:00",
  "resourceGroup": "demo",
  "stdOutErrPathPrefix": "$AZ_BATCHAI_MOUNT_ROOT/azurefileshare",
  "tags": null,
  "tensorFlowSettings": null,
  "toolType": "CNTK",
  "type": "Microsoft.BatchAI/Jobs"
}
```

## <a name="monitor-job"></a>Monitorizar tarefa

Utilize o comando [az batchai job list](/cli/azure/batchai/job#az_batchai_job_list) para obter uma descrição geral do estado da tarefa:

```azurecli
az batchai job list -o table
```

O resultado é semelhante ao seguinte:

```azurecli
Name        Resource Group    Cluster    Cluster RG      Nodes  State    Exit code
----------  ----------------  ---------  --------------- -----  -------  -----------
myjob       myresourcegroup   mycluster  myresourcegroup 1      running

```

Para obter mais detalhes, execute o comando [az batchai job show](/cli/azure/batchai/job#az_batchai_job_show).

`executionState` contém o estado atual de execução da tarefa:

* `queued`: a tarefa está a aguardar que os nós do cluster fiquem disponíveis
* `running`: a tarefa está em execução
*   `succeeded` (ou `failed`): a tarefa está concluída e `executionInfo` contém detalhes sobre o resultado


## <a name="list-stdout-and-stderr-output"></a>Listar o resultado de stdout e stderr
Utilize o comando [az batchai job list-files](/cli/azure/batchai/job#az_batchai_job_list_files) para listar as ligações aos ficheiros de registo stdout e stderr:

```azurecli
az batchai job list-files --name myjob --output-directory-id stdouterr
```

O resultado é semelhante ao seguinte:

```azurecli
[
  {
    "contentLength": 733,
    "downloadUrl": "https://batchaisamples.file.core.windows.net/batchaiquickstart/10d0b7c6-9243-4713-91a9-2730375d3a1b/demo/jobs/cntk_job/stderr.txt?sv=2016-05-31&sr=f&sig=Rh%2BuTg9C1yQxm7NfA9YWiKb%2B5FRKqWmEXiGNRDeFMd8%3D&se=2017-10-05T07%3A44%3A38Z&sp=rl",
    "lastModified": "2017-10-05T06:44:38+00:00",
    "name": "stderr.txt"
  },
  {
    "contentLength": 300,
    "downloadUrl": "https://batchaisamples.file.core.windows.net/batchaiquickstart/10d0b7c6-9243-4713-91a9-2730375d3a1b/demo/jobs/cntk_job/stdout.txt?sv=2016-05-31&sr=f&sig=jMhJfQOGry9jr4Hh3YyUFpW5Uaxnp38bhVWNrTTWMtk%3D&se=2017-10-05T07%3A44%3A38Z&sp=rl",
    "lastModified": "2017-10-05T06:44:29+00:00",
    "name": "stdout.txt"
  }
]
```


## <a name="observe-output"></a>Observar o resultado

Pode transmitir ou seguir os ficheiros de saída de uma tarefa enquanto esta estiver a ser executada. O exemplo seguinte utiliza o comando [az batchai job stream-file](/cli/azure/batchai/job#az_batchai_job_stream_file) para transmitir o registo stderr.txt:

```azurecli
az batchai job stream-file --job-name myjob --output-directory-id stdouterr --name stderr.txt
```

O resultado é semelhante ao seguinte. Interrompa o resultado ao premir [Ctrl]-[C].

```azurecli
…
Finished Epoch[2 of 40]: [Training] loss = 0.104846 * 60000, metric = 3.00% * 60000 3.849s (15588.5 samples/s);
Finished Epoch[3 of 40]: [Training] loss = 0.077043 * 60000, metric = 2.23% * 60000 3.902s (15376.7 samples/s);
Finished Epoch[4 of 40]: [Training] loss = 0.063050 * 60000, metric = 1.82% * 60000 3.811s (15743.9 samples/s);
…

```

## <a name="delete-resources"></a>Eliminar recursos

Utilize o comando [az batchai job delete](/cli/azure/batchai/job#az_batchai_job_delete) para eliminar a tarefa:

```azurecli
az batchai job delete --name myjob
```
Utilize o comando [az batchai cluster delete](/cli/azure/batchai/cluster#az_batchai_cluster_delete) para eliminar o cluster:

```azurecli
az batchai cluster delete --name mycluster
```

Utilize o comando `az group delete` para eliminar o grupo de recursos que criou para este início rápido:

```azurecli
az group delete --name myResourceGroup
```

## <a name="restore-azure-cli-20-default-settings"></a>Restaurar as predefinições da CLI 2.0 do Azure

Remova as predefinições configuradas anteriormente para a localização e o grupo de recursos:

```azurecli
az configure --defaults group=''

az configure --defaults location=''
```

## <a name="next-steps"></a>Passos seguintes

Neste início rápido, aprendeu a executar uma tarefa de preparação CNTK num cluster do Batch AI com a CLI do Azure. Para obter mais informações sobre como utilizar o Batch AI com toolkits diferentes, veja as [receitas de preparação](https://github.com/Azure/BatchAI).

Para saber mais sobre como utilizar a CLI 2.0 do Azure para gerir o Batch AI, veja a [documentação do github](https://github.com/Azure/BatchAI/blob/master/documentation/using-azure-cli-20.md).
