---
title: Comboio com conjuntos de dados azureml
titleSuffix: Azure Machine Learning
description: Saiba como utilizar conjuntos de dados em formação
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 07/31/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: aa1ba4aa07ee4cdc097bd4ed3e6e4d7563360a5d
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91331799"
---
# <a name="train-with-datasets-in-azure-machine-learning"></a>Treine com conjuntos de dados em Azure Machine Learning


Neste artigo, aprende a trabalhar com [conjuntos de dados de Aprendizagem automática Azure](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py&preserve-view=true) nas suas experiências de treino.  Pode utilizar conjuntos de dados no seu alvo de computação local ou remota sem se preocupar com cordas de ligação ou caminhos de dados.

Os conjuntos de dados Azure Machine Learning proporcionam uma integração perfeita com a funcionalidade de formação de aprendizagem automática Azure como [scriptRunConfig,](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py&preserve-view=true) [hiperDrive](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive?view=azure-ml-py&preserve-view=true) e [azure machine learning pipelines](how-to-create-your-first-pipeline.md).

## <a name="prerequisites"></a>Pré-requisitos

Para criar e treinar com conjuntos de dados, precisa:

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma conta gratuita antes de começar. Experimente hoje a [versão gratuita ou paga do Azure Machine Learning.](https://aka.ms/AMLFree)

* Um [espaço de trabalho de aprendizagem automática Azure.](how-to-manage-workspace.md)

* O [Azure Machine Learning SDK para Python instalado](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true) (>= 1.13.0), que inclui o pacote de conjuntos de dados azureml.

> [!Note]
> Algumas classes dataset têm dependências do pacote [azureml-dataprep.](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py&preserve-view=true) Para os utilizadores do Linux, estas classes são suportadas apenas nas seguintes distribuições: Red Hat Enterprise Linux, Ubuntu, Fedora e CentOS.

## <a name="use-datasets-directly-in-training-scripts"></a>Utilize conjuntos de dados diretamente nos scripts de formação

Se tiver dados estruturados ainda não registados como conjunto de dados, crie um Separadors e utilize-os diretamente no seu script de treino para a sua experiência local ou remota.

Neste exemplo, cria-se um [Separadornista](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py&preserve-view=true) Não Registado e especifica-o como argumento de script no objeto ScriptRunConfig para treino. Se pretender reutilizar este SeparadorDataset com outras experiências no seu espaço de trabalho, consulte [como registar conjuntos de dados no seu espaço de trabalho.](how-to-create-register-datasets.md#register-datasets)

### <a name="create-a-tabulardataset"></a>Criar um Conjunto de Dados Tabular

O código a seguir cria um Separador Não Registado a partir de um url web.  

```Python
from azureml.core.dataset import Dataset

web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path)
```

Os objetos TabularDataset fornecem a capacidade de carregar os dados no seu SeparadorDataset num pandas ou Nodilha de Dados de Faíscas para que possa trabalhar com bibliotecas familiares de preparação e formação de dados sem ter de deixar o seu caderno.

### <a name="access-dataset-in-training-script"></a>Conjunto de dados de acesso no script de formação

O código que se segue configura um argumento de script `--input-data` que irá especificar quando configurar a sua formação (ver secção seguinte). Quando o conjunto de dados tabular é passado como o valor do argumento, o Azure ML resolverá isso para iD do conjunto de dados, que pode então utilizar para aceder ao conjunto de dados no seu script de treino (sem ter de codificar o nome ou ID do conjunto de dados no seu script). Em seguida, utiliza o [`to_pandas_dataframe()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset#to-pandas-dataframe-on-error--null---out-of-range-datetime--null--) método para carregar esse conjunto de dados num dataframe de pandas para mais exploração e preparação de dados antes do treino.

> [!Note]
> Se a sua fonte de dados original contiver NaN, cordas vazias ou valores em branco, quando `to_pandas_dataframe()` utilizar, esses valores são substituídos como um valor *nulo.*

Se precisar de carregar os dados preparados num novo conjunto de dados a partir de um dataframe de pandas na memória, escreva os dados para um ficheiro local, como um parquet, e crie um novo conjunto de dados a partir desse ficheiro. Também pode criar conjuntos de dados a partir de ficheiros ou caminhos locais nas datastores. Saiba mais sobre [como criar conjuntos de dados.](how-to-create-register-datasets.md)

```Python
%%writefile $script_folder/train_titanic.py

import argparse
from azureml.core import Dataset, Run

parser = argparse.ArgumentParser()
parser.add_argument("--input-data", type=str)
args = parser.parse_args()

run = Run.get_context()
ws = run.experiment.workspace

# get the input dataset by ID
dataset = Dataset.get_by_id(ws, id=args.input_data)

# load the TabularDataset to pandas DataFrame
df = dataset.to_pandas_dataframe()
```

### <a name="configure-the-training-run"></a>Configure a corrida de treino
Um objeto [ScriptRunConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrun?view=azure-ml-py&preserve-view=true) é usado para configurar e submeter a execução de treino.

Este código cria um objeto ScriptRunConfig, `src` que especifica

* Um diretório de guiões para os seus scripts. Todos os ficheiros neste diretório são carregados para os nós do cluster, para execução.
* O guião de treino, *train_titanic.py.*
* O conjunto de dados de entrada para o treino, `titanic_ds` como um argumento de script. O Azure ML resolverá isto com o iD correspondente do conjunto de dados quando este for passado para o seu script.
* O alvo do cálculo para a corrida.
* O ambiente para a corrida.

```python
from azureml.core import ScriptRunConfig

src = ScriptRunConfig(source_directory=script_folder,
                      script='train_titanic.py',
                      # pass dataset as an input with friendly name 'titanic'
                      arguments=['--input-dataset', titanic_ds],
                      compute_target=compute_target,
                      environment=myenv)
                             
# Submit the run configuration for your training run
run = experiment.submit(src)
run.wait_for_completion(show_output=True)                             
```

## <a name="mount-files-to-remote-compute-targets"></a>Monte ficheiros para alvos de computação remota

Se tiver dados não estruturados, crie um [Dataset de Ficheiros](https://docs.microsoft.com/python/api/azureml-core/azureml.data.filedataset?view=azure-ml-py&preserve-view=true) e monte ou descarregue os seus ficheiros de dados para os disponibilizar ao seu alvo de computação remota para treino. Saiba quando usar [o mount vs. download](#mount-vs-download) para as suas experiências de treino remoto. 

O exemplo a seguir cria um Dataset de Ficheiros e monta o conjunto de dados para o alvo do cálculo, passando-o como argumento para o script de formação. 

> [!Note]
> Se estiver a utilizar uma imagem base personalizada do Docker, terá de instalar o fusível através `apt-get install -y fuse` de uma dependência para o conjunto de dados funcionar. Aprenda a [construir uma imagem de construção personalizada.](how-to-deploy-custom-docker-image.md#build-a-custom-base-image)

### <a name="create-a-filedataset"></a>Criar um Conjunto de Dados de Ficheiros

O exemplo a seguir cria um Dataset de Ficheiros não registado a partir de urls web. Saiba mais sobre [como criar conjuntos de dados a](how-to-create-register-datasets.md) partir de outras fontes.

```Python
from azureml.core.dataset import Dataset

web_paths = [
            'http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz'
            ]
mnist_ds = Dataset.File.from_files(path = web_paths)
```

### <a name="configure-the-training-run"></a>Configure a corrida de treino
Recomendamos a passagem do conjunto de dados como argumento ao montar através `arguments` do parâmetro do `ScriptRunConfig` construtor. Ao fazê-lo, obterá o caminho de dados (ponto de montagem) no seu roteiro de treino através de argumentos. Desta forma, poderá utilizar o mesmo script de treino para depurar local e treinar remotamente em qualquer plataforma em nuvem.

O exemplo a seguir cria um ScriptRunConfig que passa no Dataset de Ficheiros via `arguments` . Após a submissão da execução, os ficheiros de dados referidos pelo conjunto de dados `mnist_ds` serão montados no alvo do cálculo.

```python
from azureml.core import ScriptRunConfig

src = ScriptRunConfig(source_directory=script_folder,
                      script='train_mnist.py',
                      # the dataset will be mounted on the remote compute and the mounted path passed as an argument to the script
                      arguments=['--data-folder', mnist_ds.as_mount(), '--regularization', 0.5],
                      compute_target=compute_target,
                      environment=myenv)

# Submit the run configuration for your training run
run = experiment.submit(src)
run.wait_for_completion(show_output=True)
```

### <a name="retrieve-the-data-in-your-training-script"></a>Recupere os dados no seu roteiro de treino

O código que se segue mostra como recuperar os dados no seu script.

```Python
%%writefile $script_folder/train_mnist.py

import argparse
import os
import numpy as np
import glob

from utils import load_data

# retrieve the 2 arguments configured through `arguments` in the ScriptRunConfig
parser = argparse.ArgumentParser()
parser.add_argument('--data-folder', type=str, dest='data_folder', help='data folder mounting point')
parser.add_argument('--regularization', type=float, dest='reg', default=0.01, help='regularization rate')
args = parser.parse_args()

data_folder = args.data_folder
print('Data folder:', data_folder)

# get the file paths on the compute
X_train_path = glob.glob(os.path.join(data_folder, '**/train-images-idx3-ubyte.gz'), recursive=True)[0]
X_test_path = glob.glob(os.path.join(data_folder, '**/t10k-images-idx3-ubyte.gz'), recursive=True)[0]
y_train_path = glob.glob(os.path.join(data_folder, '**/train-labels-idx1-ubyte.gz'), recursive=True)[0]
y_test = glob.glob(os.path.join(data_folder, '**/t10k-labels-idx1-ubyte.gz'), recursive=True)[0]

# load train and test set into numpy arrays
X_train = load_data(X_train_path, False) / 255.0
X_test = load_data(X_test_path, False) / 255.0
y_train = load_data(y_train_path, True).reshape(-1)
y_test = load_data(y_test, True).reshape(-1)
```

## <a name="mount-vs-download"></a>Monte vs download

A montagem ou descarregamento de ficheiros de qualquer formato são suportados para conjuntos de dados criados a partir do armazenamento de Azure Blob, Azure Files, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure SQL Database e Azure Database for PostgreSQL. 

Quando **monta** um conjunto de dados, anexa os ficheiros referenciados pelo conjunto de dados a um diretório (ponto de montagem) e disponibiliza-os no alvo do cálculo. A montagem é suportada para computas baseadas em Linux, incluindo Azure Machine Learning Compute, máquinas virtuais e HDInsight. 

Quando **descarrega** um conjunto de dados, todos os ficheiros referenciados pelo conjunto de dados serão descarregados para o alvo do cálculo. O download é suportado para todos os tipos de computação. 

Se o seu script processar todos os ficheiros referenciados pelo conjunto de dados e o seu disco de computação puder encaixar no conjunto de dados completo, recomenda-se o download para evitar a sobrecarga de dados de streaming dos serviços de armazenamento. Se o tamanho dos dados exceder o tamanho do disco computacional, o download não é possível. Para este cenário, recomendamos a montagem, uma vez que apenas os ficheiros de dados utilizados pelo seu script são carregados no momento do processamento.

O seguinte código monta `dataset` para o diretório temporário em `mounted_path`

```python
import tempfile
mounted_path = tempfile.mkdtemp()

# mount dataset onto the mounted_path of a Linux-based compute
mount_context = dataset.mount(mounted_path)

mount_context.start()

import os
print(os.listdir(mounted_path))
print (mounted_path)
```

## <a name="directly-access-datasets-in-your-script"></a>Aceda diretamente conjuntos de dados no seu script

Os conjuntos de dados registados são acessíveis localmente e remotamente em clusters computacional como o cálculo Azure Machine Learning. Para aceder ao conjunto de dados registado através de experiências, utilize o seguinte código para aceder ao seu espaço de trabalho e ao conjunto de dados registado pelo nome. Por predefinição, [`get_by_name()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py&preserve-view=true#&preserve-view=trueget-by-name-workspace--name--version--latest--) o método na classe devolve a versão mais recente do conjunto de `Dataset` dados registado no espaço de trabalho.

```Python
%%writefile $script_folder/train.py

from azureml.core import Dataset, Run

run = Run.get_context()
workspace = run.experiment.workspace

dataset_name = 'titanic_ds'

# Get a dataset by name
titanic_ds = Dataset.get_by_name(workspace=workspace, name=dataset_name)

# Load a TabularDataset into pandas DataFrame
df = titanic_ds.to_pandas_dataframe()
```

## <a name="accessing-source-code-during-training"></a>Aceder ao código fonte durante o treino

O armazenamento Azure Blob tem velocidades de produção mais altas do que uma partilha de ficheiros Azure e irá escalar para um grande número de empregos iniciados em paralelo. Por esta razão, recomendamos configurar as suas corridas para utilizar o armazenamento Blob para transferir ficheiros de código fonte.

O exemplo de código a seguir especifica na configuração de execução que blob datastore para usar para transferências de código fonte.

```python 
# workspaceblobstore is the default blob storage
src.run_config.source_directory_data_store = "workspaceblobstore" 
```

## <a name="notebook-examples"></a>Exemplos de cadernos

Os [cadernos de conjuntos de dados](https://aka.ms/dataset-tutorial) demonstram e expandem-se sobre conceitos neste artigo.

## <a name="next-steps"></a>Passos seguintes

* [Modelos de aprendizagem automática de máquinas](how-to-auto-train-remote.md) de comboio com Separadors.

* [Treinar modelos de classificação de imagem](https://aka.ms/filedataset-samplenotebook) com FileDatasets.

* [Treine com conjuntos de dados utilizando gasodutos.](how-to-create-your-first-pipeline.md)
