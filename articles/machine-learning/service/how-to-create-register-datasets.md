---
title: Criar conjuntos de dados para acessar os dados com o azureml-DataSets
titleSuffix: Azure Machine Learning
description: Saiba como criar conjuntos de itens de várias fontes e registrar conjuntos de registros com seu espaço de trabalho
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 10/10/2019
ms.openlocfilehash: 54f8a1248688a6d62192e4f34cf6b98a94086da8
ms.sourcegitcommit: f272ba8ecdbc126d22a596863d49e55bc7b22d37
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2019
ms.locfileid: "72274783"
---
# <a name="create-and-access-datasets-preview-in-azure-machine-learning"></a>Criar e acessar conjuntos de os (visualização) no Azure Machine Learning

Neste artigo, você aprenderá a criar conjuntos de dados do Azure Machine Learning (versão prévia) e a acessar os dados de experimentos locais ou remotos.

Com os conjuntos de Azure Machine Learning, você pode:

* **Mantenha uma única cópia dos dados em seu armazenamento** referenciado por conjuntos.

* **Acesse dados facilmente durante o treinamento do modelo** sem se preocupar com cadeias de conexão ou caminhos de dados.

* **Compartilhar dados & colaborar** com outros usuários.

## <a name="prerequisites"></a>Pré-requisitos

Para criar e trabalhar com conjuntos de os, você precisa de:

* Uma subscrição do Azure. Se você não tiver uma assinatura do Azure, crie uma conta gratuita antes de começar. Experimente a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree) hoje.

* Um [espaço de trabalho Azure Machine Learning](how-to-manage-workspace.md)

* O [SDK do Azure Machine Learning para Python instalado](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py), que inclui o pacote de conjuntos de linhas do azureml.

> [!Note]
> Algumas classes de DataSet (versão prévia) têm dependências no pacote [azureml-dataprep](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py) . Para usuários do Linux, essas classes têm suporte apenas nas seguintes distribuições: Red Hat Enterprise Linux, Ubuntu, Fedora e CentOS.

## <a name="dataset-types"></a>Tipos de conjuntos de conjunto

Os conjuntos de linhas são categorizados em dois tipos com base em como os usuários os consomem no treinamento.

