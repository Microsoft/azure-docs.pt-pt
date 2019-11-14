---
title: Criar conjuntos de dados de Azure Machine Learning para acessar o dado
titleSuffix: Azure Machine Learning
description: Saiba como criar conjuntos de dados de Azure Machine Learning para acessar seus dados para execuções de experimento de aprendizado de máquina.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 11/04/2019
ms.openlocfilehash: 50728363ffd02e189b4bc0dacebd684a7f13091e
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74030718"
---
# <a name="create-azure-machine-learning-datasets"></a>Criar conjuntos de Azure Machine Learning de os

[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste artigo, você aprenderá a criar conjuntos de dados Azure Machine Learnings para acessar os dados para seus experimentos locais ou remotos.

Com os conjuntos de Azure Machine Learning, você pode:

* Mantenha uma única cópia de dados em seu armazenamento, referenciada por conjuntos.

* Acesse dados diretamente durante o treinamento do modelo sem se preocupar com cadeias de conexão ou caminhos de dados.

* Compartilhe dados e colabore com outros usuários.

## <a name="prerequisites"></a>Pré-requisitos

Para criar e trabalhar com conjuntos de os, você precisa de:

* Uma subscrição do Azure. Se você não tiver uma, crie uma conta gratuita antes de começar. Experimente a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree).

* Um [espaço de trabalho Azure Machine Learning](how-to-manage-workspace.md).

* O [SDK do Azure Machine Learning para Python instalado](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py), que inclui o pacote de conjuntos de linhas do azureml.

> [!NOTE]
> Algumas classes de conjunto de objetos têm dependências no pacote [azureml-dataprep](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py) . Para usuários do Linux, essas classes têm suporte apenas nas seguintes distribuições: Red Hat Enterprise Linux, Ubuntu, Fedora e CentOS.

## <a name="dataset-types"></a>Tipos de conjuntos de conjunto

Há dois tipos de conjuntos de conjunto de DataSet, com base em como os usuários os consomem no treinamento:

