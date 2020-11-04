---
title: Pipeline de aprendizagem automática YAML
titleSuffix: Azure Machine Learning
description: Aprenda a definir um gasoduto de aprendizagem automática utilizando um ficheiro YAML. As definições do gasoduto YAML são utilizadas com a extensão de aprendizagem automática para o Azure CLI.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.reviewer: larryfr
ms.author: nilsp
author: NilsPohlmann
ms.date: 07/31/2020
ms.custom: devx-track-python
ms.openlocfilehash: bfeab990c841f6b65e665b4a8aabdfd8b251da60
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93323905"
---
# <a name="define-machine-learning-pipelines-in-yaml"></a>Definir oleodutos de aprendizagem automática em YAML

Saiba como definir os seus oleodutos de aprendizagem automática em [YAML](https://yaml.org/). Ao utilizar a extensão de aprendizagem automática para o CLI Azure, muitos dos comandos relacionados com o gasoduto esperam um ficheiro YAML que define o gasoduto.

A tabela que se segue lista o que é e não é atualmente suportado na definição de um gasoduto em YAML:

| Tipo de passo | Suportada? |
| ----- | :-----: |
| PythonScriptStep | Yes |
| ParallelRunStep | Yes |
| AdlaStep | Yes |
| AzureBatchStep | Yes |
| DatabricksStep | Yes |
| DataTransferStep | Yes |
| AutoMLStep | No |
| HiperDriveStep | No |
| MóduloStep | Yes |
| MPIStep | No |
| EstimativaStep | No |

## <a name="pipeline-definition"></a>Definição de gasoduto

Uma definição de gasoduto utiliza as seguintes teclas, que correspondem à classe [Pipelines:](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline.pipeline?preserve-view=true&view=azure-ml-py)

| Chave YAML | Description |
| ----- | ----- |
| `name` | A descrição do oleoduto. |
| `parameters` | Parâmetros para o oleoduto. |
| `data_reference` | Define como e onde os dados devem ser disponibilizados numa corrida. |
| `default_compute` | Meta de cálculo padrão onde todos os passos no gasoduto funcionam. |
| `steps` | Os passos usados no oleoduto. |

## <a name="parameters"></a>Parâmetros

A `parameters` secção utiliza as seguintes teclas, que correspondem à classe [PipelineParameter:](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelineparameter?preserve-view=true&view=azure-ml-py)

| Chave YAML | Description |
| ---- | ---- |
| `type` | O tipo de valor do parâmetro. Os tipos válidos `string` `int` são, `float` , , ou `bool` `datapath` . |
| `default` | O valor predefinido. |

Cada parâmetro é nomeado. Por exemplo, o seguinte snippet YAML define três parâmetros denominados `NumIterationsParameter` `DataPathParameter` , e `NodeCountParameter` :

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

A `data_references` secção utiliza as seguintes teclas, que correspondem à Referência de [Dados:](/python/api/azureml-core/azureml.data.data_reference.datareference?preserve-view=true&view=azure-ml-py)

| Chave YAML | Description |
| ----- | ----- |
| `datastore` | A loja de dados a referência. |
| `path_on_datastore` | O caminho relativo no armazenamento de suporte para a referência de dados. |

Cada referência de dados está contida numa chave. Por exemplo, o seguinte snippet YAML define uma referência de dados armazenada na chave denominada `employee_data` :

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

Os passos definem um ambiente computacional, juntamente com os ficheiros a executar no ambiente. Para definir o tipo de passo, use a `type` chave:

| Tipo de passo | Description |
| ----- | ----- |
| `AdlaStep` | Executa um script U-SQL com Azure Data Lake Analytics. Corresponde à classe [AdlaStep.](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.adlastep?preserve-view=true&view=azure-ml-py) |
| `AzureBatchStep` | Executa trabalhos usando Azure Batch. Corresponde à classe [AzureBatchStep.](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.azurebatchstep?preserve-view=true&view=azure-ml-py) |
| `DatabricsStep` | Adiciona um caderno Databricks, script Python ou JAR. Corresponde à aula [databricksStep.](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricksstep?preserve-view=true&view=azure-ml-py) |
| `DataTransferStep` | Transfere dados entre opções de armazenamento. Corresponde à classe [DataTransferStep.](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep?preserve-view=true&view=azure-ml-py) |
| `PythonScriptStep` | Tem um guião python. Corresponde à classe [PythonScriptStep.](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?preserve-view=true&view=azure-ml-py) |
| `ParallelRunStep` | Executa um script Python para processar grandes quantidades de dados assíncronosamente e em paralelo. Corresponde à classe [ParallelRunStep.](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.parallel_run_step.parallelrunstep?preserve-view=true&view=azure-ml-py) |

### <a name="adla-step"></a>Passo ADLA

| Chave YAML | Description |
| ----- | ----- |
| `script_name` | O nome do script U-SQL (relativamente ao `source_directory` ). |
| `compute_target` | O alvo de computação Azure Data Lake para usar para este passo. |
| `parameters` | [Parâmetros](#parameters) para o oleoduto. |
| `inputs` | As entradas podem ser [InputPortBinding](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding?preserve-view=true&view=azure-ml-py), [DataReference](#data-reference), [PortDataReference](/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference?preserve-view=true&view=azure-ml-py), [PipelineData,](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?preserve-view=true&view=azure-ml-py) [Dataset,](/python/api/azureml-core/azureml.core.dataset%28class%29?preserve-view=true&view=azure-ml-py) [DatasetDefinition](/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition?preserve-view=true&view=azure-ml-py)ou [PipelineDataset](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset?preserve-view=true&view=azure-ml-py). |
| `outputs` | As saídas podem ser [PipelineData](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?preserve-view=true&view=azure-ml-py) ou [OutputPortBinding](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding?preserve-view=true&view=azure-ml-py). |
| `source_directory` | Diretório que contém o guião, conjuntos, etc. |
| `priority` | O valor prioritário a ser usado para o trabalho atual. |
| `params` | Dicionário de pares de valor-nome. |
| `degree_of_parallelism` | O grau de paralelismo a ser usado para este trabalho. |
| `runtime_version` | A versão de tempo de execução do motor Data Lake Analytics. |
| `allow_reuse` | Determina se o passo deve reutilizar os resultados anteriores quando for executado novamente com as mesmas definições. |

O exemplo a seguir contém uma definição de passo ADLA:

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

| Chave YAML | Description |
| ----- | ----- |
| `compute_target` | O alvo de computação Azure Batch a ser utilizado para este passo. |
| `inputs` | As entradas podem ser [InputPortBinding](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding?preserve-view=true&view=azure-ml-py), [DataReference](#data-reference), [PortDataReference](/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference?preserve-view=true&view=azure-ml-py), [PipelineData,](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?preserve-view=true&view=azure-ml-py) [Dataset,](/python/api/azureml-core/azureml.core.dataset%28class%29?preserve-view=true&view=azure-ml-py) [DatasetDefinition](/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition?preserve-view=true&view=azure-ml-py)ou [PipelineDataset](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset?preserve-view=true&view=azure-ml-py). |
| `outputs` | As saídas podem ser [PipelineData](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?preserve-view=true&view=azure-ml-py) ou [OutputPortBinding](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding?preserve-view=true&view=azure-ml-py). |
| `source_directory` | Diretório que contém os binários do módulo, executáveis, conjuntos, etc. |
| `executable` | Nome do comando/executável que será executado como parte deste trabalho. |
| `create_pool` | Bandeira booleana para indicar se deve criar a piscina antes de executar o trabalho. |
| `delete_batch_job_after_finish` | Bandeira booleana para indicar se apagar o trabalho da conta batch depois de terminado. |
| `delete_batch_pool_after_finish` | Bandeira booleana para indicar se apagar a piscina após o fim do trabalho. |
| `is_positive_exit_code_failure` | Bandeira booleana para indicar se o trabalho falha se a tarefa sair com um código positivo. |
| `vm_image_urn` | Se `create_pool` for , e `True` vm usa `VirtualMachineConfiguration` . |
| `pool_id` | A identificação da piscina onde o trabalho vai funcionar. |
| `allow_reuse` | Determina se o passo deve reutilizar os resultados anteriores quando for executado novamente com as mesmas definições. |

O exemplo a seguir contém uma definição de passo do Azure Batch:

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

### <a name="databricks-step"></a>Passo databricks

| Chave YAML | Description |
| ----- | ----- |
| `compute_target` | O alvo de computação Azure Databricks para usar para este passo. |
| `inputs` | As entradas podem ser [InputPortBinding](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding?preserve-view=true&view=azure-ml-py), [DataReference](#data-reference), [PortDataReference](/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference?preserve-view=true&view=azure-ml-py), [PipelineData,](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?preserve-view=true&view=azure-ml-py) [Dataset,](/python/api/azureml-core/azureml.core.dataset%28class%29?preserve-view=true&view=azure-ml-py) [DatasetDefinition](/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition?preserve-view=true&view=azure-ml-py)ou [PipelineDataset](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset?preserve-view=true&view=azure-ml-py). |
| `outputs` | As saídas podem ser [PipelineData](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?preserve-view=true&view=azure-ml-py) ou [OutputPortBinding](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding?preserve-view=true&view=azure-ml-py). |
| `run_name` | O nome em Databricks para esta corrida. |
| `source_directory` | Diretório que contém o script e outros ficheiros. |
| `num_workers` | O número estático de trabalhadores para o agrupamento de databricks. |
| `runconfig` | O caminho para um `.runconfig` arquivo. Este ficheiro é uma representação YAML da classe [RunConfiguration.](/python/api/azureml-core/azureml.core.runconfiguration?preserve-view=true&view=azure-ml-py) Para obter mais informações sobre a estrutura deste ficheiro, consulte [runconfigschema.jsem](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json). |
| `allow_reuse` | Determina se o passo deve reutilizar os resultados anteriores quando for executado novamente com as mesmas definições. |

O exemplo a seguir contém um passo databricks:

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

| Chave YAML | Description |
| ----- | ----- |
| `compute_target` | O alvo de computação Azure Data Factory a ser utilizado para este passo. |
| `source_data_reference` | Ligação de entrada que serve de fonte de operações de transferência de dados. Os valores suportados são [InputPortBinding](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding?preserve-view=true&view=azure-ml-py), [DataReference](#data-reference), [PortDataReference](/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference?preserve-view=true&view=azure-ml-py), [PipelineData,](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?preserve-view=true&view=azure-ml-py) [Dataset](/python/api/azureml-core/azureml.core.dataset%28class%29?preserve-view=true&view=azure-ml-py), [DatasetDefinition](/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition?preserve-view=true&view=azure-ml-py)ou [PipelineDataset](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset?preserve-view=true&view=azure-ml-py). |
| `destination_data_reference` | Ligação de entrada que serve como destino das operações de transferência de dados. Os valores suportados são [PipelineData](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?preserve-view=true&view=azure-ml-py) e [OutputPortBinding](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding?preserve-view=true&view=azure-ml-py). |
| `allow_reuse` | Determina se o passo deve reutilizar os resultados anteriores quando for executado novamente com as mesmas definições. |

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

### <a name="python-script-step"></a>Passo de script python

| Chave YAML | Description |
| ----- | ----- |
| `inputs` | As entradas podem ser [InputPortBinding](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding?preserve-view=true&view=azure-ml-py), [DataReference](#data-reference), [PortDataReference](/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference?preserve-view=true&view=azure-ml-py), [PipelineData,](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?preserve-view=true&view=azure-ml-py) [Dataset,](/python/api/azureml-core/azureml.core.dataset%28class%29?preserve-view=true&view=azure-ml-py) [DatasetDefinition](/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition?preserve-view=true&view=azure-ml-py)ou [PipelineDataset](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset?preserve-view=true&view=azure-ml-py). |
| `outputs` | As saídas podem ser [PipelineData](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?preserve-view=true&view=azure-ml-py) ou [OutputPortBinding](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding?preserve-view=true&view=azure-ml-py). |
| `script_name` | O nome da escrita Python (relativa a `source_directory` ). |
| `source_directory` | Diretório que contém o guião, ambiente Conda, etc. |
| `runconfig` | O caminho para um `.runconfig` arquivo. Este ficheiro é uma representação YAML da classe [RunConfiguration.](/python/api/azureml-core/azureml.core.runconfiguration?preserve-view=true&view=azure-ml-py) Para obter mais informações sobre a estrutura deste ficheiro, consulte [runconfig.jsem](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json). |
| `allow_reuse` | Determina se o passo deve reutilizar os resultados anteriores quando for executado novamente com as mesmas definições. |

O exemplo a seguir contém um passo de script Python:

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

### <a name="parallel-run-step"></a>Passo de corrida paralelo

| Chave YAML | Description |
| ----- | ----- |
| `inputs` | As entradas podem ser [dataset](/python/api/azureml-core/azureml.core.dataset%28class%29?preserve-view=true&view=azure-ml-py), [DatasetDefinition,](/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition?preserve-view=true&view=azure-ml-py)ou [PipelineDataset](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset?preserve-view=true&view=azure-ml-py). |
| `outputs` | As saídas podem ser [PipelineData](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?preserve-view=true&view=azure-ml-py) ou [OutputPortBinding](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding?preserve-view=true&view=azure-ml-py). |
| `script_name` | O nome da escrita Python (relativa a `source_directory` ). |
| `source_directory` | Diretório que contém o guião, ambiente Conda, etc. |
| `parallel_run_config` | O caminho para um `parallel_run_config.yml` arquivo. Este ficheiro é uma representação YAML da classe [ParallelRunConfig.](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.parallelrunconfig?preserve-view=true&view=azure-ml-py) |
| `allow_reuse` | Determina se o passo deve reutilizar os resultados anteriores quando for executado novamente com as mesmas definições. |

O exemplo a seguir contém um passo de execução paralelo:

```yaml
pipeline:
    description: SamplePipelineFromYaml
    default_compute: cpu-cluster
    data_references:
        MyMinistInput:
            dataset_name: mnist_sample_data
    parameters:
        PipelineParamTimeout:
            type: int
            default: 600
    steps:        
        Step1:
            parallel_run_config: "yaml/parallel_run_config.yml"
            type: "ParallelRunStep"
            name: "parallel-run-step-1"
            allow_reuse: True
            arguments:
            - "--progress_update_timeout"
            - parameter:timeout_parameter
            - "--side_input"
            - side_input:SideInputData
            parameters:
                timeout_parameter:
                    source: PipelineParamTimeout
            inputs:
                InputData:
                    source: MyMinistInput
            side_inputs:
                SideInputData:
                    source: Output4
                    bind_mode: mount
            outputs:
                OutputDataStep2:
                    destination: Output5
                    datastore: workspaceblobstore
                    bind_mode: mount
```

### <a name="pipeline-with-multiple-steps"></a>Pipeline com vários passos 

| Chave YAML | Description |
| ----- | ----- |
| `steps` | Sequência de uma ou mais definições pipelineStep. Note que as `destination` chaves de um passo `outputs` tornaram-se as `source` chaves do próximo `inputs` passo.| 

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

Ao definir o calendário de um oleoduto, pode ser acionado por datas-loja ou recorrente com base num intervalo de tempo. Seguem-se as chaves utilizadas para definir um horário:

| Chave YAML | Description |
| ----- | ----- |
| `description` | Uma descrição da agenda. |
| `recurrence` | Contém definições de recorrência, se o horário for recorrente. |
| `pipeline_parameters` | Quaisquer parâmetros que sejam exigidos pelo gasoduto. |
| `wait_for_provisioning` | Se esperar que o provisionamento do horário esteja concluído. |
| `wait_timeout` | O número de segundos para esperar antes do tempo de saída. |
| `datastore_name` | A loja de dados para monitorizar para bolhas modificadas/adicionadas. |
| `polling_interval` | Quanto tempo, em minutos, entre sondagens para bolhas modificadas/adicionadas. Valor predefinido: 5 minutos. Suportado apenas para horários de datastore. |
| `data_path_parameter_name` | O nome do parâmetro do pipeline do caminho de dados para definir com o caminho do blob alterado. Suportado apenas para horários de datastore. |
| `continue_on_step_failure` | Se continuar a execução de outras etapas no PipelineRun apresentado se um passo falhar. Se for fornecida, anulará a `continue_on_step_failure` regulação do gasoduto.
| `path_on_datastore` | Opcional. O caminho na loja de dados para monitorizar para bolhas modificadas/adicionadas. O caminho está debaixo do contentor para a datastore, pelo que o caminho real que o horário monitoriza é o contentor/ `path_on_datastore` . Se nenhum, o recipiente da loja de dados é monitorizado. As adições/modificações efetuadas numa sub-dobradeira `path_on_datastore` não são monitorizadas. Suportado apenas para horários de datastore. |

O exemplo a seguir contém a definição para um calendário desencadeado por datastore:

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

Ao definir um **horário recorrente,** utilize as seguintes teclas em `recurrence` :

| Chave YAML | Description |
| ----- | ----- |
| `frequency` | Quantas vezes o horário repete-se. Valores válidos `"Minute"` são, `"Hour"` , , , `"Day"` ou `"Week"` `"Month"` . |
| `interval` | Quantas vezes o horário dispara. O valor inteiro é o número de unidades de tempo para esperar até que o horário volte a disparar. |
| `start_time` | A hora de início da agenda. O formato de corda do valor é `YYYY-MM-DDThh:mm:ss` . Se não for fornecida a hora de início, a primeira carga de trabalho é executada instantaneamente e as cargas de trabalho futuras são executadas com base no horário. Se a hora de início for no passado, a primeira carga de trabalho é executada no próximo tempo calculado de funcionação. |
| `time_zone` | O fuso horário para a hora de início. Se não for fornecido um fuso horário, o UTC é utilizado. |
| `hours` | Se `frequency` for `"Day"` `"Week"` ou, pode especificar um ou mais inteiros de 0 a 23, separados por vírgulas, como as horas do dia em que o gasoduto deve funcionar. Apenas `time_of_day` ou e pode ser `hours` `minutes` usado. |
| `minutes` | Se `frequency` for `"Day"` `"Week"` ou, pode especificar um ou mais inteiros de 0 a 59, separados por vírgulas, como os minutos da hora em que o gasoduto deve funcionar. Apenas `time_of_day` ou e pode ser `hours` `minutes` usado. |
| `time_of_day` | Se `frequency` for `"Day"` `"Week"` ou, pode especificar uma hora do dia para o horário de funcionamento. O formato de corda do valor é `hh:mm` . Apenas `time_of_day` ou e pode ser `hours` `minutes` usado. |
| `week_days` | Se `frequency` `"Week"` for, pode especificar um ou mais dias, separados por vírgulas, quando o horário deve ser executado. Valores válidos `"Monday"` são, `"Tuesday"` , , , , , e `"Wednesday"` `"Thursday"` `"Friday"` `"Saturday"` `"Sunday"` . |

O exemplo a seguir contém a definição para uma programação recorrente:

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

Saiba como [utilizar a extensão CLI para a aprendizagem automática Azure.](reference-azure-machine-learning-cli.md)