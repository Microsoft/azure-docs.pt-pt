---
title: Dados de deslocação em gasodutos ML
titleSuffix: Azure Machine Learning
description: Saiba como os oleodutos Azure Machine Learning ingerem dados e como gerir e mover dados entre etapas de pipeline.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: laobri
author: lobrien
ms.date: 02/26/2021
ms.topic: conceptual
ms.custom: how-to, contperf-fy20q4, devx-track-python, data4ml
ms.openlocfilehash: a4d1d1c4f4d6354d0206bf598a0622112dc99453
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/09/2021
ms.locfileid: "102518709"
---
# <a name="moving-data-into-and-between-ml-pipeline-steps-python"></a>Mover dados para e entre os passos de pipeline de ML (Python)

Este artigo fornece código para importar, transformar e mover dados entre etapas num oleoduto de Aprendizagem automática Azure. Para uma visão geral de como os dados funcionam no Azure Machine Learning, consulte [os dados do Access nos serviços de armazenamento Azure](how-to-access-data.md). Para os benefícios e estrutura dos oleodutos Azure Machine Learning, veja [o que são os oleodutos Azure Machine Learning?](concept-ml-pipelines.md)

Este artigo irá mostrar-lhe como:

- Utilize `Dataset` objetos para dados pré-existentes
- Aceder aos dados dentro dos seus passos
- Dividir `Dataset` dados em subconjuntos, tais como subconjuntos de treino e validação
- Criar `OutputFileDatasetConfig` objetos para transferir dados para o próximo passo do pipeline
- Use `OutputFileDatasetConfig` objetos como entrada para passos de gasoduto
- Crie novos `Dataset` objetos a partir de `OutputFileDatasetConfig` si para persistir

## <a name="prerequisites"></a>Pré-requisitos

Precisará:

- Uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma conta gratuita antes de começar. Experimente a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree).

