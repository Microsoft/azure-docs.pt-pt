---
title: Criar, correr, & rastrear os oleodutos ML
titleSuffix: Azure Machine Learning
description: Crie e execute um pipeline de aprendizagem automática com o Azure Machine Learning SDK para Python. Utilize os gasodutos ML para criar e gerir os fluxos de trabalho que cosem as fases de aprendizagem automática (ML). Estas fases incluem preparação de dados, formação de modelos, implementação de modelos e inferência/pontuação.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: sgilley
ms.author: sanpil
author: sanpil
ms.date: 12/05/2019
ms.custom: seodec18
ms.openlocfilehash: b1b0facbb7cdd0dd51c53077c21afab427facf3b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82133588"
---
# <a name="create-and-run-machine-learning-pipelines-with-azure-machine-learning-sdk"></a>Criar e executar gasodutos de aprendizagem automática com Azure Machine Learning SDK

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste artigo, aprende-se a criar, publicar, executar e rastrear um pipeline de [aprendizagem automática](concept-ml-pipelines.md) utilizando o [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).  Utilize **os gasodutos ML** para criar um fluxo de trabalho que cose várias fases ml e, em seguida, publique esse pipeline no seu espaço de trabalho Azure Machine Learning para aceder mais tarde ou partilhar com outras.  Os oleodutos ML são ideais para cenários de pontuação de lotes, utilizando várias computações, reutilizando passos em vez de reexecutá-los, bem como partilhar fluxos de trabalho ML com outros.

