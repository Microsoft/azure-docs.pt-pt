---
title: Comboio com conjuntos de dados em azureml
titleSuffix: Azure Machine Learning
description: Saiba como usar conjuntos de dados em formação
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 03/09/2020
ms.openlocfilehash: 401383f2d483836bf725051810d78167869f7b22
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283501"
---
# <a name="train-with-datasets-in-azure-machine-learning"></a>Treine com conjuntos de dados em Aprendizagem automática Azure
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste artigo, aprende-se as duas formas de consumir conjuntos de dados de [Aprendizagem automática de Azure](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py) num treino de experimentação remota sem se preocupar com cordas de ligação ou caminhos de dados.

- Opção 1: Se tiver dados estruturados, crie um Conjunto TabularDatae utilize-os diretamente no seu script de treino.

- Opção 2: Se tiver dados não estruturados, crie um FileDataset e monte ou descarregue ficheiros para uma computação remota para treino.

Os conjuntos de dados Azure Machine Learning proporcionam uma integração perfeita com produtos de formação de Machine Learning Azure como [scriptRun,](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrun?view=azure-ml-py) [Estimador,](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py) [HiperDrive](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive?view=azure-ml-py) e [Azure Machine Learning.](how-to-create-your-first-pipeline.md)

## <a name="prerequisites"></a>Pré-requisitos

