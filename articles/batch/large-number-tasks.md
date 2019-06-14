---
title: Submeta um grande número de tarefas - Azure Batch | Documentos da Microsoft
description: Como submeter com eficiência um grande número de tarefas numa única tarefa de lote do Azure
services: batch
documentationcenter: ''
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 08/24/2018
ms.author: lahugh
ms.custom: ''
ms.openlocfilehash: ed04774969f72f1d6037a350f019d81d812d73f6
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60549670"
---
# <a name="submit-a-large-number-of-tasks-to-a-batch-job"></a>Submeta um grande número de tarefas para uma tarefa do Batch

Ao executar cargas de trabalho do Azure Batch em grande escala, pode querer submeter dezenas de milhares, centenas de milhares ou até mesmo mais tarefas para uma única tarefa. 

Este artigo fornece orientações e alguns exemplos de código para submeter um grande número de tarefas com um débito significativamente maior para uma única tarefa do Batch. Depois de tarefas submetidas, entrarão em fila de Batch para processamento no conjunto especificado para a tarefa.

## <a name="use-task-collections"></a>Utilizar coleções de tarefas

As APIs do Batch fornecem métodos com eficiência adicionar tarefas a um trabalho como um *coleção*, além de uma de cada vez. Quando adiciona um grande número de tarefas, deve utilizar os métodos apropriados ou sobrecargas para adicionar tarefas como uma coleção. Em geral, constrói uma coleção de tarefas com a definição de tarefas como iterar através de um conjunto de ficheiros de entrada ou de parâmetros para a sua tarefa.

O tamanho máximo da coleção de tarefas que podem ser adicionados numa única chamada depende de usar a API do Batch:

* As seguintes APIs do Batch limitar a coleção que pretende **100 tarefas**. O limite pode ser menor, dependendo do tamanho das tarefas - por exemplo, se as tarefas têm um grande número de arquivos de recursos ou variáveis de ambiente.

    * [API REST](/rest/api/batchservice/task/addcollection)
    * [API Python](/python/api/azure-batch/azure.batch.operations.TaskOperations?view=azure-python)
    * [API do Node.js](/javascript/api/azure-batch/task?view=azure-node-latest)

  Ao usar essas APIs, terá de fornecer a lógica para dividir o número de tarefas para satisfazer o limite de coleção e lidar com erros e repetições em caso de falha de adição de tarefas. Se uma coleção de tarefas é demasiado grande para adicionar, a solicitação gera um erro e deve ser repetida novamente com menos de tarefas.

