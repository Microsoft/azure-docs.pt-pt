---
title: Submeter um grande número de tarefas
description: Como submeter eficientemente um grande número de tarefas num único trabalho do Azure Batch
ms.topic: how-to
ms.date: 08/24/2018
ms.custom: devx-track-python, devx-track-csharp
ms.openlocfilehash: 26230372a04d13a8b8f59d50aa5da1362126413b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89144061"
---
# <a name="submit-a-large-number-of-tasks-to-a-batch-job"></a>Submeta um grande número de tarefas a um trabalho de Lote

Quando você executar cargas de trabalho em grande escala Azure Batch, você pode querer submeter dezenas de milhares, centenas de milhares, ou ainda mais tarefas para um único trabalho. 

Este artigo dá orientações e alguns exemplos de código para submeter um grande número de tarefas com um rendimento substancialmente aumentado para um único trabalho de Batch. Após a apresentação das tarefas, introduzem a fila do Lote para processamento na piscina que especifica para o trabalho.

## <a name="use-task-collections"></a>Utilizar coleções de tarefas

As APIs do Lote fornecem métodos para adicionar tarefas eficientes a um trabalho como *coleção,* além de uma de cada vez. Ao adicionar um grande número de tarefas, deve utilizar os métodos ou sobrecargas adequados para adicionar tarefas como coleção. Geralmente, constrói uma recolha de tarefas definindo tarefas à medida que itera sobre um conjunto de ficheiros de entrada ou parâmetros para o seu trabalho.

O tamanho máximo da recolha de tarefas que pode adicionar numa única chamada depende da API do lote que utiliza:

* As APIs seguintes limitam a recolha a **100 tarefas**. O limite poderia ser menor dependendo do tamanho das tarefas - por exemplo, se as tarefas tiverem um grande número de ficheiros de recursos ou variáveis ambientais.

    * [API REST](/rest/api/batchservice/task/addcollection)
    * [API Python](/python/api/azure-batch/azure.batch.operations.TaskOperations)
    * [API do Node.js](/javascript/api/@azure/batch/task)

  Ao utilizar estas APIs, é necessário fornecer lógica para dividir o número de tarefas para cumprir o limite de recolha e para lidar com erros e retrações em caso de falha de tarefas. Se uma recolha de tarefas for demasiado grande para ser acrescentada, o pedido gera um erro e deve ser novamente julgado com menos tarefas.