- O [Azure Machine Learning SDK para Python,](/python/api/overview/azure/ml/intro)ou acesso ao [estúdio Azure Machine Learning.](https://ml.azure.com/)

- Uma área de trabalho do Azure Machine Learning.
  
  Ou [cria um espaço de trabalho de aprendizagem automática Azure](how-to-manage-workspace.md) ou utiliza um existente através do Python SDK. Importe a `Workspace` classe e `Datastore` carregue as suas informações de subscrição do ficheiro `config.json` utilizando a função `from_config()` . Esta função procura o ficheiro JSON no diretório atual por predefinição, mas também pode especificar um parâmetro de caminho para apontar para o ficheiro usando `from_config(path="your/file/path")` .

   ```python
   import azureml.core
   from azureml.core import Workspace, Datastore
        
   ws = Workspace.from_config()
   ```

- Alguns dados pré-existentes. Este artigo mostra brevemente a utilização de um [recipiente de bolhas Azure](../storage/blobs/storage-blobs-overview.md).

- Opcional: Um oleoduto de aprendizagem automática existente, como o descrito em [Criar e executar gasodutos de aprendizagem automática com Azure Machine Learning SDK](./how-to-create-machine-learning-pipelines.md).

## <a name="use-dataset-objects-for-pre-existing-data"></a>Utilize `Dataset` objetos para dados pré-existentes 

A forma preferida de ingerir dados num oleoduto é utilizar um objeto [Dataset.](/python/api/azureml-core/azureml.core.dataset%28class%29) `Dataset` os objetos representam dados persistentes disponíveis em todo um espaço de trabalho.

Há muitas formas de criar e registar `Dataset` objetos. Os conjuntos de dados tabulares destinam-se a dados delimitados disponíveis num ou mais ficheiros. Os conjuntos de dados de ficheiros são para dados binários (como imagens) ou para dados que irá analisar. As formas programáticas mais simples de criar `Dataset` objetos são usar bolhas existentes no armazenamento do espaço de trabalho ou URLs públicos:

```python
datastore = Datastore.get(workspace, 'training_data')
iris_dataset = Dataset.Tabular.from_delimited_files(DataPath(datastore, 'iris.csv'))

datastore_path = [
    DataPath(datastore, 'animals/dog/1.jpg'),
    DataPath(datastore, 'animals/dog/2.jpg'),
    DataPath(datastore, 'animals/cat/*.jpg')
]
cats_dogs_dataset = Dataset.File.from_files(path=datastore_path)
```

Para mais opções sobre a criação de conjuntos de dados com diferentes opções e de diferentes fontes, registá-los e revê-los na UI de Aprendizagem de Máquinas Azure, compreender como o tamanho dos dados interage com a capacidade de computação, e ver [conjuntos de dados de Aprendizagem de Máquinas Azure.](how-to-create-register-datasets.md) 

### <a name="pass-datasets-to-your-script"></a>Passe conjuntos de dados para o seu script

Para passar o caminho do conjunto de dados para o seu script, use o `Dataset` método do `as_named_input()` objeto. Pode passar o objeto resultante para o `DatasetConsumptionConfig` seu script como argumento ou, utilizando o argumento para o seu script de `inputs` pipeline, pode recuperar o conjunto de dados utilizando `Run.get_context().input_datasets[]` .

Uma vez criada uma entrada com o nome, pode escolher o seu modo de acesso: `as_mount()` ou `as_download()` . Se o seu script processar todos os ficheiros do seu conjunto de dados e o disco no seu recurso computacional for suficientemente grande para o conjunto de dados, o modo de acesso ao download é a melhor escolha. O modo de acesso ao descarregamento evitará a sobrecarga de transmissão dos dados em tempo de execução. Se o seu script aceder a um subconjunto do conjunto de dados ou for demasiado grande para o seu cálculo, utilize o modo de acesso de montagem. Para mais informações, leia [Mount vs. Download](./how-to-train-with-datasets.md#mount-vs-download)

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

## <a name="use-outputfiledatasetconfig-for-intermediate-data"></a>Utilização `OutputFileDatasetConfig` para dados intermédios

Embora `Dataset` os objetos representem apenas dados persistentes, [`OutputFileDatasetConfig`](/python/api/azureml-core/azureml.data.outputfiledatasetconfig) os objetos podem ser utilizados para a produção temporária de dados a partir de etapas de pipeline **e** dados de saída persistentes. `OutputFileDatasetConfig` suporta a escrita de dados para o armazenamento de bolhas, fileshare, adlsgen1 ou adlsgen2. Suporta o modo de montagem e o modo de upload. No modo de montagem, os ficheiros escritos no diretório montado são armazenados permanentemente quando o ficheiro está fechado. No modo de upload, os ficheiros escritos no diretório de saída são carregados no final do trabalho. Se o trabalho falhar ou for cancelado, o diretório de saída não será carregado.

 `OutputFileDatasetConfig` o comportamento padrão do objeto é escrever para a datastore padrão do espaço de trabalho. Passe os seus `OutputFileDatasetConfig` objetos para o seu `PythonScriptStep` com o `arguments` parâmetro.

```python
from azureml.data import OutputFileDatasetConfig
dataprep_output = OutputFileDatasetConfig()
input_dataset = Dataset.get_by_name(workspace, 'raw_data')

dataprep_step = PythonScriptStep(
    name="prep_data",
    script_name="dataprep.py",
    compute_target=cluster,
    arguments=[input_dataset.as_named_input('raw_data').as_mount(), dataprep_output]
    )
```

Pode optar por carregar o conteúdo do seu `OutputFileDatasetConfig` objeto no final de uma corrida. Nesse caso, utilize a `as_upload()` função juntamente com o seu `OutputFileDatasetConfig` objeto e especifique se deve substituir os ficheiros existentes no destino. 

```python
#get blob datastore already registered with the workspace
blob_store= ws.datastores['my_blob_store']
OutputFileDatasetConfig(name="clean_data", destination=(blob_store, 'outputdataset')).as_upload(overwrite=False)
```

> [!NOTE]
> Escreve a um `OutputFileDatasetConfig` testamento falhar. Não tente utilizar uma única `OutputFileDatasetConfig` simultaneamente. Não partilhe um único `OutputFileDatasetConfig` numa situação multiprocessado, como quando utilizar a formação distribuída. 

### <a name="use-outputfiledatasetconfig-as-outputs-of-a-training-step"></a>Usar `OutputFileDatasetConfig` como saídas de um passo de treino

No `PythonScriptStep` do pipeline, pode obter os caminhos de saída disponíveis com os argumentos do programa. Se este passo for o primeiro e inicializar os dados de saída, terá de criar o diretório no caminho especificado. Em seguida, pode escrever os ficheiros que pretender conter no `OutputFileDatasetConfig` .

```python
parser = argparse.ArgumentParser()
parser.add_argument('--output_path', dest='output_path', required=True)
args = parser.parse_args()

# Make directory for file
os.makedirs(os.path.dirname(args.output_path), exist_ok=True)
with open(args.output_path, 'w') as f:
    f.write("Step 1's output")
```

### <a name="read-outputfiledatasetconfig-as-inputs-to-non-initial-steps"></a>Leia `OutputFileDatasetConfig` como entradas para passos não iniciais

Após o passo inicial do pipeline escrever alguns dados para o `OutputFileDatasetConfig` caminho e torna-se uma saída desse passo inicial, pode ser usado como uma entrada para um passo posterior. 

No seguinte código: 

* `step1_output_data` indica que a saída do PythonScriptStep é escrita para a loja de `step1` dados ADLS Gen 2, `my_adlsgen2` no modo de acesso ao upload. Saiba mais sobre como [configurar permissões](how-to-access-data.md#azure-data-lake-storage-generation-2) de função para escrever dados de volta para as datas da ADLS Gen 2. 

* Após `step1` os completos e a saída é escrita para o destino indicado por , então o `step1_output_data` passo2 está pronto a ser usado como `step1_output_data` entrada. 

```python
# get adls gen 2 datastore already registered with the workspace
datastore = workspace.datastores['my_adlsgen2']
step1_output_data = OutputFileDatasetConfig(name="processed_data", destination=(datastore, "mypath/{run-id}/{output-name}")).as_upload()

step1 = PythonScriptStep(
    name="generate_data",
    script_name="step1.py",
    runconfig = aml_run_config,
    arguments = ["--output_path", step1_output_data]
)

step2 = PythonScriptStep(
    name="read_pipeline_data",
    script_name="step2.py",
    compute_target=compute,
    runconfig = aml_run_config,
    arguments = ["--pd", step1_output_data.as_input()]

)

pipeline = Pipeline(workspace=ws, steps=[step1, step2])
```

## <a name="register-outputfiledatasetconfig-objects-for-reuse"></a>Registar `OutputFileDatasetConfig` objetos para reutilização

Se quiser disponibilizar o seu `OutputFileDatasetConfig` dispor por mais tempo do que a duração da sua experiência, registe-o no seu espaço de trabalho para partilhar e reutilizar através de experiências.

```python
step1_output_ds = step1_output_data.register_on_complete(name='processed_data', 
                                                         description = 'files from step1`)
```

## <a name="delete-outputfiledatasetconfig-contents-when-no-longer-needed"></a>Eliminar `OutputFileDatasetConfig` conteúdos quando já não for necessário

O Azure não apaga automaticamente os dados intermédios escritos com `OutputFileDatasetConfig` . Para evitar encargos de armazenamento de grandes quantidades de dados não sofridos, deve:

* Excluir programaticamente dados intermédios no final de uma corrida de gasodutos, quando já não é necessário
* Utilize o armazenamento de bolhas com uma política de armazenamento de curto prazo para dados intermédios (ver [otimizar os custos automatizando os níveis de acesso ao armazenamento Azure Blob](https://docs.microsoft.com/azure/storage/blobs/storage-lifecycle-management-concepts?tabs=azure-portal)) 
* Reveja e elimine regularmente os dados já não necessários

Para obter mais informações, consulte [Plano e gerencie os custos para a Azure Machine Learning.](concept-plan-manage-cost.md)

## <a name="next-steps"></a>Passos seguintes

* [Criar um conjunto de dados de aprendizagem de máquinas Azure](how-to-create-register-datasets.md)
* [Criar e executar gasodutos de aprendizagem automática com Azure Machine Learning SDK](./how-to-create-machine-learning-pipelines.md)