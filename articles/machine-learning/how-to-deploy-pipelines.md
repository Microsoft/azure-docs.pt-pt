---
title: Publicar oleodutos ML
titleSuffix: Azure Machine Learning
description: Executar fluxos de trabalho de aprendizagem automática com oleodutos de aprendizagem automática e o Azure Machine Learning SDK para Python.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: sgilley
ms.author: laobri
author: lobrien
ms.date: 8/25/2020
ms.topic: conceptual
ms.custom: how-to, contperfq1
ms.openlocfilehash: 46a5f4036be2d670689f7e936a31dc63e0690ddc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91302388"
---
# <a name="publish-and-track-machine-learning-pipelines"></a>Publicar e rastrear gasodutos de aprendizagem de máquinas



Este artigo irá mostrar-lhe como partilhar um oleoduto de aprendizagem automática com os seus colegas ou clientes.

Os gasodutos de aprendizagem automática são fluxos de trabalho reutilizáveis para tarefas de aprendizagem automática. Um dos benefícios dos gasodutos é o aumento da colaboração. Também pode verr os oleodutos, permitindo que os clientes utilizem o modelo atual enquanto está a trabalhar numa nova versão. 

## <a name="prerequisites"></a>Pré-requisitos

* Crie um espaço de trabalho para [aprendizagem automática Azure](how-to-manage-workspace.md) para manter todos os seus recursos de pipeline

* [Configure o seu ambiente de desenvolvimento](how-to-configure-environment.md) para instalar o Azure Machine Learning SDK, ou use uma instância [computacional de aprendizagem automática Azure](concept-compute-instance.md) com o SDK já instalado

* Crie e executar um pipeline de aprendizagem automática, como por exemplo seguindo [Tutorial: Construa um oleoduto Azure Machine Learning para pontuação de lotes](tutorial-pipeline-batch-scoring-classification.md). Para outras opções, consulte [Criar e executar pipelines de aprendizagem automática com Azure Machine Learning SDK](how-to-create-your-first-pipeline.md)

## <a name="publish-a-pipeline"></a>Publicar um oleoduto

Uma vez que tenha um oleoduto em funcionamento, pode publicar um oleoduto para que este corra com diferentes entradas. Para que o ponto final do ME de um oleoduto já publicado aceite parâmetros, deve configurar o seu oleoduto para utilizar `PipelineParameter` objetos para os argumentos que variarão.

1. Para criar um parâmetro de pipeline, utilize um objeto [PipelineParameter](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.pipelineparameter?view=azure-ml-py&preserve-view=true) com um valor predefinido.

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

## <a name="run-a-published-pipeline"></a>Executar um oleoduto publicado

Todos os oleodutos publicados têm um ponto final REST. Com o ponto final do gasoduto, pode desencadear uma corrida do oleoduto a partir de quaisquer sistemas externos, incluindo clientes não-Python. Este ponto final permite a "repetibilidade gerida" em cenários de pontuação e reconversão de lotes.

