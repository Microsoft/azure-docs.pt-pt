---
title: Criar conjuntos de dados de Aprendizagem automática Azure para aceder a dados
titleSuffix: Azure Machine Learning
description: Aprenda a criar conjuntos de dados de Aprendizagem automática Azure para aceder aos seus dados para testes de aprendizagem automática.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 02/10/2020
ms.openlocfilehash: 817ff90c10a29d7db7037d89f3c3d51e7f997175
ms.sourcegitcommit: b8d0d72dfe8e26eecc42e0f2dbff9a7dd69d3116
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2020
ms.locfileid: "79037178"
---
# <a name="create-azure-machine-learning-datasets"></a>Criar conjuntos de dados de aprendizagem automática Azure

[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste artigo, aprende-se a criar conjuntos de dados de Aprendizagem automática Azure para aceder a dados para as suas experiências locais ou remotas.

Com conjuntos de dados de Aprendizagem automática Azure, pode:

* Guarde uma única cópia de dados no seu armazenamento, referenciada por conjuntos de dados.

* Aceda perfeitamente aos dados durante o treino do modelo sem se preocupar com cordas de ligação ou caminhos de dados.

* Partilhe dados e colabore com outros utilizadores.

## <a name="prerequisites"></a>Pré-requisitos
' Para criar e trabalhar com conjuntos de dados, você precisa:

* Uma subscrição do Azure. Se não tiver uma, crie uma conta gratuita antes de começar. Experimente a [versão gratuita ou paga do Azure Machine Learning.](https://aka.ms/AMLFree)

* Um [espaço de trabalho azure machine learning.](how-to-manage-workspace.md)

* O [Azure Machine Learning SDK para Python instalado,](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)que inclui o pacote de conjuntos de dados em azureml.

> [!NOTE]
> Algumas classes de conjuntos de dados têm dependências no pacote [de dataprep de dados em azureml.](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py) Para os utilizadores do Linux, estas aulas são suportadas apenas nas seguintes distribuições: Red Hat Enterprise Linux, Ubuntu, Fedora e CentOS.

## <a name="compute-size-guidance"></a>Orientação do tamanho do cálculo

Ao criar um conjunto de dados, reveja o seu poder de processamento de cálculo e o tamanho dos seus dados na memória. O tamanho dos seus dados no armazenamento não é o mesmo que o tamanho dos dados num quadro de dados. Por exemplo, os dados em ficheiros CSV podem expandir até 10x num dataframe, para que um ficheiro CSV de 1 GB possa tornar-se de 10 GB num quadro de dados. 

O principal fator é a dimensão do conjunto de dados na memória, ou seja, como um dataframe. Recomendamos que o seu tamanho de computação e a sua potência de processamento contenham 2x do tamanho de RAM. Portanto, se o seu dataframe for de 10GB, pretende um alvo computacional com mais de 20 GB de RAM para garantir que o dataframe pode encaixar-se confortável na memória e ser processado. Se os seus dados forem comprimidos, pode expandir-se ainda mais; 20 GB de dados relativamente escassos armazenados em formato parquet comprimido pode expandir-se para ~800 GB na memória. Uma vez que os ficheiros Parquet armazenam dados num formato coluna, se necessitar apenas de metade das colunas, então só precisa de carregar ~400 GB na memória.
 
Se estivera a usar Pandas, não há razão para ter mais de 1 vCPU, já que é tudo o que vai usar. Você pode facilmente paralelizar a muitos vCPUs em um único conjunto de computação de Machine Learning Azure exemplo/nó via Modin e Dask/Ray, e escalar para um grande cluster, se necessário, simplesmente mudando `import pandas as pd` para `import modin.pandas as pd`. 
 
Se não conseguir obter um virtual suficientemente grande para os dados, tem duas opções: usar uma estrutura como Spark ou Dask para realizar o processamento dos dados 'fora de memória', ou seja, o dataframe é carregado na divisória DERAM por partição e processado, sendo o resultado final a ser recolhtido ed no final. Se isto for demasiado lento, o Spark ou o Dask permitem-lhe escalar para um cluster que ainda pode ser utilizado interativamente. 

## <a name="dataset-types"></a>Tipos de conjuntode dados

Existem dois tipos de conjuntos de dados, baseados na forma como os utilizadores os consomem na formação:

* [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) representa dados num formato tabular, analisando o ficheiro ou lista de ficheiros fornecidos. Isto fornece-lhe a capacidade de materializar os dados num Pandas ou Spark DataFrame. Pode criar um `TabularDataset` objeto a partir de .csv, .tsv, .parquet, .jsonl files, e a partir de resultados de consulta SQL. Para obter uma lista completa, consulte a [classe TabularDatasetFactory](https://aka.ms/tabulardataset-api-reference).

* A classe [FileDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.file_dataset.filedataset?view=azure-ml-py) refere ficheiros únicos ou múltiplos nas suas lojas de dados ou URLs públicos. Por este método, pode descarregar ou montar os ficheiros para a sua computação como um objeto FileDataset. Os ficheiros podem estar em qualquer formato, o que permite uma gama mais alargada de cenários de aprendizagem automática, incluindo aprendizagem profunda.

Para saber mais sobre as próximas alterações da API, consulte [dataset API change notice](https://aka.ms/tabular-dataset).

## <a name="create-datasets"></a>Criar conjuntos de dados

Ao criar um conjunto de dados, cria-se uma referência à localização da fonte de dados, juntamente com uma cópia dos seus metadados. Como os dados permanecem na sua localização existente, não incorre em nenhum custo extra de armazenamento. Pode criar conjuntos de dados `TabularDataset` e `FileDataset` utilizando o Python SDK ou em https://ml.azure.com.

Para que os dados sejam acessíveis pelo Azure Machine Learning, os conjuntos de dados devem ser criados a partir de caminhos em lojas de [dados Azure](how-to-access-data.md) ou URLs web públicos. 

### <a name="use-the-sdk"></a>Use o SDK

Para criar conjuntos de dados a partir de uma [datastore Azure](how-to-access-data.md) utilizando o Python SDK:

1. Verifique se tem acesso `contributor` ou `owner` à loja de dados Azure registada.

2. Criar o conjunto de dados referenciando caminhos na loja de dados.
> [!Note]
> Pode criar um conjunto de dados a partir de múltiplos caminhos em várias lojas de dados. Não existe um limite rígido no número de ficheiros ou tamanho de dados a partir do dia. No entanto, para cada trajetória de dados, alguns pedidos serão enviados para o serviço de armazenamento para verificar se aponta para um ficheiro ou uma pasta. Esta sobrecarga pode levar a desempenho ou falha degradada. Um conjunto de dados que referencia uma pasta com 1000 ficheiros no interior é considerado referenciando uma via de dados. Recomendamos a criação de dataset referenciando menos de 100 caminhos nas lojas de dados para um desempenho ótimo.

#### <a name="create-a-tabulardataset"></a>Criar um Conjunto TabularDataset

Utilize o método [`from_delimited_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-delimited-files-path--validate-true--include-path-false--infer-column-types-true--set-column-types-none--separator------header-true--partition-format-none-) na classe `TabularDatasetFactory` para ler ficheiros em formato .csv ou .tsv e para criar um Conjunto TabularDataset não registado. Se estiver a ler de vários ficheiros, os resultados serão agregados numa representação tabular. 

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

Por predefinição, quando cria um Conjunto tabularDataset, os tipos de dados da coluna são automaticamente inferidos. Se os tipos inferidos não corresponderem às suas expectativas, pode especificar os tipos de colunautilizando o seguinte código. O `infer_column_type` parâmetro só é aplicável para conjuntos de dados criados a partir de ficheiros delimitados. Também pode [saber mais sobre tipos de dados suportados.](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.datatype?view=azure-ml-py)

> [!IMPORTANT] 
> Se o seu armazenamento estiver por trás de uma rede virtual ou firewall, apenas é suportada a criação de um conjunto de dados através do SDK. Para criar o seu conjunto de dados, certifique-se de incluir os parâmetros `validate=False` e `infer_column_types=False` no seu método `from_delimited_files()`. Isto ignora a verificação inicial de validação e garante que pode criar o seu conjunto de dados a partir destes ficheiros seguros. 

```Python
from azureml.data.dataset_factory import DataType

# create a TabularDataset from a delimited file behind a public web url and convert column "Survived" to boolean
web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path, set_column_types={'Survived': DataType.to_bool()})

# preview the first 3 rows of titanic_ds
titanic_ds.take(3).to_pandas_dataframe()
```

| |Passengerid|Sobreviveu|Pclass|Nome|Sexo|Idade|SibSp|Rio Parch|Bilhete|Tarifa|Cabana|Embarcado
-|-----------|--------|------|----|---|---|-----|-----|------|----|-----|--------|
0|1|Falso|3|Braund, Sr. Owen Harris.|masculino|22.0|1|0|A/5 21171|7.2500||S
1|2|Verdadeiro|1|Cumings, Sra. John Bradley (Florence Briggs Th...|feminino|38.0|1|0|PC 17599|71.2833|C85|C
2|3|Verdadeiro|3|Heikkinen, menina. Laina|feminino|26.0|0|0|STON/O2. 3101282|7.9250||S


Para criar um conjunto de dados a partir de um dataframe de pandas de memória, escreva os dados para um ficheiro local, como um csv, e crie o seu conjunto de dados a partir desse ficheiro. O código que se segue demonstra este fluxo de trabalho.

```python
local_path = 'data/prepared.csv'
dataframe.to_csv(local_path)
upload the local file to a datastore on the cloud
# azureml-core of version 1.0.72 or higher is required
# azureml-dataprep[pandas] of version 1.1.34 or higher is required
from azureml.core import Workspace, Dataset

subscription_id = 'xxxxxxxxxxxxxxxxxxxxx'
resource_group = 'xxxxxx'
workspace_name = 'xxxxxxxxxxxxxxxx'

workspace = Workspace(subscription_id, resource_group, workspace_name)

# get the datastore to upload prepared data
datastore = workspace.get_default_datastore()

# upload the local file from src_dir to the target_path in datastore
datastore.upload(src_dir='data', target_path='data')
# create a dataset referencing the cloud location
dataset = Dataset.Tabular.from_delimited_files(datastore.path('data/prepared.csv'))
```

Utilize o método [`from_sql_query()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-sql-query-query--validate-true--set-column-types-none-) na classe `TabularDatasetFactory` para ler a partir da Base de Dados Azure SQL:

```Python

from azureml.core import Dataset, Datastore

# create tabular dataset from a SQL database in datastore
sql_datastore = Datastore.get(workspace, 'mssql')
sql_ds = Dataset.Tabular.from_sql_query((sql_datastore, 'SELECT * FROM my_table'))
```

Em TabularDatasets, pode especificar um carimbo de tempo a partir de uma coluna nos dados ou de onde os dados do padrão de caminho são armazenados para permitir um traço de série de tempo. Esta especificação permite uma filtragem fácil e eficiente com o tempo.

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

#### <a name="create-a-filedataset"></a>Criar um Conjunto de Datatos de Ficheiros

Utilize o método [`from_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?view=azure-ml-py#from-files-path--validate-true-) na classe `FileDatasetFactory` para carregar ficheiros em qualquer formato e criar um Conjunto de Datatos de Ficheiros não registado. Se o seu armazenamento estiver por trás de uma rede virtual ou firewall, defina o parâmetro `validate =False` no seu método `from_files()`. Isto ignora a etapa inicial de validação e garante que pode criar o seu conjunto de dados a partir destes ficheiros seguros.

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

![Criar um conjunto de dados com a UI](./media/how-to-create-register-datasets/create-dataset-ui.gif)

Para criar um conjunto de dados no estúdio:
1. Inscreva-se no https://ml.azure.com.
1. Selecione **Conjuntos** de Dados na secção **Ativos** do painel esquerdo. 
1. Selecione **Criar Dataset** para escolher a fonte do seu conjunto de dados. Esta fonte pode ser arquivos locais, uma loja de dados ou URLs públicos.
1. **Selecione Tabular** ou **File** para o tipo Dataset.
1. Selecione **Next** para abrir o formulário de seleção de **Datastore e ficheiros.** Neste formulário, selecione onde manter o seu conjunto de dados após a criação, bem como selecione quais os ficheiros de dados a utilizar para o seu conjunto de dados. 
1. Selecione **Próximo** para preencher as **Definições e pré-visualização** e **formas Schema;** são povoados inteligentemente com base no tipo de ficheiro e pode configurar ainda mais o seu conjunto de dados antes da criação nestes formulários. 
1. Selecione **Next** para rever o formulário **confirmar detalhes.** Verifique as suas seleções e crie um perfil de dados opcional para o seu conjunto de dados. Saiba mais sobre [o perfil de dados.](how-to-create-portal-experiments.md#profile) 
1. Selecione **Criar** para completar a sua criação de conjunto de dados.

## <a name="register-datasets"></a>Registar conjuntos de dados

Para completar o processo de criação, registe os seus conjuntos de dados com um espaço de trabalho. Utilize o método [`register()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.abstract_dataset.abstractdataset?view=azure-ml-py#register-workspace--name--description-none--tags-none--create-new-version-false-) para registar conjuntos de dados com o seu espaço de trabalho para partilhá-los com outros e reutilizá-los em várias experiências:

```Python
titanic_ds = titanic_ds.register(workspace=workspace,
                                 name='titanic_ds',
                                 description='titanic training data')
```

> [!Note]
> Os conjuntos de dados criados através do estúdio Azure Machine Learning estão automaticamente registados no espaço de trabalho.

## <a name="create-datasets-with-azure-open-datasets"></a>Criar conjuntos de dados com conjuntos de dados abertos do Azure

[Os Conjuntos de Dados Abertos do Azure](https://azure.microsoft.com/services/open-datasets/) são conjuntos de dados públicos com curadoria que pode utilizar para adicionar funcionalidades específicas ao cenário a soluções de aprendizagem automática para modelos mais precisos. Os conjuntos de dados incluem dados de domínio público para meteorologia, recenseamento, férias, segurança pública e localização que o ajudam a treinar modelos de aprendizagem automática e a enriquecer soluções preditivas. Os Conjuntos de Dados Abertos estão na nuvem do Microsoft Azure e estão incluídos tanto no SDK como no Workspace UI.

### <a name="use-the-sdk"></a>Use o SDK

Para criar conjuntos de dados com conjuntos de dados Abertos Azure a partir do SDK, certifique-se de que instalou o pacote com `pip install azureml-opendatasets`. Cada conjunto de dados discreto é representado pela sua própria classe no SDK, e certas classes estão disponíveis como um `TabularDataset`, `FileDataset`, ou ambos. Consulte a [documentação](https://docs.microsoft.com/python/api/azureml-opendatasets/azureml.opendatasets?view=azure-ml-py) de referência para uma lista completa de aulas.

Pode recuperar determinadas classes como um `TabularDataset` ou `FileDataset`, o que lhe permite manipular e/ou descarregar os ficheiros diretamente. Outras classes **só** podem obter um conjunto de dados utilizando uma das funções `get_tabular_dataset()` ou `get_file_dataset()`. A amostra de código que se segue mostra alguns exemplos deste tipo de aulas.

```python
from azureml.opendatasets import MNIST

# MNIST class can return either TabularDataset or FileDataset
tabular_dataset = MNIST.get_tabular_dataset()
file_dataset = MNIST.get_file_dataset()

from azureml.opendatasets import Diabetes

# Diabetes class can return ONLY return TabularDataset and must be called from the static function
diabetes_tabular = Diabetes.get_tabular_dataset()
```

Quando regista um conjunto de dados criado a partir de Conjuntos de Dados Abertos, nenhum dado é imediatamente descarregado, mas os dados serão acedidos mais tarde quando solicitados (durante o treino, por exemplo) a partir de um local de armazenamento central.

### <a name="use-the-ui"></a>Use a UI

Também pode criar conjuntos de dados a partir de classes Open Datasets através da UI. No seu espaço de trabalho, selecione o separador **Datasets** em **Ativos**. No menu de entrega do conjunto de **dados Create,** selecione **From Open Datasets**.

![Abrir dataset com a UI](./media/how-to-create-register-datasets/open-datasets-1.png)

Selecione um conjunto de dados selecionando o seu azulejo. (Tem a opção de filtrar utilizando a barra de pesquisa.) Selecione **Next**.

![Escolha o conjunto de dados](./media/how-to-create-register-datasets/open-datasets-2.png)

Escolha um nome para registar o conjunto de dados e, opcionalmente, filtre os dados utilizando os filtros disponíveis. Neste caso, para o conjunto de dados de feriados, filtra-se o período de tempo para um ano e o código do país apenas para os EUA. Selecione **Criar**.

![Definir params conjunto de dados e criar conjunto de dados](./media/how-to-create-register-datasets/open-datasets-3.png)

O conjunto de dados está agora disponível no seu espaço de trabalho sob **conjuntos**de dados . Pode usá-lo da mesma forma que outros conjuntos de dados que criou.

## <a name="version-datasets"></a>Conjuntos de dados de versão

Pode registar um novo conjunto de dados com o mesmo nome criando uma nova versão. Uma versão dataset é uma forma de marcar o estado dos seus dados para que possa aplicar uma versão específica do conjunto de dados para experimentação ou reprodução futura. Saiba mais sobre [as versões do conjunto](how-to-version-track-datasets.md)de dados.
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

## <a name="access-datasets-in-your-script"></a>Aceda a conjuntos de dados no seu script

Os conjuntos de dados registados são acessíveis local e remotamente em clusters de computação como o computacional Azure Machine Learning. Para aceder ao conjunto de dados registado através de experiências, utilize o seguinte código para aceder ao seu espaço de trabalho e conjunto de dados registado pelo nome. Por padrão, o método [`get_by_name()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#get-by-name-workspace--name--version--latest--) na classe `Dataset` devolve a versão mais recente do conjunto de dados que está registado no espaço de trabalho.

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
