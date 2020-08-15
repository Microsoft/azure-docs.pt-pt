---
title: Criar, executar, & acompanhar os oleodutos ML
titleSuffix: Azure Machine Learning
description: Crie e executar um oleoduto de aprendizagem automática com o Azure Machine Learning SDK para Python. Utilize gasodutos ML para criar e gerir os fluxos de trabalho que cosem fases de aprendizagem automática (ML). Estas fases incluem preparação de dados, formação de modelos, implantação de modelos e inferência/pontuação.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: sgilley
ms.author: nilsp
author: NilsPohlmann
ms.date: 8/14/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 8b6ed41333a0ea113d939ab79bd9e9291a0dae9c
ms.sourcegitcommit: c293217e2d829b752771dab52b96529a5442a190
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/15/2020
ms.locfileid: "88244059"
---
# <a name="create-and-run-machine-learning-pipelines-with-azure-machine-learning-sdk"></a>Criar e executar gasodutos de aprendizagem automática com Azure Machine Learning SDK

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste artigo, aprende-se a criar, publicar, executar e rastrear um [oleoduto de aprendizagem automática](concept-ml-pipelines.md) utilizando o [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).  Utilize **os oleodutos ML** para criar um fluxo de trabalho que cosa várias fases ML e, em seguida, publique esse pipeline no seu espaço de trabalho Azure Machine Learning para aceder mais tarde ou partilhar com outros.  Os oleodutos ML são ideais para cenários de pontuação de lotes, utilizando vários cálculos, reutilizando passos em vez de os reexame, bem como partilhando fluxos de trabalho ML com outros.