Para invocar o funcionamento do oleoduto anterior, precisa de um sinal de cabeçalho de autenticação Azure Ative. Obter tal token é descrito na referência da [classe AzureCliAuthentication](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.azurecliauthentication?view=azure-ml-py&preserve-view=true) e na autenticação no caderno [Azure Machine Learning.](https://aka.ms/pl-restep-auth)

```python
from azureml.pipeline.core import PublishedPipeline
import requests

response = requests.post(published_pipeline1.endpoint,
                         headers=aad_token,
                         json={"ExperimentName": "My_Pipeline",
                               "ParameterAssignments": {"pipeline_arg": 20}})
```

O `json` argumento do pedido DOM deve conter, para a `ParameterAssignments` chave, um dicionário que contenha os parâmetros do gasoduto e os seus valores. Além disso, o `json` argumento pode conter as seguintes teclas:

| Chave | Descrição |
| --- | --- | 
| `ExperimentName` | O nome da experiência associada a este ponto final |
| `Description` | Texto freeform descrevendo o ponto final | 
| `Tags` | Pares de valor-chave freeform que podem ser usados para rotular e anotar pedidos  |
| `DataSetDefinitionValueAssignments` | Dicionário utilizado para alterar conjuntos de dados sem requalificação (ver discussão abaixo) | 
| `DataPathAssignments` | Dicionário utilizado para alterar datapaths sem requalificação (ver discussão abaixo) | 

### <a name="changing-datasets-and-datapaths-without-retraining"></a>Alteração de conjuntos de dados e datapats sem reconversão

Pode querer treinar e inferir diferentes conjuntos de dados e datapats. Por exemplo, pode desejar treinar num conjunto de dados mais pequeno e escasso, mas inferência no conjunto de dados completo. Troque os conjuntos de dados com `DataSetDefinitionValueAssignments` a chave no argumento do `json` pedido. Muda-se os caminhos de dados com `DataPathAssignments` . A técnica para ambos é semelhante:

1. No seu script de definição de pipeline, crie um `PipelineParameter` para o conjunto de dados. Criar a `DatasetConsumptionConfig` ou `DataPath` a partir `PipelineParameter` do:

    ```python
    tabular_dataset = Dataset.Tabular.from_delimited_files('https://dprepdata.blob.core.windows.net/demo/Titanic.csv')
    tabular_pipeline_param = PipelineParameter(name="tabular_ds_param", default_value=tabular_dataset)
    tabular_ds_consumption = DatasetConsumptionConfig("tabular_dataset", tabular_pipeline_param)
    ```

1. No seu script ML, aceda ao conjunto de dados especificado dinamicamente utilizando `Run.get_context().input_datasets` :

    ```python
    from azureml.core import Run
    
    input_tabular_ds = Run.get_context().input_datasets['tabular_dataset']
    dataframe = input_tabular_ds.to_pandas_dataframe()
    # ... etc ...
    ```

    Note que o script ML acede ao valor especificado para o `DatasetConsumptionConfig` ( ) e não o valor do ( `tabular_dataset` `PipelineParameter` `tabular_ds_param` ).

1. No seu roteiro de definição de pipeline, desafine o `DatasetConsumptionConfig` parâmetro `PipelineScriptStep` para:

    ```python
    train_step = PythonScriptStep(
        name="train_step",
        script_name="train_with_dataset.py",
        arguments=["--param1", tabular_ds_consumption],
        inputs=[tabular_ds_consumption],
        compute_target=compute_target,
        source_directory=source_directory)
    
    pipeline = Pipeline(workspace=ws, steps=[train_step])
    ```

1. Para mudar os conjuntos de dados de forma dinâmica na sua chamada DE DESCANSO inferencionante, `DataSetDefinitionValueAssignments` utilize:
    
    ```python
    tabular_ds1 = Dataset.Tabular.from_delimited_files('path_to_training_dataset')
    tabular_ds2 = Dataset.Tabular.from_delimited_files('path_to_inference_dataset')
    ds1_id = tabular_ds1.id
    d22_id = tabular_ds2.id
    
    response = requests.post(rest_endpoint, 
                             headers=aad_token, 
                             json={
                                "ExperimentName": "MyRestPipeline",
                               "DataSetDefinitionValueAssignments": {
                                    "tabular_ds_param": {
                                        "SavedDataSetReference": {"Id": ds1_id #or ds2_id
                                    }}}})
    ```

Os cadernos [que mostram dataset e PipelineParameter](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-showcasing-dataset-and-pipelineparameter.ipynb) e [Apresentando DataPath e PipelineParameter](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-showcasing-datapath-and-pipelineparameter.ipynb) têm exemplos completos desta técnica.

## <a name="create-a-versioned-pipeline-endpoint"></a>Criar um ponto final de pipeline em versão

Pode criar um Pipeline Endpoint com vários oleodutos publicados por trás. Isto dá-lhe um ponto final de REST fixo à medida que se emite e atualiza os seus gasodutos ML.

```python
from azureml.pipeline.core import PipelineEndpoint

published_pipeline = PipelineEndpoint.get(workspace=ws, name="My_Published_Pipeline")
pipeline_endpoint = PipelineEndpoint.publish(workspace=ws, name="PipelineEndpointTest",
                                            pipeline=published_pipeline, description="Test description Notebook")
```

## <a name="submit-a-job-to-a-pipeline-endpoint"></a>Submeter um trabalho a um ponto final de oleoduto

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

## <a name="use-published-pipelines-in-the-studio"></a>Use oleodutos publicados no estúdio

Também pode executar um oleoduto publicado a partir do estúdio:

1. Inscreva-se no [estúdio Azure Machine Learning](https://ml.azure.com).

1. [Veja o seu espaço de trabalho.](how-to-manage-workspace.md#view)

1. À esquerda, selecione **Endpoints**.

1. Por cima, selecione **os pontos finais do Pipeline**.
 ![lista de oleodutos publicados machine learning](./media/how-to-create-your-first-pipeline/pipeline-endpoints.png)

1. Selecione um gasoduto específico para executar, consumir ou rever os resultados de execuções anteriores do ponto final do gasoduto.

## <a name="disable-a-published-pipeline"></a>Desativar um oleoduto publicado

Para esconder um oleoduto da sua lista de oleodutos publicados, desativa-o, quer no estúdio, quer no SDK:

```python
# Get the pipeline by using its ID from Azure Machine Learning studio
p = PublishedPipeline.get(ws, id="068f4885-7088-424b-8ce2-eeb9ba5381a6")
p.disable()
```

Pode voltar a ative-lo com `p.enable()` . Para mais informações, consulte a referência [da classe PublishedPipeline.](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.publishedpipeline?view=azure-ml-py&preserve-view=true)

## <a name="next-steps"></a>Passos seguintes

- Use [estes cadernos Jupyter no GitHub](https://aka.ms/aml-pipeline-readme) para explorar ainda mais os oleodutos de aprendizagem automática.
- Consulte a ajuda de referência SDK para o pacote [de núcleo de gasodutos azureml](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py&preserve-view=true) e o pacote [de passos de gasodutos azureml.](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py&preserve-view=true)
- Consulte o ["how-to"](how-to-debug-pipelines.md) para obter dicas sobre depuração e resolução de problemas.
