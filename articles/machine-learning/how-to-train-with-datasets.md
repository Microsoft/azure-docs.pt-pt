---
title: Treinar com o azureml – conjuntos de valores
titleSuffix: Azure Machine Learning
description: Saiba como usar conjuntos de valores em treinamento
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 09/25/2019
ms.openlocfilehash: f10de425bf83720b06f3ea7772343edfefc8d952
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/10/2020
ms.locfileid: "75834829"
---
# <a name="train-with-datasets-in-azure-machine-learning"></a>Treine com conjuntos de os Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste artigo, você aprende as duas maneiras de consumir [Azure Machine Learning conjuntos](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py) de dados em execuções de treinamento de teste remoto sem se preocupar com cadeias de conexão ou caminhos.

- Opção 1: se você tiver dados estruturados, crie um TabularDataset e use-o diretamente em seu script de treinamento.

- Opção 2: se você tiver dados não estruturados, crie um filedataset e monte ou baixe arquivos para uma computação remota para treinamento.

Azure Machine Learning conjuntos de valores fornecem uma integração direta com produtos de treinamento Azure Machine Learning como [ScriptRun](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrun?view=azure-ml-py), [Estimator](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py) e [hyperdrive](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive?view=azure-ml-py).

## <a name="prerequisites"></a>Pré-requisitos

Para criar e treinar com conjuntos de os, você precisa:

* Uma subscrição do Azure. Se você não tiver uma assinatura do Azure, crie uma conta gratuita antes de começar. Experimente a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree) hoje.

* Um [espaço de trabalho Azure Machine Learning](how-to-manage-workspace.md).

* O [SDK do Azure Machine Learning para Python instalado](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py), que inclui o pacote de conjuntos de linhas do azureml.

> [!Note]
> Algumas classes de conjunto de objetos têm dependências no pacote [azureml-dataprep](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py) . Para usuários do Linux, essas classes têm suporte apenas nas seguintes distribuições: Red Hat Enterprise Linux, Ubuntu, Fedora e CentOS.

## <a name="option-1-use-datasets-directly-in-training-scripts"></a>Opção 1: usar conjuntos de valores diretamente em scripts de treinamento

Neste exemplo, você cria um [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) e o usa como uma entrada direta para o objeto `estimator` para treinamento. 

### <a name="create-a-tabulardataset"></a>Criar um TabularDataset

O código a seguir cria um TabularDataset não registrado de uma URL da Web. Você também pode criar conjuntos de valores de arquivos locais ou caminhos em repositórios de armazenamento. Saiba mais sobre [como criar conjuntos de](https://aka.ms/azureml/howto/createdatasets)os.

```Python
from azureml.core.dataset import Dataset

web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path)
```

### <a name="access-the-input-dataset-in-your-training-script"></a>Acessar o conjunto de dados de entrada em seu script de treinamento

Os objetos TabularDataset fornecem a capacidade de carregar os dados em um data frame pandas ou Spark para que você possa trabalhar com bibliotecas de treinamento e preparação de dados familiares. Para aproveitar esse recurso, você pode passar um TabularDataset como a entrada em sua configuração de treinamento e, em seguida, recuperá-lo em seu script.

Para fazer isso, acesse o conjunto de dados de entrada por meio do objeto [`Run`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py) em seu script de treinamento e use o método [`to_pandas_dataframe()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#to-pandas-dataframe--) . 

```Python
%%writefile $script_folder/train_titanic.py

from azureml.core import Dataset, Run

run = Run.get_context()
# get the input dataset by name
dataset = run.input_datasets['titanic_ds']
# load the TabularDataset to pandas DataFrame
df = dataset.to_pandas_dataframe()
```

### <a name="configure-the-estimator"></a>Configurar o estimador

Um objeto [estimador](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) é usado para enviar a execução do experimento. Azure Machine Learning tem estimadores pré-configurados para estruturas de aprendizado de máquina comuns, bem como um estimador genérico.

Esse código cria um objeto estimador genérico, `est`, que especifica

* Um diretório de script para seus scripts. Todos os ficheiros neste diretório são carregados para os nós do cluster, para execução.
* O script de treinamento, *train_titanic. py*.
* O conjunto de dados de entrada para treinamento, `titanic`.
* O destino de computação para o experimento.
* A definição de ambiente para o experimento.

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

## <a name="option-2--mount-files-to-a-remote-compute-target"></a>Opção 2: montar arquivos em um destino de computação remota

Se você desejar disponibilizar seus arquivos de dados no destino de computação para treinamento, use [Filedataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.file_dataset.filedataset?view=azure-ml-py) para montar ou baixar arquivos referenciados por ele.

### <a name="mount-vs-download"></a>Montar v.s. Transferir
Ao montar um conjunto de um DataSet, você anexa os arquivos referenciados pelo conjunto de um diretório (ponto de montagem) e os disponibiliza no destino de computação. A montagem tem suporte para computações baseadas em Linux, incluindo Azure Machine Learning computação, máquinas virtuais e HDInsight. Se o tamanho dos seus dados exceder o tamanho do disco de computação ou se você estiver carregando apenas parte do DataSet em seu script, a montagem será recomendada. Como o download de um conjunto de dados maior do que o tamanho do disco falhará, e a montagem carregará apenas a parte do dado usada pelo seu script no momento do processamento. Quando você baixa um conjunto de um DataSet, todos os arquivos referenciados pelo conjunto de um serão baixados para o destino de computação. O download tem suporte para todos os tipos de computação. Se o seu script processar todos os arquivos referenciados pelo conjunto de dados e o disco de computação puder se ajustar ao seu conjunto de dados completo, o download será recomendado para evitar a sobrecarga de streaming dos serviços de armazenamento.


### <a name="create-a-filedataset"></a>Criar um filedataset

O exemplo a seguir cria um filedataset não registrado a partir de URLs da Web. Saiba mais sobre [como criar conjuntos](https://aka.ms/azureml/howto/createdatasets) de informações de outras fontes.

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

### <a name="configure-the-estimator"></a>Configurar o estimador

Em vez de passar o conjunto de dados por meio do parâmetro `inputs` no estimador, você também pode passar o DataSet por meio de `script_params` e obter o caminho de dados (ponto de montagem) em seu script de treinamento por meio de argumentos. Dessa forma, você pode acessar seus dados e usar um script de treinamento existente.

Um objeto de estimador [SKLearn](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py) é usado para enviar a execução para experimentos de scikit-learn. Saiba mais sobre o treinamento com o [estimador do SKlearn](how-to-train-scikit-learn.md).

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

### <a name="retrieve-the-data-in-your-training-script"></a>Recuperar os dados em seu script de treinamento

Depois que você enviar a execução, os arquivos de dados referenciados pelo DataSet de `mnist` serão montados no destino de computação. O código a seguir mostra como recuperar os dados em seu script.

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

## <a name="notebook-examples"></a>Exemplos de notebook

Os [notebooks de conjunto de anotações](https://aka.ms/dataset-tutorial) demonstram e se expandem sobre os conceitos deste artigo.

## <a name="next-steps"></a>Passos seguintes

* [Treinar automaticamente modelos de aprendizado de máquina](how-to-auto-train-remote.md) com o TabularDatasets

* [Treinar modelos de classificação de imagem](https://aka.ms/filedataset-samplenotebook) com DataSets

* [Criar e gerenciar ambientes de treinamento e implantação](how-to-use-environments.md)
