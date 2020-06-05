---
title: Comboio com conjuntos de dados azureml
titleSuffix: Azure Machine Learning
description: Saiba como utilizar conjuntos de dados em formação
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 04/20/2020
ms.openlocfilehash: 2d573591c2ec70c0d9ec1598dca3af295d36b4c7
ms.sourcegitcommit: b55d1d1e336c1bcd1c1a71695b2fd0ca62f9d625
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/04/2020
ms.locfileid: "84433773"
---
# <a name="train-with-datasets-in-azure-machine-learning"></a>Treine com conjuntos de dados em Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste artigo, aprende a trabalhar com [conjuntos de dados de Aprendizagem automática Azure](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py) nas suas experiências de treino.  Pode utilizar conjuntos de dados no seu alvo de computação local ou remota sem se preocupar com cordas de ligação ou caminhos de dados.

Os conjuntos de dados Azure Machine Learning proporcionam uma integração perfeita com produtos de formação de aprendizagem automática Azure como [scriptRun,](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrun?view=azure-ml-py) [Estimador,](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py) [HyperDrive](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive?view=azure-ml-py) e [Azure Machine Learning pipelines](how-to-create-your-first-pipeline.md).

## <a name="prerequisites"></a>Pré-requisitos

Para criar e treinar com conjuntos de dados, precisa:

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma conta gratuita antes de começar. Experimente hoje a [versão gratuita ou paga do Azure Machine Learning.](https://aka.ms/AMLFree)

* Um [espaço de trabalho de aprendizagem automática Azure.](how-to-manage-workspace.md)

* O [Azure Machine Learning SDK para Python instalado,](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)que inclui o pacote de conjuntos de dados azureml.

> [!Note]
> Algumas classes dataset têm dependências do pacote [azureml-dataprep.](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py) Para os utilizadores do Linux, estas classes são suportadas apenas nas seguintes distribuições: Red Hat Enterprise Linux, Ubuntu, Fedora e CentOS.

## <a name="access-and-explore-input-datasets"></a>Aceda e explore conjuntos de dados de entrada

Você pode aceder a um Separadornus existente a partir do script de treino de uma experiência no seu espaço de trabalho, e carregar esse conjunto de dados em um dataframe pandas para mais exploração no seu ambiente local.

O código que se segue utiliza o [`get_context()`]() método na classe para aceder à entrada existente [`Run`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py) TabularDataset, `titanic` no script de treino. Em seguida, utiliza o [`to_pandas_dataframe()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset#to-pandas-dataframe-on-error--null---out-of-range-datetime--null--) método para carregar esse conjunto de dados num dataframe de pandas para mais exploração e preparação de dados antes do treino.

> [!Note]
> Se a sua fonte de dados original contiver NaN, cordas vazias ou valores em branco, quando utilizar o to_pandas_dataframe(), esses valores são substituídos como um valor *nulo.* 

```Python
%%writefile $script_folder/train_titanic.py

from azureml.core import Dataset, Run

run = Run.get_context()
# get the input dataset by name
dataset = run.input_datasets['titanic']

# load the TabularDataset to pandas DataFrame
df = dataset.to_pandas_dataframe()
```

Se precisar de carregar os dados preparados num novo conjunto de dados a partir de um dataframe de pandas de memória, escreva os dados para um ficheiro local, como um parquet, e crie um novo conjunto de dados a partir desse ficheiro. Também pode criar conjuntos de dados a partir de ficheiros ou caminhos locais nas datastores. Saiba mais sobre [como criar conjuntos de dados.](how-to-create-register-datasets.md)

## <a name="use-datasets-directly-in-training-scripts"></a>Utilize conjuntos de dados diretamente nos scripts de formação

Se tiver dados estruturados ainda não registados como conjunto de dados, crie um Separadors e utilize-os diretamente no seu script de treino para a sua experiência local ou remota.

Neste exemplo, cria-se um [Separador Não](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) Registado e utiliza-o como entrada direta no seu `estimator` objeto para o treino. Se pretender reutilizar este SeparadorDataset com outras experiências no seu espaço de trabalho, consulte [como registar conjuntos de dados no seu espaço de trabalho.](how-to-create-register-datasets.md#register-datasets)

### <a name="create-a-tabulardataset"></a>Criar um Conjunto de Dados Tabular

O código a seguir cria um Separador Não Registado a partir de um url web.  

```Python
from azureml.core.dataset import Dataset

web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path)
```

Os objetos TabularDataset fornecem a capacidade de carregar os dados no seu SeparadorDataset num pandas ou faísca dataFrame para que possa trabalhar com bibliotecas familiares de preparação e formação de dados sem ter de deixar o seu caderno. Para alavancar esta capacidade, consulte [o acesso e explore conjuntos de dados de entrada](#access-and-explore-input-datasets).

### <a name="configure-the-estimator"></a>Configure o estimador

Um [objeto estimador](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) é usado para submeter a experiência. A Azure Machine Learning tem estimativas pré-configuradas para quadros comuns de aprendizagem automática, bem como um estimador genérico.

Este código cria um objeto estimador genérico, `est` que especifica

* Um diretório de guiões para os seus scripts. Todos os ficheiros neste diretório são carregados para os nós do cluster, para execução.
* O guião de treino, *train_titanic.py.*
* O conjunto de dados de entrada para o treino, `titanic_ds` . `as_named_input()`é necessário para que o conjunto de dados de entrada possa ser referenciado pelo nome atribuído `titanic` no seu script de treino. 
* O alvo do cálculo para a experiência.
* A definição ambiental para a experiência.

```Python
est = Estimator(source_directory=script_folder,
                entry_script='train_titanic.py',
                # pass dataset object as an input with name 'titanic'
                inputs=[titanic_ds.as_named_input('titanic')],
                compute_target=compute_target,
                environment_definition= conda_env)

# Submit the estimator as part of your experiment run
experiment_run = experiment.submit(est)
experiment_run.wait_for_completion(show_output=True)
```

## <a name="mount-files-to-remote-compute-targets"></a>Monte ficheiros para alvos de computação remota

Se tiver dados não estruturados, crie um [Dataset de Ficheiros](https://docs.microsoft.com/python/api/azureml-core/azureml.data.filedataset?view=azure-ml-py) e monte ou descarregue os seus ficheiros de dados para os disponibilizar ao seu alvo de computação remota para treino. Saiba quando usar [o mount vs. download](#mount-vs-download) para as suas experiências de treino remoto. 

O exemplo a seguir cria um Dataset de Ficheiros e monta o conjunto de dados para o alvo do cálculo, passando-o como argumento no estimador para a formação. 

### <a name="create-a-filedataset"></a>Criar um Conjunto de Dados de Ficheiros

O exemplo a seguir cria um Dataset de Ficheiros não registado a partir de urls web. Saiba mais sobre [como criar conjuntos de dados a](https://aka.ms/azureml/howto/createdatasets) partir de outras fontes.

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

### <a name="configure-the-estimator"></a>Configure o estimador

Recomendamos a passagem do conjunto de dados como argumento ao montagem. Além de passar o conjunto de dados através do `inputs` parâmetro no estimador, também pode passar o conjunto de dados `script_params` e obter o caminho de dados (ponto de montagem) no seu script de treino através de argumentos. Desta forma, poderá utilizar o mesmo script de treino para depurar local e treinar remotamente em qualquer plataforma em nuvem.

Um objeto estimador [SKLearn](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py) é usado para submeter a corrida para experiências scikit-learn. Após a submissão da execução, os ficheiros de dados referidos pelo `mnist` conjunto de dados serão montados no alvo do cálculo. Saiba mais sobre o treino com o [estimador SKlearn.](how-to-train-scikit-learn.md)

```Python
from azureml.train.sklearn import SKLearn

script_params = {
    # mount the dataset on the remote compute and pass the mounted path as an argument to the training script
    '--data-folder': mnist_ds.as_named_input('mnist').as_mount(),
    '--regularization': 0.5
}

est = SKLearn(source_directory=script_folder,
              script_params=script_params,
              compute_target=compute_target,
              environment_definition=env,
              entry_script='train_mnist.py')

# Run the experiment
run = experiment.submit(est)
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

# retrieve the 2 arguments configured through script_params in estimator
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

Quando monta um conjunto de dados, anexa os ficheiros referenciados pelo conjunto de dados a um diretório (ponto de montagem) e disponibiliza-os no alvo do cálculo. A montagem é suportada para computas baseadas em Linux, incluindo Azure Machine Learning Compute, máquinas virtuais e HDInsight. 

Quando descarrega um conjunto de dados, todos os ficheiros referenciados pelo conjunto de dados serão descarregados para o alvo do cálculo. O download é suportado para todos os tipos de computação. 

Se o seu script processar todos os ficheiros referenciados pelo conjunto de dados e o seu disco de computação puder encaixar no conjunto de dados completo, recomenda-se o download para evitar a sobrecarga de dados de streaming dos serviços de armazenamento. Se o tamanho dos dados exceder o tamanho do disco computacional, o download não é possível. Para este cenário, recomendamos a montagem, uma vez que apenas os ficheiros de dados utilizados pelo seu script são carregados no momento do processamento.

O seguinte código monta `dataset` para o diretório temporário em`mounted_path`

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

## <a name="notebook-examples"></a>Exemplos de cadernos

Os [cadernos de conjuntos de dados](https://aka.ms/dataset-tutorial) demonstram e expandem-se sobre conceitos neste artigo.

## <a name="next-steps"></a>Passos seguintes

* [Modelos de aprendizagem automática de máquinas](how-to-auto-train-remote.md) de comboio com Separadors.

* [Treinar modelos de classificação de imagem](https://aka.ms/filedataset-samplenotebook) com FileDatasets.

* [Treine com conjuntos de dados utilizando gasodutos.](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/pipeline-with-datasets/pipeline-for-image-classification.ipynb)
