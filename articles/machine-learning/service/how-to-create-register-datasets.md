---
title: Criar conjuntos de dados para aceder a dados com conjuntos de dados azureml
titleSuffix: Azure Machine Learning service
description: Saiba como criar conjuntos de dados de várias origens e registar os conjuntos de dados com a sua área de trabalho
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 05/21/2019
ms.openlocfilehash: b4c22caae86e20b8379db2b7feffb1ca82001239
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66753153"
---
# <a name="create-and-access-datasets-preview-in-azure-machine-learning"></a>Criar e aceder a conjuntos de dados (pré-visualização) no Azure Machine Learning

Neste artigo, aprenderá como criar conjuntos de dados do Azure Machine Learning (pré-visualização) e como aceder os dados a partir de experimentações de locais e remotas.

Com conjuntos de dados geridos, pode: 
* **Facilmente aceder aos dados durante a preparação de modelos** sem a restabelecer ligação para armazenamentos subjacentes

* **Certifique-se a consistência dos dados e capacidade de reprodução** usando o ponteiro do mesmo na experimentações: blocos de notas, ml automatizada, pipelines, visual interface

* **Partilhar dados e colaborar** com outros utilizadores

* **Explorar dados** & Gerir o ciclo de vida de instantâneos de dados e de versões

* **Compare dados** na formação para produção


## <a name="prerequisites"></a>Pré-requisitos

Para criar e trabalhar com conjuntos de dados, terá de:

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma conta gratuita antes de começar. Experimente o [uma versão gratuita ou paga do serviço Azure Machine Learning](https://aka.ms/AMLFree) hoje mesmo.

* Um [área de trabalho do serviço Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/setup-create-workspace)

* O [do Azure Machine Learning SDK para Python instalada](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py), que inclui o pacote do azureml-conjuntos de dados.

> [!Note]
> Algumas classes de conjunto de dados (pré-visualização) tem dependências do [azureml dataprep](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py) pacote (GA). Os utilizadores do Linux, essas classes são suportadas apenas nas distribuições seguintes:  Red Hat Enterprise Linux, Ubuntu, Fedora, and CentOS.

## <a name="data-formats"></a>Formatos de dados

Pode criar um conjunto de dados do Azure Machine Learning a partir os seguintes dados:
+ [delimited](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset#from-delimited-files-path--separator------header--promoteheadersbehavior-all-files-have-same-headers--3---encoding--fileencoding-utf8--0---quoting-false--infer-column-types-true--skip-rows-0--skip-mode--skiplinesbehavior-no-rows--0---comment-none--include-path-false--archive-options-none-)
+ [binary](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-binary-files-path-)
+ [json](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-json-files-path--encoding--fileencoding-utf8--0---flatten-nested-arrays-false--include-path-false-)
+ [Excel](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-excel-files-path--sheet-name-none--use-column-headers-false--skip-rows-0--include-path-false--infer-column-types-true-)
+ [Parquet](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-parquet-files-path--include-path-false-)
+ [Base de Dados SQL do Azure](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-sql-query-data-source--query-)
+ [Geração de Azure Data Lake. 1](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-sql-query-data-source--query-)

## <a name="create-datasets"></a>Criar conjuntos de dados 

Pode interagir com os conjuntos de dados com o pacote de conjuntos de dados azureml na [SDK de Python do Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) e, especificamente [o `Dataset` classe](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py).

### <a name="create-from-local-files"></a>Criar a partir de ficheiros locais

Carregar ficheiros do seu computador local, especificando o caminho de ficheiro ou pasta com o [ `auto_read_files()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py#auto-read-files-path--include-path-false-) método a partir do `Dataset` classe.  Esse método realiza as etapas a seguir, sem precisar especificar o tipo de ficheiro ou análise de argumentos:

* Inferindo e definindo o delimitador.
* A ignorar registos vazios na parte superior do ficheiro.
* Inferindo e definir a linha de cabeçalho.
* Inferindo e conversão de tipos de dados de coluna.

```Python
from azureml.core.dataset import Dataset

dataset = Dataset.auto_read_files('./data/crime.csv')
```

Em alternativa, utilize as funções de ficheiro específicas para controlar explicitamente a análise do seu ficheiro. 


### <a name="create-from-azure-datastores"></a>Criar a partir de arquivos de dados do Azure

Para criar conjuntos de dados a partir de um arquivo de dados do Azure:

* Certifique-se de que tenha `contributor` ou `owner` acesso para o arquivo de dados registados do Azure.

* Importar os [ `Workspace` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) e [ `Datastore` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#definition) e `Dataset` pacotes do SDK.

```Python
from azureml.core.workspace import Workspace
from azureml.core.datastore import Datastore
from azureml.core.dataset import Dataset

datastore_name = 'your datastore name'

# get existing workspace
workspace = Workspace.from_config()
```

 O `get()` método obtém um arquivo de dados existente na área de trabalho.

```
dstore = Datastore.get(workspace, datastore_name)
```

Utilize o `from_delimited_files()` método de leitura de arquivos delimitados e criar um conjunto de dados de anular o registo.

```Python
# create an in-memory Dataset on your local machine
datapath = dstore.path('data/src/crime.csv')
dataset = Dataset.from_delimited_files(datapath)

# returns the first 5 rows of the Dataset as a pandas Dataframe.
dataset.head(5)
```

## <a name="register-datasets"></a>Registe-se de conjuntos de dados

Para concluir o processo de criação, registe seus conjuntos de dados com a área de trabalho:

Utilize o [ `register()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#register-workspace--name--description-none--tags-none--visible-true--exist-ok-false--update-if-exist-false-) método para registar os conjuntos de dados para a área de trabalho para que possam ser partilhados com outras pessoas e reutilizados em vários experimentações.

```Python
dataset = dataset.register(workspace = workspace,
                           name = 'dataset_crime',
                           description = 'Training data',
                           exist_ok = False
                           )
```

>[!NOTE]
> Se `exist_ok = False` (predefinição), e tentar registar um conjunto de dados com o mesmo nome que outra, ocorre um erro. Definido como `True` para substituir a existente.

## <a name="access-data-in-datasets"></a>Aceder a dados nos conjuntos de dados

Conjuntos de dados registados estão acessíveis e consumível localmente, remotamente e em clusters de computação, como a computação do Azure Machine Learning. Para reutilizar o conjunto de dados registado em experimentações e ambientes de computação, utilize o seguinte código para obter a sua área de trabalho e o conjunto de dados registado pelo nome.

```Python
workspace = Workspace.from_config()

# See list of datasets registered in workspace.
Dataset.list(workspace)

# Get dataset by name
dataset = workspace.datasets['dataset_crime']
```

## <a name="next-steps"></a>Passos Seguintes

* [Explore e preparar conjuntos de dados](how-to-explore-prepare-data.md).
* [Gerir o ciclo de vida de definições do conjunto de dados de](how-to-manage-dataset-definitions.md).
* Para obter um exemplo da utilização de conjuntos de dados, consulte a [blocos de notas de exemplo](https://aka.ms/dataset-tutorial).