Embora possa utilizar um tipo diferente de oleoduto chamado [Gasoduto Azure](https://docs.microsoft.com/azure/devops/pipelines/targets/azure-machine-learning?context=azure%2Fmachine-learning%2Fservice%2Fcontext%2Fml-context&view=azure-devops&tabs=yaml) para automatização de CI/CD de tarefas ML, esse tipo de gasoduto não é armazenado no seu espaço de trabalho. [Compare estes diferentes oleodutos.](concept-ml-pipelines.md#which-azure-pipeline-technology-should-i-use)

Cada fase de um gasoduto ML, como a preparação de dados e a formação de modelos, pode incluir uma ou mais etapas.

Os gasodutos ML que cria são visíveis para os membros do seu espaço de [trabalho](how-to-manage-workspace.md)Azure Machine Learning . 

Os gasodutos ML utilizam alvos de computação remota para computação e dados temporários associados a esse gasoduto. Podem ler e escrever dados de e para locais de armazenamento suportados do [Azure.](https://docs.microsoft.com/azure/storage/)

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

* Desaponte os [alvos de cálculo](concept-azure-machine-learning-architecture.md#compute-targets) nos quais os seus passos de gasoduto irão funcionar.

### <a name="set-up-a-datastore"></a>Criar uma data-tore

Uma loja de dados armazena os dados para o pipeline aceder. Cada espaço de trabalho tem uma datastore padrão. Pode registar datas mais. 

Quando cria o seu espaço de trabalho, os [Ficheiros Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) e [o armazenamento Azure Blob](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) estão ligados ao espaço de trabalho. Está registada uma loja de dados predefinida para ligar ao armazenamento da Azure Blob. Para saber mais, consulte [decidir quando utilizar ficheiros Azure, Blobs Azure ou Discos Azure](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks). 

```python
# Default datastore 
def_data_store = ws.get_default_datastore()

# Get the blob storage associated with the workspace
def_blob_store = Datastore(ws, "workspaceblobstore")

# Get file storage associated with the workspace
def_file_store = Datastore(ws, "workspacefilestore")

```

Faça o upload de ficheiros de dados ou diretórios para a datastore para que fiquem acessíveis a partir dos seus oleodutos. Este exemplo utiliza o armazenamento blob como datastore:

```python
def_blob_store.upload_files(
    ["iris.csv"],
    target_path="train-dataset",
    overwrite=True)
```

Um oleoduto consiste em um ou mais passos. Um passo é uma unidade executada num alvo computacional. As medidas podem consumir fontes de dados e produzir dados "intermédios". Um passo pode criar dados como um modelo, um diretório com modelos e ficheiros dependentes, ou dados temporários. Estes dados estão então disponíveis para outros passos mais tarde no oleoduto.

Para saber mais sobre a ligação do seu pipeline aos seus dados, consulte os artigos [Como Aceder aos Dados](how-to-access-data.md) e [Como Registar Conjuntos de Dados.](how-to-create-register-datasets.md) 

### <a name="configure-data-using-dataset-and-pipelinedata-objects"></a>Configure dados usando `Dataset` e `PipelineData` objetos

Acabou de criar uma fonte de dados que pode ser referenciada num oleoduto como uma entrada para um passo. A forma preferida de fornecer dados a um pipeline é um objeto [dataset.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.Dataset) O `Dataset` objeto aponta para dados que vivem dentro ou estão acessíveis a partir de uma datastore ou em um URL web. A `Dataset` classe é abstrata, por isso irá criar um exemplo de um `FileDataset` (referindo-se a um ou mais ficheiros) ou de um `TabularDataset` que é criado por um ou mais ficheiros com colunas de dados delimitadas.

`Dataset` objetos suportam versões, difusões e estatísticas sumárias. `Dataset`s são preguiçosamente avaliados (como geradores Python) e é eficiente subconsumo-los dividindo ou filtrando. 

Cria-se um `Dataset` método de utilização como [from_file](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?view=azure-ml-py#from-files-path--validate-true-) ou [from_delimited_files](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-delimited-files-path--validate-true--include-path-false--infer-column-types-true--set-column-types-none--separator------header-true--partition-format-none--support-multi-line-false-).

```python
from azureml.core import Dataset

iris_tabular_dataset = Dataset.Tabular.from_delimited_files([(def_blob_store, 'train-dataset/iris.csv')])
```

Os dados intermédios (ou saída de um passo) são representados por um objeto [PipelineData.](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) `output_data1` é produzido como a saída de um passo, e usado como a entrada de um ou mais passos futuros. `PipelineData` introduz uma dependência de dados entre passos, e cria uma ordem de execução implícita no oleoduto. Este objeto será utilizado mais tarde ao criar passos de gasoduto.

```python
from azureml.pipeline.core import PipelineData

output_data1 = PipelineData(
    "output_data1",
    datastore=def_blob_store,
    output_name="output_data1")
```

Mais detalhes e código de amostra para trabalhar com conjuntos de dados e dados do gasoduto estão em [mover dados para dentro e entre etapas de gasoduto ML (Python)](how-to-move-data-in-out-of-pipelines.md).

## <a name="set-up-a-compute-target"></a>Configurar um alvo de computação

No Azure Machine Learning, o termo __compute__ (ou __meta de computação)__ refere-se às máquinas ou clusters que realizam os passos computacionais no seu pipeline de aprendizagem automática. Consulte [metas de cálculo para formação](how-to-set-up-training-targets.md) de modelos para uma lista completa de alvos de computação e como criá-los e anexá-los ao seu espaço de trabalho. O processo de criação e ou fixação de um alvo de computação é o mesmo, quer esteja a treinar um modelo ou a executar um passo de pipeline. Depois de criar e fixar o seu alvo de cálculo, utilize o `ComputeTarget` objeto no seu passo de [pipeline](#steps).

> [!IMPORTANT]
> A realização de operações de gestão em objetivos de computação não é suportada a partir de trabalhos remotos internos. Uma vez que os gasodutos de aprendizagem automática são submetidos como um trabalho remoto, não utilize operações de gestão em alvos de computação a partir do interior do oleoduto.

Abaixo estão exemplos de criação e anexação de metas de computação para:

* Computação do Machine Learning
* Azure Databricks 
* Azure Data Lake Analytics

[!INCLUDE [low-pri-note](../../includes/machine-learning-low-pri-vm.md)]

### <a name="azure-machine-learning-compute"></a>Azure Machine Learning compute

Pode criar um computamento Azure Machine Learning para executar os seus passos.

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

### <a name="azure-databricks"></a><a id="databricks"></a>Azure Databricks

Azure Databricks é um ambiente baseado em Apache Spark na nuvem Azure. Pode ser usado como um alvo de computação com um pipeline Azure Machine Learning.

Crie um espaço de trabalho Azure Databricks antes de o utilizar. Para criar um recurso de espaço de trabalho, consulte o trabalho Run a Spark no documento [Azure Databricks.](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal)

Para anexar a Azure Databricks como alvo de computação, forneça as seguintes informações:

* __Databricks computação nome__: O nome que pretende atribuir a este recurso de computação.
* __Databricks nome do espaço de trabalho__: O nome do espaço de trabalho Azure Databricks.
* __Databricks acesso token__: O token de acesso usado para autenticar a Azure Databricks. Para gerar um token de acesso, consulte o documento [de Autenticação.](https://docs.azuredatabricks.net/dev-tools/api/latest/authentication.html)

O código que se segue demonstra como anexar a Azure Databricks como alvo de computação com o Azure Machine Learning SDK __(O espaço de trabalho Databricks precisa de estar presente na mesma subscrição que o seu espaço de trabalho AML):__

```python
import os
from azureml.core.compute import ComputeTarget, DatabricksCompute
from azureml.exceptions import ComputeTargetException

databricks_compute_name = os.environ.get(
    "AML_DATABRICKS_COMPUTE_NAME", "<databricks_compute_name>")
databricks_workspace_name = os.environ.get(
    "AML_DATABRICKS_WORKSPACE", "<databricks_workspace_name>")
databricks_resource_group = os.environ.get(
    "AML_DATABRICKS_RESOURCE_GROUP", "<databricks_resource_group>")
databricks_access_token = os.environ.get(
    "AML_DATABRICKS_ACCESS_TOKEN", "<databricks_access_token>")

try:
    databricks_compute = ComputeTarget(
        workspace=ws, name=databricks_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('databricks_compute_name {}'.format(databricks_compute_name))
    print('databricks_workspace_name {}'.format(databricks_workspace_name))
    print('databricks_access_token {}'.format(databricks_access_token))

    # Create attach config
    attach_config = DatabricksCompute.attach_configuration(resource_group=databricks_resource_group,
                                                           workspace_name=databricks_workspace_name,
                                                           access_token=databricks_access_token)
    databricks_compute = ComputeTarget.attach(
        ws,
        databricks_compute_name,
        attach_config
    )

    databricks_compute.wait_for_completion(True)
```

Para um exemplo mais detalhado, consulte um [caderno de exemplo](https://aka.ms/pl-databricks) no GitHub.

### <a name="azure-data-lake-analytics"></a><a id="adla"></a>Azure Data Lake Analytics

Azure Data Lake Analytics é uma plataforma de análise de dados na nuvem Azure. Pode ser usado como um alvo de computação com um pipeline Azure Machine Learning.

Crie uma conta Azure Data Lake Analytics antes de a utilizar. Para criar este recurso, consulte o [Get start with Azure Data Lake Analytics.](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-get-started-portal)

Para anexar o Data Lake Analytics como um alvo de computação, deve utilizar o Azure Machine Learning SDK e fornecer as seguintes informações:

* __Nome do cálculo__: O nome que pretende atribuir a este recurso de cálculo.
* __Grupo de Recursos__: O grupo de recursos que contém a conta Data Lake Analytics.
* __Nome da conta__: O nome da conta Data Lake Analytics.

O seguinte código demonstra como anexar o Data Lake Analytics como um alvo de computação:

```python
import os
from azureml.core.compute import ComputeTarget, AdlaCompute
from azureml.exceptions import ComputeTargetException


adla_compute_name = os.environ.get(
    "AML_ADLA_COMPUTE_NAME", "<adla_compute_name>")
adla_resource_group = os.environ.get(
    "AML_ADLA_RESOURCE_GROUP", "<adla_resource_group>")
adla_account_name = os.environ.get(
    "AML_ADLA_ACCOUNT_NAME", "<adla_account_name>")

try:
    adla_compute = ComputeTarget(workspace=ws, name=adla_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('adla_compute_name {}'.format(adla_compute_name))
    print('adla_resource_id {}'.format(adla_resource_group))
    print('adla_account_name {}'.format(adla_account_name))
    # create attach config
    attach_config = AdlaCompute.attach_configuration(resource_group=adla_resource_group,
                                                     account_name=adla_account_name)
    # Attach ADLA
    adla_compute = ComputeTarget.attach(
        ws,
        adla_compute_name,
        attach_config
    )

    adla_compute.wait_for_completion(True)
```

Para um exemplo mais detalhado, consulte um [caderno de exemplo](https://aka.ms/pl-adla) no GitHub.

> [!TIP]
> Os oleodutos Azure Machine Learning só podem funcionar com dados armazenados na loja de dados predefinida da conta Data Lake Analytics. Se os dados com os qual precisa trabalhar estiverem numa loja não padrão, pode utilizar um [`DataTransferStep`](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep?view=azure-ml-py) para copiar os dados antes do treino.

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

O código acima mostra duas opções para lidar com dependências. Como apresentado, `USE_CURATED_ENV = True` com, a configuração baseia-se num ambiente curado. Os ambientes curados são "pré-preparados" com bibliotecas interdependentes comuns e podem ser significativamente mais rápidos de colocar online. Ambientes curados têm imagens estivais pré-construídas no [Registo de Contentores da Microsoft.](https://hub.docker.com/publishers/microsoftowner) O caminho tomado se mudar `USE_CURATED_ENV` para `False` mostrar o padrão para definir explicitamente as suas dependências. Nesse cenário, será criada e registada uma nova imagem personalizada do Docker num Registo de Contentores Azure dentro do seu grupo de recursos (ver [Introdução aos registos privados de contentores Docker em Azure).](https://docs.microsoft.com/azure/container-registry/container-registry-intro) Construir e registar esta imagem pode demorar alguns minutos.

## <a name="construct-your-pipeline-steps"></a><a id="steps"></a>Construa os seus passos de oleoduto

Assim que tiver o recurso compute e o ambiente criados, está pronto para definir os passos do seu pipeline. Existem muitos passos incorporados disponíveis através do Azure Machine Learning SDK, como pode ver na [documentação de referência do `azureml.pipeline.steps` pacote.](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps?view=azure-ml-py) A classe mais flexível é [PythonScriptStep,](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py)que executa um script Python.

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

training_results = PipelineData(
    "training_results",
    datastore=def_blob_store,
    output_name="training_results")

train_step = PythonScriptStep(
    script_name=train_entry_point,
    source_directory=train_source_dir,
    arguments=["--prepped_data", output_data1, "--training_results", training_results],
    inputs=[output_data1],
    outputs=[training_results],
    compute_target=compute_target,
    runconfig=aml_run_config,
    allow_reuse=True
)
```

O código acima é muito semelhante ao da etapa de preparação de dados. O código de formação encontra-se num diretório separado do código de preparação de dados. A `PipelineData` saída do passo de preparação de dados, é usada como `output_data1` _entrada_ para o passo de treino. Um novo `PipelineData` objeto é criado para manter os `training_results` resultados para uma comparação subsequente ou passo de implementação. 

Depois de definir os seus passos, constrói o oleoduto utilizando alguns ou todos esses passos.

> [!NOTE]
> Nenhum ficheiro ou dados é enviado para a Azure Machine Learning quando define os passos ou constrói o pipeline.

```python
# list of steps to run (`compare_step` definition not shown)
compare_models = [data_prep_step, train_step, compare_step]

from azureml.pipeline.core import Pipeline

# Build the pipeline
pipeline1 = Pipeline(workspace=ws, steps=[compare_models])
```

O exemplo a seguir utiliza o alvo computacional Azure Databricks criado anteriormente: 

```python
from azureml.pipeline.steps import DatabricksStep

dbStep = DatabricksStep(
    name="databricksmodule",
    inputs=[step_1_input],
    outputs=[step_1_output],
    num_workers=1,
    notebook_path=notebook_path,
    notebook_params={'myparam': 'testparam'},
    run_name='demo run name',
    compute_target=databricks_compute,
    allow_reuse=False
)
# List of steps to run
steps = [dbStep]

# Build the pipeline
pipeline1 = Pipeline(workspace=ws, steps=steps)
```

### <a name="use-a-dataset"></a>Utilize um conjunto de dados 

Conjuntos de dados criados a partir do armazenamento de Azure Blob, Ficheiros Azure, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure SQL Database e Azure Database for PostgreSQL podem ser usados como entrada para qualquer passo de pipeline. Pode escrever saída para um [DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep?view=azure-ml-py), [DatabricksStep,](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricks_step.databricksstep?view=azure-ml-py)ou se pretender escrever dados para uma loja de dados específica utilizar [o PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py). 

> [!IMPORTANT]
> A produção de dados de volta para uma loja de dados utilizando o PipelineData é suportada apenas para as lojas de dados de partilha de ficheiros Azure Blob e Azure File. Esta funcionalidade não é suportada para [datastores da ADLS Gen 2](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_data_lake_datastore.azuredatalakegen2datastore?view=azure-ml-py) neste momento.

```python
dataset_consuming_step = PythonScriptStep(
    script_name="iris_train.py",
    inputs=[iris_tabular_dataset.as_named_input("iris_data")],
    compute_target=compute_target,
    source_directory=project_folder
)
```

Em seguida, recupere o conjunto de dados no seu pipeline utilizando o dicionário [Run.input_datasets.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#input-datasets)

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

Para mais informações, consulte a referência da [classe Experiment.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py)

### <a name="view-results-of-a-pipeline"></a>Ver resultados de um oleoduto

Veja a lista de todos os seus oleodutos e os seus detalhes de execução no estúdio:

1. Inscreva-se no [estúdio Azure Machine Learning](https://ml.azure.com).

1. [Veja o seu espaço de trabalho.](how-to-manage-workspace.md#view)

1. À esquerda, selecione Pipelines para ver todos os seus **gasodutos.**
 ![lista de oleodutos de aprendizagem automática](./media/how-to-create-your-first-pipeline/pipelines.png)
 
1. Selecione um oleoduto específico para ver os resultados da execução.

## <a name="git-tracking-and-integration"></a>Rastreio e integração de Git

Quando se inicia uma corrida de treinamento onde o diretório de origem é um repositório local de Git, a informação sobre o repositório é armazenada na história da execução. Para obter mais informações, consulte [a integração do Git para a Azure Machine Learning.](concept-train-model-git-integration.md)

## <a name="publish-a-pipeline"></a>Publicar um oleoduto

Pode publicar um oleoduto para executá-lo com entradas diferentes mais tarde. Para o ponto final do REST de um oleoduto já publicado para aceitar parâmetros, deve parametrizar o oleoduto antes de publicar.

1. Para criar um parâmetro de pipeline, utilize um objeto [PipelineParameter](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.pipelineparameter?view=azure-ml-py) com um valor predefinido.

   ```python
   from azureml.pipeline.core.graph import PipelineParameter
   
   pipeline_param = PipelineParameter(
     name="pipeline_arg",
     default_value=10)
   ```

2. Adicione este `PipelineParameter` objeto como parâmetro a qualquer um dos passos na tubagem da seguinte forma:

   ```python
   compareStep = PythonScriptStep(
     script_name="compare.py",
     arguments=["--comp_data1", comp_data1, "--comp_data2", comp_data2, "--output_data", out_data3, "--param1", pipeline_param],
     inputs=[ comp_data1, comp_data2],
     outputs=[out_data3],
     compute_target=compute_target,
     source_directory=project_folder)
   ```

3. Publique este oleoduto que aceitará um parâmetro quando invocado.

   ```python
   published_pipeline1 = pipeline_run1.publish_pipeline(
        name="My_Published_Pipeline",
        description="My Published Pipeline Description",
        version="1.0")
   ```

### <a name="run-a-published-pipeline"></a>Executar um oleoduto publicado

Todos os oleodutos publicados têm um ponto final REST. Com o ponto final do gasoduto, pode desencadear uma corrida do oleoduto a partir de quaisquer sistemas externos, incluindo clientes não-Python. Este ponto final permite a "repetibilidade gerida" em cenários de pontuação e reconversão de lotes.

Para invocar o funcionamento do oleoduto anterior, precisa de um sinal de cabeçalho de autenticação Azure Ative, conforme descrito na referência da [classe AzureCliAuthentication](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.azurecliauthentication?view=azure-ml-py) ou obter mais detalhes no caderno [de autenticação em Azure Machine Learning.](https://aka.ms/pl-restep-auth)

```python
from azureml.pipeline.core import PublishedPipeline
import requests

response = requests.post(published_pipeline1.endpoint,
                         headers=aad_token,
                         json={"ExperimentName": "My_Pipeline",
                               "ParameterAssignments": {"pipeline_arg": 20}})
```

## <a name="create-a-versioned-pipeline-endpoint"></a>Criar um ponto final de pipeline em versão

Pode criar um Pipeline Endpoint com vários oleodutos publicados por trás. Isto pode ser usado como um oleoduto publicado, mas dá-lhe um ponto final rest fixo à medida que se iteriza e atualiza os seus gasodutos ML.

```python
from azureml.pipeline.core import PipelineEndpoint

published_pipeline = PipelineEndpoint.get(workspace=ws, name="My_Published_Pipeline")
pipeline_endpoint = PipelineEndpoint.publish(workspace=ws, name="PipelineEndpointTest",
                                            pipeline=published_pipeline, description="Test description Notebook")
```

### <a name="submit-a-job-to-a-pipeline-endpoint"></a>Submeter um trabalho a um ponto final de oleoduto

Pode submeter um trabalho à versão padrão de um ponto final do gasoduto:

```python
pipeline_endpoint_by_name = PipelineEndpoint.get(workspace=ws, name="PipelineEndpointTest")
run_id = pipeline_endpoint_by_name.submit("PipelineEndpointExperiment")
print(run_id)
```

Também pode submeter um trabalho a uma versão específica:

```python
run_id = pipeline_endpoint_by_name.submit("PipelineEndpointExperiment", pipeline_version="0")
print(run_id)
```

O mesmo pode ser feito utilizando a API REST:

```python
rest_endpoint = pipeline_endpoint_by_name.endpoint
response = requests.post(rest_endpoint, 
                         headers=aad_token, 
                         json={"ExperimentName": "PipelineEndpointExperiment",
                               "RunSource": "API",
                               "ParameterAssignments": {"1": "united", "2":"city"}})
```

### <a name="use-published-pipelines-in-the-studio"></a>Use oleodutos publicados no estúdio

Também pode executar um oleoduto publicado a partir do estúdio:

1. Inscreva-se no [estúdio Azure Machine Learning](https://ml.azure.com).

1. [Veja o seu espaço de trabalho.](how-to-manage-workspace.md#view)

1. À esquerda, selecione **Endpoints**.

1. Por cima, selecione **os pontos finais do Pipeline**.
 ![lista de oleodutos publicados machine learning](./media/how-to-create-your-first-pipeline/pipeline-endpoints.png)

1. Selecione um gasoduto específico para executar, consumir ou rever os resultados de execuções anteriores do ponto final do gasoduto.

### <a name="disable-a-published-pipeline"></a>Desativar um oleoduto publicado

Para esconder um oleoduto da sua lista de oleodutos publicados, desativa-o, quer no estúdio, quer no SDK:

```python
# Get the pipeline by using its ID from Azure Machine Learning studio
p = PublishedPipeline.get(ws, id="068f4885-7088-424b-8ce2-eeb9ba5381a6")
p.disable()
```

Pode voltar a ative-lo com `p.enable()` . Para mais informações, consulte a referência [da classe PublishedPipeline.](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.publishedpipeline?view=azure-ml-py)

## <a name="caching--reuse"></a>Caching & reutilização  

Para otimizar e personalizar o comportamento dos seus oleodutos, pode fazer algumas coisas em torno do caching e reutilização. Por exemplo, pode optar por:
+ **Desligue a reutilização predefinida da saída de marcha** por passo, regulando durante a `allow_reuse=False` [definição do passo](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py). A reutilização é fundamental quando se utilizam gasodutos num ambiente colaborativo, uma vez que eliminar corridas desnecessárias oferece agilidade. No entanto, pode optar por não reutilizar.
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

## <a name="next-steps"></a>Passos seguintes

- Use [estes cadernos Jupyter no GitHub](https://aka.ms/aml-pipeline-readme) para explorar ainda mais os oleodutos de aprendizagem automática.
- Consulte a ajuda de referência SDK para o pacote [de núcleo de gasodutos azureml](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) e o pacote [de passos de gasodutos azureml.](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py)
- Consulte o ["how-to"](how-to-debug-pipelines.md) para obter dicas sobre depuração e resolução de problemas.

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]