* As seguintes APIs oferecem suporte a muito maiores coleções de tarefas - limitadas apenas pela disponibilidade de RAM no cliente submeter. Essas APIs transparente processam dividindo a coleção de tarefas em "partes" para o nível inferior APIs e as repetições se falhar a adição de tarefas.

    * [API .NET](/dotnet/api/microsoft.azure.batch.cloudjob.addtaskasync?view=azure-dotnet)
    * [API de Java](/java/api/com.microsoft.azure.batch.protocol.tasks.addcollectionasync?view=azure-java-stable)
    * [A extensão do Azure Batch CLI](batch-cli-templates.md) com os modelos da CLI do Batch
    * [Extensão do Python SDK](https://pypi.org/project/azure-batch-extensions/)

## <a name="increase-throughput-of-task-submission"></a>Aumentar o débito de submissão da tarefa

Ele pode demorar algum tempo para adicionar uma grande coleção de tarefas para uma tarefa - por exemplo, cópia de segurança para 1 minuto para adicionar a 20.000 tarefas através da API .NET. Dependendo da sua carga de trabalho e a API do Batch, pode melhorar o débito de tarefa ao modificar um ou mais dos seguintes procedimentos:

* **Tamanho de tarefas** -adicionar tarefas grandes demora mais tempo do que adicionar menores. Para reduzir o tamanho de cada tarefa numa coleção, pode simplificar a linha de comandos de tarefas, reduzir o número de variáveis de ambiente ou lidar com os requisitos para a execução da tarefa de forma mais eficiente. Por exemplo, em vez de usar um grande número de ficheiros de recursos, instale as dependências de tarefas com um [tarefa de início](batch-api-basics.md#start-task) no conjunto ou utilize um [pacote de aplicação](batch-application-packages.md) ou [contentor do Docker](batch-docker-container-workloads.md).

* **Número de operações simultâneas** - consoante a API do Batch, débito de aumento, aumentando o número máximo de operações simultâneas pelo cliente do Batch. Configurar esta definição através da [BatchClientParallelOptions.MaxDegreeOfParallelism](/dotnet/api/microsoft.azure.batch.batchclientparalleloptions.maxdegreeofparallelism) propriedade na API do .NET, ou o `threads` parâmetro de métodos como [TaskOperations.add_collection](/python/api/azure-batch/azure.batch.operations.TaskOperations?view=azure-python)na extensão do SDK de Python do Batch. (Esta propriedade não está disponível no SDK do Python nativo do Batch.) Por predefinição, esta propriedade é definida como 1, mas configurá-lo superior para melhorar o débito de operações. Alternar o débito de maior consumo de largura de banda e um desempenho de CPU. Aumenta a taxa de transferência de tarefa, até 100 vezes a `MaxDegreeOfParallelism` ou `threads`. Na prática, deve definir o número de operações simultâneas inferior a 100. 
 
  A extensão de CLI do Azure Batch com modelos do Batch aumenta o número de operações simultâneas automaticamente com base no número de núcleos disponíveis, mas esta propriedade não é configurável na CLI. 

* **Limites de conexão HTTP** -o número de conexões simultâneas de HTTP pode limitar o desempenho do cliente do Batch quando está a adicionar um grande número de tarefas. O número de ligações de HTTP é limitado com determinadas APIs. Ao desenvolver com a API de .NET, por exemplo, o [ServicePointManager.DefaultConnectionLimit](/dotnet/api/system.net.servicepointmanager.defaultconnectionlimit) propriedade está definida como 2 por predefinição. Recomendamos que aumente o valor para um número perto ou superior ao número de operações simultâneas.

## <a name="example-batch-net"></a>Exemplo: .NET do Batch

Os fragmentos do c# seguintes mostram as definições para configurar ao adicionar um grande número de tarefas com a API de .NET do Batch.

Para aumentar o débito de tarefas, aumentar o valor do [MaxDegreeOfParallelism](/dotnet/api/microsoft.azure.batch.batchclientparalleloptions.maxdegreeofparallelism) propriedade da [BatchClient](/dotnet/api/microsoft.azure.batch.batchclient?view=azure-dotnet). Por exemplo:

```csharp
BatchClientParallelOptions parallelOptions = new BatchClientParallelOptions()
  {
    MaxDegreeOfParallelism = 15
  };
...
```
Adicionar uma coleção de tarefas para a tarefa usando a sobrecarga adequada dos [AddTaskAsync](/dotnet/api/microsoft.azure.batch.cloudjob.addtaskasync?view=azure-dotnet) ou [AddTask](/dotnet/api/microsoft.azure.batch.cloudjob.addtask?view=azure-dotnet
) método. Por exemplo:

```csharp
// Add a list of tasks as a collection
List<CloudTask> tasksToAdd = new List<CloudTask>(); // Populate with your tasks
...
await batchClient.JobOperations.AddTaskAsync(jobId, tasksToAdd, parallelOptions);
```


## <a name="example-batch-cli-extension"></a>Exemplo: Extensão CLI do batch

Usando as extensões da CLI do Azure Batch com [modelos da CLI do Batch](batch-cli-templates.md), crie um ficheiro JSON do modelo de tarefa que inclui um [factory tarefas](https://github.com/Azure/azure-batch-cli-extensions/blob/master/doc/taskFactories.md). A fábrica de tarefas configura uma coleção de tarefas relacionadas de uma tarefa a partir de uma definição de tarefa única.  

Segue-se um modelo de tarefa de exemplo para uma tarefa de unidimensional varrimento paramétrico com um grande número de tarefas - neste caso, 250.000. A linha de comandos da tarefa é um simples `echo` comando.

```json
{
    "job": {
        "type": "Microsoft.Batch/batchAccounts/jobs",
        "apiVersion": "2016-12-01",
        "properties": {
            "id": "myjob",
            "constraints": {
                "maxWallClockTime": "PT5H",
                "maxTaskRetryCount": 1
            },
            "poolInfo": {
                "poolId": "mypool"
            },
            "taskFactory": {
                "type": "parametricSweep",
                "parameterSets": [
                    {
                        "start": 1,
                        "end": 250000,
                        "step": 1
                    }
                ],
                "repeatTask": {
                    "commandLine": "/bin/bash -c 'echo Hello world from task {0}'",
                    "constraints": {
                        "retentionTime":"PT1H"
                    }
                }
            },
            "onAllTasksComplete": "terminatejob"
        }
    }
}
```
Para executar uma tarefa com o modelo, veja [modelos de utilização do Azure Batch CLI e transferência de ficheiros](batch-cli-templates.md).

## <a name="example-batch-python-sdk-extension"></a>Exemplo: Extensão de Python SDK do batch

Para utilizar a extensão do SDK de Python do Azure Batch, primeiro de instalar o SDK de Python e a extensão:

```
pip install azure-batch
pip install azure-batch-extensions
```

Configurar um `BatchExtensionsClient` que utiliza a extensão SDK:

```python

client = batch.BatchExtensionsClient(base_url=BATCH_ACCOUNT_URL, resource_group=RESOURCE_GROUP_NAME, batch_account=BATCH_ACCOUNT_NAME)
...
```

Crie uma coleção de tarefas para adicionar a uma tarefa. Por exemplo:


```python
tasks=list()
# Populate the list with your tasks
...

```

Adicionar a coleção de tarefas utilizando [add_collection](/python/api/azure-batch/azure.batch.operations.TaskOperations?view=azure-python). Definir o `threads` parâmetro para aumentar o número de operações simultâneas:

```python
try:
    client.task.add_collection(job_id, threads=100)
except Exception as e:
    raise e
```

A extensão do SDK de Python do Batch também suporta parâmetros da tarefa de adicionar à tarefa com uma especificação de JSON para uma fábrica de tarefa. Por exemplo, configure parâmetros da tarefa para um varrimento paramétrico semelhante ao exemplo de modelo de CLI do Batch anterior:

```python
parameter_sweep = {
    "job": {
        "type": "Microsoft.Batch/batchAccounts/jobs",
        "apiVersion": "2016-12-01",
        "properties": {
            "id": "myjob",
            "poolInfo": {
                "poolId": "mypool"
            },
            "taskFactory": {
                "type": "parametricSweep",
                "parameterSets": [
                    {
                        "start": 1,
                        "end": 250000,
                        "step": 1
                    }
                ],
                "repeatTask": {
                    "commandLine": "/bin/bash -c 'echo Hello world from task {0}'",
                    "constraints": {
                        "retentionTime":"PT1H"
                    }
                }
            },
            "onAllTasksComplete": "terminatejob"
        }
    }
}
...
job_json = client.job.expand_template(parameter_sweep)
job_parameter = client.job.jobparameter_from_json(job_json)
```

Adicione os parâmetros da tarefa ao trabalho. Definir o `threads` parâmetro para aumentar o número de operações simultâneas:

```python
try:
    client.job.add(job_parameter, threads=50)
except Exception as e:
    raise e
```

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre como utilizar a extensão de CLI do Azure Batch com [modelos da CLI do Batch](batch-cli-templates.md).
* Saiba mais sobre o [extensão de SDK de Python do Batch](https://pypi.org/project/azure-batch-extensions/).
