---
title: Gasoduto de Aprendizagem automática YAML
titleSuffix: Azure Machine Learning
description: Aprenda a definir um pipeline de aprendizagem automática utilizando um ficheiro YAML. As definições de gasoduto YAML são utilizadas com a extensão de aprendizagem automática para o Azure CLI.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: sanpil
author: sanpil
ms.date: 11/11/2019
ms.openlocfilehash: 40e6d7f3d9c28708c5adec26ddc3c0463e75adc0
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81529710"
---
# <a name="define-machine-learning-pipelines-in-yaml"></a>Defina os gasodutos de aprendizagem automática em YAML

Aprenda a definir os seus oleodutos de aprendizagem automática em [YAML](https://yaml.org/). Ao utilizar a extensão de aprendizagem automática para o Azure CLI, muitos dos comandos relacionados com o gasoduto esperam um ficheiro YAML que define o gasoduto.

A tabela que se segue enumera o que é e não é atualmente suportado na definição de um gasoduto em YAML:

| Tipo de passo | Suportada? |
| ----- | :-----: |
| PythonScriptStep | Sim |
| AdlaStep | Sim |
| AzureBatchStep | Sim |
| DatabricksStep | Sim |
| DataTransferStep | Sim |
| AutoMLStep | Não |
| HyperDriveStep | Não |
| MóduloStep | Sim |
| MPIStep | Não |
| EstimativaSStep | Não |

## <a name="pipeline-definition"></a>Definição do gasoduto

Uma definição de gasoduto utiliza as seguintes teclas, que correspondem à classe [Pipelines:](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline.pipeline?view=azure-ml-py)

| Chave YAML | Descrição |
| ----- | ----- |
| `name` | A descrição do oleoduto. |
| `parameters` | Parâmetros para o gasoduto. |
| `data_reference` | Define como e onde os dados devem ser disponibilizados numa execução. |
| `default_compute` | Alvo de computação padrão onde todos os passos do gasoduto são executados. |
| `steps` | Os passos usados no oleoduto. |

## <a name="parameters"></a>Parâmetros

A `parameters` secção utiliza as seguintes teclas, que correspondem à classe [PipelineParameter:](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelineparameter?view=azure-ml-py)

| Chave YAML | Descrição |
| ---- | ---- |
| `type` | O tipo de valor do parâmetro. Os tipos `string` `int`válidos são, , `float`ou `bool` `datapath`. |
| `default` | O valor padrão. |

Cada parâmetro é nomeado. Por exemplo, o seguinte snippet YAML `NumIterationsParameter`define `DataPathParameter`três `NodeCountParameter`parâmetros nomeados, e:

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

A `data_references` secção utiliza as seguintes teclas, que correspondem à [DataReference:](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py)

| Chave YAML | Descrição |
| ----- | ----- |
| `datastore` | A loja de dados para referência. |
| `path_on_datastore` | O caminho relativo no armazenamento de apoio para a referência de dados. |

Cada referência de dados está contida numa chave. Por exemplo, o seguinte snippet YAML define uma referência `employee_data`de dados armazenada na chave denominada:

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

Os passos definem um ambiente computacional, juntamente com os ficheiros para executar no ambiente. Para definir o tipo de `type` passo, use a chave:

| Tipo de passo | Descrição |
| ----- | ----- |
| `AdlaStep` | Executa um script U-SQL com Azure Data Lake Analytics. Corresponde à classe [AdlaStep.](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.adlastep?view=azure-ml-py) |
| `AzureBatchStep` | Gere trabalhos usando o Lote Azure. Corresponde à classe [AzureBatchStep.](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.azurebatchstep?view=azure-ml-py) |
| `DatabricsStep` | Adiciona um caderno databricks, script Python ou JAR. Corresponde à classe [DatabricksStep.](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricksstep?view=azure-ml-py) |
| `DataTransferStep` | Transfere dados entre opções de armazenamento. Corresponde à classe [DataTransferStep.](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep?view=azure-ml-py) |
| `PythonScriptStep` | Tem um guião python. Corresponde à classe [PythonScriptStep.](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py) |

### <a name="adla-step"></a>Passo ADLA

| Chave YAML | Descrição |
| ----- | ----- |
| `script_name` | O nome do script U-SQL `source_directory`(em relação ao ). |
| `compute_target` | O objetivo de computação do Lago de Dados Azure é usado para este passo. |
| `parameters` | [Parâmetros](#parameters) para o oleoduto. |
| `inputs` | As inputs podem ser [InputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding?view=azure-ml-py), [DataReference](#data-reference), [PortDataReference](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference?view=azure-ml-py), [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py), [Dataset](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py), [DatasetDefinition](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition?view=azure-ml-py)ou [PipelineDataset .](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset?view=azure-ml-py) |
| `outputs` | As saídas podem ser [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) ou [OutputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding?view=azure-ml-py). |
| `source_directory` | Diretório que contém o guião, assembléias, etc. |
| `priority` | O valor prioritário a utilizar para o trabalho atual. |
| `params` | Dicionário de pares de valor de nome. |
| `degree_of_parallelism` | O grau de paralelismo a usar para este trabalho. |
| `runtime_version` | A versão em tempo de execução do motor Data Lake Analytics. |
| `allow_reuse` | Determina se o passo deve reutilizar os resultados anteriores quando for executado novamente com as mesmas definições. |

O exemplo que se segue contém uma definição de Passo ADLA:

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

### <a name="azure-batch-step"></a>Passo do Lote Azure

| Chave YAML | Descrição |
| ----- | ----- |
| `compute_target` | O objetivo de computação do Lote Azure para utilizar para este passo. |
| `inputs` | As inputs podem ser [InputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding?view=azure-ml-py), [DataReference](#data-reference), [PortDataReference](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference?view=azure-ml-py), [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py), [Dataset](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py), [DatasetDefinition](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition?view=azure-ml-py)ou [PipelineDataset .](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset?view=azure-ml-py) |
| `outputs` | As saídas podem ser [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) ou [OutputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding?view=azure-ml-py). |
| `source_directory` | Diretório que contém os binários do módulo, executáveis, conjuntos, etc. |
| `executable` | Nome do comando/executável que será executado como parte deste trabalho. |
| `create_pool` | Bandeira booleana para indicar se deve criar a piscina antes de dirigir o trabalho. |
| `delete_batch_job_after_finish` | Bandeira booleana para indicar se deve apagar o trabalho da conta Batch depois de terminado. |
| `delete_batch_pool_after_finish` | Bandeira booleana para indicar se deve apagar a piscina após o trabalho terminar. |
| `is_positive_exit_code_failure` | Bandeira booleana para indicar se o trabalho falhar se a tarefa sair com um código positivo. |
| `vm_image_urn` | Se `create_pool` `True`for, e `VirtualMachineConfiguration`vM usa . |
| `pool_id` | A identificação da piscina onde o trabalho vai funcionar. |
| `allow_reuse` | Determina se o passo deve reutilizar os resultados anteriores quando for executado novamente com as mesmas definições. |

O exemplo que se segue contém uma definição de etapa do Lote Azure:

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

### <a name="databricks-step"></a>Passo de tijolos de dados

| Chave YAML | Descrição |
| ----- | ----- |
| `compute_target` | O objetivo de computação Azure Databricks para este passo. |
| `inputs` | As inputs podem ser [InputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding?view=azure-ml-py), [DataReference](#data-reference), [PortDataReference](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference?view=azure-ml-py), [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py), [Dataset](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py), [DatasetDefinition](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition?view=azure-ml-py)ou [PipelineDataset .](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset?view=azure-ml-py) |
| `outputs` | As saídas podem ser [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) ou [OutputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding?view=azure-ml-py). |
| `run_name` | O nome em Databricks para esta corrida. |
| `source_directory` | Diretório que contém o guião e outros ficheiros. |
| `num_workers` | O número estático de trabalhadores para os Databricks funcionam. |
| `runconfig` | O caminho `.runconfig` para um arquivo. Este ficheiro é uma representação YAML da classe [RunConfiguration.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py) Para obter mais informações sobre a estrutura deste ficheiro, consulte [runconfigschema.json](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json). |
| `allow_reuse` | Determina se o passo deve reutilizar os resultados anteriores quando for executado novamente com as mesmas definições. |

O exemplo que se segue contém um passo de Databricks:

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

### <a name="data-transfer-step"></a>Passo de transferência de dados

| Chave YAML | Descrição |
| ----- | ----- |
| `compute_target` | O objetivo de computação da Azure Data Factory para este passo. |
| `source_data_reference` | Ligação de entrada que serve como fonte de operações de transferência de dados. Os valores suportados são [InputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding?view=azure-ml-py), [DataReference](#data-reference), [PortDataReference](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference?view=azure-ml-py), [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py), [DatasetDefinition,](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py)ou [PipelineDataset .](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset?view=azure-ml-py) [DatasetDefinition](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition?view=azure-ml-py) |
| `destination_data_reference` | Ligação de entrada que serve como destino de operações de transferência de dados. Os valores suportados são [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) e [OutputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding?view=azure-ml-py). |
| `allow_reuse` | Determina se o passo deve reutilizar os resultados anteriores quando for executado novamente com as mesmas definições. |

O exemplo que se segue contém um passo de transferência de dados:

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

### <a name="python-script-step"></a>Passo do roteiro python

| Chave YAML | Descrição |
| ----- | ----- |
| `inputs` | As inputs podem ser [InputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding?view=azure-ml-py), [DataReference](#data-reference), [PortDataReference](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference?view=azure-ml-py), [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py), [Dataset](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py), [DatasetDefinition](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition?view=azure-ml-py)ou [PipelineDataset .](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset?view=azure-ml-py) |
| `outputs` | As saídas podem ser [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) ou [OutputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding?view=azure-ml-py). |
| `script_name` | O nome do guião `source_directory`python (em relação a). |
| `source_directory` | Diretório que contém o guião, ambiente Conda, etc. |
| `runconfig` | O caminho `.runconfig` para um arquivo. Este ficheiro é uma representação YAML da classe [RunConfiguration.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py) Para obter mais informações sobre a estrutura deste ficheiro, consulte [runconfig.json](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json). |
| `allow_reuse` | Determina se o passo deve reutilizar os resultados anteriores quando for executado novamente com as mesmas definições. |

O exemplo seguinte contém um passo de guião python:

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

### <a name="pipeline-with-multiple-steps"></a>Gasoduto com vários passos 

| Chave YAML | Descrição |
| ----- | ----- |
| `steps` | Sequência de uma ou mais definições pipelineStep. Note que `destination` o passo `outputs` de um passo `inputs` tornou-se as chaves da .| 

```yaml
pipeline:
    name: SamplePipelineFromYAML
    description: Sample multistep YAML pipeline
    data_references:
        TitanicDS:
            dataset_name: 'titanic_ds'
            bind_mode: download
    default_compute: cpu-cluster
    steps:
        Dataprep:
            type: "PythonScriptStep"
            name: "DataPrep Step"
            compute: cpu-cluster
            runconfig: ".\\default_runconfig.yml"
            script_name: "prep.py"
            arguments:
            - '--train_path'
            - output:train_path
            - '--test_path'
            - output:test_path
            allow_reuse: True
            inputs:
                titanic_ds:
                    source: TitanicDS
                    bind_mode: download
            outputs:
                train_path:
                    destination: train_csv
                    datastore: workspaceblobstore
                test_path:
                    destination: test_csv
        Training:
            type: "PythonScriptStep"
            name: "Training Step"
            compute: cpu-cluster
            runconfig: ".\\default_runconfig.yml"
            script_name: "train.py"
            arguments:
            - "--train_path"
            - input:train_path
            - "--test_path"
            - input:test_path
            inputs:
                train_path:
                    source: train_csv
                    bind_mode: download
                test_path:
                    source: test_csv
                    bind_mode: download

```

## <a name="schedules"></a>Agendas

Ao definir o horário de um pipeline, pode ser acionado por uma loja de dados ou recorrente com base num intervalo de tempo. Seguem-se as teclas utilizadas para definir um horário:

| Chave YAML | Descrição |
| ----- | ----- |
| `description` | Uma descrição da agenda. |
| `recurrence` | Contém definições de recorrência, se o horário for recorrente. |
| `pipeline_parameters` | Quaisquer parâmetros que sejam exigidos pelo gasoduto. |
| `wait_for_provisioning` | Se esperar pelo fornecimento do horário para completar. |
| `wait_timeout` | O número de segundos para esperar antes de sair. |
| `datastore_name` | A loja de dados para monitorizar as bolhas modificadas/adicionadas. |
| `polling_interval` | Quanto tempo, em minutos, entre as sondagens para bolhas modificadas/adicionadas. Valor predefinido: 5 minutos. Apenas suportado para horários de datastore. |
| `data_path_parameter_name` | O nome do parâmetro do gasoduto da trajetória de dados para definir com o caminho de bolha alterado. Apenas suportado para horários de datastore. |
| `continue_on_step_failure` | Se continuar a executar outras etapas no PipelineRun submetido se um passo falhar. Se fornecido, anulará `continue_on_step_failure` a regulação do gasoduto.
| `path_on_datastore` | Opcional. O caminho na loja de dados para monitorizar as bolhas modificadas/adicionadas. O caminho está sob o recipiente para o datastore, por isso`path_on_datastore`o caminho real que os monitores de horário supor é recipiente/ . Em caso afirmativo, o recipiente da loja de dados é monitorizado. Não são monitorizadas aditamentos/modificações efetuadas numa subpasta da subpasta. `path_on_datastore` Apenas suportado para horários de datastore. |

O exemplo que se segue contém a definição para um horário desencadeado por uma loja de dados:

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

Ao definir um **horário recorrente,** utilize `recurrence`as seguintes teclas em :

| Chave YAML | Descrição |
| ----- | ----- |
| `frequency` | Quantas vezes o horário repete-se. Os valores `"Hour"` `"Day"`válidos `"Month"`são, `"Minute"`, ou `"Week"`. |
| `interval` | Quantas vezes o horário dispara. O valor inteiro é o número de unidades de tempo para esperar até que o horário volte a disparar. |
| `start_time` | A hora de início da agenda. O formato de `YYYY-MM-DDThh:mm:ss`cadeia do valor é . Se não for fornecida a hora de início, a primeira carga de trabalho é executada instantaneamente e futuras cargas de trabalho são executadas com base no horário. Se a hora de início for no passado, a primeira carga de trabalho é executada no próximo tempo de execução calculado. |
| `time_zone` | O fuso horário para a hora de início. Se não for fornecido um fuso horário, a UTC é utilizada. |
| `hours` | Se `frequency` `"Day"` for `"Week"`ou , pode especificar um ou mais inteiros de 0 a 23, separados por vírgulas, como as horas do dia em que o gasoduto deve funcionar. `time_of_day` Apenas `hours` `minutes` ou e pode ser usado. |
| `minutes` | Se `frequency` `"Day"` for `"Week"`ou , pode especificar um ou mais inteiros de 0 a 59, separados por vírgulas, como os minutos da hora em que o gasoduto deve funcionar. `time_of_day` Apenas `hours` `minutes` ou e pode ser usado. |
| `time_of_day` | Se `frequency` `"Day"` for `"Week"`ou , pode especificar uma hora do dia para o horário de execução. O formato de `hh:mm`cadeia do valor é . `time_of_day` Apenas `hours` `minutes` ou e pode ser usado. |
| `week_days` | Se `frequency` `"Week"`for, pode especificar um ou mais dias, separados por vírgulas, quando o horário deve ser executado. Os valores `"Tuesday"` `"Wednesday"`válidos `"Saturday"`são, `"Sunday"` `"Monday"`, `"Thursday"`, `"Friday"`, e . |

O exemplo que se segue contém a definição para um calendário recorrente:

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

Aprenda a [utilizar a extensão CLI para o Azure Machine Learning](reference-azure-machine-learning-cli.md).
