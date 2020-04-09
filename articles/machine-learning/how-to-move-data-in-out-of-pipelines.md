---
title: Dados de entrada e de saída dos gasodutos ML
titleSuffix: Azure Machine Learning
description: Preparar, consumir e gerar dados em oleodutos De Aprendizagem Automática Azure
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: laobri
author: lobrien
ms.date: 04/01/2020
ms.openlocfilehash: 3dd1a82bf7fad1f201f5c0f52af944ef44a3fdf9
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879768"
---
# <a name="moving-data-into-and-between-ml-pipeline-steps-python"></a>Mover dados para e entre os passos do gasoduto ML (Python)

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Os dados são centrais para os gasodutos de aprendizagem automática. Este artigo fornece código para importar, transformar e mover dados entre passos num oleoduto Azure Machine Learning. Para uma visão geral de como os dados funcionam em Azure Machine Learning, consulte os dados de acesso nos serviços de [armazenamento do Azure.](how-to-access-data.md) Para obter os benefícios e estrutura dos oleodutos Azure Machine Learning, veja [o que são os oleodutos Azure Machine Learning?](concept-ml-pipelines.md)

Este artigo irá mostrar-lhe como:

- Utilize `Dataset` objetos para dados pré-existentes
- Aceder aos dados dentro dos seus passos
- Divida `Dataset` os dados em subconjuntos, tais como subconjuntos de formação e validação
- Criar `PipelineData` objetos para transferir dados para o próximo passo do oleoduto
- Use `PipelineData` objetos como entrada para os passos do gasoduto
- Crie `Dataset` novos `PipelineData` objetos de que deseja persistir

## <a name="prerequisites"></a>Pré-requisitos

Precisa de:

- Uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma conta gratuita antes de começar. Experimente a [versão gratuita ou paga do Azure Machine Learning.](https://aka.ms/AMLFree)

- O [Azure Machine Learning SDK para Python,](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)ou acesso ao [estúdio Azure Machine Learning.](https://ml.azure.com/)

- Uma área de trabalho do Azure Machine Learning.
  
  Ou cria um espaço de [trabalho azure machine learning](how-to-manage-workspace.md) ou usa um existente através do Python SDK. Importe `Workspace` `Datastore` a e a classe e `config.json` carregue as `from_config()`suas informações de subscrição a partir do ficheiro utilizando a função . Esta função procura o ficheiro JSON no diretório atual por padrão, mas também pode `from_config(path="your/file/path")`especificar um parâmetro de percurso para apontar para o ficheiro utilizando .

   ```python
   import azureml.core
   from azureml.core import Workspace, Datastore
        
   ws = Workspace.from_config()
   ```

- Alguns dados pré-existentes. Este artigo mostra brevemente a utilização de um [recipiente de bolha Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview).

- Opcional: Um gasoduto de aprendizagem automática existente, como o descrito em Criar e executar gasodutos de [aprendizagem automática com Azure Machine Learning SDK](how-to-create-your-first-pipeline.md).

## <a name="use-dataset-objects-for-pre-existing-data"></a>Utilize `Dataset` objetos para dados pré-existentes 

A forma preferida de ingerir dados num pipeline é utilizar um objeto [dataset.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py) `Dataset`objetos representam dados persistentes disponíveis em todo um espaço de trabalho.

Há muitas formas `Dataset` de criar e registar objetos. Os conjuntos de dados tabular são para dados delimitados disponíveis num ou mais ficheiros. Os conjuntos de dados de ficheiros são para dados binários (como imagens) ou para dados que irá analisar. As formas programáticas `Dataset` mais simples de criar objetos são usar bolhas existentes no armazenamento do espaço de trabalho ou URLs públicos:

```python
datastore = Datastore.get(workspace, 'training_data')
iris_dataset = Dataset.Tabular.from_delimited_files(DataPath(datastore, 'iris.csv'))

cats_dogs_dataset = Dataset.File.from_files(
    paths='https://download.microsoft.com/download/3/E/1/3E1C3F21-ECDB-4869-8368-6DEBA77B919F/kagglecatsanddogs_3367a.zip',
    archive_options=ArchiveOptions(archive_type=ArchiveType.ZIP, entry_glob='**/*.jpg')
)
```

Para mais opções sobre a criação de conjuntos de dados com diferentes opções e de diferentes fontes, registá-los e revê-los no Azure Machine Learning UI, compreender como o tamanho dos dados interage com a capacidade da computação, e versonizá-los, ver Conjuntos de [dados Create Azure Machine Learning.](how-to-create-register-datasets.md) 

### <a name="pass-datasets-to-your-script"></a>Passe conjuntos de dados para o seu script

Para passar o caminho do conjunto de `Dataset` dados para `as_named_input()` o seu script, use o método do objeto. Pode passar o `DatasetConsumptionConfig` objeto resultante para o seu script `inputs` como argumento ou, utilizando o argumento `Run.get_context().input_datasets[]`para o seu script de pipeline, pode recuperar o conjunto de dados utilizando .

Uma vez criado um contributo nomeado, pode escolher `as_mount()` o `as_download()`seu modo de acesso: ou . Se o seu script processar todos os ficheiros do seu conjunto de dados e o disco no seu recurso computacional for suficientemente grande para o conjunto de dados, o modo de acesso ao download é a melhor escolha. O modo de acesso ao download evitará a sobrecarga de streaming dos dados em tempo de execução. Se o seu script aceder a um subconjunto do conjunto de dados ou se for demasiado grande para a sua computação, utilize o modo de acesso ao suporte. Para mais informações, leia [Mount vs. Download](https://docs.microsoft.com/azure/machine-learning/how-to-train-with-datasets#mount-vs-download)

Para passar um conjunto de dados para o seu passo de pipeline:

1. Utilizar `TabularDataset.as_named_inputs()` `FileDataset.as_named_input()` ou (sem 's' no `DatasetConsumptionConfig` final) para criar um objeto
1. Utilizar `as_mount()` `as_download()` ou definir o modo de acesso
1. Passe os conjuntos de dados para `arguments` os `inputs` seus passos de pipeline usando o argumento ou o argumento

O seguinte corte mostra o padrão comum de `PythonScriptStep` combinar estes passos dentro do construtor: 

```python

train_step = PythonScriptStep(
    name="train_data",
    script_name="train.py",
    compute_target=cluster,
    inputs=[iris_dataset.as_named_inputs('iris').as_mount()]
)
```

Também pode utilizar métodos como `random_split()` e `take_sample()` criar múltiplas inputs ou reduzir a quantidade de dados passados para o seu passo de pipeline:

```python
seed = 42 # PRNG seed
smaller_dataset = iris_dataset.take_sample(0.1, seed=seed) # 10%
train, test = smaller_dataset.random_split(percentage=0.8, seed=seed)

train_step = PythonScriptStep(
    name="train_data",
    script_name="train.py",
    compute_target=cluster,
    inputs=[train.as_named_inputs('train').as_download(), test.as_named_inputs('test').as_download()]
)
```

### <a name="access-datasets-within-your-script"></a>Aceder a conjuntos de dados dentro do seu script

As inputs nomeadas para o seu script `Run` de passo de pipeline estão disponíveis como um dicionário dentro do objeto. Recupere `Run` o `Run.get_context()` objeto ativo utilizando e, em seguida, recupere o dicionário de inputs nomeados utilizando `input_datasets`. Se passar `DatasetConsumptionConfig` o objeto `arguments` usando o `inputs` argumento e não `ArgParser` o argumento, aceda aos dados usando código. Ambas as técnicas são demonstradas no seguinte corte.

```python
# In pipeline definition script:
# Code for demonstration only: It would be very confusing to split datasets between `arguments` and `inputs`
train_step = PythonScriptStep(
    name="train_data",
    script_name="train.py",
    compute_target=cluster,
    arguments=['--training-folder', train.as_named_inputs('train').as_download()]
    inputs=[test.as_named_inputs('test').as_download()]
)

# In pipeline script
parser = argparse.ArgumentParser()
parser.add_argument('--training-folder', type=str, dest='train_folder', help='training data folder mounting point')
args = parser.parse_args()
training_data_folder = args.train_folder

testing_data_folder = Run.get_context().input_datasets['test']
```

O valor passado será o caminho para os ficheiros conjuntos de dados.

Também é possível aceder `Dataset` diretamente a um registado. Uma vez que os conjuntos de dados registados são persistentes e partilhados através de um espaço de trabalho, pode recuperá-los diretamente:

```python
run = Run.get_context()
ws = run.experiment.workspace
ds = Dataset.get_by_name(workspace=ws, name='mnist_opendataset')
```

## <a name="use-pipelinedata-for-intermediate-data"></a>Utilização `PipelineData` para dados intermédios

Embora `Dataset` os objetos representem dados persistentes, os objetos [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) são utilizados para dados temporários que são provenientes de passos de pipeline. Como o tempo `PipelineData` de vida de um objeto é mais longo do que um único passo de oleoduto, define-o no script de definição de pipeline. Quando cria `PipelineData` um objeto, deve fornecer um nome e um datastore no qual os dados irão residir. Passe `PipelineData` o seu objeto(s) `arguments` para `outputs` o seu `PythonScriptStep` uso _dos_ argumentos e dos argumentos:

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

Pode optar por `PipelineData` criar o seu objeto utilizando um modo de acesso que fornece um upload imediato. Nesse caso, quando criar `PipelineData`o `upload_mode` seu, detete o para `"upload"` e use o `output_path_on_compute` argumento para especificar o caminho para o qual estará a escrever os dados:

```python
PipelineData("clean_data", datastore=def_blob_store, output_mode="upload", output_path_on_compute="clean_data_output/")
```

### <a name="use-pipelinedata-as-outputs-of-a-training-step"></a>Usar `PipelineData` como saídas de um passo de treino

Dentro do `PythonScriptStep`seu pipeline, pode recuperar os caminhos de saída disponíveis utilizando os argumentos do programa. Se este passo for o primeiro e rubricar os dados de saída, deve criar o diretório no caminho especificado. Pode então escrever os ficheiros que `PipelineData`quiser estar contidos no .

```python
parser = argparse.ArgumentParser()
parser.add_argument('--output_path', dest='output_path', required=True)
args = parser.parse_args()

# Make directory for file
os.makedirs(os.path.dirname(args.output_path), exist_ok=True)
with open(args.output_path, 'w') as f:
    f.write("Step 1's output")
```

Se criou `PipelineData` o `is_directory` seu com `True`o argumento definido, seria `os.makedirs()` suficiente apenas para executar a chamada e então seria livre de escrever os ficheiros que quisesse para o caminho. Para mais detalhes, consulte a documentação de referência [PipelineData.](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py)

### <a name="read-pipelinedata-as-inputs-to-non-initial-steps"></a>Ler `PipelineData` como inputs a passos não iniciais

Após o passo inicial do gasoduto `PipelineData` escrever alguns dados para o caminho e torna-se uma saída desse passo inicial, pode ser usado como entrada para um passo posterior:

```python
step1_output_data = PipelineData("processed_data", datastore=def_blob_store, output_mode="upload")

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

O valor `PipelineData` de uma entrada é o caminho para a saída anterior. Se, como mostrado anteriormente, o primeiro passo escreveu um único ficheiro, consumi-lo pode parecer: 

```python
parser = argparse.ArgumentParser()
parser.add_argument('--pd', dest='pd', required=True)
args = parser.parse_args()

with open(args.pd) as f:
    print(f.read())
```

## <a name="convert-pipelinedata-objects-to-datasets"></a>Converter `PipelineData` objetos em `Dataset`s

Se quiser disponibilizar a `PipelineData` sua disponível por mais tempo do `as_dataset()` que a duração de uma execução, utilize a sua função para convertê-la em `Dataset`. Pode então `Dataset`registar o , tornando-o um cidadão de primeira classe no seu espaço de trabalho. Uma `PipelineData` vez que o seu objeto terá um caminho diferente sempre `create_new_version` que `True` o gasoduto `Dataset` funciona, `PipelineData` é altamente recomendado que se ajuste ao registar um criado a partir de um objeto.

```python
step1_output_ds = step1_output_data.as_dataset()
step1_output_ds.register(name="processed_data", create_new_version=True)
```

## <a name="next-steps"></a>Passos seguintes

* [Criar um conjunto de dados de aprendizagem automática Azure](how-to-create-register-datasets.md)
* [Criar e executar gasodutos de aprendizagem automática com Azure Machine Learning SDK](how-to-create-your-first-pipeline.md)
