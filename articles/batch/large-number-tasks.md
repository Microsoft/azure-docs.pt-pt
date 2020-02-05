---
title: Enviar um grande número de tarefas – Azure batch | Microsoft Docs
description: Como enviar com eficiência um número muito grande de tarefas em um único trabalho do lote do Azure
services: batch
documentationcenter: ''
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 08/24/2018
ms.author: labrenne
ms.custom: ''
ms.openlocfilehash: c3857e512da5fe4fceefa5f735ddc65f73e11623
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77026052"
---
# <a name="submit-a-large-number-of-tasks-to-a-batch-job"></a>Enviar um grande número de tarefas para um trabalho em lotes

Quando você executa cargas de trabalho de lote do Azure em grande escala, talvez queira enviar dezenas de milhares, centenas de milhares ou ainda mais tarefas para um único trabalho. 

Este artigo fornece orientações e alguns exemplos de código para enviar um grande número de tarefas com uma taxa de transferência significativamente maior para um único trabalho em lotes. Depois que as tarefas são enviadas, elas inserem a fila do lote para processamento no pool que você especificar para o trabalho.

## <a name="use-task-collections"></a>Usar coleções de tarefas

As APIs do lote fornecem métodos para adicionar tarefas com eficiência a um trabalho como uma *coleção*, além de uma de cada vez. Ao adicionar um grande número de tarefas, você deve usar os métodos ou sobrecargas apropriados para adicionar tarefas como uma coleção. Em geral, você cria uma coleção de tarefas definindo tarefas conforme você itera em um conjunto de arquivos de entrada ou parâmetros para seu trabalho.

O tamanho máximo da coleção de tarefas que você pode adicionar em uma única chamada depende da API do lote que você usa:

* As seguintes APIs do lote limitam a coleta a **100 tarefas**. O limite pode ser menor dependendo do tamanho das tarefas, por exemplo, se as tarefas tiverem um grande número de arquivos de recursos ou variáveis de ambiente.

    * [API REST](/rest/api/batchservice/task/addcollection)
    * [API Python](/python/api/azure-batch/azure.batch.operations.TaskOperations?view=azure-python)
    * [API do Node.js](/javascript/api/@azure/batch/task?view=azure-node-latest)

  Ao usar essas APIs, você precisa fornecer a lógica para dividir o número de tarefas para atender ao limite da coleção e manipular erros e repetições caso a adição de tarefas falhe. Se uma coleção de tarefas for muito grande para ser adicionada, a solicitação gerará um erro e deverá ser repetida novamente com menos tarefas.

