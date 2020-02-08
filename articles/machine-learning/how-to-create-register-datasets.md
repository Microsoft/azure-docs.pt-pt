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
ms.openlocfilehash: 99f4d8d854334b047caf36406f21890cde7eda16
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/08/2020
ms.locfileid: "77082950"
---
# <a name="create-azure-machine-learning-datasets"></a>Criar conjuntos de Azure Machine Learning de os

[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste artigo, você aprenderá a criar conjuntos de dados Azure Machine Learnings para acessar os dados para seus experimentos locais ou remotos.

Com os conjuntos de Azure Machine Learning, você pode:

* Mantenha uma única cópia de dados em seu armazenamento, referenciada por conjuntos.

* Acesse dados diretamente durante o treinamento do modelo sem se preocupar com cadeias de conexão ou caminhos de dados.

* Compartilhe dados e colabore com outros usuários.

## <a name="prerequisites"></a>Pré-requisitos

Para criar e trabalhar com conjuntos de os, você precisa de:

* Uma subscrição do Azure. Se você não tiver uma, crie uma conta gratuita antes de começar. Experimente a [versão gratuita ou paga do Azure Machine Learning.](https://aka.ms/AMLFree)

* Um [espaço de trabalho azure machine learning.](how-to-manage-workspace.md)

* O [Azure Machine Learning SDK para Python instalado,](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)que inclui o pacote de conjuntos de dados em azureml.

> [!NOTE]
> Algumas classes de conjuntos de dados têm dependências no pacote [de dataprep de dados em azureml.](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py) Para usuários do Linux, essas classes têm suporte apenas nas seguintes distribuições: Red Hat Enterprise Linux, Ubuntu, Fedora e CentOS.

## <a name="dataset-types"></a>Tipos de conjuntode dados

Há dois tipos de conjuntos de conjunto de DataSet, com base em como os usuários os consomem no treinamento:

* [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) representa dados num formato tabular, analisando o ficheiro ou lista de ficheiros fornecidos. Isso fornece a capacidade de materializar os dados em um data frame pandas ou Spark. Pode criar um `TabularDataset` objeto a partir de .csv, .tsv, .parquet, .jsonl files, e a partir de resultados de consulta SQL. Para obter uma lista completa, consulte a [classe TabularDatasetFactory](https://aka.ms/tabulardataset-api-reference).

* A classe [FileDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.file_dataset.filedataset?view=azure-ml-py) refere ficheiros únicos ou múltiplos nas suas lojas de dados ou URLs públicos. Por esse método, você pode baixar ou montar os arquivos em sua computação como um objeto filedataset. Os arquivos podem estar em qualquer formato, o que permite uma ampla gama de cenários de aprendizado de máquina, incluindo aprendizado profundo.

Para saber mais sobre as próximas alterações da API, consulte [dataset API change notice](https://aka.ms/tabular-dataset).

## <a name="create-datasets"></a>Criar conjuntos de dados

Ao criar um conjunto de dados, você cria uma referência para o local da fonte de dado, juntamente com uma cópia de seus metadados. Como os dados permanecem em seu local existente, você não incorre em nenhum custo de armazenamento extra. Pode criar conjuntos de dados `TabularDataset` e `FileDataset` utilizando a página de aterragem do Python SDK ou do espaço de trabalho (pré-visualização).

Para que os dados sejam acessíveis pelo Azure Machine Learning, os conjuntos de dados devem ser criados a partir de caminhos em lojas de [dados Azure](how-to-access-data.md) ou URLs web públicos.

### <a name="use-the-sdk"></a>Usar o SDK

Para criar conjuntos de dados a partir de uma [datastore Azure](how-to-access-data.md) utilizando o Python SDK:

1. Verifique se tem acesso `contributor` ou `owner` à loja de dados Azure registada.

2. Crie o conjunto de um referenciando caminhos no repositório de armazenamento.
> [!Note]
> Você pode criar um conjunto de um DataSet a partir de vários caminhos em vários repositórios de armazenamento. Não há nenhum limite rígido para o número de arquivos ou o tamanho de dados do qual você pode criar um DataSet. No entanto, para cada caminho de dados, algumas solicitações serão enviadas ao serviço de armazenamento para verificar se ele aponta para um arquivo ou uma pasta. Essa sobrecarga pode levar à degradação do desempenho ou falha. Um conjunto de dados que faz referência a uma pasta com 1000 arquivos dentro é considerado fazendo referência a um caminho de dado. É recomendável criar um conjunto de consulta que referencie menos de 100 caminhos em repositórios de armazenamento para um desempenho ideal.


#### <a name="create-a-tabulardataset"></a>Criar um TabularDataset

Você pode criar TabularDatasets por meio do SDK ou usando o Azure Machine Learning Studio. 

Utilize o método [`from_delimited_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-delimited-files-path--validate-true--include-path-false--infer-column-types-true--set-column-types-none--separator------header-true--partition-format-none-) na classe `TabularDatasetFactory` para ler ficheiros em formato .csv ou .tsv e para criar um Conjunto TabularDataset não registado. Se você estiver lendo de vários arquivos, os resultados serão agregados em uma representação tabular.

```Python
from azureml.core import Workspace, Datastore, Dataset

datastore_name = 'your datastore name'

# get existing workspace
workspace = Workspace.from_config()
    
# retrieve an existing datastore in the workspace by name
datastore = Datastore.get(workspace, datastore_name)

# create a TabularDataset from 3 paths in datastore
datastore_paths = [(datastore, 'ather/2018/11.csv'),
                   (datastore, 'weather/2018/12.csv'),
                   (datastore, 'weather/2019/*.csv')]
weather_ds = Dataset.Tabular.from_delimited_files(path=datastore_paths)
```

Por padrão, quando você cria um TabularDataset, os tipos de dados de coluna são inferidos automaticamente. Se os tipos deduzidos não corresponderem às suas expectativas, você poderá especificar tipos de coluna usando o código a seguir. Também pode [saber mais sobre tipos de dados suportados.](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.datatype?view=azure-ml-py)

```Python
from azureml.data.dataset_factory import DataType

# create a TabularDataset from a delimited file behind a public web url and convert column "Survived" to boolean
web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path, set_column_types={'Survived': DataType.to_bool()})

# preview the first 3 rows of titanic_ds
titanic_ds.take(3).to_pandas_dataframe()
```

| |Passageiroid|Sobreviveram|Pclass|Nome|Sexo|Idade|SibSp|Parch|Bilhete|Tarifas|Cabine|Embarcou
-|-----------|--------|------|----|---|---|-----|-----|------|----|-----|--------|
0|1|Falso|3|Braund, Sr. Owen Harris|masculino|22,0|1|0|A/5 21171|7,2500||S
1|2|Verdadeiro|1|Cumings, Sra. John Bradley (Florence Briggs th...|feminino|38,0|1|0|PC 17599|71,2833|C85|C
2|3|Verdadeiro|3|Heikkinen, erro. Laina|feminino|26,0|0|0|STON/O2. 3101282|7,9250||S

Utilize o método [`from_sql_query()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-sql-query-query--validate-true--set-column-types-none-) na classe `TabularDatasetFactory` para ler a partir da Base de Dados Azure SQL:

```Python

from azureml.core import Dataset, Datastore

# create tabular dataset from a SQL database in datastore
sql_datastore = Datastore.get(workspace, 'mssql')
sql_ds = Dataset.Tabular.from_sql_query((sql_datastore, 'SELECT * FROM my_table'))
```

No TabularDatasets, você pode especificar um carimbo de data/hora de uma coluna nos dados ou de onde quer que os dados de padrão de caminho sejam armazenados para habilitar uma característica de série temporal. Essa especificação permite uma filtragem fácil e eficiente por tempo.

Utilize o método [`with_timestamp_columns()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#with-timestamp-columns-fine-grain-timestamp--coarse-grain-timestamp-none--validate-false-) na classe`TabularDataset` para especificar a coluna de carimbos de tempo e para permitir a filtragem com o tempo. Para mais informações, consulte a demonstração da API relacionada com a [série de tempo Tabular com dados meteorológicos da NOAA](https://aka.ms/azureml-tsd-notebook).

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

Utilize o método [`from_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?view=azure-ml-py#from-files-path--validate-true-) na classe `FileDatasetFactory` para carregar ficheiros em qualquer formato e criar um Conjunto de Datatos de Ficheiros não registado:

```Python
# create a FileDataset pointing to files in 'animals' folder and its subfolders recursively
datastore_paths = [(datastore, 'animals')]
animal_ds = Dataset.File.from_files(path=datastore_paths)

# create a FileDataset from image and label files behind public web urls
web_paths = ['https://azureopendatastorage.blob.core.windows.net/mnist/train-images-idx3-ubyte.gz',
             'https://azureopendatastorage.blob.core.windows.net/mnist/train-labels-idx1-ubyte.gz']
mnist_ds = Dataset.File.from_files(path=web_paths)
```

#### <a name="on-the-web"></a>Na web 
Os seguintes passos e animação mostram como criar um conjunto de dados no estúdio Azure Machine Learning, https://ml.azure.com.

![Criar um conjunto de uma com a interface do usuário](./media/how-to-create-register-datasets/create-dataset-ui.gif)

Para criar um conjunto de um DataSet no estúdio:
1. Inscreva-se no https://ml.azure.com.
1. Selecione **Conjuntos** de Dados na secção **Ativos** do painel esquerdo. 
1. Selecione **Criar Dataset** para escolher a fonte do seu conjunto de dados. Essa fonte pode ser arquivos locais, um repositório de armazenamento ou URLs públicas.
1. **Selecione Tabular** ou **File** para o tipo Dataset.
1. Selecione **Next** para rever os formulários de **Definições e pré-visualização,** **Schema** e **Confirme;** são povoados inteligentemente com base no tipo de ficheiro. Use esses formulários para verificar suas seleções e configurar ainda mais seu conjunto de informações antes da criação.  
1. Selecione **Criar** para completar a sua criação de conjunto de dados.

## <a name="register-datasets"></a>Registrar conjuntos de os

Para concluir o processo de criação, registre seus conjuntos de registros com um espaço de trabalho. Utilize o método [`register()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.abstract_dataset.abstractdataset?view=azure-ml-py#register-workspace--name--description-none--tags-none--create-new-version-false-) para registar conjuntos de dados com o seu espaço de trabalho para partilhá-los com outros e reutilizá-los em várias experiências:

```Python
titanic_ds = titanic_ds.register(workspace=workspace,
                                 name='titanic_ds',
                                 description='titanic training data')
```

> [!Note]
> Os conjuntos de valores criados por meio do Azure Machine Learning Studio são automaticamente registrados no espaço de trabalho.

## <a name="create-datasets-with-azure-open-datasets"></a>Criar conjuntos de itens com os conjuntos de valores abertos do Azure

[Os Conjuntos de Dados Abertos do Azure](https://azure.microsoft.com/services/open-datasets/) são conjuntos de dados públicos com curadoria que pode utilizar para adicionar funcionalidades específicas ao cenário a soluções de aprendizagem automática para modelos mais precisos. DataSets incluem dados de domínio público para clima, censo, feriados, segurança pública e local que ajudam você a treinar modelos de aprendizado de máquina e enriquecer soluções preditivas. Os conjuntos de itens abertos estão na nuvem em Microsoft Azure e são incluídos no SDK e na interface do usuário do espaço de trabalho.

### <a name="use-the-sdk"></a>Usar o SDK

Para criar conjuntos de dados com conjuntos de dados Abertos Azure a partir do SDK, certifique-se de que instalou o pacote com `pip install azureml-opendatasets`. Cada conjunto de dados discreto é representado pela sua própria classe no SDK, e certas classes estão disponíveis como um `TabularDataset`, `FileDataset`, ou ambos. Consulte a [documentação](https://docs.microsoft.com/python/api/azureml-opendatasets/azureml.opendatasets?view=azure-ml-py) de referência para uma lista completa de aulas.

Pode recuperar determinadas classes como um `TabularDataset` ou `FileDataset`, o que lhe permite manipular e/ou descarregar os ficheiros diretamente. Outras classes **só** podem obter um conjunto de dados utilizando uma das funções `get_tabular_dataset()` ou `get_file_dataset()`. O exemplo de código a seguir mostra alguns exemplos desses tipos de classes.

```python
from azureml.opendatasets import MNIST

# MNIST class can return either TabularDataset or FileDataset
tabular_dataset = MNIST.get_tabular_dataset()
file_dataset = MNIST.get_file_dataset()

from azureml.opendatasets import Diabetes

# Diabetes class can return ONLY return TabularDataset and must be called from the static function
diabetes_tabular = Diabetes.get_tabular_dataset()
```

Quando você registra um conjunto de dados criado a partir de DataSets abertos, nenhum dado é baixado imediatamente, mas os dados serão acessados posteriormente quando solicitado (durante o treinamento, por exemplo) de um local de armazenamento central.

### <a name="use-the-ui"></a>Usar a interface do usuário

Você também pode criar conjuntos de valores de classes de conjuntos de itens abertos por meio da interface do usuário. No seu espaço de trabalho, selecione o separador **Datasets** em **Ativos**. No menu de entrega do conjunto de **dados Create,** selecione **From Open Datasets**.

![Abrir conjunto de um com a interface do usuário](./media/how-to-create-register-datasets/open-datasets-1.png)

Selecione um conjunto de um DataSet selecionando seu bloco. (Tem a opção de filtrar utilizando a barra de pesquisa.) Selecione **Next**.

![Escolher conjunto de um](./media/how-to-create-register-datasets/open-datasets-2.png)

Escolha um nome sob o qual registrar o conjunto de dados e, opcionalmente, filtre-os usando os filtros disponíveis. Nesse caso, para o conjunto de data de feriados públicos, você filtra o período de tempo para um ano e o código do país apenas para os EUA. Selecione **Criar**.

![Definir parâmetros de conjunto de DataSet e criar conjunto de um](./media/how-to-create-register-datasets/open-datasets-3.png)

O conjunto de dados está agora disponível no seu espaço de trabalho sob **conjuntos**de dados . Você pode usá-lo da mesma maneira que outros conjuntos de os que você criou.

## <a name="version-datasets"></a>Conjuntos de itens de versão

Você pode registrar um novo conjunto de registros com o mesmo nome criando uma nova versão. Uma versão de conjunto de dados é uma maneira de marcar o estado de seus dados de forma que você possa aplicar uma versão específica do conjunto para experimentação ou reprodução futura. Saiba mais sobre [as versões do conjunto](how-to-version-track-datasets.md)de dados.
```Python
# create a TabularDataset from Titanic training data
web_paths = ['https://dprepdata.blob.core.windows.net/demo/Titanic.csv',
             'https://dprepdata.blob.core.windows.net/demo/Titanic2.csv']
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_paths)

# create a new version of titanic_ds
titanic_ds = titanic_ds.register(workspace = workspace,
                                 name = 'titanic_ds',
                                 description = 'new titanic training data',
                                 create_new_version = True)
```

## <a name="access-datasets-in-your-script"></a>Acessar conjuntos de os em seu script

Os conjuntos de itens registrados são acessíveis localmente e remotamente em clusters de computação, como a Azure Machine Learning computação. Para acessar seu conjunto de seus conjuntos de testes entre experimentos, use o código a seguir para acessar seu espaço de trabalho e o conjunto de código registrado por nome. Por padrão, o método [`get_by_name()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#get-by-name-workspace--name--version--latest--) na classe `Dataset` devolve a versão mais recente do conjunto de dados que está registado no espaço de trabalho.

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

* Saiba [treinar com conjuntos](how-to-train-with-datasets.md)de dados.
* Utilize machine learning automatizado para [treinar com TabularDatasets](https://aka.ms/automl-dataset).
* Para obter mais exemplos de formação de conjuntos de dados, consulte os [cadernos de amostras](https://aka.ms/dataset-tutorial).
