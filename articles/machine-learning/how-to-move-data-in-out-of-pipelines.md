---
title: Dados de deslocação em gasodutos ML
titleSuffix: Azure Machine Learning
description: Saiba mais sobre a entrada & produção de dados em oleodutos Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: laobri
author: lobrien
ms.date: 08/20/2020
ms.topic: conceptual
ms.custom: how-to, contperfq4, devx-track-python
ms.openlocfilehash: 195c334500c8c540d819e949353b34bea65b3d4f
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92741902"
---
# <a name="moving-data-into-and-between-ml-pipeline-steps-python"></a>Mover dados para e entre os passos de pipeline de ML (Python)



Este artigo fornece código para importar, transformar e mover dados entre etapas num oleoduto de Aprendizagem automática Azure. Para uma visão geral de como os dados funcionam no Azure Machine Learning, consulte [os dados do Access nos serviços de armazenamento Azure](how-to-access-data.md). Para os benefícios e estrutura dos oleodutos Azure Machine Learning, veja [o que são os oleodutos Azure Machine Learning?](concept-ml-pipelines.md)

Este artigo irá mostrar-lhe como:

- Utilize `Dataset` objetos para dados pré-existentes
- Aceder aos dados dentro dos seus passos
- Dividir `Dataset` dados em subconjuntos, tais como subconjuntos de treino e validação
- Criar `PipelineData` objetos para transferir dados para o próximo passo do pipeline
- Use `PipelineData` objetos como entrada para passos de gasoduto
- Crie novos `Dataset` objetos a partir do `PipelineData` seu desejo de persistir

> [!TIP]
> Nas aulas de pré-visualização do gasoduto está disponível uma experiência melhorada para a passagem de dados temporários entre etapas de gasoduto e persistência dos seus dados após a execução do gasoduto nas aulas de pré-visualização  [`OutputFileDatasetConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.outputfiledatasetconfig?view=azure-ml-py&preserve-view=true) públicas, e [`OutputTabularDatasetConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.output_dataset_config.outputtabulardatasetconfig?view=azure-ml-py&preserve-view=true) .  Estas aulas são funcionalidades [experimentais](https://docs.microsoft.com/python/api/overview/azure/ml/?view=azure-ml-py&preserve-view=true#&preserve-view=truestable-vs-experimental) de pré-visualização, e podem mudar a qualquer momento.


## <a name="prerequisites"></a>Pré-requisitos

Precisará:

- Uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma conta gratuita antes de começar. Experimente a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree).

