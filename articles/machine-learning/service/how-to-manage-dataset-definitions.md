---
title: Definição do conjunto de dados e controle de versão
titleSuffix: Azure Machine Learning service
description: Saiba como atualizar definições do conjunto de dados e gerir o ciclo de vida de definições
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
ms.reviewer: larryfr
ms.date: 05/02/2019
ms.openlocfilehash: 7c861c8cdc9985caa42bd2beb5236a4f4e93e4c7
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028684"
---
# <a name="update-and-manage-the-lifecycle-of-dataset-definitions"></a>Atualizar e gerenciar o ciclo de vida de definições do conjunto de dados

Saiba como atualizar e gerir as definições de conjunto de dados com conjuntos de dados do Azure Machine Learning (pré-visualização).

As definições de conjunto de dados são as receitas usadas para preparar seus conjuntos de dados, que incluem ambas as a referência à sua origem de dados e os passos de transformação executados. Um conjunto de dados pode ter muitas definições, e cada definição tem seu próprio ciclo de vida.

O cenário a seguir é um exemplo de definições de conjunto de dados:

1. Criar um pipeline que consome a definição atual do seu conjunto de dados de aprendizagem automática.
1. As alterações de dados subjacente; foram adicionados novos atributos.
1. Criar uma nova versão de sua definição de que adiciona transformações Extras para lidar com os novos atributos.

Neste exemplo, o pipeline existente continua a consumir a versão da definição original. A nova versão de definição pode ser utilizada para novos cenários, como preparar um modelo ou criar um pipeline.

## <a name="prerequisites"></a>Pré-requisitos

Tem de ter uma subscrição do Azure e uma área de trabalho para registar o seu conjunto de dados para gerir o ciclo de vida de definições do conjunto de dados.

