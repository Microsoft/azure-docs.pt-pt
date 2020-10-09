---
title: Criar conjuntos de dados de aprendizagem automática Azure para aceder a dados
titleSuffix: Azure Machine Learning
description: Saiba como criar conjuntos de dados de aprendizagem automática Azure para aceder aos seus dados para experiências de aprendizagem automática.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, contperfq1
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 07/31/2020
ms.openlocfilehash: 18904987d409f526c2bbd6cd917558e8ce6f751c
ms.sourcegitcommit: b87c7796c66ded500df42f707bdccf468519943c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/08/2020
ms.locfileid: "91842636"
---
# <a name="create-azure-machine-learning-datasets"></a>Criar conjuntos de dados do Azure Machine Learning



Neste artigo, aprende-se a criar conjuntos de dados de Aprendizagem automática Azure para aceder a dados para as suas experiências locais ou remotas com o Azure Machine Learning Python SDK. Para entender onde os conjuntos de dados se encaixam no fluxo de trabalho global de acesso a dados da Azure Machine Learning, consulte o artigo [de dados de acesso seguro.](concept-data.md#data-workflow)

Ao criar um conjunto de dados, cria uma referência para a localização da origem de dados, juntamente com uma cópia dos metadados. Como os dados permanecem na sua localização existente, não incorre nenhum custo extra de armazenamento e não arrisca a integridade das suas fontes de dados. Também os conjuntos de dados são avaliados preguiçosamente, o que ajuda nas velocidades de desempenho do fluxo de trabalho. Pode criar conjuntos de dados a partir de datastores, URLs públicos e [Conjuntos de Dados Abertos Azure](../open-datasets/how-to-create-azure-machine-learning-dataset-from-open-dataset.md).

Para uma experiência de baixo código, [crie conjuntos de dados de Aprendizagem automática Azure com o estúdio Azure Machine Learning.](how-to-connect-data-ui.md#create-datasets)

Com conjuntos de dados de aprendizagem automática Azure, pode:

* Mantenha uma única cópia de dados no seu armazenamento, referenciada por conjuntos de dados.

* Aceda sem problemas aos dados durante o treino do modelo sem se preocupar com cordas de ligação ou caminhos de dados. [Saiba mais sobre como treinar com conjuntos de dados.](how-to-train-with-datasets.md)

* Partilhe dados e colabore com outros utilizadores.

## <a name="prerequisites"></a>Pré-requisitos

Para criar e trabalhar com conjuntos de dados, precisa de:

* Uma subscrição do Azure. Se não tiver uma, crie uma conta gratuita antes de começar. Experimente a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree).

* Um [espaço de trabalho de aprendizagem automática Azure.](how-to-manage-workspace.md)

* O [Azure Machine Learning SDK para Python instalado,](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true)que inclui o pacote de conjuntos de dados azureml.

    * Crie um [exemplo de computação Azure Machine Learning,](how-to-create-manage-compute-instance.md)que é um ambiente de desenvolvimento totalmente configurado e gerido que inclui cadernos integrados e o SDK já instalado.

    **OR**

    * Trabalhe no seu próprio caderno Jupyter e instale o SDK com [estas instruções.](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true)

> [!NOTE]
> Algumas classes de conjuntos de dados têm dependências do pacote [azureml-dataprep,](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py&preserve-view=true) que só é compatível com Python de 64 bits. Para os utilizadores do Linux, estas classes são suportadas apenas nas seguintes distribuições: Red Hat Enterprise Linux (7, 8), Ubuntu (14.04, 16.04, 18.04), Fedora (27, 28), Debian (8, 9) e CentOS (7). Se estiver a utilizar distros não suportados, siga [este guia](https://docs.microsoft.com/dotnet/core/install/linux) para instalar .NET Core 2.1 para prosseguir. 

## <a name="compute-size-guidance"></a>Orientação do tamanho do cálculo

Ao criar um conjunto de dados, reveja o seu poder de processamento de computação e o tamanho dos seus dados na memória. O tamanho dos seus dados no armazenamento não é o mesmo que o tamanho dos dados num dataframe. Por exemplo, os dados em ficheiros CSV podem expandir-se até 10x num dataframe, para que um ficheiro CSV de 1 GB possa tornar-se 10 GB num dataframe. 

Se os seus dados forem comprimidos, pode expandir-se ainda mais; 20 GB de dados relativamente escassos armazenados em formato de parquet comprimido podem expandir-se para ~800 GB na memória. Uma vez que os ficheiros Parquet armazenam dados num formato colunar, se só precisa de metade das colunas, então só precisa carregar ~400 GB na memória.

[Saiba mais sobre a otimização do processamento de dados em Azure Machine Learning.](concept-optimize-data-processing.md)

## <a name="dataset-types"></a>Tipos de conjuntos de dados

Existem dois tipos de conjuntos de dados, baseados na forma como os utilizadores os consomem em formação; Ficheiros eDatasets Tabular. Ambos os tipos podem ser utilizados em fluxos de trabalho de formação Azure Machine Learning envolvendo, estimadores, AutoML, hyperDrive e pipelines. 

### <a name="filedataset"></a>Conjunto de Dados de Ficheiros

Um [Dataset de ficheiros](https://docs.microsoft.com/python/api/azureml-core/azureml.data.file_dataset.filedataset?view=azure-ml-py&preserve-view=true) refere ficheiros únicos ou múltiplos nas suas datastores ou URLs públicos. Se os seus dados já estiverem limpos e prontos a ser utilizados em experiências de treino, pode [descarregar ou montar](how-to-train-with-datasets.md#mount-vs-download) os ficheiros no seu cálculo como um objeto FileDataset. 

Recomendamos datasets de ficheiros para os seus fluxos de trabalho de aprendizagem automática, uma vez que os ficheiros de origem podem estar em qualquer formato, o que permite uma ampla gama de cenários de aprendizagem automática, incluindo aprendizagem profunda.

Crie um FileDataset com o [Python SDK](#create-a-filedataset) ou o [estúdio Azure Machine Learning](how-to-connect-data-ui.md#create-datasets) .
### <a name="tabulardataset"></a>Conjunto de Dados Tabular

Um [SeparadorDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py&preserve-view=true) representa dados num formato tabular, analisando o ficheiro ou lista de ficheiros fornecidos. Isto fornece-lhe a capacidade de materializar os dados em um pandas ou Spark DataFrame para que possa trabalhar com bibliotecas familiares de preparação e formação de dados sem ter que deixar o seu caderno. Pode criar um `TabularDataset` objeto a partir de .csv, .tsv, .parquet, ficheiros .jsonl e a partir dos resultados da consulta [SQL](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py&preserve-view=true#&preserve-view=truefrom-sql-query-query--validate-true--set-column-types-none--query-timeout-30-).

Com os Separadores Tabular, pode especificar um carimbo de tempo a partir de uma coluna nos dados ou de onde quer que os dados do padrão do caminho seja armazenado para ativar um traço de série de tempo. Esta especificação permite uma filtragem fácil e eficiente pelo tempo. Por exemplo, consulte a [demonstração de API relacionada com a série de tempo Tabular com dados meteorológicos da NOAA](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/timeseries-datasets/tabular-timeseries-dataset-filtering.ipynb).

Crie um TabularDataset com o estúdio [Python SDK](#create-a-tabulardataset) ou [Azure Machine Learning](how-to-connect-data-ui.md#create-datasets).

>[!NOTE]
> Os fluxos de trabalho autoML gerados através do estúdio Azure Machine Learning suportam atualmente apenas Os Separadores Tabular. 

## <a name="access-datasets-in-a-virtual-network"></a>Aceder a conjuntos de dados numa rede virtual

Se o seu espaço de trabalho estiver numa rede virtual, tem de configurar o conjunto de dados para saltar a validação. Para obter mais informações sobre como utilizar datas-tores e conjuntos de dados numa rede virtual, consulte [Secure a workspace e recursos associados](how-to-secure-workspace-vnet.md#secure-datastores-and-datasets).

<a name="datasets-sdk"></a>

## <a name="create-datasets"></a>Criar conjuntos de dados

Para que os dados sejam acessíveis pela Azure Machine Learning, os conjuntos de dados devem ser criados a partir de caminhos nas [datastores Azure](how-to-access-data.md) ou URLs web públicos. 

Para criar conjuntos de dados a partir de uma [loja de dados Azure](how-to-access-data.md) com o Python SDK:

1. Verifique se tem `contributor` ou `owner` acede à loja de dados Azure registada.

2. Crie o conjunto de dados referindo caminhos na datastore. Pode criar um conjunto de dados a partir de vários caminhos em várias datastores. Não existe um limite rígido no número de ficheiros ou tamanho de dados a partir do qual pode criar um conjunto de dados a partir de. 

> [!NOTE]
> Para cada caminho de dados, serão enviados alguns pedidos para o serviço de armazenamento para verificar se aponta para um ficheiro ou uma pasta. Esta sobrecarga pode levar a um desempenho ou falha degradado. Um conjunto de dados que faz referência a uma pasta com 1000 ficheiros no interior é considerado como referenciação de um caminho de dados. Recomendamos a criação de conjuntos de dados que referenciam menos de 100 caminhos nas datastores para um desempenho ideal.

### <a name="create-a-filedataset"></a>Criar um Conjunto de Dados de Ficheiros

Utilize o [`from_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?view=azure-ml-py&preserve-view=true#&preserve-view=truefrom-files-path--validate-true-) método na classe para carregar `FileDatasetFactory` ficheiros em qualquer formato e para criar um Data-Dataset de ficheiros não registado. 

Se o seu armazenamento estiver por detrás de uma rede virtual ou firewall, desa um parâmetro `validate=False` no seu `from_files()` método. Isto contorna o passo de validação inicial e garante que pode criar o seu conjunto de dados a partir destes ficheiros seguros. Saiba mais sobre como [utilizar datas-tores e conjuntos de dados numa rede virtual.](how-to-secure-workspace-vnet.md#secure-datastores-and-datasets)

```Python
# create a FileDataset pointing to files in 'animals' folder and its subfolders recursively
datastore_paths = [(datastore, 'animals')]
animal_ds = Dataset.File.from_files(path=datastore_paths)

# create a FileDataset from image and label files behind public web urls
web_paths = ['https://azureopendatastorage.blob.core.windows.net/mnist/train-images-idx3-ubyte.gz',
             'https://azureopendatastorage.blob.core.windows.net/mnist/train-labels-idx1-ubyte.gz']
mnist_ds = Dataset.File.from_files(path=web_paths)
```
Para reutilizar e partilhar conjuntos de dados através de experiências no seu espaço de trabalho, [registe o seu conjunto de dados](#register-datasets). 

> [!TIP] 
> Faça upload de ficheiros a partir de um diretório local e crie um FileDataset num único método com o método de pré-visualização público, [upload_directory()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.filedataset?view=azure-ml-py&preserve-view=true#methods). Este método é uma funcionalidade de pré-visualização [experimental,](https://docs.microsoft.com/python/api/overview/azure/ml/?view=azure-ml-py&preserve-view=true#stable-vs-experimental) e pode mudar a qualquer momento. 
> 
>  Este método envia dados para o seu armazenamento subjacente e, como resultado, incorre em custos de armazenamento. 
### <a name="create-a-tabulardataset"></a>Criar um Conjunto de Dados Tabular

Utilize o [`from_delimited_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory) método na classe para ler `TabularDatasetFactory` ficheiros em formato .csv ou .tsv e para criar um Separador Não Registado. Se estiver a ler vários ficheiros, os resultados serão agregados numa representação tabular. 

Se o seu armazenamento estiver por detrás de uma rede virtual ou firewall, desa um parâmetro `validate=False` no seu `from_delimited_files()` método. Isto contorna o passo de validação inicial e garante que pode criar o seu conjunto de dados a partir destes ficheiros seguros. Saiba mais sobre como utilizar [datas-tores e conjuntos de dados numa rede virtual.](how-to-secure-workspace-vnet.md#secure-datastores-and-datasets)

O código seguinte obtém o espaço de trabalho existente e a datastore desejada pelo nome. E, em seguida, passa a datastore e localizações de ficheiros para o `path` parâmetro para criar um novo Separadorset Tabular, `weather_ds` .

```Python
from azureml.core import Workspace, Datastore, Dataset

datastore_name = 'your datastore name'

# get existing workspace
workspace = Workspace.from_config()
    
# retrieve an existing datastore in the workspace by name
datastore = Datastore.get(workspace, datastore_name)

# create a TabularDataset from 3 file paths in datastore
datastore_paths = [(datastore, 'weather/2018/11.csv'),
                   (datastore, 'weather/2018/12.csv'),
                   (datastore, 'weather/2019/*.csv')]

weather_ds = Dataset.Tabular.from_delimited_files(path=datastore_paths)
```

Por predefinição, quando cria um Separadors, os tipos de dados de coluna são inferidos automaticamente. Se os tipos inferidos não corresponderem às suas expectativas, pode especificar tipos de colunas utilizando o seguinte código. O parâmetro `infer_column_type` só é aplicável para conjuntos de dados criados a partir de ficheiros delimitados. [Saiba mais sobre tipos de dados suportados.](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.datatype?view=azure-ml-py&preserve-view=true)


```Python
from azureml.core import Dataset
from azureml.data.dataset_factory import DataType

# create a TabularDataset from a delimited file behind a public web url and convert column "Survived" to boolean
web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path, set_column_types={'Survived': DataType.to_bool()})

# preview the first 3 rows of titanic_ds
titanic_ds.take(3).to_pandas_dataframe()
```

|(Índice)|PassengerId|Sobreviveu|Pclass|Nome|Sexo|Idade|SibSp|Pergaminho|Pedido de Suporte|Tarifa|Cabana|Embarcado
-|-----------|--------|------|----|---|---|-----|-----|------|----|-----|--------|
0|1|Falso|3|Braund, Sr. Owen Harris.|masculino|22.0|1|0|A/5 21171|7.2500||S
1|2|Verdadeiro|1|Cumings, Sra. John Bradley (Florence Briggs Th...|feminino|38.0|1|0|PC 17599|71.2833|C85|C
2|3|Verdadeiro|3|Heikkinen, menina. Laina|feminino|26.0|0|0|STON/O2. 3101282|7.9250||S

Para reutilizar e partilhar conjuntos de dados através de experiências no seu espaço de trabalho, [registe o seu conjunto de dados](#register-datasets).

## <a name="create-a-dataset-from-pandas-dataframe"></a>Criar um conjunto de dados a partir do dataframe dos pandas

Para criar um Conjunto de Dados Tabular A partir de um dataframe de pandas de memória, escreva os dados para um ficheiro local, como um csv, e crie o seu conjunto de dados a partir desse ficheiro. O seguinte código demonstra este fluxo de trabalho.

```python
# azureml-core of version 1.0.72 or higher is required
# azureml-dataprep[pandas] of version 1.1.34 or higher is required

from azureml.core import Workspace, Dataset
local_path = 'data/prepared.csv'
dataframe.to_csv(local_path)

# upload the local file to a datastore on the cloud

subscription_id = 'xxxxxxxxxxxxxxxxxxxxx'
resource_group = 'xxxxxx'
workspace_name = 'xxxxxxxxxxxxxxxx'

workspace = Workspace(subscription_id, resource_group, workspace_name)

# get the datastore to upload prepared data
datastore = workspace.get_default_datastore()

# upload the local file from src_dir to the target_path in datastore
datastore.upload(src_dir='data', target_path='data')

# create a dataset referencing the cloud location
dataset = Dataset.Tabular.from_delimited_files(path = [(datastore, ('data/prepared.csv')]
```

> [!TIP]
> Crie e registe um Conjunto de Dados Tabular a partir de um dataframe de faíscas ou pandas na memória com um único método com métodos de pré-visualização [`register_spark_dataframe()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py&preserve-view=true#methods) públicos, e [`register_pandas_dataframe()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py&preserve-view=true#methods) . Estes métodos de registo são funcionalidades [experimentais](https://docs.microsoft.com/python/api/overview/azure/ml/?view=azure-ml-py&preserve-view=true#stable-vs-experimental) de pré-visualização e podem mudar a qualquer momento. 
> 
>  Estes métodos carregam dados para o seu armazenamento subjacente e, como resultado, incorrem em custos de armazenamento. 

## <a name="register-datasets"></a>Registar conjuntos de dados

Para completar o processo de criação, registe os seus conjuntos de dados com um espaço de trabalho. Utilize o [`register()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.abstract_dataset.abstractdataset?view=azure-ml-py&preserve-view=true#&preserve-view=trueregister-workspace--name--description-none--tags-none--create-new-version-false-) método para registar conjuntos de dados com o seu espaço de trabalho para partilhá-los com outros e reutilizá-los através de experiências no seu espaço de trabalho:

```Python
titanic_ds = titanic_ds.register(workspace=workspace,
                                 name='titanic_ds',
                                 description='titanic training data')
```

## <a name="create-datasets-using-azure-resource-manager"></a>Criar conjuntos de dados utilizando o Gestor de Recursos Azure

Há uma série de modelos [https://github.com/Azure/azure-quickstart-templates/tree/master/101-machine-learning-dataset-create-*](https://github.com/Azure/azure-quickstart-templates/tree/master/) que podem ser usados para criar conjuntos de dados.

Para obter informações sobre a utilização destes modelos, consulte [utilize um modelo de Gestor de Recursos Azure para criar um espaço de trabalho para a aprendizagem de máquinas Azure](how-to-create-workspace-template.md).


## <a name="create-datasets-with-azure-open-datasets"></a>Criar conjuntos de dados com conjuntos de dados abertos Azure

[Os conjuntos de dados Azure Open](https://azure.microsoft.com/services/open-datasets/) são conjuntos de dados públicos com curadoria que pode utilizar para adicionar funcionalidades específicas de cenários a soluções de machine learning para modelos mais precisos. Os conjuntos de dados incluem dados de domínio público para o tempo, recenseamento, feriados, segurança pública e localização que o ajudam a treinar modelos de aprendizagem automática e a enriquecer soluções preditivas. Os Conjuntos de Dados Abertos estão na nuvem no Microsoft Azure e estão incluídos tanto no SDK como no estúdio.

Saiba como criar [conjuntos de dados de aprendizagem de máquinas Azure a partir de conjuntos de dados Azure Open](../open-datasets/how-to-create-azure-machine-learning-dataset-from-open-dataset.md). 

## <a name="train-with-datasets"></a>Preparar com conjuntos de dados

Utilize os seus conjuntos de dados nas suas experiências de aprendizagem automática para treinar modelos ML. [Saiba mais sobre como treinar com conjuntos de dados](how-to-train-with-datasets.md)

## <a name="version-datasets"></a>Conjuntos de dados de versão

Pode registar um novo conjunto de dados com o mesmo nome, criando uma nova versão. Uma versão do conjunto de dados é uma forma de reservar o estado dos seus dados para que possa aplicar uma versão específica do conjunto de dados para experimentação ou reprodução futura. Saiba mais sobre [as versões dataset](how-to-version-track-datasets.md).
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

## <a name="next-steps"></a>Passos seguintes

* Saiba [como treinar com conjuntos de dados.](how-to-train-with-datasets.md)
* Utilize machine learning automatizado para [treinar com OsDatasets Tabular](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb).
* Para obter mais exemplos de formação de conjuntos de dados, consulte os [cadernos](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/work-with-data/)da amostra .