* As APIs a seguir dão suporte a coleções de tarefas muito maiores – limitadas apenas pela disponibilidade de RAM no cliente remetente. Essas APIs tratam de forma transparente a divisão da coleção de tarefas em "partes" para as APIs de nível inferior e novas tentativas se a adição de tarefas falhar.

    * [API .NET](/dotnet/api/microsoft.azure.batch.cloudjob.addtaskasync?view=azure-dotnet)
    * [API de Java](/java/api/com.microsoft.azure.batch.protocol.tasks.addcollectionasync?view=azure-java-stable)
    * [Extensão da CLI do lote do Azure](batch-cli-templates.md) com modelos da CLI do lote
    * [Extensão do SDK do Python](https://pypi.org/project/azure-batch-extensions/)

## <a name="increase-throughput-of-task-submission"></a>Aumentar a taxa de transferência do envio de tarefas

Pode levar algum tempo para adicionar uma grande coleção de tarefas a um trabalho, por exemplo, até 1 minuto para adicionar 20.000 tarefas por meio da API do .NET. Dependendo da API do lote e da carga de trabalho, você pode melhorar a taxa de transferência da tarefa modificando uma ou mais das seguintes opções:

* **Tamanho da tarefa** : a adição de tarefas grandes demora mais do que a adição de partes menores. Para reduzir o tamanho de cada tarefa em uma coleção, você pode simplificar a linha de comando da tarefa, reduzir o número de variáveis de ambiente ou tratar requisitos para a execução da tarefa com mais eficiência. Por exemplo, em vez de usar um grande número de arquivos de recursos, instale dependências de tarefas usando uma [tarefa inicial](batch-api-basics.md#start-task) no pool ou use um [pacote de aplicativos](batch-application-packages.md) ou um [contêiner do Docker](batch-docker-container-workloads.md).

* **Número de operações paralelas** – dependendo da API do lote, aumente a taxa de transferência aumentando o número máximo de operações simultâneas pelo cliente do lote. Configure esta definição utilizando a propriedade [BatchClientParallelOptions.MaxDegreeOfParallelism](/dotnet/api/microsoft.azure.batch.batchclientparalleloptions.maxdegreeofparallelism) na API .NET ou o parâmetro `threads` de métodos como [TaskOperations.add_collection](/python/api/azure-batch/azure.batch.operations.TaskOperations?view=azure-python) na extensão Batch Python SDK. (Essa propriedade não está disponível no SDK do Python do lote nativo.) Por padrão, essa propriedade é definida como 1, mas a define como maior para melhorar a taxa de transferência de operações. Você compensa o aumento da taxa de transferência consumindo largura de banda de rede e algum desempenho de CPU. A taxa de transferência da tarefa aumenta em até 100 vezes o `MaxDegreeOfParallelism` ou `threads`. Na prática, você deve definir o número de operações simultâneas abaixo de 100. 
 
  A extensão da CLI do lote do Azure com modelos de lote aumenta o número de operações simultâneas automaticamente com base no número de núcleos disponíveis, mas essa propriedade não é configurável na CLI. 

* **Limites de conexão http** – o número de conexões http simultâneas pode limitar o desempenho do cliente de lote quando ele está adicionando um grande número de tarefas. O número de conexões HTTP é limitado a determinadas APIs. Ao desenvolver com a API do .NET, por exemplo, a propriedade [ServicePointManager. DefaultConnectionLimit](/dotnet/api/system.net.servicepointmanager.defaultconnectionlimit) é definida como 2 por padrão. Recomendamos que você aumente o valor para um número próximo ou maior que o número de operações paralelas.

## <a name="example-batch-net"></a>Exemplo: .NET do lote

Os trechos de código a seguir C# mostram as configurações a serem definidas ao adicionar um grande número de tarefas usando a API .net do lote.

Para aumentar a taxa de transferência da tarefa, aumente o valor da propriedade [MaxDegreeOfParallelism](/dotnet/api/microsoft.azure.batch.batchclientparalleloptions.maxdegreeofparallelism) do [BatchClient](/dotnet/api/microsoft.azure.batch.batchclient?view=azure-dotnet). Por exemplo:

```csharp
BatchClientParallelOptions parallelOptions = new BatchClientParallelOptions()
  {
    MaxDegreeOfParallelism = 15
  };
...
```
Adicione uma coleção de tarefas ao trabalho usando a sobrecarga apropriada do método [AddTaskAsync](/dotnet/api/microsoft.azure.batch.cloudjob.addtaskasync?view=azure-dotnet) ou [AddTask](/dotnet/api/microsoft.azure.batch.cloudjob.addtask?view=azure-dotnet
) . Por exemplo:

```csharp
// Add a list of tasks as a collection
List<CloudTask> tasksToAdd = new List<CloudTask>(); // Populate with your tasks
...
await batchClient.JobOperations.AddTaskAsync(jobId, tasksToAdd, parallelOptions);
```


## <a name="example-batch-cli-extension"></a>Exemplo: extensão da CLI do lote

Usando as extensões da CLI do lote do Azure com [modelos de CLI do lote](batch-cli-templates.md), crie um arquivo JSON de modelo de trabalho que inclui uma fábrica de [tarefas](https://github.com/Azure/azure-batch-cli-extensions/blob/master/doc/taskFactories.md). A fábrica de tarefas configura uma coleção de tarefas relacionadas para um trabalho de uma única definição de tarefa.  

Veja a seguir um modelo de trabalho de exemplo para um trabalho de limpeza paramétrica unidimensional com um grande número de tarefas – nesse caso, 250.000. A linha de comando da tarefa é um simples comando `echo`.

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
Para executar um trabalho com o modelo, consulte [usar modelos da CLI do lote do Azure e transferência de arquivos](batch-cli-templates.md).

## <a name="example-batch-python-sdk-extension"></a>Exemplo: extensão do SDK do Python do lote

Para usar a extensão do SDK do Python do lote do Azure, primeiro instale o SDK do Python e a extensão:

```
pip install azure-batch
pip install azure-batch-extensions
```

Configure um `BatchExtensionsClient` que usa a extensão do SDK:

```python

client = batch.BatchExtensionsClient(
    base_url=BATCH_ACCOUNT_URL, resource_group=RESOURCE_GROUP_NAME, batch_account=BATCH_ACCOUNT_NAME)
...
```

Crie uma coleção de tarefas para adicionar a um trabalho. Por exemplo:


```python
tasks = list()
# Populate the list with your tasks
...
```

Adicione a recolha de tarefas utilizando [task.add_collection](/python/api/azure-batch/azure.batch.operations.TaskOperations?view=azure-python). Defina o parâmetro `threads` para aumentar o número de operações simultâneas:

```python
try:
    client.task.add_collection(job_id, threads=100)
except Exception as e:
    raise e
```

A extensão do SDK do Python do lote também dá suporte à adição de parâmetros de tarefa ao trabalho usando uma especificação JSON para uma fábrica de tarefas. Por exemplo, configure os parâmetros de trabalho para uma limpeza paramétrica semelhante à do exemplo de modelo CLI do lote anterior:

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
                        "retentionTime": "PT1H"
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

Adicione os parâmetros de trabalho ao trabalho. Defina o parâmetro `threads` para aumentar o número de operações simultâneas:

```python
try:
    client.job.add(job_parameter, threads=50)
except Exception as e:
    raise e
```

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre como usar a extensão da CLI do lote do Azure com [modelos da CLI do lote](batch-cli-templates.md).
* Saiba mais sobre a [extensão do SDK do Python do lote](https://pypi.org/project/azure-batch-extensions/).