- O [Azure Machine Learning SDK para Python,](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true)ou acesso ao [estúdio Azure Machine Learning.](https://ml.azure.com/)

- Uma área de trabalho do Azure Machine Learning.
  
  Ou [cria um espaço de trabalho de aprendizagem automática Azure](how-to-manage-workspace.md) ou utiliza um existente através do Python SDK. Importe a `Workspace` classe e `Datastore` carregue as suas informações de subscrição do ficheiro `config.json` utilizando a função `from_config()` . Esta função procura o ficheiro JSON no diretório atual por predefinição, mas também pode especificar um parâmetro de caminho para apontar para o ficheiro usando `from_config(path="your/file/path")` .

   ```python
   import azureml.core
   from azureml.core import Workspace, Datastore
        
   ws = Workspace.from_config()
   ```

- Alguns dados pré-existentes. Este artigo mostra brevemente a utilização de um [recipiente de bolhas Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview).

- Opcional: Um oleoduto de aprendizagem automática existente, como o descrito em [Criar e executar gasodutos de aprendizagem automática com Azure Machine Learning SDK](how-to-create-your-first-pipeline.md).

## <a name="use-dataset-objects-for-pre-existing-data"></a>Utilize `Dataset` objetos para dados pré-existentes 

A forma preferida de ingerir dados num oleoduto é utilizar um objeto [Dataset.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py&preserve-view=true) `Dataset` os objetos representam dados persistentes disponíveis em todo um espaço de trabalho.

Há muitas formas de criar e registar `Dataset` objetos. Os conjuntos de dados tabulares destinam-se a dados delimitados disponíveis num ou mais ficheiros. Os conjuntos de dados de ficheiros são para dados binários (como imagens) ou para dados que irá analisar. As formas programáticas mais simples de criar `Dataset` objetos são usar bolhas existentes no armazenamento do espaço de trabalho ou URLs públicos:

```python
datastore = Datastore.get(workspace, 'training_data')
iris_dataset = Dataset.Tabular.from_delimited_files(DataPath(datastore, 'iris.csv'))

cats_dogs_dataset = Dataset.File.from_files(
    paths='https://download.microsoft.com/download/3/E/1/3E1C3F21-ECDB-4869-8368-6DEBA77B919F/kagglecatsanddogs_3367a.zip',
    archive_options=ArchiveOptions(archive_type=ArchiveType.ZIP, entry_glob='**/*.jpg')
)
```

Para mais opções sobre a criação de conjuntos de dados com diferentes opções e de diferentes fontes, registá-los e revê-los na UI de Aprendizagem de Máquinas Azure, compreender como o tamanho dos dados interage com a capacidade de computação, e ver [conjuntos de dados de Aprendizagem de Máquinas Azure.](how-to-create-register-datasets.md) 

### <a name="pass-datasets-to-your-script"></a>Passe conjuntos de dados para o seu script

Para passar o caminho do conjunto de dados para o seu script, use o `Dataset` método do `as_named_input()` objeto. Pode passar o objeto resultante para o `DatasetConsumptionConfig` seu script como argumento ou, utilizando o argumento para o seu script de `inputs` pipeline, pode recuperar o conjunto de dados utilizando `Run.get_context().input_datasets[]` .

Uma vez criada uma entrada com o nome, pode escolher o seu modo de acesso: `as_mount()` ou `as_download()` . Se o seu script processar todos os ficheiros do seu conjunto de dados e o disco no seu recurso computacional for suficientemente grande para o conjunto de dados, o modo de acesso ao download é a melhor escolha. O modo de acesso ao descarregamento evitará a sobrecarga de transmissão dos dados em tempo de execução. Se o seu script aceder a um subconjunto do conjunto de dados ou for demasiado grande para o seu cálculo, utilize o modo de acesso de montagem. Para mais informações, leia [Mount vs. Download](https://docs.microsoft.com/azure/machine-learning/how-to-train-with-datasets#mount-vs-download)

Para passar um conjunto de dados para o seu passo de pipeline:

1. Use `TabularDataset.as_named_input()` ou `FileDataset.as_named_input()` (não 's' no final) para criar um `DatasetConsumptionConfig` objeto
1. Utilizar `as_mount()` ou definir o modo de `as_download()` acesso
1. Passe os conjuntos de dados para os seus passos de pipeline usando o `arguments` argumento ou o `inputs` argumento

O seguinte corte mostra o padrão comum de combinar estes passos dentro do `PythonScriptStep` construtor: 

```python

train_step = PythonScriptStep(
    name="train_data",
    script_name="train.py",
    compute_target=cluster,
    inputs=[iris_dataset.as_named_input('iris').as_mount()]
)
```

> [!NOTE]
> Precisaria de substituir os valores por todos estes argumentos (isto é, `"train_data"` `"train.py"` , `cluster` `iris_dataset` e) pelos seus próprios dados. O snippet acima apenas mostra a forma da chamada e não faz parte de uma amostra da Microsoft. 

Também pode utilizar métodos como `random_split()` e `take_sample()` criar múltiplas entradas ou reduzir a quantidade de dados passados para o seu passo de pipeline:

```python
seed = 42 # PRNG seed
smaller_dataset = iris_dataset.take_sample(0.1, seed=seed) # 10%
train, test = smaller_dataset.random_split(percentage=0.8, seed=seed)

train_step = PythonScriptStep(
    name="train_data",
    script_name="train.py",
    compute_target=cluster,
    inputs=[train.as_named_input('train').as_download(), test.as_named_input('test').as_download()]
)
```

### <a name="access-datasets-within-your-script"></a>Aceder a conjuntos de dados dentro do seu script

As entradas nomeadas para o seu script de etapa de gasoduto estão disponíveis como um dicionário dentro do `Run` objeto. Recupere o objeto ativo `Run` utilizando `Run.get_context()` e, em seguida, recupere o dicionário de entradas nomeadas utilizando `input_datasets` . Se passar o `DatasetConsumptionConfig` objeto usando o `arguments` argumento em vez do `inputs` argumento, aceda aos dados usando o `ArgParser` código. Ambas as técnicas são demonstradas no seguinte corte.

```python
# In pipeline definition script:
# Code for demonstration only: It would be very confusing to split datasets between `arguments` and `inputs`
train_step = PythonScriptStep(
    name="train_data",
    script_name="train.py",
    compute_target=cluster,
    arguments=['--training-folder', train.as_named_input('train').as_download()]
    inputs=[test.as_named_input('test').as_download()]
)

# In pipeline script
parser = argparse.ArgumentParser()
parser.add_argument('--training-folder', type=str, dest='train_folder', help='training data folder mounting point')
args = parser.parse_args()
training_data_folder = args.train_folder

testing_data_folder = Run.get_context().input_datasets['test']
```

O valor passado será o caminho para o(s) ficheiros de conjunto de dados.

Também é possível aceder a um registrado `Dataset` diretamente. Uma vez que os conjuntos de dados registados são persistentes e partilhados num espaço de trabalho, pode recuperá-los diretamente:

```python
run = Run.get_context()
ws = run.experiment.workspace
ds = Dataset.get_by_name(workspace=ws, name='mnist_opendataset')
```

> [!NOTE]
> Os snippets anteriores mostram a forma das chamadas e não fazem parte de uma amostra da Microsoft. Deve substituir os vários argumentos por valores do seu próprio projeto.

## <a name="use-pipelinedata-for-intermediate-data"></a>Utilização `PipelineData` para dados intermédios

Enquanto `Dataset` os objetos representam dados persistentes, os objetos [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py&preserve-view=true) são utilizados para dados temporários que são saídas a partir de etapas de pipeline. Como o tempo de vida útil de um `PipelineData` objeto é maior do que um único passo de oleoduto, define-os no roteiro de definição de gasoduto. Quando criar um `PipelineData` objeto, deve fornecer um nome e uma loja de dados em que os dados residirão. Passe o `PipelineData` seu(s) objeto(s) para o seu `PythonScriptStep` uso _tanto_ dos argumentos como `arguments` dos `outputs` argumentos:

```python

default_datastore = workspace.get_default_datastore()
dataprep_output = PipelineData("clean_data", datastore=default_datastore)

dataprep_step = PythonScriptStep(
    name="prep_data",
    script_name="dataprep.py",
    compute_target=cluster,
    arguments=["--output-path", dataprep_output]
    inputs=[Dataset.get_by_name(workspace, 'raw_data')],
    outputs=[dataprep_output]
)

```

Pode optar por criar o seu `PipelineData` objeto utilizando um modo de acesso que forneça um upload imediato. Nesse caso, quando criar o seu `PipelineData` , desista o `upload_mode` argumento e use o argumento para `"upload"` `output_path_on_compute` especificar o caminho para o qual irá escrever os dados:

```python
PipelineData("clean_data", datastore=def_blob_store, output_mode="upload", output_path_on_compute="clean_data_output/")
```

> [!NOTE]
> Os snippets anteriores mostram a forma das chamadas e não fazem parte de uma amostra da Microsoft. Deve substituir os vários argumentos por valores do seu próprio projeto.

> [!TIP]
> Uma experiência melhorada para a passagem de dados intermédios entre etapas de gasoduto está disponível com a classe de pré-visualização pública, [`OutputFileDatasetConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.outputfiledatasetconfig?view=azure-ml-py&preserve-view=true) . Para obter um exemplo de código `OutputFileDatasetConfig` utilizando, consulte como [construir um gasoduto ML](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/pipeline-with-datasets/pipeline-for-image-classification.ipynb)de dois passos .


### <a name="use-pipelinedata-as-outputs-of-a-training-step"></a>Usar `PipelineData` como saídas de um passo de treino
Dentro do seu `PythonScriptStep` pipeline, pode recuperar os caminhos de saída disponíveis usando os argumentos do programa. Se este passo for o primeiro e rubricar os dados de saída, deve criar o diretório na trajetória especificada. Em seguida, pode escrever os ficheiros que pretender conter no `PipelineData` .

```python
parser = argparse.ArgumentParser()
parser.add_argument('--output_path', dest='output_path', required=True)
args = parser.parse_args()
# Make directory for file
os.makedirs(os.path.dirname(args.output_path), exist_ok=True)
with open(args.output_path, 'w') as f:
    f.write("Step 1's output")
```

Se criasse o seu `PipelineData` com o argumento definido `is_directory` `True` para, seria suficiente apenas para executar a `os.makedirs()` chamada e então você seria livre para escrever quaisquer ficheiros que desejasse para o caminho. Para mais detalhes, consulte a documentação de referência [do PipelineData.](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py&preserve-view=true)


### <a name="read-pipelinedata-as-inputs-to-non-initial-steps"></a>Leia `PipelineData` como entradas para passos não iniciais

Após o passo inicial do gasoduto escrever alguns dados para o `PipelineData` caminho e torna-se uma saída desse passo inicial, pode ser usado como uma entrada para um passo posterior:

```python
step1_output_data = PipelineData("processed_data", datastore=def_blob_store, output_mode="upload")
# get adls gen 2 datastore already registered with the workspace
datastore = workspace.datastores['my_adlsgen2']

step1 = PythonScriptStep(
    name="generate_data",
    script_name="step1.py",
    runconfig = aml_run_config,
    arguments = ["--output_path", step1_output_data],
    inputs=[],
    outputs=[step1_output_data]
)

step2 = PythonScriptStep(
    name="read_pipeline_data",
    script_name="step2.py",
    compute_target=compute,
    runconfig = aml_run_config,
    arguments = ["--pd", step1_output_data],
    inputs=[step1_output_data]
)
pipeline = Pipeline(workspace=ws, steps=[step1, step2])
```

O valor de uma `PipelineData` entrada é o caminho para a saída anterior. 

> [!NOTE]
> Os snippets anteriores mostram a forma das chamadas e não fazem parte de uma amostra da Microsoft. Deve substituir os vários argumentos por valores do seu próprio projeto.

> [!TIP]
> Uma experiência melhorada para a passagem de dados intermédios entre etapas de gasoduto está disponível com a classe de pré-visualização pública, [`OutputFileDatasetConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.outputfiledatasetconfig?view=azure-ml-py&preserve-view=true) . Para obter um exemplo de código `OutputFileDatasetConfig` utilizando, consulte como [construir um gasoduto ML](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/pipeline-with-datasets/pipeline-for-image-classification.ipynb)de dois passos .

Se, como mostrado anteriormente, o primeiro passo escreveu um único ficheiro, consumi-lo pode parecer: 

```python
parser = argparse.ArgumentParser()
parser.add_argument('--pd', dest='pd', required=True)
args = parser.parse_args()
with open(args.pd) as f:
    print(f.read())
```

## <a name="convert-pipelinedata-objects-to-datasets"></a>Converter `PipelineData` objetos em `Dataset` s

Se quiser disponibilizar o seu `PipelineData` disponível por mais tempo do que a duração de uma execução, use a sua `as_dataset()` função para convertê-la em . `Dataset` Em seguida, pode registar o `Dataset` , tornando-o um cidadão de primeira classe no seu espaço de trabalho. Uma vez que o seu `PipelineData` objeto terá um caminho diferente cada vez que o gasoduto funciona, é altamente recomendado que você ajuste `create_new_version` ao registar um criado a partir de um `True` `Dataset` `PipelineData` objeto.

```python
step1_output_ds = step1_output_data.as_dataset()
step1_output_ds.register(name="processed_data", create_new_version=True)

```
> [!TIP]
> Uma experiência melhorada para persistir os seus dados intermédios fora do seu pipeline runs está disponível com a classe de pré-visualização pública, [`OutputFileDatasetConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.outputfiledatasetconfig?view=azure-ml-py&preserve-view=true) . Para obter um exemplo de código `OutputFileDatasetConfig` utilizando, consulte como [construir um gasoduto ML](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/pipeline-with-datasets/pipeline-for-image-classification.ipynb)de dois passos .

## <a name="next-steps"></a>Passos seguintes

* [Criar um conjunto de dados de aprendizagem de máquinas Azure](how-to-create-register-datasets.md)
* [Criar e executar gasodutos de aprendizagem automática com Azure Machine Learning SDK](how-to-create-your-first-pipeline.md)
