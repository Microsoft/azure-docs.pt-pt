---
title: Criar e executar gasodutos ML
titleSuffix: Azure Machine Learning
description: Crie e execute gasodutos de aprendizagem automática para criar e gerir os fluxos de trabalho que cosem fases de aprendizagem automática (ML).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: sgilley
ms.author: nilsp
author: NilsPohlmann
ms.date: 10/21/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, contperfq1
ms.openlocfilehash: 57806ecaf4b0e295457c78faaff6033126ddb2c8
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96463021"
---
# <a name="create-and-run-machine-learning-pipelines-with-azure-machine-learning-sdk"></a>Criar e executar gasodutos de aprendizagem automática com Azure Machine Learning SDK



Neste artigo, aprende-se a criar e executar um [oleoduto de aprendizagem automática](concept-ml-pipelines.md) utilizando o [Azure Machine Learning SDK](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py). Utilize **gasodutos ML** para criar um fluxo de trabalho que cosa várias fases ML. Em seguida, publique o oleoduto para mais tarde aceder ou partilhar com outros. Rastreia os oleodutos ML para ver como o seu modelo está a funcionar no mundo real e para detetar a deriva de dados. Os oleodutos ML são ideais para cenários de pontuação de lotes, utilizando vários cálculos, reutilizando passos em vez de os reexame, bem como partilhando fluxos de trabalho ML com outros.

Embora possa utilizar um tipo diferente de oleoduto chamado [Gasoduto Azure](/azure/devops/pipelines/targets/azure-machine-learning?context=azure%2fmachine-learning%2fservice%2fcontext%2fml-context&preserve-view=true&tabs=yaml&view=azure-devops) para automatização de CI/CD de tarefas ML, esse tipo de gasoduto não é armazenado no seu espaço de trabalho. [Compare estes diferentes oleodutos.](concept-ml-pipelines.md#which-azure-pipeline-technology-should-i-use)

Os gasodutos ML que cria são visíveis para os membros do seu espaço de [trabalho](how-to-manage-workspace.md)Azure Machine Learning . 

Os gasodutos ML executam em alvos de computação (ver [quais são os alvos de cálculo em Azure Machine Learning).](./concept-compute-target.md) Os oleodutos podem ler e escrever dados de e para locais de armazenamento suportados do [Azure.](../storage/index.yml)

Se não tiver uma subscrição do Azure, crie uma conta gratuita antes de começar. Experimente a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree).

## <a name="prerequisites"></a>Pré-requisitos

* Crie um espaço de [trabalho para aprendizagem automática Azure](how-to-manage-workspace.md) para manter todos os seus recursos de pipeline.

* [Configure o seu ambiente de desenvolvimento](how-to-configure-environment.md) para instalar o Azure Machine Learning SDK, ou use uma instância de cálculo [Azure Machine Learning](concept-compute-instance.md) com o SDK já instalado.

Comece por anexar o seu espaço de trabalho:

```Python
import azureml.core
from azureml.core import Workspace, Datastore

ws = Workspace.from_config()
```

## <a name="set-up-machine-learning-resources"></a>Criar recursos de aprendizagem automática

Criar os recursos necessários para executar um gasoduto ML:

* Crie uma datastore utilizada para aceder aos dados necessários nas etapas do gasoduto.

