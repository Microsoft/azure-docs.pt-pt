---
title: Criar conjuntos de dados para acessar os dados com o azureml-DataSets
titleSuffix: Azure Machine Learning service
description: Saiba como criar conjuntos de itens de várias fontes e registrar conjuntos de registros com seu espaço de trabalho
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 08/22/2019
ms.openlocfilehash: 215660b0f0b8748461849f20e65a3585f939085e
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/10/2019
ms.locfileid: "70858790"
---
# <a name="create-and-access-datasets-preview-in-azure-machine-learning"></a>Criar e acessar conjuntos de os (visualização) no Azure Machine Learning

Neste artigo, você aprenderá a criar conjuntos de dados do Azure Machine Learning (versão prévia) e a acessar os dados de experimentos locais ou remotos.

Com os conjuntos de Azure Machine Learning, você pode: 

* **Mantenha uma única cópia dos dados em seu armazenamento** referenciado por conjuntos. 

* **Acesse dados facilmente durante o treinamento do modelo** sem se preocupar com cadeias de conexão ou caminhos de dados.

* **Compartilhar dados & colaborar** com outros usuários.

## <a name="prerequisites"></a>Pré-requisitos

Para criar e trabalhar com conjuntos de os, você precisa de:

* Uma subscrição do Azure. Se você não tiver uma assinatura do Azure, crie uma conta gratuita antes de começar. Experimente a [versão gratuita ou paga do serviço de Azure Machine Learning](https://aka.ms/AMLFree) hoje mesmo.

* Um [espaço de trabalho de serviço do Azure Machine Learning](how-to-manage-workspace.md)

* O [SDK do Azure Machine Learning para Python instalado](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py), que inclui o pacote de conjuntos de linhas do azureml.

> [!Note]
> Algumas classes de DataSet (versão prévia) têm dependências no pacote [azureml-dataprep](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py) . Para usuários do Linux, essas classes têm suporte apenas nas seguintes distribuições:  Red Hat Enterprise Linux, Ubuntu, Fedora e CentOS.

## <a name="dataset-types"></a>Tipos de conjuntos de conjunto

Os conjuntos de linhas são categorizados em vários tipos com base em como os usuários os consomem no treinamento. Lista de tipos de conjunto de conjuntos:
* [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) representa dados em um formato tabular analisando o arquivo fornecido ou a lista de arquivos. Isso fornece a capacidade de materializar os dados em um dataframe do pandas. Um `TabularDataset` objeto pode ser criado A partir de arquivos CSV, TSV, parquet, resultados da consulta SQL etc. Para obter uma lista completa, visite nossa [documentação](https://aka.ms/tabulardataset-api-reference).
* [Filedataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.file_dataset.filedataset?view=azure-ml-py) faz referência a um ou vários arquivos em seus armazenamentos de dados ou URLs públicas. Isso fornece a capacidade de baixar ou montar os arquivos em sua computação. Os arquivos podem ser de qualquer formato, o que permite uma ampla gama de cenários de aprendizado de máquina, incluindo aprendizado profundo.

Para saber mais sobre as futuras alterações de API, consulte [aqui](https://aka.ms/tabular-dataset).

## <a name="create-datasets"></a>Criar conjuntos de dados 

Ao criar um conjunto de dados, você cria uma referência para o local da fonte de dado, juntamente com uma cópia de seus metadados. Os dados permanecem em seu local existente, portanto, nenhum custo de armazenamento extra é incorrido.

Para que os dados sejam acessíveis pelo serviço Azure Machine Learning, os conjuntos de dados devem ser criados a partir de caminhos em [repositórios de dados do Azure](how-to-access-data.md) ou URLs da Web públicas.

Para criar conjuntos de valores de um [repositório de armazenamento do Azure](how-to-access-data.md):

* Verifique se você `contributor` tem `owner` ou tem acesso ao repositório de armazenamento do Azure registrado.

* Crie o conjunto de um fazendo referência a um caminho no repositório de armazenamento.

```Python
from azureml.core.workspace import Workspace
from azureml.core.datastore import Datastore
from azureml.core.dataset import Dataset

datastore_name = 'your datastore name'

# get existing workspace
workspace = Workspace.from_config()

# retrieve an existing datastore in the workspace by name
datastore = Datastore.get(workspace, datastore_name)
```
### <a name="create-tabulardatasets"></a>Criar TabularDatasets

Use o `from_delimited_files()` método na `TabularDatasetFactory` classe para ler arquivos no formato CSV ou TSV e crie um TabularDataset não registrado. Se você estiver lendo de vários arquivos, os resultados serão agregados em uma representação tabular.

```Python
# create a TabularDataset from multiple paths in datastore
datastore_paths = [
                  (datastore, 'weather/2018/11.csv'),
                  (datastore, 'weather/2018/12.csv'),
                  (datastore, 'weather/2019/*.csv')
                 ]
weather_ds = Dataset.Tabular.from_delimited_files(path=datastore_paths)

# create a TabularDataset from a delimited file behind a public web url
web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path)

# preview the first 3 rows of titanic_ds
titanic_ds.take(3).to_pandas_dataframe()
```

| |Passageiroid|Sobreviveram|Pclass|Name|Sexo|Idade|SibSp|Parch|Concessão|Tarifas|Cabine|Embarcou
-|-----------|--------|------|----|---|---|-----|-----|------|----|-----|--------|
0|1|0|3|Braund, Sr. Owen Harris|masculino|22,0|1|0|A/5 21171|7,2500||D
1|2|1|1|Cumings, Sra. John Bradley (Florence Briggs th...|feminino|38,0|1|0|PC 17599|71,2833|C85|C
2|3|1|3|Heikkinen, erro. Laina|feminino|26,0|0|0|STON/O2. 3101282|7,9250||D

### <a name="create-filedatasets"></a>Criar DataSets
Use o `from_files()` método na `FileDatasetFactory` classe para carregar arquivos em qualquer formato e criar um filedataset não registrado.

```Python
# create a FileDataset from multiple paths in datastore
datastore_paths = [
                  (datastore, 'animals/dog/1.jpg'),
                  (datastore, 'animals/dog/2.jpg'),
                  (datastore, 'animals/dog/*.jpg')
                 ]
animal_ds = Dataset.File.from_files(path=datastore_paths)

# create a FileDataset from image and label files behind public web urls
web_paths = [
            'https://azureopendatastorage.blob.core.windows.net/mnist/train-images-idx3-ubyte.gz',
            'https://azureopendatastorage.blob.core.windows.net/mnist/train-labels-idx1-ubyte.gz'
           ]          
mnist_ds = Dataset.File.from_files(path=web_paths)
```
## <a name="register-datasets"></a>Registrar conjuntos de os

Para concluir o processo de criação, registre seus conjuntos de registros com o espaço de trabalho:

Use o `register()` método para registrar conjuntos de registros em seu espaço de trabalho para que eles possam ser compartilhados com outras pessoas e reutilizados em vários experimentos.

```Python
titanic_ds = titanic_ds.register(workspace = workspace,
                                 name = 'titanic_ds',
                                 description = 'titanic training data')
```

## <a name="version-datasets"></a>Conjuntos de itens de versão

Você pode registrar um novo conjunto de registros com o mesmo nome criando uma nova versão. A versão do conjunto de dados é uma maneira de marcar o estado de seus dados, de modo que você possa aplicar uma versão específica do DataSet para experimentação ou reprodução futura. Cenários típicos para considerar o controle de versão: 
* Quando novos dados estão disponíveis para novo treinamento.
* Ao aplicar as abordagens de preparação de dados ou de engenharia de recursos diferentes.

```Python
# create a TabularDataset from new Titanic training data
web_paths = [
            'https://dprepdata.blob.core.windows.net/demo/Titanic.csv',
            'https://dprepdata.blob.core.windows.net/demo/Titanic2.csv'
           ]          
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_paths)

# create a new version of titanic_ds
titanic_ds = titanic_ds.register(workspace = workspace,
                                 name = 'titanic_ds',
                                 description = 'new titanic training data',
                                 create_new_version = True)
```


## <a name="access-your-data-during-training"></a>Acesse seus dados durante o treinamento

Os conjuntos de itens registrados são acessíveis localmente e remotamente em clusters de computação, como a Azure Machine Learning computação. Para acessar seu conjunto de seus conjuntos de testes entre experimentos, use o código a seguir para obter seu espaço de trabalho e o conjunto de um registrado pelo nome. Por [`get_by_name()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#get-by-name-workspace--name--version--latest--) padrão, o `Dataset` método na classe retorna a versão mais recente do conjunto de informações registrado com o espaço de trabalho.

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

## <a name="next-steps"></a>Passos seguintes

* Use o Machine Learning automatizado para [treinar com o TabularDatasets](https://aka.ms/automl-dataset).
* Para obter mais exemplos de treinamento com conjuntos de informações, consulte os [blocos de anotações de exemplo](https://aka.ms/dataset-tutorial).