Para criar e treinar com conjuntos de dados, precisa de:

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma conta gratuita antes de começar. Experimente hoje a [versão gratuita ou paga do Azure Machine Learning.](https://aka.ms/AMLFree)

* Um [espaço de trabalho azure machine learning.](how-to-manage-workspace.md)

* O [Azure Machine Learning SDK para Python instalado,](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)que inclui o pacote de conjuntos de dados em azureml.

> [!Note]
> Algumas classes dataset têm dependências no pacote [de dataprep de dados em azureml.](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py) Para os utilizadores do Linux, estas aulas são suportadas apenas nas seguintes distribuições: Red Hat Enterprise Linux, Ubuntu, Fedora e CentOS.

## <a name="option-1-use-datasets-directly-in-training-scripts"></a>Opção 1: Utilizar conjuntos de dados diretamente em scripts de treino

Neste exemplo, cria-se um [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) e utiliza-o `estimator` como entrada direta para o seu objeto para treino. 

### <a name="create-a-tabulardataset"></a>Criar um Conjunto TabularDataset

O código seguinte cria um TabularDataset não registado a partir de um url web. Também pode criar conjuntos de dados a partir de ficheiros ou caminhos locais em lojas de dados. Saiba mais sobre [como criar conjuntos de dados.](https://aka.ms/azureml/howto/createdatasets)

```Python
from azureml.core.dataset import Dataset

web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path)
```

### <a name="access-the-input-dataset-in-your-training-script"></a>Aceda ao conjunto de dados de entrada no seu script de treino

Os objetos TabularDataset fornecem a capacidade de carregar os dados em pandas ou spark DataFrame para que possa trabalhar com bibliotecas familiares de preparação e formação de dados. Para alavancar esta capacidade, pode passar um TabularDataset como entrada na configuração de treino e, em seguida, recuperá-la no seu script.

Para isso, aceda ao conjunto de [`Run`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py) dados de entrada através [`to_pandas_dataframe()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset#to-pandas-dataframe-on-error--null---out-of-range-datetime--null--) do objeto no seu script de treino e utilize o método. 

```Python
%%writefile $script_folder/train_titanic.py

from azureml.core import Dataset, Run

run = Run.get_context()
# get the input dataset by name
dataset = run.input_datasets['titanic']
# load the TabularDataset to pandas DataFrame
df = dataset.to_pandas_dataframe()
```

### <a name="configure-the-estimator"></a>Configure o estimador

Um objeto [estimador](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) é usado para submeter a execução da experiência. O Azure Machine Learning tem estimativas pré-configuradas para quadros comuns de aprendizagem automática, bem como um estimador genérico.

Este código cria um objeto `est`estimador genérico, que especifica

* Um diretório para os seus guiões. Todos os ficheiros neste diretório são carregados para os nós do cluster, para execução.
* O guião de treino, *train_titanic.py.*
* O conjunto de dados `titanic`de entrada para treino, . `as_named_input()`é necessário para que o conjunto de dados de entrada possa ser referenciado pelo nome atribuído no seu script de treino. 
* O alvo da computação para a experiência.
* A definição de ambiente para a experiência.

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


## <a name="option-2--mount-files-to-a-remote-compute-target"></a>Opção 2: Monte ficheiros para um alvo de computação remota

Se pretender disponibilizar os seus ficheiros de dados no alvo da computação para treino, utilize o [FileDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.file_dataset.filedataset?view=azure-ml-py) para montar ou descarregar ficheiros por ele referidos.

### <a name="mount-vs-download"></a>Monte vs. Download

A montagem ou descarregamento de ficheiros de qualquer formato são suportados para conjuntos de dados criados a partir do armazenamento Azure Blob, Ficheiros Azure, Armazenamento de Lagos Azure, Armazenamento de Lagos Azure Gen2, Base de Dados Azure SQL e Base de Dados Azure para PostgreSQL. 

Quando monta um conjunto de dados, fixa-se os ficheiros referenciados pelo conjunto de dados a um diretório (ponto de montagem) e disponibiliza-os no alvo da computação. A montagem é suportada para computações baseadas em Linux, incluindo A Computação de Machine Learning Azure, máquinas virtuais e HDInsight. Quando descarregar um conjunto de dados, todos os ficheiros referenciados pelo conjunto de dados serão descarregados para o alvo da computação. O download é suportado para todos os tipos de computação. 

Se o seu script processar todos os ficheiros referenciados pelo conjunto de dados, e o seu disco computacional puder encaixar no seu conjunto de dados completo, recomenda-se o download para evitar a sobrecarga de dados de streaming dos serviços de armazenamento. Se o tamanho dos seus dados exceder o tamanho do disco computacional, o download não é possível. Para este cenário, recomendamos a montagem, uma vez que apenas os ficheiros de dados utilizados pelo seu script são carregados no momento do processamento.

O código seguinte `dataset` monta para o diretório temporário em`mounted_path`

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

### <a name="create-a-filedataset"></a>Criar um Conjunto de Datatos de Ficheiros

O exemplo seguinte cria um Conjunto de Datatos de Ficheiros não registados a partir de urls web. Saiba mais sobre [como criar conjuntos](https://aka.ms/azureml/howto/createdatasets) de dados de outras fontes.

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

Além de passar o `inputs` conjunto de dados através do parâmetro no estimador, também pode passar o conjunto de dados e `script_params` obter o caminho de dados (ponto de montagem) no seu script de treino através de argumentos. Desta forma, pode manter o seu guião de treino independente do azureml-sdk. Por outras palavras, poderá utilizar o mesmo roteiro de treino para depuração local e treino remoto em qualquer plataforma de nuvem.

Um objeto estimador [SKLearn](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py) é usado para submeter a corrida para experiências de aprendizagem de scikit. Saiba mais sobre formação com o [estimador SKlearn.](how-to-train-scikit-learn.md)

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

### <a name="retrieve-the-data-in-your-training-script"></a>Recupere os dados do seu roteiro de treino

Depois de submeter a execução, `mnist` os ficheiros de dados referidos pelo conjunto de dados serão montados no alvo do cálculo. O código que se segue mostra como recuperar os dados do seu script.

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

## <a name="notebook-examples"></a>Exemplos de cadernos

Os [cadernos conjuntos](https://aka.ms/dataset-tutorial) de dados demonstram e expandem-se sobre conceitos neste artigo.

## <a name="next-steps"></a>Passos seguintes

* [Modelos](how-to-auto-train-remote.md) de aprendizagem automática de máquinas de comboio com TabularDatasets

* [Modelos](https://aka.ms/filedataset-samplenotebook) de classificação de imagem de comboio com FileDatasets

* [Treine com conjuntos de dados usando oleodutos](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/pipeline-with-datasets/pipeline-for-image-classification.ipynb)