O ficheiro de exemplo usado nos exemplos neste documento está disponível em [ https://dprepdata.blob.core.windows.net/dataset-sample-files/crime.csv ](https://dprepdata.blob.core.windows.net/dataset-sample-files/crime.csv).

## <a name="update-dataset-definitions"></a>Atualizar definições do conjunto de dados

Iremos primeiro criar e registar um conjunto de dados com a sua área de trabalho.

```python
from azureml.core import Workspace, Datastore, Dataset

# change the configuration for workspace and Datastore
subscription_id = 'your subscription id'
resource_group = 'your resource group name'
workspace_name = 'your workspace name'
datastore_name = 'your datastore name'

# get existing workspace
workspace = Workspace(subscription_id, resource_group, workspace_name)

# get a Datastore that has already been created in the workspace
dstore = Datastore.get(workspace, datastore_name)

# create an in-memory Dataset from Datastore
datapath = dstore.path('data/src/crime.csv')
dataset = Dataset.from_delimited_files(datapath)

# register the Dataset with the workspace. if a Dataset with the same name already exists, retrieve it by turning `exist_ok = True`
dataset_name = 'crime dataset'
dataset = dataset.register(workspace = workspace, 
                 name = dataset_name, 
                 description = 'crime dataset for demo', 
                 tags = {'year':'2019', 'month':'Apr'},
                 exist_ok = True)
```

A primeira definição de conjunto de dados (`version_id` = 1) é criada quando o conjunto de dados é criado. Em seguida, sempre que atualizar a definição do conjunto de dados, um novo version_id será atribuído para a definição mais recente.

```python
# get the Dataset from the workspace by name
dataset = workspace.datasets['dataset_name']

# get the latest Dataset definition
ds_def = dataset.get_definition()

# update the Dataset definition to select only the columns I'm interested in
ds_def = ds_def.keep_columns(['ID', 'Arrest', 'Latitude', 'Longitude'])

# with this update, the latest definition for dataset now has `version_id = 2`
dataset = dataset.update_definition(ds_def, 'select useful column')

dataset = workspace.datasets['dataset_name']
dataset.head(5)
```
<div>
<style scoped> .dataframe tbody tr th: só de-de-type {vertical-align: intermédia;}

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>ID</th>
      <th>Prisão</th>
      <th>Latitude</th>
      <th>Longitude</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>10498554</td>
      <td>FALSO</td>
      <td>41.69283384</td>
      <td>-87.60431945</td>
    </tr>
    <tr>
      <th>1</th>
      <td>10516598</td>
      <td>FALSO</td>
      <td>41.74410697</td>
      <td>-87.66449429</td>
    </tr>
    <tr>
      <th>2</th>
      <td>10519196</td>
      <td>FALSO</td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>3</th>
      <td>10519591</td>
      <td>FALSO</td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>4</th>
      <td>10534446</td>
      <td>FALSO</td>
      <td></td>
      <td></td>
    </tr>
  </tbody>
</table>
</div>

Esta atualização de definição não apagará as definições de conjunto de dados anteriores. Pode ainda aceder e consumir as definições anteriores.

```python
# specify `version_id` to get a previous definition 
ds_def_old = dataset.get_definition(version_id = 1)
ds_def_old.head(5)
```
<div>
<style scoped> .dataframe tbody tr th: só de-de-type {vertical-align: intermédia;}

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>ID</th>
      <th>Número de caso</th>
      <th>Date</th>
      <th>Bloquear</th>
      <th>IUCR</th>
      <th>Tipo de principal</th>
      <th>Descrição</th>
      <th>Descrição de localização</th>
      <th>Prisão</th>
      <th>Nacionais</th>
      <th>...</th>
      <th>Ward</th>
      <th>Área de Comunidade</th>
      <th>Código do FBI</th>
      <th>Coordenada x</th>
      <th>Coordenada Y</th>
      <th>Ano</th>
      <th>Atualizado em</th>
      <th>Latitude</th>
      <th>Longitude</th>
      <th>Localização</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>10498554</td>
      <td>HZ239907</td>
      <td>4/4/2016 23:56</td>
      <td>007XX E 111TH ST</td>
      <td>1153</td>
      <td>ENGANOSA PRÁTICA</td>
      <td>ROUBO DE IDENTIDADES FINANCEIRAS AO LONGO DE US $ 300</td>
      <td>OUTROS</td>
      <td>FALSO</td>
      <td>FALSO</td>
      <td>...</td>
      <td>9</td>
      <td>50</td>
      <td>11</td>
      <td>1183356</td>
      <td>1831503</td>
      <td>2016</td>
      <td>5/11/2016 15:48</td>
      <td>41.69283384</td>
      <td>-87.60431945</td>
      <td>(41.692833841, -87.60431945)</td>
    </tr>
    <tr>
      <th>1</th>
      <td>10516598</td>
      <td>HZ258664</td>
      <td>4/15/2016 17:00</td>
      <td>082XX S MARSHFIELD AVE</td>
      <td>890</td>
      <td>ROUBO</td>
      <td>DESDE A CRIAÇÃO</td>
      <td>RESIDÊNCIA</td>
      <td>FALSO</td>
      <td>FALSO</td>
      <td>...</td>
      <td>21</td>
      <td>71</td>
      <td>6</td>
      <td>1166776</td>
      <td>1850053</td>
      <td>2016</td>
      <td>5/12/2016 15:48</td>
      <td>41.74410697</td>
      <td>-87.66449429</td>
      <td>(41.744106973, -87.664494285)</td>
    </tr>
    <tr>
      <th>2</th>
      <td>10519196</td>
      <td>HZ261252</td>
      <td>4/15/2016 10:00</td>
      <td>104XX S SACRAMENTO AVE</td>
      <td>1154</td>
      <td>ENGANOSA PRÁTICA</td>
      <td>US $300 DE ROUBO DE IDENTIDADES FINANCEIRAS E, EM</td>
      <td>RESIDÊNCIA</td>
      <td>FALSO</td>
      <td>FALSO</td>
      <td>...</td>
      <td>19</td>
      <td>74</td>
      <td>11</td>
      <td></td>
      <td></td>
      <td>2016</td>
      <td>5/12/2016 15:50</td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>3</th>
      <td>10519591</td>
      <td>HZ261534</td>
      <td>4/15/2016 9:00</td>
      <td>113XX S PRAIRIE AVE</td>
      <td>1120</td>
      <td>ENGANOSA PRÁTICA</td>
      <td>FALSIFICAÇÃO</td>
      <td>RESIDÊNCIA</td>
      <td>FALSO</td>
      <td>FALSO</td>
      <td>...</td>
      <td>9</td>
      <td>49</td>
      <td>10</td>
      <td></td>
      <td></td>
      <td>2016</td>
      <td>5/13/2016 15:51</td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>4</th>
      <td>10534446</td>
      <td>HZ277630</td>
      <td>4/15/2016 10:00</td>
      <td>055XX N KEDZIE AVE</td>
      <td>890</td>
      <td>ROUBO</td>
      <td>DESDE A CRIAÇÃO</td>
      <td>INSTITUIÇÃO DE ENSINO, PÚBLICA, CRIAÇÃO</td>
      <td>FALSO</td>
      <td>FALSO</td>
      <td>...</td>
      <td>40</td>
      <td>13</td>
      <td>6</td>
      <td></td>
      <td></td>
      <td>2016</td>
      <td>5/25/2016 15:59</td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
  </tbody>
</table>
</div>

Como podemos ver do resultado, a primeira versão da definição do conjunto de dados mantém ainda todas as colunas dos dados de crime, independentemente da atualização posterior. Por exemplo, se tiver um aprendizado de máquina de modelo atualmente a primeira versão do conjunto de dados de consumo e usando `Date` coluna do conjunto de dados como um dos recursos, não terá qualquer erro com a atualização de definição mais recente, o que mantém única`ID`, `Arrest`, `Latitude`, `Longitude` colunas a partir dos dados de crime.

## <a name="how-to-access-dataset-definitions"></a>Como aceder a definições de conjunto de dados

Para obter uma lista de todas as definições, utilize `get_definitions()`. Para obter uma versão específica de uma definição, utilize `get_definition()`. O exemplo seguinte demonstra a obter uma lista de todas as definições e, em seguida, obter a versão 1:

```python
# list all definitions for the dataset
dataset.get_definitions()
# get version ID 1
dataset.get_definition(version_id=1)
```

A saída de `get_definitions()` é semelhante ao seguinte exemplo:

```text
{'2': VersionID: 2, State: active, Created: 2019-04-19 16:43:52.439890+00:00, Modified: 2019-04-19 16:43:52.439890+00:00, Notes: select useful column,
 '1': VersionID: 1, State: active, Created: 2019-04-19 16:39:14.112046+00:00, Modified: 2019-04-19 16:44:12.912663+00:00, Notes: None}
```

Depois de recuperar uma definição, pode usá-lo com os modelos de machine learning ou num pipeline de machine learning.

## <a name="manage-lifecycle-of-dataset-definitions"></a>Gerir o ciclo de vida de definições do conjunto de dados

Pode gerir o ciclo de vida de cada definição de conjunto de dados de forma independente. Existem três fases do ciclo de vida: Active Directory, preteridos ou arquivados.

### <a name="active"></a>Ativa

Quando é criada uma nova definição de conjunto de dados, ele está ativo por predefinição. 

### <a name="deprecate"></a>Preterir

Definições do conjunto de dados podem ser preteridas quando já não é recomendada a utilização e uma substituição está disponível. Quando uma definição de conjunto de dados preterida é usada no machine learning pipelines do, uma mensagem de aviso é retornada, mas a execução não será bloqueada.

Quando a descontinuar uma definição de conjunto de dados, especifique o ID de conjunto de dados e o ID de versão da definição de conjunto de dados para a definição de substituição. Estas informações são armazenadas e usadas na mensagem de aviso quando tentar consumir uma definição de conjunto de dados preterida.

```python
# get the definition that you want to deprecate
ds_def = dataset.get_definition(version_id = 1)

# deprecate it by providing the information for the replacement definition, which is recommended to be used in machine learning scenarios
ds_def.deprecate(deprecate_by_dataset_id=dataset.id, deprecated_by_definition_version=2)
```

### <a name="archive"></a>Arquivo

Definições do conjunto de dados podem ser arquivadas quando as definições não devem ser utilizados por qualquer motivo (por exemplo, os dados subjacentes não está mais disponíveis). Quando uma definição de conjunto de dados arquivada é usada no machine learning pipelines do, execução será bloqueada com o erro.

```python
# archive the definition with `version_id = 1`
ds_def = dataset.get_definition(version_id = 1)
ds_def.archive()
```

### <a name="reactivate"></a>Reativar

Pode reativar facilmente a qualquer definição de conjunto de dados preterida ou arquivada.

```python
# reactivate Dataset definition with `version_id =1` which was archived in the previous step
ds_def = dataset.get_definition(version_id = 1)
ds_def.reactivate()
```

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre como trabalhar com conjuntos de dados, consulte [criar e registar conjuntos de dados do Azure Machine Learning](how-to-create-register-datasets.md).

Para obter um exemplo da utilização de conjuntos de dados, consulte a [blocos de notas de exemplo](https://aka.ms/dataset-tutorial).