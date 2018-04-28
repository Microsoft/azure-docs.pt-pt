---
title: Início Rápido do Azure - Executar Tarefa do Batch - CLI
description: Aprenda mais rapidamente a executar uma tarefa do Batch com a CLI do Azure.
services: batch
author: dlepow
manager: jeconnoc
ms.service: batch
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 01/16/2018
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: de0ff088b3a61c48ddcbdff01f105884870886e1
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2018
---
# <a name="quickstart-run-your-first-batch-job-with-the-azure-cli"></a>Início Rápido; Executar o seu primeiro trabalho do Batch com a CLI do Azure

A CLI do Azure é utilizada para criar e gerir recursos do Azure a partir da linha de comandos ou em scripts. Este início rápido mostra como utilizar a CLI do Azure para criar uma conta do Batch, um *conjunto* de nós de computação (máquinas virtuais) e uma *tarefa* que executa *tarefas* no conjunto. Cada tarefa de exemplo executa um comando básico num dos nós do conjunto. Depois de concluir este início rápido, irá compreender os conceitos principais do serviço do Batch e estará pronto para experimentar o Batch com cargas de trabalho mais realistas em grande escala.

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se optar por instalar e usar a CLI localmente, este início rápido requer a execução da versão 2.0.20 ou posterior da CLI do Azure. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [instalar o Azure CLI 2.0](/cli/azure/install-azure-cli). 

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com o comando [az group create](/cli/azure/group#az_group_create). Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos. 

O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroup* na localização *eualeste2*.

```azurecli-interactive 
az group create \
    --name myResourceGroup \
    --location eastus2
```

## <a name="create-a-storage-account"></a>Create a storage account

Pode associar uma conta de Armazenamento do Microsoft Azure à sua conta do Batch. Apesar de não ser obrigatório neste início rápido, a conta de armazenamento é útil para implementar aplicações e armazenar dados de entrada e saída para a maioria das cargas de trabalho do mundo real. Utilize o comando [az storage account create](/cli/azure/storage/account#az_storage_account_create) para criar uma conta de armazenamento no seu grupo de recursos.

```azurecli-interactive
az storage account create \
    --resource-group myResourceGroup \
    --name mystorageaccount \
    --location eastus2 \
    --sku Standard_LRS
```

## <a name="create-a-batch-account"></a>Criar uma conta do Batch

Crie uma conta do Batch com o comando [az batch account create](/cli/azure/batch/account#az_batch_account_create). Precisa de uma conta para criar recursos de computação (conjuntos de nós de computação) e tarefas do Batch.

O exemplo seguinte cria uma conta do Batch com o nome *mybatchaccount* em *myResourceGroup* e associa a conta de armazenamento criada.  

```azurecli-interactive 
az batch account create \
    --name mybatchaccount \
    --storage-account mystorageaccount \
    --resource-group myResourceGroup \
    --location eastus2
```

Para criar e gerir conjuntos e tarefas de computação, tem de autenticar com o Batch. Inicie sessão na conta com o comando [az batch account login](/cli/azure/batch/account#az_batch_account_login). Depois de iniciar sessão, os comandos `az batch` utilizam este contexto de conta.

```azurecli-interactive 
az batch account login \
    --name mybatchaccount \
    --resource-group myResourceGroup \
    --shared-key-auth
```

## <a name="create-a-pool-of-compute-nodes"></a>Criar um conjunto de nós de computação

Agora que tem uma conta do Batch, crie um conjunto de exemplo de nós de computação do Linux através do comando [az batch pool create](/cli/azure/batch/pool#az_batch_pool_create). O exemplo seguinte cria um conjunto com o nome *mypool* de dois nós de tamanho *Standard_A1_v2* com Ubuntu 16.04 LTS. O tamanho do nó sugerido oferece um bom equilíbrio de desempenho em comparação com o custo deste exemplo rápido.
 
```azurecli-interactive
az batch pool create \
    --id mypool --vm-size Standard_A1_v2 \
    --target-dedicated-nodes 2 \
    --image canonical:ubuntuserver:16.04.0-LTS \
    --node-agent-sku-id "batch.node.ubuntu 16.04" 
```

O Batch cria o conjunto de imediato, mas demora alguns minutos a alocar e a iniciar os nós de computação. Durante este período, o conjunto está no estado `resizing`. Para ver o estado do conjunto, execute o comando [az batch pool show](/cli/azure/batch/pool#az_batch_pool_show). Este comando mostra todas as propriedades do conjunto e pode consultar as propriedades específicas. O comando seguinte obtém o estado de alocação do conjunto:

```azurecli-interactive
az batch pool show --pool-id mypool \
    --query "allocationState"
```

Continue para os passos seguintes para criar tarefas enquanto o estado do conjunto é alterado. O conjunto está pronto para executar tarefas, quando o estado de atribuição for `steady` e todos os nós estiverem em execução. 

## <a name="create-a-job"></a>Criar uma tarefa

Agora que tem um conjunto, crie um trabalho para ser executado no mesmo.  Os trabalhos do Batch são grupos lógicos de uma ou mais tarefas. Uma tarefa inclui definições comuns de tarefas, como a prioridade e o conjunto em que as tarefas são executadas. Crie uma tarefa do Batch através do comando [az batch job create](/cli/azure/batch/job#az_batch_job_create). O exemplo seguinte cria uma tarefa *myjob* no conjunto *mypool*. Inicialmente, o trabalho não tem tarefas.

```azurecli-interactive 
az batch job create \
    --id myjob \
    --pool-id mypool
```

## <a name="create-tasks"></a>Criar tarefas

Agora utilize o comando [az batch task create](/cli/azure/batch/task#az_batch_task_create) para criar algumas tarefas para executar na tarefa. Neste exemplo, cria quatro tarefas idênticas. Cada tarefa executa um `command-line` para apresentar as variáveis de ambiente do Batch num nó de computação e, em seguida, aguarda 90 segundos. Quando utiliza o Batch, esta linha de comandos é onde especifica a aplicação ou o script. O Batch apresenta várias formas de implementar aplicações e scripts para os nós de computação.

O seguinte script do Bash cria 4 tarefas paralelas (*mytask1* para *mytask4*).

```azurecli-interactive 
for i in {1..4}
do
   az batch task create \
    --task-id mytask$i \
    --job-id myjob \
    --command-line "/bin/bash -c 'printenv | grep AZ_BATCH; sleep 90s'"
done
```

A saída do comando mostra as definições para cada uma das tarefas. O Batch distribui as tarefas pelos nós de computação.

## <a name="view-task-status"></a>Ver estado da tarefa

Depois de criar uma tarefa, o Batch coloca em fila para a execução no conjunto. Assim que um nó estiver disponível para executá-lo, a tarefa é executada.

Utilize o comando [az batch task show](/cli/azure/batch/task#az_batch_task_show) para ver o estado das tarefas do Batch. O exemplo seguinte mostra os detalhes do *mytask1* em execução num dos nós do conjunto.

```azurecli-interactive 
az batch task show \
    --job-id myjob \
    --task-id mytask1
```

A saída do comando inclui muitos detalhes, mas tome nota do `exitCode` da linha de comandos de tarefas e o `nodeId`. Um `exitCode` de 0 indica que a linha de comandos da tarefa foi concluída com êxito. O `nodeId` indica o ID do nó no conjunto no qual a tarefa foi executada.

## <a name="view-task-output"></a>Ver resultado das tarefas

Para listar os ficheiros criados por uma tarefa num nó de computação, utilize o comando [az batch task file list](/cli/azure/batch/task#az_batch_task_file_list). O comando seguinte lista os ficheiros criados por *mytask1*: 

```azurecli-interactive 
az batch task file list \
    --job-id myjob \
    --task-id mytask1 \
    --output table
```

O resultado é semelhante ao seguinte:

```
Name        URL                                                                                         Is Directory      Content Length
----------  ------------------------------------------------------------------------------------------  --------------  ----------------
stdout.txt  https://mybatchaccount.eastus2.batch.azure.com/jobs/myjob/tasks/mytask1/files/stdout.txt  False                  695
certs       https://mybatchaccount.eastus2.batch.azure.com/jobs/myjob/tasks/mytask1/files/certs       True
wd          https://mybatchaccount.eastus2.batch.azure.com/jobs/myjob/tasks/mytask1/files/wd          True
stderr.txt  https://mybatchaccount.eastus2.batch.azure.com/jobs/myjob/tasks/mytask1/files/stderr.txt  False                     0

```

Para transferir um dos ficheiros de saída para um diretório local, utilize o comando [az batch task file download](/cli/azure/batch/task#az_batch_task_file_download). Neste exemplo, o resultado da tarefa está em `stdout.txt`. 

```azurecli-interactive
az batch task file download \
    --job-id myjob \
    --task-id mytask1 \
    --file-path stdout.txt \
    --destination ./stdout.txt
```

Pode ver os conteúdos de `stdout.txt` num editor de texto. O conteúdo mostra as variáveis de ambiente do Azure Batch que estão definidas no nó. Quando criar as suas próprias tarefas do Batch, pode referenciar estas variáveis de ambiente nas linhas de comandos de tarefas e nas aplicações e scripts executados pelas linhas de comandos.

```
AZ_BATCH_TASK_DIR=/mnt/batch/tasks/workitems/myjob/job-1/mytask3
AZ_BATCH_NODE_STARTUP_DIR=/mnt/batch/tasks/startup
AZ_BATCH_CERTIFICATES_DIR=/mnt/batch/tasks/workitems/myjob/job-1/mytask3/certs
AZ_BATCH_ACCOUNT_URL=https://mybatchaccount.eastus2batch.azure.com/
AZ_BATCH_TASK_WORKING_DIR=/mnt/batch/tasks/workitems/myjob/job-1/mytask3/wd
AZ_BATCH_NODE_SHARED_DIR=/mnt/batch/tasks/shared
AZ_BATCH_TASK_USER=_azbatch
AZ_BATCH_NODE_ROOT_DIR=/mnt/batch/tasks
AZ_BATCH_JOB_ID=myjob
AZ_BATCH_NODE_IS_DEDICATED=true
AZ_BATCH_NODE_ID=tvm-1392786932_2-20171026t223740z
AZ_BATCH_POOL_ID=mypool-linux
AZ_BATCH_TASK_ID=mytask3
AZ_BATCH_ACCOUNT_NAME=mybatchaccount
AZ_BATCH_TASK_USER_IDENTITY=PoolNonAdmin
```
## <a name="clean-up-resources"></a>Limpar recursos
Se pretender continuar com os tutoriais e exemplos do Batch, utilize a conta do Batch e a conta de armazenamento associada que foi criada neste início rápido. A conta do Batch em si não é cobrada.

É cobrado o conjunto enquanto os nós estiverem em execução, mesmo se não existirem tarefas agendadas. Quando já não precisar de um conjunto, elimine-o com o comando [az batch pool delete](/cli/azure/batch/pool#az_batch_pool_delete):

```azurecli-interactive
az batch pool delete --pool-id mypool
```

Quando já não for preciso, pode utilizar o comando [az group delete](/cli/azure/group#az_group_delete) para remover o grupo de recursos, conta do Batch e todos os recursos relacionados. Elimine os recursos da seguinte forma:

```azurecli-interactive 
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Passos seguintes

Neste início rápido, criou uma conta, um conjunto e um trabalho do Batch. O trabalho executou tarefas de exemplo e viu os resultados num dos nós. Agora que compreende os conceitos principais do serviço do Batch, está pronto para experimentar o Batch com cargas de trabalho mais realistas em grande escala. Para saber mais acerca do Azure Batch, avance para os tutoriais do Azure Batch. 


> [!div class="nextstepaction"]
> [Azure Batch tutorials](./tutorial-parallel-dotnet.md) (Tutoriais do Azure Batch)
