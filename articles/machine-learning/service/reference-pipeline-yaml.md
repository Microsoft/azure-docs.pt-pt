---
title: YAML do pipeline de Machine Learning
titleSuffix: Azure Machine Learning
description: Saiba como definir um pipeline de Machine Learning usando um arquivo YAML. As definições de pipeline YAML são usadas com a extensão de aprendizado de máquina para o CLI do Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: sanpil
author: sanpil
ms.date: 11/11/2019
ms.openlocfilehash: 474a184b24ca3318a33adb89b25640939a814474
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2019
ms.locfileid: "73929502"
---
# <a name="define-machine-learning-pipelines-in-yaml"></a>Definir pipelines de Machine Learning no YAML

Saiba como definir seus pipelines de Machine Learning no [YAML](https://yaml.org/). Ao usar a extensão do Machine Learning para o CLI do Azure, muitos dos comandos relacionados ao pipeline esperam um arquivo YAML que define o pipeline.

A tabela a seguir lista o que é e não tem suporte atualmente ao definir um pipeline no YAML:

| Tipo de etapa | Suportada? |
| ----- | :-----: |
| PythonScriptStep | Sim |
| AdlaStep | Sim |
| AzureBatchStep | Sim |
| DatabricksStep | Sim |
| DataTransferStep | Sim |
| AutoMLStep | Não |
| HyperDriveStep | Não |
| ModuleStep | Não |
| MPIStep | Não |
| EstimatorStep | Não |

## <a name="pipeline-definition"></a>Definição de pipeline

Uma definição de pipeline usa as seguintes chaves, que correspondem à classe [pipelines](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline.pipeline?view=azure-ml-py) :

| Chave YAML | Descrição |
| ----- | ----- |
| `name` | A descrição do pipeline. |
| `parameters` | Parâmetro (s) para o pipeline. |
| `data_reference` | Define como e onde os dados devem ser disponibilizados em uma execução. |
| `default_compute` | Destino de computação padrão onde todas as etapas no pipeline são executadas. |
| `steps` | As etapas usadas no pipeline. |

## <a name="parameters"></a>Parâmetros

A seção `parameters` usa as seguintes chaves, que correspondem à classe [PipelineParameter](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelineparameter?view=azure-ml-py) :

| Chave YAML | Descrição |
| ---- | ---- |
| `type` | O tipo de valor do parâmetro. Os tipos válidos são `string`, `int`, `float`, `bool`ou `datapath`. |
| `default` | O valor padrão. |

Cada parâmetro é nomeado. Por exemplo, o trecho de código YAML a seguir define três parâmetros chamados `NumIterationsParameter`, `DataPathParameter`e `NodeCountParameter`:

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        NumIterationsParameter:
            type: int
            default: 40
        DataPathParameter:
            type: datapath
            default:
                datastore: workspaceblobstore
                path_on_datastore: sample2.txt
        NodeCountParameter:
            type: int
            default: 4
```

## <a name="data-reference"></a>Referência de dados

A seção `data_references` usa as seguintes chaves, que correspondem à [referência de Datareferente](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py):

| Chave YAML | Descrição |
| ----- | ----- |
| `datastore` | O repositório de armazenamento para referenciar. |
| `path_on_datastore` | O caminho relativo no armazenamento de backup para a referência de dados. |

Cada referência de dados está contida em uma chave. Por exemplo, o trecho de código YAML a seguir define uma referência de dados armazenada na chave chamada `employee_data`:

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        PipelineParam1:
            type: int
            default: 3
    data_references:
        employee_data:
            datastore: adftestadla
            path_on_datastore: "adla_sample/sample_input.csv"
```

## <a name="steps"></a>Passos

As etapas definem um ambiente computacional, juntamente com os arquivos a serem executados no ambiente. Para definir o tipo de uma etapa, use a chave de `type`:

| Tipo de etapa | Descrição |
| ----- | ----- |
| `AdlaStep` | Executa um script U-SQL com Azure Data Lake Analytics. Corresponde à classe [AdlaStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.adlastep?view=azure-ml-py) . |
| `AzureBatchStep` | Executa trabalhos usando o lote do Azure. Corresponde à classe [AzureBatchStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.azurebatchstep?view=azure-ml-py) . |
| `DatabricsStep` | Adiciona um bloco de anotações do databricks, script Python ou JAR. Corresponde à classe [DatabricksStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricksstep?view=azure-ml-py) . |
| `DataTransferStep` | Transfere dados entre as opções de armazenamento. Corresponde à classe [DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep?view=azure-ml-py) . |
| `PythonScriptStep` | Executa um script Python. Corresponde à classe [PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py) . |

### <a name="adla-step"></a>Etapa ADLA

| Chave YAML | Descrição |
| ----- | ----- |
| `script_name` | O nome do script U-SQL (relativo ao `source_directory`). |
| `compute_target` | O destino de computação Azure Data Lake a ser usado para esta etapa. |
| `parameters` | [Parâmetros](#parameters) para o pipeline. |
| `inputs` | As entradas podem ser [InputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding?view=azure-ml-py), [DataReference](#data-reference), [PortDataReference](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference?view=azure-ml-py), [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py), [DataSet](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py), [DatasetDefinition](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition?view=azure-ml-py)ou [PipelineDataset](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset?view=azure-ml-py). |
| `outputs` | As saídas podem ser [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) ou [OutputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding?view=azure-ml-py). |
| `source_directory` | Diretório que contém o script, assemblies, etc. |
| `priority` | O valor de prioridade a ser usado para o trabalho atual. |
| `params` | Dicionário de pares nome-valor. |
| `degree_of_parallelism` | O grau de paralelismo a ser usado para este trabalho. |
| `runtime_version` | A versão de tempo de execução do mecanismo de Data Lake Analytics. |
| `allow_reuse` | Determina se a etapa deve reutilizar os resultados anteriores quando executado novamente com as mesmas configurações. |

O exemplo a seguir contém uma definição de etapa ADLA:

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        PipelineParam1:
            type: int
            default: 3
    data_references:
        employee_data:
            datastore: adftestadla
            path_on_datastore: "adla_sample/sample_input.csv"
    default_compute: adlacomp
    steps:
        Step1:
            runconfig: "D:\\Yaml\\default_runconfig.yml"
            parameters:
                NUM_ITERATIONS_2:
                    source: PipelineParam1
                NUM_ITERATIONS_1: 7
            type: "AdlaStep"
            name: "MyAdlaStep"
            script_name: "sample_script.usql"
            source_directory: "D:\\scripts\\Adla"
            inputs:
                employee_data:
                    source: employee_data
            outputs:
                OutputData:
                    destination: Output4
                    datastore: adftestadla
                    bind_mode: mount
```

### <a name="azure-batch-step"></a>Etapa do lote do Azure

| Chave YAML | Descrição |
| ----- | ----- |
| `compute_target` | O destino de computação do lote do Azure a ser usado para esta etapa. |
| `inputs` | As entradas podem ser [InputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding?view=azure-ml-py), [DataReference](#data-reference), [PortDataReference](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference?view=azure-ml-py), [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py), [DataSet](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py), [DatasetDefinition](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition?view=azure-ml-py)ou [PipelineDataset](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset?view=azure-ml-py). |
| `outputs` | As saídas podem ser [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) ou [OutputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding?view=azure-ml-py). |
| `source_directory` | Diretório que contém os binários de módulo, executáveis, assemblies, etc. |
| `executable` | Nome do comando/executável que será executado como parte desse trabalho. |
| `create_pool` | Sinalizador booliano para indicar se o pool deve ser criado antes da execução do trabalho. |
| `delete_batch_job_after_finish` | Sinalizador booliano para indicar se o trabalho da conta do lote deve ser excluído após ser concluído. |
| `delete_batch_pool_after_finish` | Sinalizador booliano para indicar se o pool deve ser excluído após a conclusão do trabalho. |
| `is_positive_exit_code_failure` | Sinalizador booliano para indicar se o trabalho falhará se a tarefa for encerrada com um código positivo. |
| `vm_image_urn` | Se `create_pool` for `True`e a VM usar `VirtualMachineConfiguration`. |
| `pool_id` | A ID do pool em que o trabalho será executado. |
| `allow_reuse` | Determina se a etapa deve reutilizar os resultados anteriores quando executado novamente com as mesmas configurações. |

O exemplo a seguir contém uma definição de etapa do lote do Azure:

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        PipelineParam1:
            type: int
            default: 3
    data_references:
        input:
            datastore: workspaceblobstore
            path_on_datastore: "input.txt"
    default_compute: testbatch
    steps:
        Step1:
            runconfig: "D:\\Yaml\\default_runconfig.yml"
            parameters:
                NUM_ITERATIONS_2:
                    source: PipelineParam1
                NUM_ITERATIONS_1: 7
            type: "AzureBatchStep"
            name: "MyAzureBatchStep"
            pool_id: "MyPoolName"
            create_pool: true
            executable: "azurebatch.cmd"
            source_directory: "D:\\scripts\\AureBatch"
            allow_reuse: false
            inputs:
                input:
                    source: input
            outputs:
                output:
                    destination: output
                    datastore: workspaceblobstore
```

### <a name="databricks-step"></a>Etapa do databricks

| Chave YAML | Descrição |
| ----- | ----- |
| `compute_target` | O destino de computação Azure Databricks a ser usado para esta etapa. |
| `inputs` | As entradas podem ser [InputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding?view=azure-ml-py), [DataReference](#data-reference), [PortDataReference](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference?view=azure-ml-py), [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py), [DataSet](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py), [DatasetDefinition](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition?view=azure-ml-py)ou [PipelineDataset](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset?view=azure-ml-py). |
| `outputs` | As saídas podem ser [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) ou [OutputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding?view=azure-ml-py). |
| `run_name` | O nome em databricks para esta execução. |
| `source_directory` | Diretório que contém o script e outros arquivos. |
| `num_workers` | O número estático de trabalhos para o databricks executam o cluster. |
| `runconfig` | O caminho para um arquivo de `.runconfig`. Esse arquivo é uma representação YAML da classe [RunConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py) . Para obter mais informações sobre a estrutura desse arquivo, consulte [runconfigschema. JSON](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json). |
| `allow_reuse` | Determina se a etapa deve reutilizar os resultados anteriores quando executado novamente com as mesmas configurações. |

O exemplo a seguir contém uma etapa do databricks:

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        PipelineParam1:
            type: int
            default: 3
    data_references:
        adls_test_data:
            datastore: adftestadla
            path_on_datastore: "testdata"
        blob_test_data:
            datastore: workspaceblobstore
            path_on_datastore: "dbtest"
    default_compute: mydatabricks
    steps:
        Step1:
            runconfig: "D:\\Yaml\\default_runconfig.yml"
            parameters:
                NUM_ITERATIONS_2:
                    source: PipelineParam1
                NUM_ITERATIONS_1: 7
            type: "DatabricksStep"
            name: "MyDatabrickStep"
            run_name: "DatabricksRun"
            python_script_name: "train-db-local.py"
            source_directory: "D:\\scripts\\Databricks"
            num_workers: 1
            allow_reuse: true
            inputs:
                blob_test_data:
                    source: blob_test_data
            outputs:
                OutputData:
                    destination: Output4
                    datastore: workspaceblobstore
                    bind_mode: mount
```

### <a name="data-transfer-step"></a>Etapa de transferência de dados

| Chave YAML | Descrição |
| ----- | ----- |
| `compute_target` | O destino de computação Azure Data Factory a ser usado para esta etapa. |
| `source_data_reference` | Conexão de entrada que serve como a fonte de operações de transferência de dados. Os valores com suporte são [InputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding?view=azure-ml-py), [DataReference](#data-reference), [PortDataReference](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference?view=azure-ml-py), [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py), [DataSet](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py), [DatasetDefinition](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition?view=azure-ml-py)ou [PipelineDataset](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset?view=azure-ml-py). |
| `destination_data_reference` | Conexão de entrada que serve como o destino das operações de transferência de dados. Os valores com suporte são [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) e [OutputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding?view=azure-ml-py). |
| `allow_reuse` | Determina se a etapa deve reutilizar os resultados anteriores quando executado novamente com as mesmas configurações. |

O exemplo a seguir contém uma etapa de transferência de dados:

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        PipelineParam1:
            type: int
            default: 3
    data_references:
        adls_test_data:
            datastore: adftestadla
            path_on_datastore: "testdata"
        blob_test_data:
            datastore: workspaceblobstore
            path_on_datastore: "testdata"
    default_compute: adftest
    steps:
        Step1:
            runconfig: "D:\\Yaml\\default_runconfig.yml"
            parameters:
                NUM_ITERATIONS_2:
                    source: PipelineParam1
                NUM_ITERATIONS_1: 7
            type: "DataTransferStep"
            name: "MyDataTransferStep"
            adla_compute_name: adftest
            source_data_reference:
                adls_test_data:
                    source: adls_test_data
            destination_data_reference:
                blob_test_data:
                    source: blob_test_data
```

### <a name="python-script-step"></a>Etapa de script do Python

| Chave YAML | Descrição |
| ----- | ----- |
| `compute_target` | O destino de computação a ser usado para esta etapa. O destino de computação pode ser um Azure Machine Learning computação, uma máquina virtual (como o VM de Ciência de Dados) ou o HDInsight. |
| `inputs` | As entradas podem ser [InputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding?view=azure-ml-py), [DataReference](#data-reference), [PortDataReference](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference?view=azure-ml-py), [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py), [DataSet](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py), [DatasetDefinition](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition?view=azure-ml-py)ou [PipelineDataset](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset?view=azure-ml-py). |
| `outputs` | As saídas podem ser [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) ou [OutputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding?view=azure-ml-py). |
| `script_name` | O nome do script Python (relativo a `source_directory`). |
| `source_directory` | Diretório que contém o script, o ambiente Conda, etc. |
| `runconfig` | O caminho para um arquivo de `.runconfig`. Esse arquivo é uma representação YAML da classe [RunConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py) . Para obter mais informações sobre a estrutura desse arquivo, consulte [runconfig. JSON](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json). |
| `allow_reuse` | Determina se a etapa deve reutilizar os resultados anteriores quando executado novamente com as mesmas configurações. |

O exemplo a seguir contém uma etapa de script Python:

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        PipelineParam1:
            type: int
            default: 3
    data_references:
        DataReference1:
            datastore: workspaceblobstore
            path_on_datastore: testfolder/sample.txt
    default_compute: cpu-cluster
    steps:
        Step1:
            runconfig: "D:\\Yaml\\default_runconfig.yml"
            parameters:
                NUM_ITERATIONS_2:
                    source: PipelineParam1
                NUM_ITERATIONS_1: 7
            type: "PythonScriptStep"
            name: "MyPythonScriptStep"
            script_name: "train.py"
            allow_reuse: True
            source_directory: "D:\\scripts\\PythonScript"
            inputs:
                InputData:
                    source: DataReference1
            outputs:
                OutputData:
                    destination: Output4
                    datastore: workspaceblobstore
                    bind_mode: mount
```

## <a name="schedules"></a>Agendas

Ao definir o agendamento de um pipeline, ele pode ser disparado por armazenamento de datastore ou recorrente com base em um intervalo de tempo. A seguir estão as chaves usadas para definir uma agenda:

| Chave YAML | Descrição |
| ----- | ----- |
| `description` | Uma descrição da agenda. |
| `recurrence` | Contém configurações de recorrência, se a agenda for recorrente. |
| `pipeline_parameters` | Quaisquer parâmetros exigidos pelo pipeline. |
| `wait_for_provisioning` | Se deve aguardar a conclusão do provisionamento da agenda. |
| `wait_timeout` | O número de segundos a aguardar antes de atingir o tempo limite. |
| `datastore_name` | O repositório de armazenamento a ser monitorado para BLOBs modificados/adicionados. |
| `polling_interval` | Por quanto tempo, em minutos, entre a sondagem para BLOBs modificados/adicionados. Valor padrão: 5 minutos. Com suporte apenas para agendas de repositório de armazenamento. |
| `data_path_parameter_name` | O nome do parâmetro de pipeline de caminho de dados a ser definido com o caminho de blob alterado. Com suporte apenas para agendas de repositório de armazenamento. |
| `continue_on_step_failure` | Se deseja continuar a execução de outras etapas no PipelineRun enviado se uma etapa falhar. Se fornecido, substituirá a configuração de `continue_on_step_failure` do pipeline.
| `path_on_datastore` | Opcional. O caminho no repositório de armazenamento a ser monitorado para BLOBs modificados/adicionados. O caminho está sob o contêiner para o armazenamento de dados, portanto, o caminho real que o agendamento monitora é contêiner/`path_on_datastore`. Se nenhum, o contêiner do repositório de armazenamento é monitorado. Adições/modificações feitas em uma subpasta da `path_on_datastore` não são monitoradas. Com suporte apenas para agendas de repositório de armazenamento. |

O exemplo a seguir contém a definição de uma agenda disparada por repositório de armazenamento:

```yaml
Schedule: 
      description: "Test create with datastore" 
      recurrence: ~ 
      pipeline_parameters: {} 
      wait_for_provisioning: True 
      wait_timeout: 3600 
      datastore_name: "workspaceblobstore" 
      polling_interval: 5 
      data_path_parameter_name: "input_data" 
      continue_on_step_failure: None 
      path_on_datastore: "file/path" 
```

Ao definir um **agendamento recorrente**, use as seguintes chaves em `recurrence`:

| Chave YAML | Descrição |
| ----- | ----- |
| `frequency` | Com que frequência a agenda se repete. Os valores válidos são `"Minute"`, `"Hour"`, `"Day"`, `"Week"`ou `"Month"`. |
| `interval` | Com que frequência a agenda é acionada. O valor inteiro é o número de unidades de tempo a aguardar até que a agenda seja acionada novamente. |
| `start_time` | A hora de início da agenda. O formato da cadeia de caracteres do valor é `YYYY-MM-DDThh:mm:ss`. Se nenhuma hora de início for fornecida, a primeira carga de trabalho será executada instantaneamente e as cargas de trabalho futuras serão executadas com base na agenda. Se a hora de início estiver no passado, a primeira carga de trabalho será executada no próximo tempo de execução calculado. |
| `time_zone` | O fuso horário da hora de início. Se nenhum fuso horário for fornecido, o UTC será usado. |
| `hours` | Se `frequency` for `"Day"` ou `"Week"`, você poderá especificar um ou mais números inteiros de 0 a 23, separados por vírgulas, como as horas do dia em que o pipeline deve ser executado. Somente `time_of_day` ou `hours` e `minutes` podem ser usados. |
| `minutes` | Se `frequency` for `"Day"` ou `"Week"`, você poderá especificar um ou mais números inteiros de 0 a 59, separados por vírgulas, como os minutos da hora em que o pipeline deve ser executado. Somente `time_of_day` ou `hours` e `minutes` podem ser usados. |
| `time_of_day` | Se `frequency` for `"Day"` ou `"Week"`, você poderá especificar uma hora do dia para a execução da agenda. O formato da cadeia de caracteres do valor é `hh:mm`. Somente `time_of_day` ou `hours` e `minutes` podem ser usados. |
| `week_days` | Se `frequency` for `"Week"`, você poderá especificar um ou mais dias, separados por vírgulas, quando a agenda deve ser executada. Os valores válidos são `"Monday"`, `"Tuesday"`, `"Wednesday"`, `"Thursday"`, `"Friday"`, `"Saturday"`e `"Sunday"`. |

O exemplo a seguir contém a definição de um agendamento recorrente:

```yaml
Schedule: 
    description: "Test create with recurrence" 
    recurrence: 
        frequency: Week # Can be "Minute", "Hour", "Day", "Week", or "Month". 
        interval: 1 # how often fires 
        start_time: 2019-06-07T10:50:00 
        time_zone: UTC 
        hours: 
        - 1 
        minutes: 
        - 0 
        time_of_day: null 
        week_days: 
        - Friday 
    pipeline_parameters: 
        'a': 1 
    wait_for_provisioning: True 
    wait_timeout: 3600 
    datastore_name: ~ 
    polling_interval: ~ 
    data_path_parameter_name: ~ 
    continue_on_step_failure: None 
    path_on_datastore: ~ 
```

## <a name="next-steps"></a>Passos seguintes

Saiba como [usar a extensão da CLI para Azure Machine Learning](reference-azure-machine-learning-cli.md).