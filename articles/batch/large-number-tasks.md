---
title: Submeter um grande número de tarefas
description: Como submeter eficientemente um número muito grande de tarefas num único trabalho do Lote Azure
ms.topic: article
ms.date: 08/24/2018
ms.openlocfilehash: 0be30e1a413a224d566db535d369a0b285b1f668
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82117408"
---
# <a name="submit-a-large-number-of-tasks-to-a-batch-job"></a>Submeta um grande número de tarefas a um trabalho de Lote

Quando executa cargas de trabalho em larga escala do Lote Azure, talvez queira submeter dezenas de milhares, centenas de milhares, ou ainda mais tarefas a um único trabalho. 

Este artigo dá orientação e alguns exemplos de código para submeter um grande número de tarefas com um aumento substancial de produção para um único trabalho de Lote. Após a apresentação das tarefas, entram na fila do Lote para processamento na piscina que especifica para o trabalho.

## <a name="use-task-collections"></a>Utilizar coleções de tarefas

As APIs do Lote fornecem métodos para adicionar eficientemente tarefas a um trabalho como *coleção,* além de uma de cada vez. Ao adicionar um grande número de tarefas, deve utilizar os métodos ou sobrecargas apropriados para adicionar tarefas como coleção. Geralmente, constrói uma coleção de tarefas definindo tarefas à medida que itera sobre um conjunto de ficheiros ou parâmetros de entrada para o seu trabalho.

O tamanho máximo da recolha de tarefas que pode adicionar numa única chamada depende da API do lote que utiliza:

* As seguintes APIs de lote limitam a recolha a **100 tarefas**. O limite poderia ser menor dependendo da dimensão das tarefas - por exemplo, se as tarefas tiverem um grande número de ficheiros de recursos ou variáveis ambientais.

    * [REST API](/rest/api/batchservice/task/addcollection)
    * [API Python](/python/api/azure-batch/azure.batch.operations.TaskOperations?view=azure-python)
    * [Node.js API](/javascript/api/@azure/batch/task?view=azure-node-latest)

  Ao utilizar estas APIs, é necessário fornecer lógica para dividir o número de tarefas para cumprir o limite de recolha e lidar com erros e repetições no caso de a adição de tarefas falhar. Se uma recolha de tarefas for demasiado grande para ser acrescentada, o pedido gera um erro e deve ser novamente julgado com menos tarefas.