* [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) representa dados em um formato tabular analisando o arquivo fornecido ou a lista de arquivos. Isso fornece a capacidade de materializar os dados em um data frame pandas ou Spark. Você pode criar um objeto de `TabularDataset` de arquivos. csv,. tsv e parquet e dos resultados da consulta SQL. Para obter uma lista completa, consulte [classe TabularDatasetFactory](https://aka.ms/tabulardataset-api-reference).

* A classe [Filedataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.file_dataset.filedataset?view=azure-ml-py) faz referência a um ou vários arquivos em seus armazenamentos de dados ou URLs públicas. Por esse método, você pode baixar ou montar os arquivos em sua computação como um objeto filedataset. Os arquivos podem estar em qualquer formato, o que permite uma ampla gama de cenários de aprendizado de máquina, incluindo aprendizado profundo.

Para saber mais sobre as próximas alterações de API, consulte [aviso de alteração da API do conjunto de notícias](https://aka.ms/tabular-dataset).

## <a name="create-datasets"></a>Criar conjuntos de dados

Ao criar um conjunto de dados, você cria uma referência para o local da fonte de dado, juntamente com uma cópia de seus metadados. Como os dados permanecem em seu local existente, você não incorre em nenhum custo de armazenamento extra. Você pode criar `TabularDataset` e `FileDataset` conjuntos de dados usando o SDK do Python ou a página de aterrissagem do espaço de trabalho (versão prévia).

Para que os dados sejam acessíveis por Azure Machine Learning, os conjuntos de dados devem ser criados a partir de caminhos em [repositórios de dados do Azure](how-to-access-data.md) ou URLs da Web públicas.

### <a name="use-the-sdk"></a>Usar o SDK

Para criar conjuntos de itens de um [repositório de armazenamento do Azure](how-to-access-data.md) usando o SDK do Python:

1. Verifique se você tem `contributor` ou `owner` acesso ao Azure datastore registrado.

1. Crie o conjunto de um fazendo referência a um caminho no repositório de armazenamento:

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

#### <a name="create-a-tabulardataset"></a>Criar um TabularDataset

Você pode criar TabularDatasets por meio do SDK ou usando Azure Machine Learning Studio. Você pode especificar um carimbo de data/hora de uma coluna nos dados ou do padrão de caminho em que os dados são armazenados para habilitar uma característica de série temporal. Essa especificação permite uma filtragem fácil e eficiente por tempo.

Use o método [`from_delimited_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-delimited-files-path--validate-true--include-path-false--infer-column-types-true--set-column-types-none--separator------header--promoteheadersbehavior-all-files-have-same-headers--3---partition-format-none-) na classe `TabularDatasetFactory` para ler arquivos no formato. csv ou. tsv e para criar um TabularDataset não registrado. Se você estiver lendo de vários arquivos, os resultados serão agregados em uma representação tabular.

```Python
# create a TabularDataset from multiple paths in datastore
datastore_paths = [
                  (datastore, 'weather/2018/11.csv'),
                  (datastore, 'weather/2018/12.csv'),
                  (datastore, 'weather/2019/*.csv')
                 ]
weather_ds = Dataset.Tabular.from_delimited_files(path=datastore_paths)
```

Por padrão, quando você cria um TabularDataset, os tipos de dados de coluna são inferidos automaticamente. Se os tipos deduzidos não corresponderem às suas expectativas, você poderá especificar tipos de coluna usando o código a seguir. Você também pode [saber mais sobre os tipos de dados com suporte](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.datatype?view=azure-ml-py).

```Python
from azureml.data.dataset_factory import DataType

# create a TabularDataset from a delimited file behind a public web url and convert column "Survived" to boolean
web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path, set_column_types={'Survived': DataType.to_bool()})

# preview the first 3 rows of titanic_ds
titanic_ds.take(3).to_pandas_dataframe()
```

| |Passageiroid|Sobreviveram|Pclass|Nome|Sexo|Idade|SibSp|Parch|Concessão|Tarifas|Cabine|Embarcou
-|-----------|--------|------|----|---|---|-----|-----|------|----|-----|--------|
0|1|Falso|3|Braund, Sr. Owen Harris|masculino|22,0|1|0|A/5 21171|7,2500||S
1|2|Verdadeiro|1|Cumings, Sra. John Bradley (Florence Briggs th...|feminino|38,0|1|0|PC 17599|71,2833|C85|C
2|3|Verdadeiro|3|Heikkinen, erro. Laina|feminino|26,0|0|0|STON/O2. 3101282|7,9250||S

Use o método [`from_sql_query()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-sql-query-query--validate-true--set-column-types-none-) na classe `TabularDatasetFactory` para ler do banco de dados SQL do Azure:

```Python

from azureml.core import Dataset, Datastore

# create tabular dataset from a SQL database in datastore
sql_datastore = Datastore.get(workspace, 'mssql')
sql_ds = Dataset.Tabular.from_sql_query((sql_datastore, 'SELECT * FROM my_table'))
```

No TabularDatasets, você pode especificar um carimbo de data/hora de uma coluna nos dados ou de onde quer que os dados de padrão de caminho sejam armazenados para habilitar uma característica de série temporal. Essa especificação permite uma filtragem fácil e eficiente por tempo.

Use o método [`with_timestamp_columns()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#with-timestamp-columns-fine-grain-timestamp--coarse-grain-timestamp-none--validate-false-) na classe`TabularDataset` para especificar a coluna de carimbo de data/hora e habilitar a filtragem por tempo. Para obter mais informações, consulte [tabela de demonstração de API relacionada à série temporal com dados meteorológicos NOAA](https://aka.ms/azureml-tsd-notebook).

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

#### <a name="create-a-filedataset"></a>Criar um filedataset

Use o método [`from_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?view=azure-ml-py#from-files-path--validate-true-) na classe `FileDatasetFactory` para carregar arquivos em qualquer formato e para criar um filedataset não registrado:

```Python
# create a FileDataset pointing to files in 'animals' folder and its subfolders recursively
datastore_paths = [
                  (datastore, 'animals')
                 ]
animal_ds = Dataset.File.from_files(path=datastore_paths)

# create a FileDataset from image and label files behind public web urls
web_paths = [
            'https://azureopendatastorage.blob.core.windows.net/mnist/train-images-idx3-ubyte.gz',
            'https://azureopendatastorage.blob.core.windows.net/mnist/train-labels-idx1-ubyte.gz'
           ]
mnist_ds = Dataset.File.from_files(path=web_paths)
```

#### <a name="on-the-web"></a>Na Web 
As etapas e a animação a seguir mostram como criar um conjunto de Azure Machine Learning Studio, https://ml.azure.com.

![Criar um conjunto de uma com a interface do usuário](media/how-to-create-register-datasets/create-dataset-ui.gif)

Para criar um conjunto de um DataSet no estúdio:
1. Entre em https://ml.azure.com.
1. Selecione **conjuntos** de itens na seção **ativos** do painel esquerdo. 
1. Selecione **criar conjunto** de um para escolher a origem do conjunto de seus conjuntos de um. Essa fonte pode ser arquivos locais, um repositório de armazenamento ou URLs públicas.
1. Selecione **tabela** ou **arquivo** para o tipo de conjunto de texto.
1. Selecione **Avançar** para revisar as **configurações e Visualizar**, **esquema** e confirmar formulários de **detalhes** ; Eles são preenchidos de forma inteligente com base no tipo de arquivo. Use esses formulários para verificar suas seleções e configurar ainda mais seu conjunto de informações antes da criação.  
1. Selecione **criar** para concluir a criação do conjunto de seus conjuntos de os.

## <a name="register-datasets"></a>Registrar conjuntos de os

Para concluir o processo de criação, registre seus conjuntos de registros com um espaço de trabalho. Use o método [`register()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#register-workspace--name--description-none--tags-none--visible-true--exist-ok-false--update-if-exist-false-) para registrar conjuntos de registros com seu espaço de trabalho a fim de compartilhá-los com outras pessoas e reutilizá-los em vários experimentos:

```Python
titanic_ds = titanic_ds.register(workspace=workspace,
                                 name='titanic_ds',
                                 description='titanic training data')
```

> [!Note]
> Os conjuntos de valores criados por meio de Azure Machine Learning Studio são automaticamente registrados no espaço de trabalho.

## <a name="create-datasets-with-azure-open-datasets"></a>Criar conjuntos de itens com os conjuntos de valores abertos do Azure

Os conjuntos de itens [abertos do Azure](https://azure.microsoft.com/services/open-datasets/) são conjuntos de informações públicos estruturados que você pode usar para adicionar recursos específicos do cenário a soluções de aprendizado de máquina para modelos mais precisos. DataSets incluem dados de domínio público para clima, censo, feriados, segurança pública e local que ajudam você a treinar modelos de aprendizado de máquina e enriquecer soluções preditivas. Os conjuntos de itens abertos estão na nuvem em Microsoft Azure e são incluídos no SDK e na interface do usuário do espaço de trabalho.

### <a name="use-the-sdk"></a>Usar o SDK

Para criar conjuntos de itens com o Azure Open DataSets do SDK, verifique se você instalou o pacote com `pip install azureml-opendatasets`. Cada conjunto de dados discretos é representado por sua própria classe no SDK, e determinadas classes estão disponíveis como um `TabularDataset`, `FileDataset`ou ambos. Consulte a [documentação de referência](https://docs.microsoft.com/python/api/azureml-opendatasets/azureml.opendatasets?view=azure-ml-py) para obter uma lista completa de classes.

A maioria das classes herdam de e retornam uma instância de `TabularDataset`. Exemplos dessas classes incluem `PublicHolidays`, `BostonSafety`e `UsPopulationZip`. Para criar um `TabularDataset` a partir desses tipos de classes, use o construtor sem argumentos. Quando você registra um conjunto de dados criado a partir de DataSets abertos, nenhum dado é baixado imediatamente, mas os dados serão acessados posteriormente quando solicitado (durante o treinamento, por exemplo) de um local de armazenamento central. 

```python
from azureml.opendatasets import UsPopulationZip

tabular_dataset = UsPopulationZip()
tabular_dataset = tabular_dataset.register(workspace=workspace, name="pop data", description="US population data by zip code")
```

Você pode recuperar determinadas classes como um `TabularDataset` ou `FileDataset`, o que permite manipular e/ou baixar os arquivos diretamente. Outras classes só podem obter um conjunto de um DataSet usando as funções `get_tabular_dataset()` ou `get_file_dataset()`. O exemplo de código a seguir mostra alguns exemplos desses tipos de classes:

```python
from azureml.opendatasets import MNIST

# MNIST class can return either TabularDataset or FileDataset
tabular_dataset = MNIST.get_tabular_dataset()
file_dataset = MNIST.get_file_dataset()

from azureml.opendatasets import Diabetes

# Diabetes class can return ONLY return TabularDataset and must be called from the static function
diabetes_tabular = Diabetes.get_tabular_dataset()
```

### <a name="use-the-ui"></a>Usar a interface do usuário

Você também pode criar conjuntos de valores de classes de conjuntos de itens abertos por meio da interface do usuário. Em seu espaço de trabalho, selecione a guia **conjuntos de valores** em **ativos**. No menu suspenso **criar conjunto** de linhas, selecione **de conjuntos de valores abertos**.

![Abrir conjunto de um com a interface do usuário](media/how-to-create-register-datasets/open-datasets-1.png)

Selecione um conjunto de um DataSet selecionando seu bloco. (Você tem a opção de filtrar usando a barra de pesquisa.) Selecione **Avançar**.

![Escolher conjunto de um](media/how-to-create-register-datasets/open-datasets-2.png)

Escolha um nome sob o qual registrar o conjunto de dados e, opcionalmente, filtre-os usando os filtros disponíveis. Nesse caso, para o conjunto de data de feriados públicos, você filtra o período de tempo para um ano e o código do país apenas para os EUA. Selecione **Criar**.

![Definir parâmetros de conjunto de DataSet e criar conjunto de um](media/how-to-create-register-datasets/open-datasets-3.png)

Agora, o conjunto de linhas está disponível em seu espaço de trabalho em **conjuntos**de os. Você pode usá-lo da mesma maneira que outros conjuntos de os que você criou.

## <a name="version-datasets"></a>Conjuntos de itens de versão

Você pode registrar um novo conjunto de registros com o mesmo nome criando uma nova versão. Uma versão de conjunto de dados é uma maneira de marcar o estado de seus dados de forma que você possa aplicar uma versão específica do conjunto para experimentação ou reprodução futura. Saiba mais sobre [as versões do conjunto](how-to-version-track-datasets.md)de informações.
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

Os conjuntos de itens registrados são acessíveis localmente e remotamente em clusters de computação, como a Azure Machine Learning computação. Para acessar seu conjunto de seus conjuntos de testes entre experimentos, use o código a seguir para acessar seu espaço de trabalho e o conjunto de código registrado por nome. Por padrão, o método [`get_by_name()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#get-by-name-workspace--name--version--latest--) na classe `Dataset` retorna a versão mais recente do conjunto de informações registrado com o espaço de trabalho.

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

* Saiba [como treinar com conjuntos de](how-to-train-with-datasets.md)informações.
* Use o Machine Learning automatizado para [treinar com o TabularDatasets](https://aka.ms/automl-dataset).
* Para obter mais exemplos de treinamento de conjunto de informações, consulte os [blocos de anotações de exemplo](https://aka.ms/dataset-tutorial).