* As APIs seguintes suportam coleções de tarefas muito maiores - limitadas apenas pela disponibilidade de RAM no cliente que o submete. Estas APIs lidam de forma transparente dividindo a recolha de tarefas em "pedaços" para as APIs de nível inferior e as retrações se a adição de tarefas falhar.

    * [API .NET](/dotnet/api/microsoft.azure.batch.cloudjob.addtaskasync)
    * [API de Java](/java/api/com.microsoft.azure.batch.protocol.tasks.addcollectionasync)
    * [Extensão CLI do lote de Azure](batch-cli-templates.md) com modelos de CLI de lote
    * [Extensão Python SDK](https://pypi.org/project/azure-batch-extensions/)

## <a name="increase-throughput-of-task-submission"></a>Aumentar a produção de submissão de tarefas

Pode levar algum tempo para adicionar uma grande coleção de tarefas a um trabalho - por exemplo, até 1 minuto para adicionar 20.000 tarefas através da API .NET. Dependendo da API do Lote e da sua carga de trabalho, pode melhorar a produção da tarefa modificando um ou mais dos seguintes seguintes:

* **Tamanho da tarefa** - Adicionar grandes tarefas leva mais tempo do que adicionar as mais pequenas. Para reduzir o tamanho de cada tarefa numa coleção, pode simplificar a linha de comando de tarefa, reduzir o número de variáveis ambientais ou lidar com os requisitos para a execução da tarefa de forma mais eficiente. Por exemplo, em vez de utilizar um grande número de ficheiros de recursos, instale dependências de tarefas utilizando uma [tarefa inicial](jobs-and-tasks.md#start-task) na piscina ou utilize um pacote [de aplicações](batch-application-packages.md) ou [um recipiente Docker](batch-docker-container-workloads.md).

* **Número de operações paralelas** - Dependendo da API do Lote, aumente a produção aumentando o número máximo de operações simultâneas pelo cliente Batch. Configure esta definição utilizando a propriedade [BatchClientParallelOptions.MaxDegreeOfParallelism](/dotnet/api/microsoft.azure.batch.batchclientparalleloptions.maxdegreeofparallelism) na API .NET, ou o `threads` parâmetro de métodos como [TaskOperations.add_collection](/python/api/azure-batch/azure.batch.operations.TaskOperations) na extensão Batch Python SDK. (Esta propriedade não está disponível no Batch Python SDK nativo.) Por padrão, esta propriedade está definida para 1, mas defini-lo mais alto para melhorar o rendimento das operações. Você negoceia uma produção aumentada consumindo largura de banda de rede e algum desempenho de CPU. A produção de tarefas aumenta até 100 vezes o `MaxDegreeOfParallelism` ou `threads` . Na prática, deverá definir o número de operações simultâneas abaixo de 100. 
 
  A extensão CLI do Lote Azure com modelos de Lote aumenta automaticamente o número de operações simultâneas com base no número de núcleos disponíveis, mas esta propriedade não é configurável no CLI. 

* **Limites de ligação HTTP** - O número de ligações HTTP simultâneas pode acelerar o desempenho do cliente Batch quando este está a adicionar um grande número de tarefas. O número de ligações HTTP é limitado com determinadas APIs. Ao desenvolver-se com a API .NET, por exemplo, a propriedade [ServicePointManager.DefaultConnectionLimit](/dotnet/api/system.net.servicepointmanager.defaultconnectionlimit) é definida para 2 por padrão. Recomendamos que aumente o valor para um número próximo ou superior ao número de operações paralelas.

## <a name="example-batch-net"></a>Exemplo: Lote .NET

Os seguintes snippets C# mostram configurações para configurar ao adicionar um grande número de tarefas utilizando o Lote .NET API.

Para aumentar a produção de tarefas, aumente o valor da propriedade [MaxDegreeOfParallelism](/dotnet/api/microsoft.azure.batch.batchclientparalleloptions.maxdegreeofparallelism) do [BatchClient](/dotnet/api/microsoft.azure.batch.batchclient). Por exemplo:

```csharp
BatchClientParallelOptions parallelOptions = new BatchClientParallelOptions()
  {
    MaxDegreeOfParallelism = 15
  };
...
```
Adicione uma coleção de tarefas ao trabalho utilizando a sobrecarga adequada do método [AddTaskAsync](/dotnet/api/microsoft.azure.batch.cloudjob.addtaskasync) ou [AddTask.](/dotnet/api/microsoft.azure.batch.cloudjob.addtask
) Por exemplo:

```csharp
// Add a list of tasks as a collection
List<CloudTask> tasksToAdd = new List<CloudTask>(); // Populate with your tasks
...
await batchClient.JobOperations.AddTaskAsync(jobId, tasksToAdd, parallelOptions);
```


## <a name="example-batch-cli-extension"></a>Exemplo: Extensão do Lote CLI

Utilizando as extensões CLI do Lote Azure com [modelos de CLI do lote,](batch-cli-templates.md)crie um ficheiro JSON de modelo de trabalho que inclua uma [fábrica de tarefas](https://github.com/Azure/azure-batch-cli-extensions/blob/master/doc/taskFactories.md). A fábrica de tarefas configura uma recolha de tarefas relacionadas para um trabalho a partir de uma única definição de tarefa.  

Segue-se um modelo de trabalho de amostra para um trabalho de varredura paramétrica unidimensional com um grande número de tarefas - neste caso, 250.000. A linha de comando de tarefa é um `echo` simples comando.

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
Para executar um trabalho com o modelo, consulte [use modelos CLI do lote de Azure e transferência de ficheiros](batch-cli-templates.md).

## <a name="example-batch-python-sdk-extension"></a>Exemplo: Extensão SDK de Lote Python

Para utilizar a extensão Azure Batch Python SDK, instale primeiro o Python SDK e a extensão:

```
pip install azure-batch
pip install azure-batch-extensions
```

Configurar uma `BatchExtensionsClient` que utilize a extensão SDK:

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

Adicione a recolha de tarefas utilizando [task.add_collection](/python/api/azure-batch/azure.batch.operations.TaskOperations). Definir o `threads` parâmetro para aumentar o número de operações simultâneas:

```python
try:
    client.task.add_collection(job_id, threads=100)
except Exception as e:
    raise e
```

A extensão Batch Python SDK também suporta a adição de parâmetros de tarefa ao trabalho usando uma especificação JSON para uma fábrica de tarefas. Por exemplo, configurar parâmetros de trabalho para uma varredura paramétrica semelhante à do modelo CLI do lote anterior:

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

Adicione os parâmetros de trabalho ao trabalho. Definir o `threads` parâmetro para aumentar o número de operações simultâneas:

```python
try:
    client.job.add(job_parameter, threads=50)
except Exception as e:
    raise e
```

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre a utilização da extensão CLI do Lote Azure com [os modelos De Lote CLI](batch-cli-templates.md).
* Saiba mais sobre a [extensão Batch Python SDK](https://pypi.org/project/azure-batch-extensions/).