* As seguintes APIs suportam coleções de tarefas muito maiores - limitadas apenas pela disponibilidade de RAM no cliente de apresentação. Estas APIs lidam de forma transparente dividindo a coleção de tarefas em "pedaços" para as APIs de nível inferior e retenta se a adição de tarefas falhar.

    * [API .NET](/dotnet/api/microsoft.azure.batch.cloudjob.addtaskasync?view=azure-dotnet)
    * [API de Java](/java/api/com.microsoft.azure.batch.protocol.tasks.addcollectionasync?view=azure-java-stable)
    * [Extensão CLI do lote azure](batch-cli-templates.md) com modelos CLI de lote
    * [Extensão SDK Python](https://pypi.org/project/azure-batch-extensions/)

## <a name="increase-throughput-of-task-submission"></a>Aumentar o contributo da submissão de tarefas

Pode levar algum tempo para adicionar uma grande coleção de tarefas a um trabalho - por exemplo, até 1 minuto para adicionar 20.000 tarefas através da API .NET. Dependendo da API do lote e da sua carga de trabalho, pode melhorar a produção de tarefa modificando um ou mais dos seguintes:

* **Tamanho da tarefa** - Adicionar tarefas grandes demora mais tempo do que adicionar as mais pequenas. Para reduzir o tamanho de cada tarefa numa coleção, pode simplificar a linha de comando de tarefas, reduzir o número de variáveis ambientais ou lidar com os requisitos para a execução de tarefas de forma mais eficiente. Por exemplo, em vez de utilizar um grande número de ficheiros de recursos, instale dependências de tarefas utilizando uma [tarefa](batch-api-basics.md#start-task) inicial na piscina ou utilize um pacote de [aplicação](batch-application-packages.md) ou [um recipiente Docker](batch-docker-container-workloads.md).

* **Número de operações paralelas** - Dependendo da API do lote, aumente a produção aumentando o número máximo de operações simultâneas pelo cliente Batch. Configure esta definição utilizando a propriedade [BatchClientParallelOptions.MaxDegreeOfParallelism](/dotnet/api/microsoft.azure.batch.batchclientparalleloptions.maxdegreeofparallelism) na `threads` API .NET ou o parâmetro de métodos como [TaskOperations.add_collection](/python/api/azure-batch/azure.batch.operations.TaskOperations?view=azure-python) na extensão Batch Python SDK. (Esta propriedade não está disponível no Batch Python SDK.) Por padrão, esta propriedade está definida para 1, mas deforma-a mais para melhorar a entrada de operações. Você troca o aumento da entrada, consumindo largura de banda da rede e algum desempenho cpu. A entrada de tarefas aumenta até `MaxDegreeOfParallelism` 100 vezes a ou `threads`. Na prática, deve fixar o número de operações simultâneas abaixo de 100. 
 
  A extensão CLI do lote Azure com modelos de lote aumenta automaticamente o número de operações simultâneas com base no número de núcleos disponíveis, mas esta propriedade não é configurável no CLI. 

* **LIMITES** de ligação HTTP - O número de ligações http simultâneas pode acelerar o desempenho do cliente Batch quando está a adicionar um grande número de tarefas. O número de ligações HTTP é limitado a certas APIs. Ao desenvolver com a API .NET, por exemplo, a propriedade [ServicePointManager.DefaultConnectionLimit](/dotnet/api/system.net.servicepointmanager.defaultconnectionlimit) é definida para 2 por padrão. Recomendamos que aumente o valor para um número próximo ou superior ao número de operações paralelas.

## <a name="example-batch-net"></a>Exemplo: Lote .NET

Os seguintes snippets C# mostram configurações para configurar ao adicionar um grande número de tarefas utilizando o Batch .NET API.

Para aumentar a produção de tarefas, aumente o valor da propriedade [MaxDegreeOfParallelism](/dotnet/api/microsoft.azure.batch.batchclientparalleloptions.maxdegreeofparallelism) do [BatchClient](/dotnet/api/microsoft.azure.batch.batchclient?view=azure-dotnet). Por exemplo:

```csharp
BatchClientParallelOptions parallelOptions = new BatchClientParallelOptions()
  {
    MaxDegreeOfParallelism = 15
  };
...
```
Adicione uma recolha de tarefas ao trabalho utilizando a sobrecarga adequada do método [AddTaskAsync](/dotnet/api/microsoft.azure.batch.cloudjob.addtaskasync?view=azure-dotnet) ou [AddTask.](/dotnet/api/microsoft.azure.batch.cloudjob.addtask?view=azure-dotnet
) Por exemplo:

```csharp
// Add a list of tasks as a collection
List<CloudTask> tasksToAdd = new List<CloudTask>(); // Populate with your tasks
...
await batchClient.JobOperations.AddTaskAsync(jobId, tasksToAdd, parallelOptions);
```


## <a name="example-batch-cli-extension"></a>Exemplo: Extensão CLI do lote

Utilizando as extensões CLI do lote Azure com [modelos CLI de lote,](batch-cli-templates.md)crie um ficheiro JSON modelo de trabalho que inclua uma fábrica de [tarefas](https://github.com/Azure/azure-batch-cli-extensions/blob/master/doc/taskFactories.md). A fábrica de tarefas configura uma coleção de tarefas relacionadas para um trabalho a partir de uma única definição de tarefa.  

Segue-se um modelo de trabalho de amostra para um trabalho de varredura paramétrica unidimensional com um grande número de tarefas - neste caso, 250.000. A linha de comando `echo` de tarefaé um simples comando.

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
Para executar um trabalho com o modelo, consulte [utilize modelos CLI](batch-cli-templates.md)de lote azure e transferência de ficheiros .

## <a name="example-batch-python-sdk-extension"></a>Exemplo: Extensão de Lote Python SDK

Para utilizar a extensão SDK de Lote Azure, instale primeiro o Python SDK e a extensão:

```
pip install azure-batch
pip install azure-batch-extensions
```

Configurar `BatchExtensionsClient` uma extensão sdk que utilize:

```python

client = batch.BatchExtensionsClient(
    base_url=BATCH_ACCOUNT_URL, resource_group=RESOURCE_GROUP_NAME, batch_account=BATCH_ACCOUNT_NAME)
...
```

Criar uma coleção de tarefas para adicionar a um trabalho. Por exemplo:


```python
tasks = list()
# Populate the list with your tasks
...
```

Adicione a recolha de tarefas utilizando [task.add_collection](/python/api/azure-batch/azure.batch.operations.TaskOperations?view=azure-python). Definir `threads` o parâmetro para aumentar o número de operações simultâneas:

```python
try:
    client.task.add_collection(job_id, threads=100)
except Exception as e:
    raise e
```

A extensão Batch Python SDK também suporta a adição de parâmetros de tarefa para o trabalho utilizando uma especificação JSON para uma fábrica de tarefas. Por exemplo, configurar parâmetros de trabalho para uma varredura paramétrica semelhante à do modelo CLI do lote anterior:

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

Adicione os parâmetros de trabalho ao trabalho. Definir `threads` o parâmetro para aumentar o número de operações simultâneas:

```python
try:
    client.job.add(job_parameter, threads=50)
except Exception as e:
    raise e
```

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre a utilização da extensão CLI do lote Azure com [modelos CLI do lote](batch-cli-templates.md).
* Saiba mais sobre a [extensão Batch Python SDK](https://pypi.org/project/azure-batch-extensions/).