* Configure um `Dataset` objeto para apontar para dados persistentes que vivem ou estão acessíveis numa datastore. Configure um `PipelineData` objeto para dados temporários passados entre as etapas do gasoduto. 

    > [!TIP]
    > Uma experiência melhorada para a passagem de dados temporários entre etapas de gasoduto está disponível na classe de pré-visualização pública,  [`OutputFileDatasetConfig`](/python/api/azureml-core/azureml.data.outputfiledatasetconfig?preserve-view=true&view=azure-ml-py) .  Esta aula é uma funcionalidade de pré-visualização [experimental,](/python/api/overview/azure/ml/?preserve-view=true&view=azure-ml-py#&preserve-view=truestable-vs-experimental) e pode mudar a qualquer momento.

* Desaponte os [alvos de cálculo](concept-azure-machine-learning-architecture.md#compute-targets) nos quais os seus passos de gasoduto irão funcionar.

### <a name="set-up-a-datastore"></a>Criar uma data-tore

Uma loja de dados armazena os dados para o pipeline aceder. Cada espaço de trabalho tem uma datastore padrão. Pode registar datas mais. 

Quando cria o seu espaço de trabalho, os [Ficheiros Azure](../storage/files/storage-files-introduction.md) e [o armazenamento Azure Blob](../storage/blobs/storage-blobs-introduction.md) estão ligados ao espaço de trabalho. Está registada uma loja de dados predefinida para ligar ao armazenamento da Azure Blob. Para saber mais, consulte [decidir quando utilizar ficheiros Azure, Blobs Azure ou Discos Azure](../storage/common/storage-introduction.md). 

```python
# Default datastore 
def_data_store = ws.get_default_datastore()

# Get the blob storage associated with the workspace
def_blob_store = Datastore(ws, "workspaceblobstore")

# Get file storage associated with the workspace
def_file_store = Datastore(ws, "workspacefilestore")

```

As etapas geralmente consomem dados e produzem dados de produção. Um passo pode criar dados como um modelo, um diretório com modelos e ficheiros dependentes, ou dados temporários. Estes dados estão então disponíveis para outros passos mais tarde no oleoduto. Para saber mais sobre a ligação do seu pipeline aos seus dados, consulte os artigos [Como Aceder aos Dados](how-to-access-data.md) e [Como Registar Conjuntos de Dados.](how-to-create-register-datasets.md) 

### <a name="configure-data-with-dataset-and-pipelinedata-objects"></a>Configurar dados com `Dataset` e `PipelineData` objetos

A forma preferida de fornecer dados a um pipeline é um objeto [dataset.](/python/api/azureml-core/azureml.core.dataset.Dataset) O `Dataset` objeto aponta para dados que vivem dentro ou estão acessíveis a partir de uma datastore ou em um URL web. A `Dataset` classe é abstrata, por isso irá criar um exemplo de um `FileDataset` (referindo-se a um ou mais ficheiros) ou de um `TabularDataset` que é criado por um ou mais ficheiros com colunas de dados delimitadas.


Cria-se um `Dataset` método de utilização como [from_files](/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?preserve-view=true&view=azure-ml-py#&preserve-view=truefrom-files-path--validate-true-) ou [from_delimited_files](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?preserve-view=true&view=azure-ml-py#&preserve-view=truefrom-delimited-files-path--validate-true--include-path-false--infer-column-types-true--set-column-types-none--separator------header-true--partition-format-none--support-multi-line-false-).

```python
from azureml.core import Dataset

my_dataset = Dataset.File.from_files([(def_blob_store, 'train-images/')])
```
Os dados intermédios (ou saída de um passo) são representados por um objeto [PipelineData.](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?preserve-view=true&view=azure-ml-py) `output_data1` é produzido como a saída de um passo, e usado como a entrada de um ou mais passos futuros. `PipelineData` introduz uma dependência de dados entre passos, e cria uma ordem de execução implícita no oleoduto. Este objeto será utilizado mais tarde ao criar passos de gasoduto.

```python
from azureml.pipeline.core import PipelineData

output_data1 = PipelineData(
    "output_data1",
    datastore=def_blob_store,
    output_name="output_data1")

```

> [!TIP]
> Os dados intermédios persistentes entre as etapas do gasoduto também são possíveis com a classe de pré-visualização pública, [`OutputFileDatasetConfig`](/python/api/azureml-core/azureml.data.outputfiledatasetconfig?preserve-view=true&view=azure-ml-py) . Para um exemplo de código utilizando a `OutputFileDatasetConfig` classe, consulte como [construir um gasoduto ML](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/pipeline-with-datasets/pipeline-for-image-classification.ipynb)de dois passos .

## <a name="set-up-a-compute-target"></a>Configurar um alvo de computação


No Azure Machine Learning, o termo __compute__ (ou __meta de computação)__ refere-se às máquinas ou clusters que realizam os passos computacionais no seu pipeline de aprendizagem automática.   Consulte [metas de cálculo para formação](concept-compute-target.md#train) de modelos para uma lista completa de alvos de computação e Crie metas de [computação](how-to-create-attach-compute-studio.md) para como criá-los e anexá-los ao seu espaço de trabalho.   O processo de criação e ou fixação de um alvo de computação é o mesmo, quer esteja a treinar um modelo ou a executar um passo de pipeline. Depois de criar e fixar o seu alvo de cálculo, utilize o `ComputeTarget` objeto no seu passo de [pipeline](#steps).

> [!IMPORTANT]
> A realização de operações de gestão em objetivos de computação não é suportada a partir de trabalhos remotos internos. Uma vez que os gasodutos de aprendizagem automática são submetidos como um trabalho remoto, não utilize operações de gestão em alvos de computação a partir do interior do oleoduto.

### <a name="azure-machine-learning-compute"></a>Azure Machine Learning compute

Pode criar um computamento Azure Machine Learning para executar os seus passos. O código para outros alvos de computação é muito semelhante, com parâmetros ligeiramente diferentes, dependendo do tipo. 

```python
from azureml.core.compute import ComputeTarget, AmlCompute

compute_name = "aml-compute"
vm_size = "STANDARD_NC6"
if compute_name in ws.compute_targets:
    compute_target = ws.compute_targets[compute_name]
    if compute_target and type(compute_target) is AmlCompute:
        print('Found compute target: ' + compute_name)
else:
    print('Creating a new compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(vm_size=vm_size,  # STANDARD_NC6 is GPU-enabled
                                                                min_nodes=0,
                                                                max_nodes=4)
    # create the compute target
    compute_target = ComputeTarget.create(
        ws, compute_name, provisioning_config)

    # Can poll for a minimum number of nodes and for a specific timeout.
    # If no min node count is provided it will use the scale settings for the cluster
    compute_target.wait_for_completion(
        show_output=True, min_node_count=None, timeout_in_minutes=20)

    # For a more detailed view of current cluster status, use the 'status' property
    print(compute_target.status.serialize())
```

## <a name="configure-the-training-runs-environment"></a>Configure o ambiente do treino

O próximo passo é garantir que o treino remoto tenha todas as dependências necessárias pelas etapas de treino. As dependências e o contexto de tempo de execução são definidos através da criação e configuração de um `RunConfiguration` objeto. 

```python
from azureml.core.runconfig import RunConfiguration
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core import Environment 

aml_run_config = RunConfiguration()
# `compute_target` as defined in "Azure Machine Learning compute" section above
aml_run_config.target = compute_target

USE_CURATED_ENV = True
if USE_CURATED_ENV :
    curated_environment = Environment.get(workspace=ws, name="AzureML-Tutorial")
    aml_run_config.environment = curated_environment
else:
    aml_run_config.environment.python.user_managed_dependencies = False
    
    # Add some packages relied on by data prep step
    aml_run_config.environment.python.conda_dependencies = CondaDependencies.create(
        conda_packages=['pandas','scikit-learn'], 
        pip_packages=['azureml-sdk', 'azureml-dataprep[fuse,pandas]'], 
        pin_sdk_version=False)
```

O código acima mostra duas opções para lidar com dependências. Como apresentado, `USE_CURATED_ENV = True` com, a configuração baseia-se num ambiente curado. Os ambientes curados são "pré-preparados" com bibliotecas interdependentes comuns e podem ser significativamente mais rápidos de colocar online. Ambientes curados têm imagens estivais pré-construídas no [Registo de Contentores da Microsoft.](https://hub.docker.com/publishers/microsoftowner) Para mais informações, consulte [ambientes curados da Azure Machine Learning.](resource-curated-environments.md)

O caminho tomado se mudar `USE_CURATED_ENV` para `False` mostrar o padrão para definir explicitamente as suas dependências. Nesse cenário, será criada e registada uma nova imagem personalizada do Docker num Registo de Contentores Azure dentro do seu grupo de recursos (ver [Introdução aos registos privados de contentores Docker em Azure).](../container-registry/container-registry-intro.md) Construir e registar esta imagem pode demorar alguns minutos.

## <a name="construct-your-pipeline-steps"></a><a id="steps"></a>Construa os seus passos de oleoduto

Assim que tiver o recurso compute e o ambiente criados, está pronto para definir os passos do seu pipeline. Existem muitos passos incorporados disponíveis através do Azure Machine Learning SDK, como pode ver na [documentação de referência do `azureml.pipeline.steps` pacote.](/python/api/azureml-pipeline-steps/azureml.pipeline.steps?preserve-view=true&view=azure-ml-py) A classe mais flexível é [PythonScriptStep,](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?preserve-view=true&view=azure-ml-py)que executa um script Python.

```python
from azureml.pipeline.steps import PythonScriptStep
dataprep_source_dir = "./dataprep_src"
entry_point = "prepare.py"
# `my_dataset` as defined above
ds_input = my_dataset.as_named_input('input1')

# `output_data1`, `compute_target`, `aml_run_config` as defined above
data_prep_step = PythonScriptStep(
    script_name=entry_point,
    source_directory=dataprep_source_dir,
    arguments=["--input", ds_input.as_download(), "--output", output_data1],
    inputs=[ds_input],
    outputs=[output_data1],
    compute_target=compute_target,
    runconfig=aml_run_config,
    allow_reuse=True
)
```

O código acima mostra um passo típico do gasoduto inicial. O seu código de preparação de dados está numa subdiretório (neste exemplo, `"prepare.py"` no `"./dataprep.src"` diretório). Como parte do processo de criação do gasoduto, este diretório é fechado e carregado para o `compute_target` e o passo executa o script especificado como o valor para `script_name` .

Os `arguments` `inputs` valores , e `outputs` valores especificam as entradas e saídas do passo. No exemplo acima, os dados de base são o `my_dataset` conjunto de dados. Os dados correspondentes serão descarregados para o recurso computacional uma vez que o código os especifica como `as_download()` . O script `prepare.py` faz quaisquer tarefas de transformação de dados adequadas à tarefa em questão e produz os dados para `output_data1` , de tipo `PipelineData` . Para obter mais informações, consulte [a deslocação de dados para e entre os passos do gasoduto ML (Python)](how-to-move-data-in-out-of-pipelines.md). 

O passo será executado na máquina definida `compute_target` por, utilizando a configuração `aml_run_config` . 

A reutilização dos resultados anteriores `allow_reuse` () é fundamental quando se utilizam gasodutos num ambiente colaborativo, uma vez que eliminar repetições desnecessárias oferece agilidade. A reutilização é o comportamento padrão quando o script_name, as entradas e os parâmetros de um passo permanecem os mesmos. Quando a reutilização é permitida, os resultados da execução anterior são imediatamente enviados para o passo seguinte. Se `allow_reuse` estiver programado para , uma nova `False` execução será sempre gerada para este passo durante a execução do gasoduto.

É possível criar um oleoduto com um único passo, mas quase sempre escolherá dividir o seu processo global em vários passos. Por exemplo, pode ter passos para a preparação de dados, treino, comparação de modelos e implantação. Por exemplo, podemos imaginar que depois do `data_prep_step` acima especificado, o próximo passo pode ser o treino:

```python
train_source_dir = "./train_src"
train_entry_point = "train.py"

training_results = PipelineData(name = "training_results", 
                                datastore=def_blob_store,
                                output_name="training_results")

    
train_step = PythonScriptStep(
    script_name=train_entry_point,
    source_directory=train_source_dir,
    arguments=["--prepped_data", output_data1.as_input(), "--training_results", training_results],
    compute_target=compute_target,
    runconfig=aml_run_config,
    allow_reuse=True
)
```

O código acima é muito semelhante ao da etapa de preparação de dados. O código de formação encontra-se num diretório separado do código de preparação de dados. A `PipelineData` saída do passo de preparação de dados, é usada como `output_data1` _entrada_ para o passo de treino. Um novo `PipelineData` objeto é criado para manter os `training_results` resultados para uma comparação subsequente ou passo de implementação. 


> [!TIP]
> Para uma experiência melhorada e a capacidade de escrever dados intermédios nas suas datastores no final do seu percurso de pipeline, utilize a classe de pré-visualização pública, [`OutputFileDatasetConfig`](/python/api/azureml-core/azureml.data.outputfiledatasetconfig?preserve-view=true&view=azure-ml-py) . Para exemplos de código, consulte como [construir um oleoduto ML](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/pipeline-with-datasets/pipeline-for-image-classification.ipynb) de dois passos e como escrever [dados de volta às datastores após a conclusão do prazo](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/scriptrun-with-data-input-output/how-to-use-scriptrun.ipynb).

Depois de definir os seus passos, constrói o oleoduto utilizando alguns ou todos esses passos.

> [!NOTE]
> Nenhum ficheiro ou dados é enviado para a Azure Machine Learning quando define os passos ou constrói o pipeline. Os ficheiros são carregados quando liga para [Experiment.submit()](/python/api/azureml-core/azureml.core.experiment.experiment?preserve-view=true&view=azure-ml-py#&preserve-view=truesubmit-config--tags-none----kwargs-).

```python
# list of steps to run (`compare_step` definition not shown)
compare_models = [data_prep_step, train_step, compare_step]

from azureml.pipeline.core import Pipeline

# Build the pipeline
pipeline1 = Pipeline(workspace=ws, steps=[compare_models])
```

### <a name="how-python-environments-work-with-pipeline-parameters"></a>Como os ambientes python funcionam com parâmetros de pipeline

Como discutido anteriormente em [Configure o ambiente do treino, o](#configure-the-training-runs-environment)estado ambiente e as dependências da biblioteca Python são especificados usando um `Environment` objeto. Geralmente, pode especificar uma existente `Environment` referindo-se ao seu nome e, opcionalmente, uma versão:

```python
aml_run_config = RunConfiguration()
aml_run_config.environment.name = 'MyEnvironment'
aml_run_config.environment.version = '1.0'
```

No entanto, se optar por utilizar `PipelineParameter` objetos para definir dinamicamente variáveis em tempo de execução para os seus passos de pipeline, não pode utilizar esta técnica de se referir a uma existente `Environment` . Em vez disso, se pretender utilizar `PipelineParameter` objetos, tem de definir `environment` o campo do objeto para um `RunConfiguration` `Environment` objeto. É da sua responsabilidade assegurar que tal um `Environment` tem as suas dependências em pacotes externos python devidamente definidos.

### <a name="use-a-dataset"></a>Utilize um conjunto de dados 

Conjuntos de dados criados a partir do armazenamento de Azure Blob, Ficheiros Azure, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure SQL Database e Azure Database for PostgreSQL podem ser usados como entrada para qualquer passo de pipeline. Pode escrever saída para um [DataTransferStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep?preserve-view=true&view=azure-ml-py), [DatabricksStep,](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricks_step.databricksstep?preserve-view=true&view=azure-ml-py)ou se pretender escrever dados para uma loja de dados específica utilizar [o PipelineData](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?preserve-view=true&view=azure-ml-py). 

> [!IMPORTANT]
> A produção de dados de volta para uma loja de dados utilizando o PipelineData é suportada apenas para as lojas de dados de partilha de ficheiros Azure Blob e Azure File. 
>
> Para escrever os dados de saída de volta para Azure Blob, Azure File share, ADLS Gen 1 e ADLS Gen 2 datastores use a classe de pré-visualização pública, [`OutputFileDatasetConfig`](/python/api/azureml-core/azureml.data.output_dataset_config.outputfiledatasetconfig?preserve-view=true&view=azure-ml-py) .

```python
dataset_consuming_step = PythonScriptStep(
    script_name="iris_train.py",
    inputs=[iris_tabular_dataset.as_named_input("iris_data")],
    compute_target=compute_target,
    source_directory=project_folder
)
```

Em seguida, recupere o conjunto de dados no seu pipeline utilizando o dicionário [Run.input_datasets.](/python/api/azureml-core/azureml.core.run.run?preserve-view=true&view=azure-ml-py#&preserve-view=trueinput-datasets)

```python
# iris_train.py
from azureml.core import Run, Dataset

run_context = Run.get_context()
iris_dataset = run_context.input_datasets['iris_data']
dataframe = iris_dataset.to_pandas_dataframe()
```

Vale `Run.get_context()` a pena destacar a linha. Esta função recupera uma `Run` representação do atual ensaio experimental. Na amostra acima, utilizamo-la para recuperar um conjunto de dados registado. Outro uso comum do `Run` objeto é recuperar tanto a experiência em si como o espaço de trabalho em que a experiência reside: 

```python
# Within a PythonScriptStep

ws = Run.get_context().experiment.workspace
```

Para obter mais detalhes, incluindo formas alternativas de passar e aceder aos dados, consulte [os dados de deslocação para e entre as etapas do gasoduto ML (Python)](how-to-move-data-in-out-of-pipelines.md).

## <a name="caching--reuse"></a>Caching & reutilização  

Para otimizar e personalizar o comportamento dos seus oleodutos, pode fazer algumas coisas em torno do caching e reutilização. Por exemplo, pode optar por:
+ **Desligue a reutilização predefinida da saída de marcha** por passo, regulando durante a `allow_reuse=False` [definição do passo](/python/api/azureml-pipeline-steps/?preserve-view=true&view=azure-ml-py). A reutilização é fundamental quando se utilizam gasodutos num ambiente colaborativo, uma vez que eliminar corridas desnecessárias oferece agilidade. No entanto, pode optar por não reutilizar.
+ **Forçar a regeneração da saída para todos os passos em uma corrida** com `pipeline_run = exp.submit(pipeline, regenerate_outputs=False)`

Por predefinição, `allow_reuse` para os passos está ativado e `source_directory` o especificado na definição de passo é hashed. Assim, se o guião para um dado passo permanecer o mesmo ( `script_name` entradas e parâmetros), e nada mais no ` source_directory` mudou, a saída de um passo anterior é reutilizado, o trabalho não é submetido ao cálculo, e os resultados da execução anterior estão imediatamente disponíveis para o passo seguinte.

```python
step = PythonScriptStep(name="Hello World",
                        script_name="hello_world.py",
                        compute_target=aml_compute,
                        source_directory=source_directory,
                        allow_reuse=False,
                        hash_paths=['hello_world.ipynb'])
```

## <a name="submit-the-pipeline"></a>Enviar o oleoduto

Ao submeter o pipeline, a Azure Machine Learning verifica as dependências de cada passo e envia uma imagem do diretório de origem especificado. Se não for especificado nenhum diretório de origem, o diretório local atual é carregado. O instantâneo também é armazenado como parte da experiência no seu espaço de trabalho.

> [!IMPORTANT]
> [!INCLUDE [amlinclude-info](../../includes/machine-learning-amlignore-gitignore.md)]
>
> Para mais informações, consulte [Snapshots](concept-azure-machine-learning-architecture.md#snapshots).

```python
from azureml.core import Experiment

# Submit the pipeline to be run
pipeline_run1 = Experiment(ws, 'Compare_Models_Exp').submit(pipeline1)
pipeline_run1.wait_for_completion()
```

Quando se faz um oleoduto pela primeira vez, a Azure Machine Learning:

* Descarrega o instantâneo do projeto para o alvo do cálculo a partir do armazenamento Blob associado ao espaço de trabalho.
* Constrói uma imagem docker correspondente a cada passo no oleoduto.
* Descarrega a imagem do Docker para cada passo para o alvo do cálculo a partir do registo do contentor.
* Configura o acesso a `Dataset` `PipelineData` e objetos. Como `as_mount()` modo de acesso, o FUSE é utilizado para fornecer acesso virtual. Se a montagem não for suportada ou se o utilizador tiver especificado o acesso como `as_download()` , os dados são copiados para o alvo do cálculo.

* Corre o passo no alvo do cálculo especificado na definição do passo. 
* Cria artefactos, tais como troncos, estalidos e stderr, métricas e saídas especificadas pelo passo. Estes artefactos são então carregados e mantidos na loja de dados predefinidos do utilizador.

![Diagrama de executar uma experiência como um oleoduto](./media/how-to-create-your-first-pipeline/run_an_experiment_as_a_pipeline.png)

Para mais informações, consulte a referência da [classe Experiment.](/python/api/azureml-core/azureml.core.experiment.experiment?preserve-view=true&view=azure-ml-py)

## <a name="use-pipeline-parameters-for-arguments-that-change-at-inference-time"></a>Utilize parâmetros de gasoduto para argumentos que mudam no tempo de inferência

## <a name="view-results-of-a-pipeline"></a>Ver resultados de um oleoduto

Veja a lista de todos os seus oleodutos e os seus detalhes de execução no estúdio:

1. Inscreva-se no [estúdio Azure Machine Learning](https://ml.azure.com).

1. [Veja o seu espaço de trabalho.](how-to-manage-workspace.md#view)

1. À esquerda, selecione Pipelines para ver todos os seus **gasodutos.**
 ![lista de oleodutos de aprendizagem automática](./media/how-to-create-your-first-pipeline/pipelines.png)
 
1. Selecione um oleoduto específico para ver os resultados da execução.

### <a name="git-tracking-and-integration"></a>Rastreio e integração de Git

Quando se inicia uma corrida de treinamento onde o diretório de origem é um repositório local de Git, a informação sobre o repositório é armazenada na história da execução. Para obter mais informações, consulte [a integração do Git para a Azure Machine Learning.](concept-train-model-git-integration.md)

## <a name="next-steps"></a>Passos seguintes

- Para partilhar o seu oleoduto com colegas ou clientes, consulte [os pipelines de machine learning da Publish](how-to-deploy-pipelines.md)
- Use [estes cadernos Jupyter no GitHub](https://aka.ms/aml-pipeline-readme) para explorar ainda mais os oleodutos de aprendizagem automática
- Consulte a ajuda de referência SDK para o pacote [azureml-pipelines-core](/python/api/azureml-pipeline-core/?preserve-view=true&view=azure-ml-py) e o pacote [de passos de gasodutos azureml](/python/api/azureml-pipeline-steps/?preserve-view=true&view=azure-ml-py)
- Consulte o ["how-to"](how-to-debug-pipelines.md) para obter dicas sobre depuração e resolução de problemas de gasodutos=
- Saiba como executar blocos de notas ao seguir o artigo [Utilizar blocos de notas Jupyter para explorar este serviço](samples-notebooks.md).