Embora possa utilizar um tipo diferente de pipeline chamado [Pipeline Azure](https://docs.microsoft.com/azure/devops/pipelines/targets/azure-machine-learning?context=azure%2Fmachine-learning%2Fservice%2Fcontext%2Fml-context&view=azure-devops&tabs=yaml) para automatização ci/CD de tarefas ML, esse tipo de pipeline nunca é armazenado dentro do seu espaço de trabalho. [Compare estes diferentes oleodutos.](concept-ml-pipelines.md#which-azure-pipeline-technology-should-i-use)

Cada fase de um gasoduto ML, como a preparação de dados e a formação de modelos, pode incluir um ou mais passos.

Os gasodutos ML que cria são visíveis para os membros do seu espaço de [trabalho](how-to-manage-workspace.md)Azure Machine Learning . 

Os gasodutos ML utilizam objetivos de computação remota para a computação e o armazenamento dos dados intermédios e finais associados a esse gasoduto. Podem ler e escrever dados de e para locais de [armazenamento azure](https://docs.microsoft.com/azure/storage/) suportados.

Se não tiver uma subscrição do Azure, crie uma conta gratuita antes de começar. Experimente a [versão gratuita ou paga do Azure Machine Learning.](https://aka.ms/AMLFree)

## <a name="prerequisites"></a>Pré-requisitos

* Crie um espaço de [trabalho azure machine learning](how-to-manage-workspace.md) para manter todos os seus recursos de pipeline.

* [Configure o seu ambiente](how-to-configure-environment.md) de desenvolvimento para instalar o Azure Machine Learning SDK, ou utilize uma instância de computação de [Machine Learning Azure (pré-visualização)](concept-compute-instance.md) com o SDK já instalado.

Comece por anexar o seu espaço de trabalho:

```Python
import azureml.core
from azureml.core import Workspace, Datastore

ws = Workspace.from_config()
```

## <a name="set-up-machine-learning-resources"></a>Configurar recursos de aprendizagem automática

Criar os recursos necessários para executar um oleoduto ML:

* Criar uma loja de dados utilizada para aceder aos dados necessários nas etapas do gasoduto.

* Configure `Dataset` um objeto para apontar para dados persistentes que vivam ou sejam acessíveis numa loja de dados. Configure `PipelineData` um objeto para dados temporários passados entre os passos do gasoduto. 

* Configurar os [alvos da computação](concept-azure-machine-learning-architecture.md#compute-targets) em que os seus passos de gasoduto serão executados.

### <a name="set-up-a-datastore"></a>Criar uma loja de dados

Uma loja de dados armazena os dados para o pipeline aceder. Cada espaço de trabalho tem uma loja de dados padrão. Pode registar reservas adicionais de dados. 

Quando cria o seu espaço de trabalho, [os Ficheiros Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) e o [armazenamento Azure Blob](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) estão ligados ao espaço de trabalho. Está registada uma loja de dados predefinida para ligar ao armazenamento do Blob Azure. Para saber mais, consulte [decidir quando utilizar Ficheiros Azure, Blobs Azure ou Discos Azure](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks). 

```python
# Default datastore 
def_data_store = ws.get_default_datastore()

# Get the blob storage associated with the workspace
def_blob_store = Datastore(ws, "workspaceblobstore")

# Get file storage associated with the workspace
def_file_store = Datastore(ws, "workspacefilestore")

```

Faça upload de ficheiros de dados ou diretórios para a loja de dados para que sejam acessíveis a partir dos seus oleodutos. Este exemplo utiliza o armazenamento Blob como a loja de dados:

```python
def_blob_store.upload_files(
    ["iris.csv"],
    target_path="train-dataset",
    overwrite=True)
```

Um gasoduto consiste em um ou mais passos. Um passo é uma unidade executada num alvo de cálculo. As medidas podem consumir fontes de dados e produzir dados "intermédios". Um passo pode criar dados como um modelo, um diretório com modelo e ficheiros dependentes, ou dados temporários. Estes dados estão então disponíveis para outras etapas posteriores no oleoduto.

Para saber mais sobre a ligação do seu pipeline aos seus dados, consulte os artigos [Como Aceder aos Dados](how-to-access-data.md) e Como Registar [Datasets](how-to-create-register-datasets.md). 

### <a name="configure-data-using-dataset-and-pipelinedata-objects"></a>Configurar a `Dataset` `PipelineData` utilização de dados e objetos

Acabade criar uma fonte de dados que pode ser referenciada num oleoduto como uma entrada para um passo. A forma preferida de fornecer dados a um pipeline é um objeto [dataset.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.Dataset) O `Dataset` objeto aponta para dados que vivem ou são acessíveis a partir de uma loja de dados ou de um URL Web. A `Dataset` classe é abstrata, por isso irá `FileDataset` criar uma instância de um `TabularDataset` (referindo-se a um ou mais ficheiros) ou de um que é criado por um ou mais ficheiros com colunas de dados delimitadas.

`Dataset`objetos suportam versões, difusões e estatísticas sumárias. `Dataset`s são preguiçosamente avaliados (como geradores Python) e é eficiente subconá-los dividindo ou filtrando. 

Cria-se `Dataset` um uso de métodos como [from_file](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?view=azure-ml-py#from-files-path--validate-true-) ou [from_delimited_files.](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-delimited-files-path--validate-true--include-path-false--infer-column-types-true--set-column-types-none--separator------header-true--partition-format-none--support-multi-line-false-)

```python
from azureml.core import Dataset

iris_tabular_dataset = Dataset.Tabular.from_delimited_files([(def_blob_store, 'train-dataset/iris.csv')])
```

Os dados intermédios (ou saída de um passo) são representados por um objeto [PipelineData.](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) `output_data1`é produzido como a saída de um passo, e usado como a entrada de um ou mais passos futuros. `PipelineData`introduz uma dependência de dados entre passos, e cria uma ordem de execução implícita no oleoduto. Este objeto será utilizado mais tarde ao criar passos de gasoduto.

```python
from azureml.pipeline.core import PipelineData

output_data1 = PipelineData(
    "output_data1",
    datastore=def_blob_store,
    output_name="output_data1")
```

Mais detalhes e código de amostra para trabalhar com conjuntos de dados e dados de pipeline podem ser encontrados em mover dados para e entre as etapas do [gasoduto ML (Python)](how-to-move-data-in-out-of-pipelines.md).

## <a name="set-up-a-compute-target"></a>Configurar um alvo de cálculo

No Azure Machine Learning, o termo __computação__ (ou __alvo de cálculo)__ refere-se às máquinas ou clusters que realizam os passos computacionais no seu pipeline de aprendizagem automática.   Consulte [os alvos computacionais para o treino](how-to-set-up-training-targets.md) de modelos para uma lista completa de alvos de computação e como criá-los e anexá-los ao seu espaço de trabalho.  O processo de criação e ou fixação de um alvo computacional é o mesmo, independentemente de estar a treinar um modelo ou a executar um passo de pipeline. Depois de criar e fixar o `ComputeTarget` seu alvo de cálculo, utilize o objeto no seu passo de [pipeline](#steps).

> [!IMPORTANT]
> A realização de operações de gestão em alvos de cálculo não é suportada a partir de trabalhos remotos internos. Uma vez que os gasodutos de aprendizagem automática são submetidos como um trabalho remoto, não utilize operações de gestão em alvos de cálculo a partir de dentro do oleoduto.

Abaixo estão exemplos de criação e anexação de metas computacionais para:

* Computação do Machine Learning
* Azure Databricks 
* Azure Data Lake Analytics

### <a name="azure-machine-learning-compute"></a>Computação azure machine learning

Pode criar um computador de Aprendizagem automática Azure para executar os seus passos.

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

Azure Databricks é um ambiente baseado em Apache Spark na nuvem Azure. Pode ser usado como um alvo de computação com um oleoduto de Aprendizagem automática Azure.

Crie um espaço de trabalho Azure Databricks antes de o utilizar. Para criar um recurso espaço de trabalho, consulte o trabalho run a Spark no documento [Azure Databricks.](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal)

Para anexar os Bricks Azure como um alvo de cálculo, forneça as seguintes informações:

* __Nome computacional de databricks__: O nome que pretende atribuir a este recurso de computação.
* Nome do __espaço de trabalho databricks__: O nome do espaço de trabalho Azure Databricks.
* Ficha de acesso a __databricks__: O sinal de acesso usado para autenticar os Tijolos de Dados Azure. Para gerar um sinal de acesso, consulte o documento [autenticação.](https://docs.azuredatabricks.net/dev-tools/api/latest/authentication.html)

O seguinte código demonstra como anexar os Bricks Azure como um alvo de computação com o Azure Machine Learning SDK ( O espaço de__trabalho databricks precisa estar presente na mesma subscrição que o seu espaço__de trabalho AML ):

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

O Azure Data Lake Analytics é uma plataforma de análise de big data na nuvem Azure. Pode ser usado como um alvo de computação com um oleoduto de Aprendizagem automática Azure.

Crie uma conta Azure Data Lake Analytics antes de a utilizar. Para criar este recurso, consulte o Get started com o documento [Azure Data Lake Analytics.](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-get-started-portal)

Para anexar o Data Lake Analytics como um alvo de cálculo, deve utilizar o Azure Machine Learning SDK e fornecer as seguintes informações:

* __Nome computacional__: O nome que pretende atribuir a este recurso de computação.
* __Grupo de Recursos__: O grupo de recursos que contém a conta Data Lake Analytics.
* __Nome da conta__: O nome da conta Data Lake Analytics.

O seguinte código demonstra como anexar data lake analytics como um alvo computacional:

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
> Os oleodutos Azure Machine Learning só podem funcionar com dados armazenados na loja de dados predefinida da conta Data Lake Analytics. Se os dados com os seus dados precisarem de trabalhar [`DataTransferStep`](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep?view=azure-ml-py) estiverem numa loja não predefinida, pode utilizar um para copiar os dados antes do treino.

## <a name="construct-your-pipeline-steps"></a><a id="steps"></a>Construa os seus passos de oleoduto

Assim que criar e anexar um alvo de cálculo ao seu espaço de trabalho, está pronto para definir um passo de pipeline. Existem muitos passos incorporados disponíveis através do Azure Machine Learning SDK. O mais básico destes passos é um [PythonScriptStep,](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py)que executa um script Python num alvo computacional especificado:

```python
from azureml.pipeline.steps import PythonScriptStep

ds_input = my_dataset.as_named_input('input1')

trainStep = PythonScriptStep(
    script_name="train.py",
    arguments=["--input", ds_input.as_download(), "--output", output_data1],
    inputs=[ds_input],
    outputs=[output_data1],
    compute_target=compute_target,
    source_directory=project_folder,
    allow_reuse=True
)
```

A reutilização`allow_reuse`de resultados anteriores é fundamental quando se utilizam gasodutos num ambiente colaborativo, uma vez que eliminar repetições desnecessárias oferece agilidade. Reutilizar é o comportamento padrão quando o script_name, as inputs e os parâmetros de um passo permanecem os mesmos. Quando a saída do passo é reutilizada, o trabalho não é submetido ao cálculo, em vez disso, os resultados da execução anterior estão imediatamente disponíveis para o próximo passo. Se `allow_reuse` for definido como falso, uma nova execução será sempre gerada para este passo durante a execução do gasoduto. 

Depois de definir os seus passos, constrói o oleoduto usando alguns ou todos esses passos.

> [!NOTE]
> Nenhum ficheiro ou dados é enviado para o Azure Machine Learning quando define os passos ou constrói o pipeline.

```python
# list of steps to run
compareModels = [trainStep, extractStep, compareStep]

from azureml.pipeline.core import Pipeline

# Build the pipeline
pipeline1 = Pipeline(workspace=ws, steps=[compareModels])
```

O exemplo que se segue utiliza o alvo computacional Azure Databricks criado anteriormente: 

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

Conjuntos de dados criados a partir do armazenamento Azure Blob, Ficheiros Azure, Armazenamento de Lagos De dados Azure Gen1, Armazenamento de Lagos De dados Azure Gen2, Base de Dados Azure SQL e Base de Dados Azure para PostgreSQL podem ser usados como entrada para qualquer passo de pipeline. Com exceção da escrita da saída a [dataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep?view=azure-ml-py) ou [DatabricksStep,](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricks_step.databricksstep?view=azure-ml-py)os dados de saída[(PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py)) só podem ser escritos para as lojas de dados de partilha de dados da Azure Blob e do Ficheiro Azure.

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

Vale `Run.get_context()` a pena destacar a linha. Esta função `Run` recupera uma representação da atual execução experimental. Na amostra acima, utilizamo-la para recuperar um conjunto de dados registado. Outro uso comum do `Run` objeto é recuperar tanto a experiência em si como o espaço de trabalho em que a experiência reside: 

```python
# Within a PythonScriptStep

ws = Run.get_context().experiment.workspace
```

Para mais detalhes, incluindo formas alternativas de passar e aceder a dados, consulte a [deslocação de dados para dentro e entre os passos do gasoduto ML (Python)](how-to-move-data-in-out-of-pipelines.md).

## <a name="submit-the-pipeline"></a>Submeter o gasoduto

Quando submete o pipeline, o Azure Machine Learning verifica as dependências de cada passo e envia uma imagem do diretório de origem que especificou. Se não for especificado um diretório de origem, o atual diretório local é carregado. O instantâneo também é armazenado como parte da experiência no seu espaço de trabalho.

> [!IMPORTANT]
> Para evitar que os ficheiros sejam incluídos no `.amlignore` instantâneo, crie um [.gitignore](https://git-scm.com/docs/gitignore) ou ficheiro no diretório e adicione os ficheiros ao mesmo. O `.amlignore` ficheiro utiliza a mesma sintaxe e padrões que o ficheiro [.gitignore.](https://git-scm.com/docs/gitignore) Se ambos os `.amlignore` ficheiros existirem, o ficheiro tem precedência.
>
> Para mais informações, consulte [Snapshots](concept-azure-machine-learning-architecture.md#snapshots).

```python
from azureml.core import Experiment

# Submit the pipeline to be run
pipeline_run1 = Experiment(ws, 'Compare_Models_Exp').submit(pipeline1)
pipeline_run1.wait_for_completion()
```

Quando executa um oleoduto pela primeira vez, Azure Machine Learning:

* Descarrega o instantâneo do projeto para o alvo da computação a partir do armazenamento Blob associado ao espaço de trabalho.
* Constrói uma imagem do Docker correspondente a cada passo do oleoduto.
* Descarrega a imagem do Docker para cada passo para o alvo da computação a partir do registo de contentores.
* Configura o `Dataset` `PipelineData` acesso e os objetos. Para `as_mount()` o modo de acesso, o FUSE é utilizado para fornecer acesso virtual. Se o suporte não for suportado ou `as_download()`se o utilizador tiver especificado o acesso como , os dados são copiados para o alvo do cálculo.
* Corre o passo no alvo da computação especificado na definição de etapa. 
* Cria artefactos, tais como troncos, dstout e stderr, métricas e saída especificadas pelo passo. Estes artefactos são então carregados e mantidos na loja de dados padrão do utilizador.

![Diagrama de executar uma experiência como um oleoduto](./media/how-to-create-your-first-pipeline/run_an_experiment_as_a_pipeline.png)

Para mais informações, consulte a referência da [classe Experiment.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py)

### <a name="view-results-of-a-pipeline"></a>Ver resultados de um oleoduto

Consulte a lista de todos os seus oleodutos e os seus detalhes de execução no estúdio:

1. Inscreva-se no [estúdio Azure Machine Learning.](https://ml.azure.com)

1. [Veja o seu espaço de trabalho.](how-to-manage-workspace.md#view)

1. À esquerda, selecione **Pipelines** para ver todo o seu oleoduto.
 ![lista de gasodutos de aprendizagem automática](./media/how-to-create-your-first-pipeline/pipelines.png)
 
1. Selecione um pipeline específico para ver os resultados da execução.

## <a name="git-tracking-and-integration"></a>Rastreio e integração de Git

Quando se inicia uma corrida de formação onde o diretório de origem é um repositório local de Git, a informação sobre o repositório é armazenada na história da execução. Para mais informações, consulte a [integração de Git para Azure Machine Learning.](concept-train-model-git-integration.md)

## <a name="publish-a-pipeline"></a>Publicar um oleoduto

Pode publicar um oleoduto para executá-lo com diferentes inputs mais tarde. Para que o ponto final rest de um oleoduto já publicado aceite parâmetros, tem de parametrizar o gasoduto antes de publicar.

1. Para criar um parâmetro de gasoduto, utilize um objeto [PipelineParameter](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.pipelineparameter?view=azure-ml-py) com um valor predefinido.

   ```python
   from azureml.pipeline.core.graph import PipelineParameter
   
   pipeline_param = PipelineParameter(
     name="pipeline_arg",
     default_value=10)
   ```

2. Adicione `PipelineParameter` este objeto como parâmetro a qualquer um dos passos do oleoduto da seguinte forma:

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

Todos os oleodutos publicados têm um ponto final DE REST. Este ponto final invoca a execução do gasoduto a partir de sistemas externos, como os clientes não-Python. Este ponto final permite a "repetível gerida" em cenários de pontuação e reconversão de lotes.

Para invocar a execução do oleoduto anterior, precisa de um token de autenticação de diretório Ativo Azure, como descrito na referência da [classe AzureCliAuthentication](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.azurecliauthentication?view=azure-ml-py) ou obter mais detalhes no caderno [de autenticação em Azure Machine Learning.](https://aka.ms/pl-restep-auth)

```python
from azureml.pipeline.core import PublishedPipeline
import requests

response = requests.post(published_pipeline1.endpoint,
                         headers=aad_token,
                         json={"ExperimentName": "My_Pipeline",
                               "ParameterAssignments": {"pipeline_arg": 20}})
```

## <a name="create-a-versioned-pipeline-endpoint"></a>Criar um ponto final de pipeline versonizado

Pode criar um Pipeline Endpoint com vários oleodutos publicados por trás. Isto pode ser usado como um pipeline publicado, mas dá-lhe um ponto final DE REST fixo à medida que itera e atualiza os seus oleodutos ML.

```python
from azureml.pipeline.core import PipelineEndpoint

published_pipeline = PublishedPipeline.get(workspace="ws", name="My_Published_Pipeline")
pipeline_endpoint = PipelineEndpoint.publish(workspace=ws, name="PipelineEndpointTest",
                                            pipeline=published_pipeline, description="Test description Notebook")
```

### <a name="submit-a-job-to-a-pipeline-endpoint"></a>Submeta um trabalho para um ponto final de gasoduto

Pode submeter um trabalho à versão padrão de um ponto final de pipeline:

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

O mesmo pode ser realizado utilizando a API REST:

```python
rest_endpoint = pipeline_endpoint_by_name.endpoint
response = requests.post(rest_endpoint, 
                         headers=aad_token, 
                         json={"ExperimentName": "PipelineEndpointExperiment",
                               "RunSource": "API",
                               "ParameterAssignments": {"1": "united", "2":"city"}})
```

### <a name="use-published-pipelines-in-the-studio"></a>Use oleodutos publicados no estúdio

Você também pode executar um pipeline publicado a partir do estúdio:

1. Inscreva-se no [estúdio Azure Machine Learning.](https://ml.azure.com)

1. [Veja o seu espaço de trabalho.](how-to-manage-workspace.md#view)

1. À esquerda, selecione **Pontos Finais**.

1. No topo, selecione **pontos finais do Pipeline**.
 ![lista de pipelines publicados em machine learning](./media/how-to-create-your-first-pipeline/pipeline-endpoints.png)

1. Selecione um gasoduto específico para executar, consumir ou rever os resultados de ensaios anteriores do ponto final do gasoduto.

### <a name="disable-a-published-pipeline"></a>Desativar um oleoduto publicado

Para ocultar um oleoduto da sua lista de oleodutos publicados, desativa-o, quer no estúdio, quer no SDK:

```python
# Get the pipeline by using its ID from Azure Machine Learning studio
p = PublishedPipeline.get(ws, id="068f4885-7088-424b-8ce2-eeb9ba5381a6")
p.disable()
```

Pode voltar a `p.enable()`ativar com . Para mais informações, consulte a referência da [classe PublishedPipeline.](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.publishedpipeline?view=azure-ml-py)

## <a name="caching--reuse"></a>Reutilização de & de caching  

Para otimizar e personalizar o comportamento dos seus oleodutos, pode fazer algumas coisas em torno do cache e reutilizar. Por exemplo, pode optar por:
+ **Desligue a reutilização predefinida da saída de execução do passo,** definindo `allow_reuse=False` durante a [definição](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py)do passo . A reutilização é fundamental quando se utilizam oleodutos num ambiente colaborativo, uma vez que eliminar corridas desnecessárias oferece agilidade. No entanto, pode optar por não reutilizar.
+ **Regeneração da produção de força para todos os passos em uma corrida** com`pipeline_run = exp.submit(pipeline, regenerate_outputs=False)`

Por defeito, `allow_reuse` para os `source_directory` passos está ativado e o especificado na definição de etapa é hashed. Assim, se o guião para um`script_name`determinado passo permanecer o mesmo ( inputs` source_directory` e os parâmetros), e nada mais mudou, a saída de um passo anterior é reutilizada, o trabalho não é submetido ao cálculo, e os resultados da execução anterior estão imediatamente disponíveis para o passo seguinte.

```python
step = PythonScriptStep(name="Hello World",
                        script_name="hello_world.py",
                        compute_target=aml_compute,
                        source_directory=source_directory,
                        allow_reuse=False,
                        hash_paths=['hello_world.ipynb'])
```

## <a name="next-steps"></a>Passos seguintes

- Use [estes cadernos Jupyter no GitHub](https://aka.ms/aml-pipeline-readme) para explorar ainda mais os gasodutos de aprendizagem automática.
- Consulte a ajuda de referência sdk para o pacote de núcleo de [gasodutos em azureml](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) e o pacote de [passos de gasodutos em azul.](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py)
- Veja como obter dicas sobre depuração e resolução [de](how-to-debug-pipelines.md) problemas.

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]
