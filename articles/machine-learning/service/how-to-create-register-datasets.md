---
title: Criar e registar os conjuntos de dados com a sua área de trabalho
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
ms.date: 05/02/19
ms.openlocfilehash: 4b3fa69156146037ff59a41eab8c8373f6e01dc4
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029119"
---
# <a name="create-and-register-azure-machine-learning-datasets-preview"></a>Criar e registar os conjuntos de dados do Azure Machine Learning (pré-visualização)

Neste artigo, irá aprender os fluxos de trabalho do Azure Machine Learning para criar e registar os conjuntos de dados e como aceder aos mesmos para reutilização entre locais e remotas experimentações.

O Azure Machine Learning conjuntos de dados (pré-visualização) torna mais fácil de acessar e trabalhar com os seus dados. Conjuntos de dados de gerir os dados em vários cenários, como, preparação de modelos e a criação do pipeline. Utilizar o [SDK do Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py), pode trabalhar com dados em formatos populares, aceder ao armazenamento subjacente, explorar e preparar dados, gerenciar o ciclo de vida de definições do conjunto de dados diferente e comparar entre conjuntos de dados utilizados treinamento e na produção.

## <a name="prerequisites"></a>Pré-requisitos

Para criar e registar os conjuntos de dados tem de:

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma conta gratuita antes de começar. Experimente o [uma versão gratuita ou paga do serviço Azure Machine Learning](https://aka.ms/AMLFree) hoje mesmo.

* Uma área de trabalho de serviço do Azure Machine Learning. Ver [criar uma área de trabalho do serviço do Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/setup-create-workspace).

* O Azure Machine Learning SDK para Python. Para instalar ou atualizar para a versão mais recente do SDK, consulte [instalar ou atualizar o SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

## <a name="create-datasets-from-local-files"></a>Criar conjuntos de dados a partir de ficheiros locais

Carregar ficheiros do seu computador local, especificando o caminho de ficheiro ou pasta com o [ `auto_read_files()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py#auto-read-files-path--include-path-false-) método a partir do `Dataset` classe.  Esse método realiza as etapas a seguir, sem precisar especificar o tipo de ficheiro ou análise de argumentos:

* Inferindo e definindo o delimitador.
* A ignorar registos vazios na parte superior do ficheiro.
* Inferindo e definir a linha de cabeçalho.
* Inferindo e conversão de tipos de dados de coluna.

```Python
from azureml.core import Dataset

dataset = Dataset.auto_read_files('./data/crime.csv')
```

Em alternativa, utilize as funções de ficheiro específicas para controlar explicitamente a análise do seu ficheiro. Atualmente, o SDK de conjuntos de dados suporta [delimitados](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-delimited-files-path--separator------header--promoteheadersbehavior-allfileshavesameheaders--3---encoding--fileencoding-utf8--0---quoting-false--infer-column-types-true--skip-rows-0--skip-mode--skiplinesbehavior-norows--0---comment-none--include-path-false--archive-options-none-), [Excel](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-excel-files-path--sheet-name-none--use-column-headers-false--skip-rows-0--include-path-false--infer-column-types-true-), [Parquet](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-parquet-files-path--include-path-false-), [binário](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-binary-files-path-), e [json](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-json-files-path--encoding--fileencoding-utf8--0---flatten-nested-arrays-false--include-path-false-) formatos de arquivo.

## <a name="create-datasets-from-azure-datastores"></a>Criar conjuntos de dados a partir de arquivos de dados do Azure

Para criar conjuntos de dados a partir de um arquivo de dados do Azure, certifique-se de que para:

* Certifique-se de que tem Contribuidor ou proprietário de acesso para o arquivo de dados registados do Azure.

* Importar os [ `Workspace` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) e [ `Datastore` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#definition) e `Dataset` pacotes do SDK.

```Python
from azureml.core import Workspace, Datastore, Dataset

datastore_name = 'your datastore name'

# get existing workspace
workspace = Workspace.from_config()
```

 O `get()` método obtém um arquivo de dados existente na área de trabalho.

```
dstore = Datastore.get(workspace, datastore_name)
```

Utilize o `from_delimited_files()` método para leitura de arquivos delimitados e criar conjuntos de dados na memória.

```Python
# create an in-memory Dataset on your local machine
datapath = dstore.path('data/src/crime.csv')
dataset = Dataset.from_delimited_files(datapath)

# returns the first 5 rows of the Dataset as a pandas Dataframe.
dataset.head(5)
```

||ID|Número de caso|Date|Bloquear|IUCR|Tipo de principal|Descrição|Descrição de localização|Prisão|Nacionais|...|Ward|Área de Comunidade|Código do FBI|Coordenada x|Coordenada Y|Ano|Atualizado em|Latitude|Longitude|Localização|
|--|--|---|---|---|---|----|------|-------|------|-----|---|----|----|-----|-----|------|----|-----|----|----|-----
|0|10498554|HZ239907|4/4/2016 23:56|007XX E 111TH ST|1153|ENGANOSA PRÁTICA|ROUBO DE IDENTIDADES FINANCEIRAS AO LONGO DE US $ 300|OUTROS|FALSO|FALSO|...|9|50|11|1183356|1831503|2016|5/11/2016 15:48|41.69283384|-87.60431945|(41.692833841, -87.60431945)|
1|10516598|HZ258664|4/15/2016 17:00|082XX S MARSHFIELD AVE|890|ROUBO| DESDE A CRIAÇÃO|RESIDÊNCIA|FALSO|FALSO|...|21|71|6|1166776|1850053|2016|5/12/2016 15:48|41.74410697|-87.66449429|(41.744106973, -87.664494285)
2|10519196|HZ261252|4/15/2016 10:00|104XX S SACRAMENTO AVE|1154|ENGANOSA PRÁTICA|US $300 DE ROUBO DE IDENTIDADES FINANCEIRAS E, EM|RESIDÊNCIA|FALSO|FALSO|...|19|74|11|||2016|5/12/2016 15:50
3|10519591|HZ261534|4/15/2016 9:00|113XX S PRAIRIE AVE|1120|ENGANOSA PRÁTICA|FALSIFICAÇÃO|RESIDÊNCIA|FALSO|FALSO|...|9|49|10|||2016|5/13/2016 15:51
4|10534446|HZ277630|4/15/2016 10:00|055XX N KEDZIE AVE|890|ROUBO|DESDE A CRIAÇÃO|INSTITUIÇÃO DE ENSINO, PÚBLICA, CRIAÇÃO|FALSO|FALSO|...|40|13|6|||2016|5/25/2016 15:59|

## <a name="register-your-datasets-with-workspace"></a>Registe-se os conjuntos de dados com a área de trabalho

Utilize o [ `register()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#register-workspace--name--description-none--tags-none--visible-true--exist-ok-false--update-if-exist-false-) método para registar os conjuntos de dados à sua área de trabalho para compartilhar e reutilizar dentro da sua organização e entre várias experiências.

```Python
dataset = dataset.register(workspace = 'workspace_name',
                           name = "dataset_crime",
                           description = 'Training data',
                           exist_ok = False
                           )
```

>[!NOTE]
> A definição de parâmetro padrão para `register()` é ' exist_ok = False'. Resultados de um erro se tentar registar um conjunto de dados com o mesmo nome sem alterar esta definição.

O `register()` método atualiza a definição de um conjunto de dados já registado com a definição de parâmetro, `exist_ok = True`.

```Python
dataset = dataset.register(workspace = workspace_name,
                           name = "dataset_crime",
                           description = 'Training data',
                           exist_ok = True)
```

Utilize `list()` para ver todos os conjuntos de dados registados na sua área de trabalho.

```Python
Dataset.list(workspace_name)
```

O código anterior resulta no seguinte:

```Python
[Dataset(Name: dataset_crime,
         Workspace: workspace_name)]
```

## <a name="access-datasets-in-workspace"></a>Conjuntos de dados de acesso na área de trabalho

Conjuntos de dados registados estão acessíveis e consumível localmente, remotamente e em clusters de computação, como a computação do Azure Machine Learning. Para reutilizar o conjunto de dados registado em experimentações e ambientes de computação, utilize o seguinte código para obter a sua área de trabalho e o conjunto de dados registado pelo nome.

```Python
workspace = Workspace.from_config()

dataset = workspace.Datasets['dataset_crime']
```

## <a name="next-steps"></a>Passos Seguintes

* [Explore e preparar conjuntos de dados](how-to-explore-prepare-data.md).
* [Gerir o ciclo de vida de definições do conjunto de dados de](how-to-manage-dataset-definitions.md).
* Para obter um exemplo da utilização de conjuntos de dados, consulte a [blocos de notas de exemplo](https://aka.ms/dataset-tutorial).