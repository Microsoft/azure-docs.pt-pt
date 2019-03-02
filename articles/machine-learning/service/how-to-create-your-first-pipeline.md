---
title: Criar, executar e monitorizar pipelines de ML
titleSuffix: Azure Machine Learning service
description: Criar e executar uma pipeline com o SDK do Azure Machine Learning de aprendizagem para Python. Utilize pipelines para criar e gerir os fluxos de trabalho que fases de aprendizagem automática em conjunto inserir manualmente (ML). Estas fases incluem inferência, implementação do modelo, preparação de modelos e preparação de dados.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: sgilley
ms.author: sanpil
author: sanpil
ms.date: 01/08/2019
ms.custom: seodec18
ms.openlocfilehash: b1544d73478f30c7501e76a8e20975f975f9708d
ms.sourcegitcommit: c712cb5c80bed4b5801be214788770b66bf7a009
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/01/2019
ms.locfileid: "57215052"
---
# <a name="create-and-run-a-machine-learning-pipeline-by-using-azure-machine-learning-sdk"></a>Criar e executar um pipeline de machine learning com o Azure Machine Learning SDK

Neste artigo, irá aprender a criar, publicar, executar e controlar um [pipeline de machine learning](concept-ml-pipelines.md) utilizando o [do Azure Machine Learning SDK](https://aka.ms/aml-sdk).  Esses pipelines ajudam a criar e gerir os fluxos de trabalho que reunir várias fases de aprendizado de máquina. Cada fase de um pipeline, por exemplo, a preparação de dados e preparação de modelos, pode incluir um ou mais passos.

Os pipelines que cria são visíveis para os membros do seu serviço Azure Machine Learning [área de trabalho](how-to-manage-workspace.md). 

Pipelines usar destinos de computação remoto para computação e o armazenamento dos dados intermediários e finais associados a esse pipeline. Pipelines podem ler e suporte de dados de escrita de e para [armazenamento do Azure](https://docs.microsoft.com/azure/storage/) localizações.

Se não tiver uma subscrição do Azure, crie uma conta gratuita antes de começar. Experimente o [uma versão gratuita ou paga do serviço Azure Machine Learning](http://aka.ms/AMLFree).

## <a name="prerequisites"></a>Pré-requisitos

* [Configurar o ambiente de desenvolvimento](how-to-configure-environment.md) para instalar o SDK do Azure Machine Learning.

* Criar uma [área de trabalho do Azure Machine Learning](how-to-configure-environment.md#workspace) para conter todos os seus recursos do pipeline. 

 ```python
 ws = Workspace.create(
     name = '<workspace-name>',
     subscription_id = '<subscription-id>',
     resource_group = '<resource-group>',
     location = '<workspace_region>',
     exist_ok = True)
 ```

## <a name="set-up-machine-learning-resources"></a>Configurar recursos de aprendizado de máquina

Crie os recursos necessários para executar um pipeline:

* Configure um arquivo de dados usado para acessar os dados necessários nos passos de pipeline.

* Configurar um `DataReference` de objeto para apontar para dados que residem na ou estão acessível num arquivo de dados.

* Configurar o [destinos de computação](concept-azure-machine-learning-architecture.md#compute-target) em que executará as etapas do pipeline.

### <a name="set-up-a-datastore"></a>Configurar um arquivo de dados
Um arquivo de dados armazena os dados para o pipeline aceder. Cada área de trabalho tem um arquivo de dados padrão. Pode registrar os arquivos de dados adicionais. 

Quando cria a área de trabalho, [ficheiros do Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) e [armazenamento de Blobs do Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) estão anexados à área de trabalho, por predefinição. O Azure Files é o arquivo de dados predefinida para uma área de trabalho, mas também pode utilizar o armazenamento de BLOBs como um arquivo de dados. Para obter mais informações, consulte [decidindo quando usar os ficheiros do Azure, Blobs do Azure ou discos do Azure para](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks). 

```python
# Default datastore (Azure file storage)
def_data_store = ws.get_default_datastore() 

# The above call is equivalent to this 
def_data_store = Datastore(ws, "workspacefilestore")

# Get blob storage associated with the workspace
def_blob_store = Datastore(ws, "workspaceblobstore")
```

Carregar ficheiros de dados ou diretórios para o arquivo de dados para que sejam acessíveis a partir de seus pipelines. Este exemplo utiliza a versão de armazenamento de Blobs do arquivo de dados:

```python
def_blob_store.upload_files(
    ["./data/20news.pkl"],
    target_path="20newsgroups", 
    overwrite=True)
```

Um pipeline consiste num ou mais passos. Um passo é uma unidade executada num destino de computação. Passos podem consumir origens de dados e produzir dados de "intermediários". Um passo pode criar dados como um modelo, um diretório com o modelo e os arquivos dependentes ou dados temporários. Estes dados, em seguida, estão disponíveis para outros passos mais tarde no pipeline.

### <a name="configure-data-reference"></a>Configurar a referência de dados

Acabou de criar uma origem de dados que pode ser referenciada num pipeline como entrada para um passo. Uma origem de dados num pipeline é representada por um [DataReference](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference) objeto. O `DataReference` objeto aponta para dados que residem ou estão acessível a partir de um arquivo de dados.

```python
blob_input_data = DataReference(
    datastore=def_blob_store,
    data_reference_name="test_data",
    path_on_datastore="20newsgroups/20news.pkl")
```

Dados intermédios (ou de saída de um passo) é representada por um [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) objeto. `output_data1` é produzido como o resultado de um passo e utilizado como entrada de um ou mais passos futuras. `PipelineData` Apresenta uma dependência de dados entre passos e cria uma ordem de execução implícita no pipeline.

```python
output_data1 = PipelineData(
    "output_data1",
    datastore=def_blob_store,
    output_name="output_data1")
```

## <a name="set-up-compute-target"></a>Configurar o destino de computação

No Azure Machine Learning, o termo __computação__ (ou __destino de computação__) refere-se a computadores ou clusters que executam os passos de computacionais no seu pipeline de machine learning.   Ver [destinos de computação para a preparação de modelos](how-to-set-up-training-targets.md) para obter uma lista completa de destinos de computação e de como criar e anexe-os a sua área de trabalho.  O processo para criar e ou anexar um destino de computação é o mesmo, independentemente se estiver a preparar um modelo ou a executar um passo de pipeline. Depois de criar e anexar o destino de computação, utilize o `ComputeTarget` objeto em seu [passo pipeline](#steps).

> [!IMPORTANT]
> Realizar operações de gestão nos destinos de computação não é suportado de conteúdo remoto de tarefas. Uma vez que os pipelines de aprendizagem são submetidas como uma tarefa remota, não utilize operações de gestão de destinos de computação de dentro do pipeline.

Seguem-se exemplos de criação e anexar destinos de computação para:

* Computação do Azure Machine Learning
* Azure Databricks 
* Azure Data Lake Analytics

### <a name="azure-machine-learning-compute"></a>Computação do Azure Machine Learning

Pode criar uma computação do Azure Machine Learning para executar os passos.

```python
compute_name = "aml-compute"
 if compute_name in ws.compute_targets:
    compute_target = ws.compute_targets[compute_name]
    if compute_target and type(compute_target) is AmlCompute:
        print('Found compute target: ' + compute_name)
else:
    print('Creating a new compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(vm_size = vm_size, # NC6 is GPU-enabled
                                                                min_nodes = 1, 
                                                                max_nodes = 4)
     # create the compute target
    compute_target = ComputeTarget.create(ws, compute_name, provisioning_config)
    
    # Can poll for a minimum number of nodes and for a specific timeout. 
    # If no min node count is provided it will use the scale settings for the cluster
    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
    
     # For a more detailed view of current cluster status, use the 'status' property    
    print(compute_target.status.serialize())
```

### <a id="databricks"></a>O Azure Databricks

O Azure Databricks é um ambiente baseado em Apache Spark na cloud do Azure. Ele pode ser usado como um destino de computação com um pipeline do Azure Machine Learning.

Crie uma área de trabalho do Azure Databricks antes de o utilizar. Para criar esses recursos, consulte a [executar uma tarefa do Spark no Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal) documento.

Para anexar o Azure Databricks, como um destino de computação, forneça as seguintes informações:

* __Nome de computação do Databricks__: O nome que pretende atribuir a este recurso de computação.
* __Nome de área de trabalho do Databricks__: O nome da área de trabalho do Azure Databricks.
* __Token de acesso do Databricks__: O token de acesso utilizado para autenticar para o Azure Databricks. Para gerar um token de acesso, consulte a [autenticação](https://docs.azuredatabricks.net/api/latest/authentication.html) documento.

O código a seguir demonstra como anexar o Azure Databricks como um destino de computação com o SDK do Azure Machine Learning:

```python
import os
from azureml.core.compute import ComputeTarget, DatabricksCompute
from azureml.exceptions import ComputeTargetException

databricks_compute_name = os.environ.get("AML_DATABRICKS_COMPUTE_NAME", "<databricks_compute_name>")
databricks_workspace_name = os.environ.get("AML_DATABRICKS_WORKSPACE", "<databricks_workspace_name>")
databricks_resource_group = os.environ.get("AML_DATABRICKS_RESOURCE_GROUP", "<databricks_resource_group>")
databricks_access_token = os.environ.get("AML_DATABRICKS_ACCESS_TOKEN", "<databricks_access_token>")

try:
    databricks_compute = ComputeTarget(workspace=ws, name=databricks_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('databricks_compute_name {}'.format(databricks_compute_name))
    print('databricks_workspace_name {}'.format(databricks_workspace_name))
    print('databricks_access_token {}'.format(databricks_access_token))

    # Create attach config
    attach_config = DatabricksCompute.attach_configuration(resource_group = databricks_resource_group,
                                                           workspace_name = databricks_workspace_name,
                                                           access_token = databricks_access_token)
    databricks_compute = ComputeTarget.attach(
             ws,
             databricks_compute_name,
             attach_config
         )
    
    databricks_compute.wait_for_completion(True)
```
### <a id="adla"></a>Azure Data Lake Analytics

O Azure Data Lake Analytics é uma plataforma de análise de macrodados na cloud do Azure. Ele pode ser usado como um destino de computação com um pipeline do Azure Machine Learning.

Crie uma conta do Azure Data Lake Analytics antes de o utilizar. Para criar este recurso, consulte a [introdução ao Azure Data Lake Analytics](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-get-started-portal) documento.

Para anexar como um destino de computação do Data Lake Analytics, tem de utilizar o SDK do Azure Machine Learning e forneça as seguintes informações:

* __Nome de computação__: O nome que pretende atribuir a este recurso de computação.
* __Grupo de recursos__: O grupo de recursos que contém a conta do Data Lake Analytics.
* __Nome da conta__: O nome da conta do Data Lake Analytics.

O código a seguir demonstra como anexar como um destino de computação do Data Lake Analytics:

```python
import os
from azureml.core.compute import ComputeTarget, AdlaCompute
from azureml.exceptions import ComputeTargetException


adla_compute_name = os.environ.get("AML_ADLA_COMPUTE_NAME", "<adla_compute_name>")
adla_resource_group = os.environ.get("AML_ADLA_RESOURCE_GROUP", "<adla_resource_group>")
adla_account_name = os.environ.get("AML_ADLA_ACCOUNT_NAME", "<adla_account_name>")

try:
    adla_compute = ComputeTarget(workspace=ws, name=adla_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('adla_compute_name {}'.format(adla_compute_name))
    print('adla_resource_id {}'.format(adla_resource_group))
    print('adla_account_name {}'.format(adla_account_name))
    # create attach config
    attach_config = AdlaCompute.attach_configuration(resource_group = adla_resource_group,
                                                     account_name = adla_account_name)
    # Attach ADLA
    adla_compute = ComputeTarget.attach(
             ws,
             adla_compute_name,
             attach_config
         )
    
    adla_compute.wait_for_completion(True)
```

> [!TIP]
> O Azure Machine Learning pipelines só podem trabalhar com dados armazenados no arquivo de dados predefinido da conta do Data Lake Analytics. Se os dados tiver de com seja de um arquivo de não-padrão, pode utilizar um [ `DataTransferStep` ](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep?view=azure-ml-py) para copiar os dados antes de treinamento.

## <a id="steps"></a>Construir suas etapas de pipeline

Depois de criar e anexar um destino de computação à sua área de trabalho, está pronto para definir um passo de pipeline. Existem muitos passos internos disponível através do SDK do Azure Machine Learning. As mais básicas destes passos é uma [PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py), que executa um script de Python num destino de computação especificado:

```python
trainStep = PythonScriptStep(
    script_name="train.py",
    arguments=["--input", blob_input_data, "--output", processed_data1],
    inputs=[blob_input_data],
    outputs=[processed_data1],
    compute_target=compute_target,
    source_directory=project_folder
)
```

Depois de definir suas etapas, criar o pipeline com alguns ou todos esses passos.

>[!NOTE]
>Não existem dados ou ficheiro são carregados para o serviço Azure Machine Learning quando definir os passos ou criar o pipeline.

```python
# list of steps to run
compareModels = [trainStep, extractStep, compareStep]

# Build the pipeline
pipeline1 = Pipeline(workspace=ws, steps=[compareModels])
```

O exemplo seguinte utiliza o destino de computação do Azure Databricks criado anteriormente: 

```python
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

Para obter mais informações, consulte a [pacote de passos de pipeline do azure](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py) e [Pipeline classe](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline%28class%29?view=azure-ml-py) referência.

## <a name="submit-the-pipeline"></a>Submeter o pipeline

Quando submete o pipeline, o serviço Azure Machine Learning verifica as dependências para cada passo e carrega um instantâneo do diretório de origem que especificou. Se não for especificado nenhum diretório de origem, o atual diretório de local é carregado.


```python
# Submit the pipeline to be run
pipeline_run1 = Experiment(ws, 'Compare_Models_Exp').submit(pipeline1)
pipeline_run1.wait_for_completion()
```

Quando executa primeiro um pipeline, o Azure Machine Learning:

* Transfere o instantâneo de projeto para o destino de computação do armazenamento de BLOBs associado com a área de trabalho.
* Cria uma imagem do Docker correspondente a cada etapa no pipeline.
* Transfere a imagem do docker para cada passo para o destino de computação do container registry.
* Monta o arquivo de dados, se um `DataReference` objeto foi especificado um passo. Se não é suportada a montagem, os dados em vez disso, são copiados para o destino de computação.
* Executa o passo no destino de computação especificado na definição do passo. 
* Cria artefatos, como registos, stdout e stderr, métricas e especificado pelo passo de saída. Estes artefactos, em seguida, são carregados e mantidos no armazenamento de dados do usuário padrão.

![Diagrama da execução de uma experimentação como um pipeline](./media/how-to-create-your-first-pipeline/run_an_experiment_as_a_pipeline.png)

Para obter mais informações, consulte a [experimentar classe](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py) referência.

## <a name="publish-a-pipeline"></a>Publicar um pipeline

Pode publicar um pipeline para executá-lo mais tarde com entradas diferentes. Para o ponto final REST de um pipeline já publicado para aceitar parâmetros, tem de parametrizar o pipeline antes de publicar. 

1. Para criar um parâmetro de pipeline, utilize um [PipelineParameter](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.pipelineparameter?view=azure-ml-py) objeto com um valor predefinido.

 ```python
 pipeline_param = PipelineParameter(
     name="pipeline_arg", 
     default_value=10)
 ```

2. Adicionar isso `PipelineParameter` objeto como um parâmetro para qualquer uma das etapas no pipeline da seguinte forma:

 ```python
 compareStep = PythonScriptStep(
     script_name="compare.py",
     arguments=["--comp_data1", comp_data1, "--comp_data2", comp_data2, "--output_data", out_data3, "--param1", pipeline_param],
     inputs=[ comp_data1, comp_data2],
     outputs=[out_data3],    
     target=compute_target, 
     source_directory=project_folder)
 ```

3. Publica este pipeline, que aceita um parâmetro, quando invocado.

   ```python
   published_pipeline1 = pipeline1.publish(
       name="My_Published_Pipeline", 
       description="My Published Pipeline Description")
   ```

## <a name="run-a-published-pipeline"></a>Executar um pipeline publicado

Todos os pipelines de publicação tem um ponto final REST. Este ponto final invoca a execução do pipeline de sistemas externos, tais como clientes de não-Python. Este ponto final permite "geridos repetibilidade" no lote de classificação e cenários de reparametrização.

Para invocar a execução do pipeline anterior, tem um token de cabeçalho de autenticação do Azure Active Directory, conforme descrito em [classe AzureCliAuthentication](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.azurecliauthentication?view=azure-ml-py) ou obter mais detalhes em [autenticação numa máquina do Azure Aprendizagem](https://aka.ms/pl-restep-auth) bloco de notas.

```python
response = requests.post(published_pipeline1.endpoint, 
    headers=aad_token, 
    json={"ExperimentName": "My_Pipeline",
        "ParameterAssignments": {"pipeline_arg": 20}})
```
## <a name="view-results"></a>Ver resultados

Ver a lista de todos os seus pipelines e os respetivos detalhes de execução:
1. Inicie sessão no [portal do Azure](https://portal.azure.com/).  

1. [Ver a sua área de trabalho](how-to-manage-workspace.md#view-a-workspace) para encontrar a lista de pipelines.
 ![lista de pipelines de aprendizagem](./media/how-to-create-your-first-pipeline/list_of_pipelines.png)
 
1. Selecione um pipeline específico para ver os resultados de execução.

## <a name="next-steps"></a>Passos Seguintes
- Uso [estes blocos de notas do Jupyter no GitHub](https://aka.ms/aml-pipeline-readme) para explorar ainda mais pipelines do machine learning.
- Ler a ajuda de referência do SDK para o [azureml pipelines-núcleo](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) pacote e o [passos de pipelines azureml](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py) pacote.

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]
