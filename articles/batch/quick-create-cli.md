---
title: Quickstart - Executar o seu primeiro trabalho em Batch com o Azure CLI
description: Este quickstart mostra como criar uma conta Batch e executar um trabalho em Batch com o Azure CLI.
ms.topic: quickstart
ms.date: 08/13/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 297af47b6280381646e654eaededfe8b71a5d874
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/11/2020
ms.locfileid: "97106687"
---
# <a name="quickstart-run-your-first-batch-job-with-the-azure-cli"></a>Início Rápido; Executar o seu primeiro trabalho do Batch com a CLI do Azure

Começa com o Azure Batch utilizando o CLI Azure para criar uma conta Batch, um conjunto de nós computacional (máquinas virtuais) e um trabalho que executa tarefas na piscina. Cada tarefa de exemplo executa um comando básico num dos nós do conjunto.

A CLI do Azure é utilizada para criar e gerir recursos do Azure a partir da linha de comandos ou em scripts. Depois de concluir este início rápido, irá compreender os conceitos principais do serviço do Batch e estará pronto para experimentar o Batch com cargas de trabalho mais realistas em grande escala.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Este arranque rápido requer a versão 2.0.20 ou posterior do Azure CLI. Se utilizar o Azure Cloud Shell, a versão mais recente já está instalada.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com o comando [az group create](/cli/azure/group#az-group-create). Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos.

O exemplo a seguir cria um grupo de recursos chamado *QuickstartBatch-rg* na localização *eastus2.*

```azurecli-interactive
az group create \
    --name QuickstartBatch-rg \
    --location eastus2
```

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Pode associar uma conta de Armazenamento do Microsoft Azure à sua conta do Batch. Apesar de não ser obrigatório para este início rápido, a conta de armazenamento é útil para implementar aplicações e armazenar dados de entrada e saída para a maioria das cargas de trabalho do mundo real. Utilize o comando [az storage account create](/cli/azure/storage/account#az-storage-account-create) para criar uma conta de armazenamento no seu grupo de recursos.

```azurecli-interactive
az storage account create \
    --resource-group QuickstartBatch-rg \
    --name mystorageaccount \
    --location eastus2 \
    --sku Standard_LRS
```

## <a name="create-a-batch-account"></a>Criar uma conta do Batch

Crie uma conta do Batch com o comando [az batch account create](/cli/azure/batch/account#az-batch-account-create). Precisa de uma conta para criar recursos de computação (conjuntos de nós de computação) e tarefas do Batch.

O exemplo a seguir cria uma conta Batch chamada *mybatchaccount* em *QuickstartBatch-rg*, e liga a conta de armazenamento que criou.  

```azurecli-interactive
az batch account create \
    --name mybatchaccount \
    --storage-account mystorageaccount \
    --resource-group QuickstartBatch-rg \
    --location eastus2
```

Para criar e gerir conjuntos e tarefas de computação, tem de autenticar com o Batch. Inicie sessão na conta com o comando [az batch account login](/cli/azure/batch/account#az-batch-account-login). Depois de iniciar sessão, os comandos `az batch` utilizam este contexto de conta.

```azurecli-interactive
az batch account login \
    --name mybatchaccount \
    --resource-group QuickstartBatch-rg \
    --shared-key-auth
```

## <a name="create-a-pool-of-compute-nodes"></a>Criar um conjunto de nós de computação

Agora que tem uma conta do Batch, crie um conjunto de exemplo de nós de computação do Linux através do comando [az batch pool create](/cli/azure/batch/pool#az-batch-pool-create). O exemplo seguinte cria um conjunto com o nome *mypool* de dois nós de tamanho *Standard_A1_v2* com Ubuntu 16.04 LTS. O tamanho do nó sugerido oferece um bom equilíbrio de desempenho em comparação com o custo deste exemplo rápido.
 
```azurecli-interactive
az batch pool create \
    --id mypool --vm-size Standard_A1_v2 \
    --target-dedicated-nodes 2 \
    --image canonical:ubuntuserver:16.04-LTS \
    --node-agent-sku-id "batch.node.ubuntu 16.04"
```

O Batch cria o conjunto de imediato, mas demora alguns minutos a alocar e a iniciar os nós de computação. Durante este período, o conjunto está no estado `resizing`. Para ver o estado do conjunto, execute o comando [az batch pool show](/cli/azure/batch/pool#az-batch-pool-show). Este comando mostra todas as propriedades do conjunto e pode consultar as propriedades específicas. O comando seguinte obtém o estado de alocação do conjunto:

```azurecli-interactive
az batch pool show --pool-id mypool \
    --query "allocationState"
```

Continue para os passos seguintes para criar tarefas enquanto o estado do conjunto é alterado. O conjunto está pronto para executar tarefas, quando o estado de atribuição for `steady` e todos os nós estiverem em execução.

## <a name="create-a-job"></a>Criar um trabalho

Agora que tem um conjunto, crie um trabalho para ser executado no mesmo. Os trabalhos do Batch são grupos lógicos de uma ou mais tarefas. Os trabalhos incluem definições comuns às tarefas, como a prioridade e o conjunto no qual as tarefas vão ser executadas. Crie uma tarefa do Batch através do comando [az batch job create](/cli/azure/batch/job#az-batch-job-create). O exemplo seguinte cria uma tarefa *myjob* no conjunto *mypool*. Inicialmente, o trabalho não tem tarefas.

```azurecli-interactive
az batch job create \
    --id myjob \
    --pool-id mypool
```

## <a name="create-tasks"></a>Criar tarefas

Agora utilize o comando [az batch task create](/cli/azure/batch/task#az-batch-task-create) para criar algumas tarefas para executar na tarefa. Neste exemplo, cria quatro tarefas idênticas. Cada tarefa executa um `command-line` para apresentar as variáveis de ambiente do Batch num nó de computação e, em seguida, aguarda 90 segundos. Quando utiliza o Batch, esta linha de comandos é onde especifica a aplicação ou o script. O Batch apresenta várias formas de implementar aplicações e scripts para os nós de computação.

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

Utilize o comando [az batch task show](/cli/azure/batch/task#az-batch-task-show) para ver o estado das tarefas do Batch. O exemplo seguinte mostra os detalhes do *mytask1* em execução num dos nós do conjunto.

```azurecli-interactive
az batch task show \
    --job-id myjob \
    --task-id mytask1
```

A saída do comando inclui muitos detalhes, mas tome nota do `exitCode` da linha de comandos de tarefas e o `nodeId`. Um `exitCode` de 0 indica que a linha de comandos da tarefa foi concluída com êxito. O `nodeId` indica o ID do nó no conjunto no qual a tarefa foi executada.

## <a name="view-task-output"></a>Ver resultado das tarefas

Para listar os ficheiros criados por uma tarefa num nó de computação, utilize o comando [az batch task file list](/cli/azure/batch/task). O comando seguinte lista os ficheiros criados por *mytask1*:

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

Para transferir um dos ficheiros de saída para um diretório local, utilize o comando [az batch task file download](/cli/azure/batch/task). Neste exemplo, o resultado da tarefa está em `stdout.txt`.

```azurecli-interactive
az batch task file download \
    --job-id myjob \
    --task-id mytask1 \
    --file-path stdout.txt \
    --destination ./stdout.txt
```

Pode ver os conteúdos de `stdout.txt` num editor de texto. O conteúdo mostra as variáveis de ambiente do Azure Batch que estão definidas no nó. Quando criar as suas próprias tarefas do Batch, pode referenciar estas variáveis de ambiente nas linhas de comandos de tarefas e nas aplicações e scripts executados pelas linhas de comandos. Por exemplo:

```
AZ_BATCH_TASK_DIR=/mnt/batch/tasks/workitems/myjob/job-1/mytask1
AZ_BATCH_NODE_STARTUP_DIR=/mnt/batch/tasks/startup
AZ_BATCH_CERTIFICATES_DIR=/mnt/batch/tasks/workitems/myjob/job-1/mytask1/certs
AZ_BATCH_ACCOUNT_URL=https://mybatchaccount.eastus2.batch.azure.com/
AZ_BATCH_TASK_WORKING_DIR=/mnt/batch/tasks/workitems/myjob/job-1/mytask1/wd
AZ_BATCH_NODE_SHARED_DIR=/mnt/batch/tasks/shared
AZ_BATCH_TASK_USER=_azbatch
AZ_BATCH_NODE_ROOT_DIR=/mnt/batch/tasks
AZ_BATCH_JOB_ID=myjobl
AZ_BATCH_NODE_IS_DEDICATED=true
AZ_BATCH_NODE_ID=tvm-257509324_2-20180703t215033z
AZ_BATCH_POOL_ID=mypool
AZ_BATCH_TASK_ID=mytask1
AZ_BATCH_ACCOUNT_NAME=mybatchaccount
AZ_BATCH_TASK_USER_IDENTITY=PoolNonAdmin
```

## <a name="clean-up-resources"></a>Limpar os recursos

Se pretender continuar com os tutoriais e exemplos do Batch, utilize a conta do Batch e a conta de armazenamento associada que foi criada neste início rápido. A conta do Batch em si não é cobrada.

É cobrado o conjunto enquanto os nós estiverem em execução, mesmo se não existirem tarefas agendadas. Quando já não precisar de um conjunto, elimine-o com o comando [az batch pool delete](/cli/azure/batch/pool#az-batch-pool-delete). Quando eliminar o conjunto, todos os resultados da tarefa nos nós são eliminados.

```azurecli-interactive
az batch pool delete --pool-id mypool
```

Quando já não for necessário, pode utilizar o comando [az group delete](/cli/azure/group#az-group-delete) para remover o grupo de recursos, a conta do Batch, os conjuntos e todos os recursos relacionados. Elimine os recursos da seguinte forma:

```azurecli-interactive
az group delete --name QuickstartBatch-rg
```

## <a name="next-steps"></a>Passos seguintes

Neste início rápido, criou uma conta, um conjunto e um trabalho do Batch. O trabalho executou tarefas de exemplo e viu os resultados num dos nós. Agora que compreende os conceitos principais do serviço do Batch, está pronto para experimentar o Batch com cargas de trabalho mais realistas em grande escala. Para saber mais acerca do Azure Batch, avance para os tutoriais do Azure Batch.

> [!div class="nextstepaction"]
> [Azure Batch tutorials](./tutorial-parallel-dotnet.md) (Tutoriais do Azure Batch)