* [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) representa dados em um formato tabular analisando o arquivo fornecido ou a lista de arquivos. Isso fornece a capacidade de materializar os dados em um data frame pandas ou Spark. Um objeto `TabularDataset` pode ser criado a partir de arquivos CSV, TSV, parquet, resultados da consulta SQL etc. Para obter uma lista completa, visite nossa [documentação](https://aka.ms/tabulardataset-api-reference).

* [Filedataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.file_dataset.filedataset?view=azure-ml-py) faz referência a um ou vários arquivos em seus armazenamentos de dados ou URLs públicas. Isso fornece a capacidade de baixar ou montar os arquivos em sua computação. Os arquivos podem ser de qualquer formato, o que permite uma ampla gama de cenários de aprendizado de máquina, incluindo aprendizado profundo.

Para saber mais sobre as futuras alterações de API, consulte [aqui](https://aka.ms/tabular-dataset).

## <a name="create-datasets"></a>Criar conjuntos de dados

Ao criar um conjunto de dados, você cria uma referência para o local da fonte de dado, juntamente com uma cópia de seus metadados. Os dados permanecem em seu local existente, portanto, nenhum custo de armazenamento extra é incorrido. Os TabularDatasets e filedatasets podem ser criados usando o SDK do Python ou a página de aterrissagem do espaço de trabalho (versão prévia). 

Para que os dados sejam acessíveis por Azure Machine Learning, os conjuntos de dados devem ser criados a partir de caminhos em [repositórios de dados do Azure](how-to-access-data.md) ou URLs da Web públicas.

### <a name="using-the-sdk"></a>Utilizar o SDK

Para criar conjuntos de valores de um [repositório de armazenamento do Azure](how-to-access-data.md) usando o SDK do Python:

* Verifique se você tem acesso `contributor` ou `owner` ao Azure datastore registrado.

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
#### <a name="create-tabulardatasets"></a>Criar TabularDatasets

Use o método [`from_delimited_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-delimited-files-path--validate-true--include-path-false--infer-column-types-true--set-column-types-none--separator------header--promoteheadersbehavior-all-files-have-same-headers--3---partition-format-none-) na classe `TabularDatasetFactory` para ler arquivos no formato CSV ou TSV e criar um TabularDataset não registrado. Se você estiver lendo de vários arquivos, os resultados serão agregados em uma representação tabular.

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

| |Passageiroid|Sobreviveram|Pclass|Nome|Sexo|Idade|SibSp|Parch|Concessão|Tarifas|Cabine|Embarcou
-|-----------|--------|------|----|---|---|-----|-----|------|----|-----|--------|
0|1|0|3|Braund, Sr. Owen Harris|masculino|22,0|1|0|A/5 21171|7,2500||S
1|2|1|1|Cumings, Sra. John Bradley (Florence Briggs th...|feminino|38,0|1|0|PC 17599|71,2833|C85|C
2|3|1|3|Heikkinen, erro. Laina|feminino|26,0|0|0|STON/O2. 3101282|7,9250||S

Use o método [`from_sql_query()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-sql-query-query--validate-true--set-column-types-none-) na classe `TabularDatasetFactory` para ler do banco de dados SQL do Azure.

```Python

from azureml.core import Dataset, Datastore

# create tabular dataset from a SQL database in datastore
sql_datastore = Datastore.get(workspace, 'mssql')
sql_ds = Dataset.Tabular.from_sql_query((sql_datastore, 'SELECT * FROM my_table'))
```

No TabularDatasets, um carimbo de data/hora pode ser especificado de uma coluna nos dados ou os dados de padrão de caminho são armazenados no para habilitar uma característica de série temporal, que permite a filtragem fácil e eficiente por tempo.

Use o método [`with_timestamp_columns()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#with-timestamp-columns-fine-grain-timestamp--coarse-grain-timestamp-none--validate-false-) na classe `TabularDataset` para especificar a coluna de carimbo de data/hora e habilitar a filtragem por tempo. Mais exemplos e detalhes podem ser encontrados [aqui](https://aka.ms/azureml-tsd-notebook).

```Python
# create a TabularDataset with time series trait
datastore_paths = [(datastore, 'weather/*/*/*/data.parquet')]

# get a coarse timestamp column from the path pattern
dataset = Dataset.Tabular.from_parquet_files(path=datastore_path, partition_format='weather/{coarse_time:yyy/MM/dd}/data.parquet')

# set coarse timestamp to the virtual column created, and fine grain timestamp from a column in the data
dataset = dataset.with_timestamp_columns(fine_grain_timestamp='datetime', coarse_grain_timestamp='coarse_time')

# filter with time-series-trait-specific methods
data_slice = dataset.time_before(datetime(2019, 1, 1))
data_slice = dataset.time_after(datetime(2019, 1, 1))
data_slice = dataset.time_between(datetime(2019, 1, 1), datetime(2019, 2, 1))
data_slice = dataset.time_recent(timedelta(weeks=1, days=1))
```

#### <a name="create-filedatasets"></a>Criar DataSets

Use o método [`from_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?view=azure-ml-py#from-files-path--validate-true-) na classe `FileDatasetFactory` para carregar arquivos em qualquer formato e criar um arquivo de dados não registrado.

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

### <a name="using-the-workspace-landing-page"></a>Usando a página de aterrissagem do espaço de trabalho

Entre na página de [aterrissagem do espaço de trabalho](https://ml.azure.com) para criar um conjunto de um DataSet por meio da experiência na Web. A página de aterrissagem do espaço de trabalho dá suporte à criação de TabularDatasets e filedatasets.

A animação a seguir mostra como criar um conjunto de um DataSet na página de aterrissagem do espaço de trabalho.

Primeiro, selecione **conjuntos** de itens na seção **ativos** do painel esquerdo. Em seguida, selecione **+ criar conjunto** de um para escolher a origem do conjunto de seus conjuntos de seus. Isso pode ser de arquivos locais, repositório de armazenamento ou URLs da Web públicas. Selecione o **tipo de conjunto de texto**: * tabela ou arquivo. As **configurações e a visualização** e os formulários de **esquema** são preenchidos de forma inteligente com base no tipo de arquivo. Selecione **Avançar** para revisá-los ou para configurar ainda mais seu conjunto de informações antes da criação. Selecione **concluído** para concluir a criação do conjunto de conjuntos.

![Criar um conjunto de uma com a interface do usuário](media/how-to-create-register-datasets/create-dataset-ui.gif)

## <a name="register-datasets"></a>Registrar conjuntos de os

Para concluir o processo de criação, registre seus conjuntos de registros com um espaço de trabalho.

Use o método [`register()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#register-workspace--name--description-none--tags-none--visible-true--exist-ok-false--update-if-exist-false-) para registrar conjuntos de registros com seu espaço de trabalho para que eles possam ser compartilhados com outras pessoas e reutilizados em vários experimentos.

```Python
titanic_ds = titanic_ds.register(workspace = workspace,
                                 name = 'titanic_ds',
                                 description = 'titanic training data')
```

>[!Note]
> Os conjuntos de valores criados por meio da página de aterrissagem do espaço de trabalho são automaticamente registrados no espaço de trabalho.

## <a name="version-datasets"></a>Conjuntos de itens de versão

Você pode registrar um novo conjunto de registros com o mesmo nome criando uma nova versão. A versão do conjunto de dados é uma maneira de marcar o estado de seus dados, de modo que você possa aplicar uma versão específica do DataSet para experimentação ou reprodução futura. Os cenários típicos a serem considerados para o controle de versão são: 

* Quando novos dados estão disponíveis para novo treinamento.
* Ao aplicar as abordagens de preparação de dados ou de engenharia de recursos diferentes.

```Python
# create a TabularDataset from Titanic training data
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


## <a name="access-datasets-in-your-script"></a>Acessar conjuntos de os em seu script

Os conjuntos de itens registrados são acessíveis localmente e remotamente em clusters de computação, como a Azure Machine Learning computação. Para acessar seu conjunto de seus conjuntos de testes entre experimentos, use o código a seguir para obter seu espaço de trabalho e o conjunto de um registrado pelo nome. Por padrão, o método [`get_by_name()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#get-by-name-workspace--name--version--latest--) na classe `Dataset` retorna a versão mais recente do conjunto de informações registrado com o espaço de trabalho.

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

* Saiba [como treinar com conjuntos de](how-to-train-with-datasets.md) os
* Use o Machine Learning automatizado para [treinar com o TabularDatasets](https://aka.ms/automl-dataset).
* Para obter mais exemplos de treinamento com conjuntos de informações, consulte os [blocos de anotações de exemplo](https://aka.ms/dataset-tutorial).
