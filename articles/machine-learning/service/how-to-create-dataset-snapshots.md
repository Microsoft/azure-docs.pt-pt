---
title: Comparar & reproduzir os dados com instantâneos de conjunto de dados
titleSuffix: Azure Machine Learning service
description: Saiba como comparar dados ao longo do tempo e certifique-se a capacidade de reprodução com instantâneos de conjunto de dados
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
ms.date: 05/23/2019
ms.openlocfilehash: 525660be0f38c9458590e52cfcd575acb4cf5444
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66162061"
---
# <a name="compare-data-and-ensure-reproducibility-with-snapshots-preview"></a>Comparação de dados e certifique-se a capacidade de reprodução com instantâneos (pré-visualização)

Neste artigo, saiba como criar e gerir instantâneos dos seus [conjuntos de dados do Azure Machine Learning](how-to-create-register-datasets.md) (conjuntos de dados) para que possa capturar ou comparar dados ao longo do tempo. Conjuntos de dados tornam mais fácil de acessar e trabalhar com os seus dados na cloud em vários cenários.

**Conjunto de dados instantâneos** armazenar um perfil (estatísticas de resumo) dos dados no momento em que é criado. Pode optar por armazenar também uma cópia dos dados no seu instantâneo para a capacidade de reprodução.

>[!Important]
> Instantâneos de incorrer em custos de armazenamento. Armazenar uma cópia dos dados no seu instantâneo requer armazenamento ainda mais. Uso [ `dataset.delete_snapshot()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#delete-snapshot-snapshot-name-) quando já não forem necessários.

## <a name="when-to-use-snapshots"></a>Quando utilizar instantâneos

Existem três principais utilizações instantâneos:

+ **Validação de modelo**: Compare o perfil de dados de instantâneos diferentes entre execuções de preparação ou em relação a dados de produção.

+ **Modelar a capacidade de reprodução**: Reproduza os resultados ao chamar um instantâneo que inclui dados durante o treinamento.

+ **Controlar os dados ao longo do tempo**: Veja como o conjunto de dados evoluiu por [comparando perfis](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_snapshot.datasetsnapshot?view=azure-ml-py#compare-profiles-rhs-dataset-snapshot--include-columns-none--exclude-columns-none--histogram-compare-method--histogramcomparemethod-wasserstein--0--)
  
## <a name="prerequisites"></a>Pré-requisitos

Para criar instantâneos de conjunto de dados, terá de um conjunto de dados de Aprendizado de máquina de Azure registados. Se não tiver uma, veja [conjuntos de dados criar e registar](how-to-create-register-datasets.md).

## <a name="create-dataset-snapshots"></a>Criar instantâneos de conjunto de dados

Para criar um instantâneo de um conjunto de dados, utilize [ `dataset.create_snapshot()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset(class)?#create-snapshot-snapshot-name--compute-target-none--create-data-snapshot-false--target-datastore-none-) do pacote azureml-conjuntos de dados do SDK do Azure Machine Learning.

Por predefinição, o instantâneo armazena o perfil (estatísticas de resumo) dos dados com a versão mais recente [definição de conjunto de dados](how-to-manage-dataset-definitions.md) aplicada. Uma definição de conjunto de dados contém um registro de quaisquer passos de transformação definidos para os dados. É uma excelente forma de fazer a preparação de dados de trabalho reproduzível.

Opcionalmente, também pode incluir uma cópia dos dados no seu instantâneo adicionando `create_data_snapshot = True`.  Estes dados podem ser úteis para a capacidade de reprodução.

Este exemplo utiliza [crime dados de exemplo](https://dprepdata.blob.core.windows.net/dataset-sample-files/crime.csv) e um conjunto de dados chamado `dataset_crime` criadas com o artigo ["Criar e registar os conjuntos de dados"](how-to-create-register-datasets.md).

```Python
from azureml.core.workspace import Workspace
from azureml.core.dataset import Dataset
from azureml.data.dataset_snapshot import DatasetSnapshot
import datetime

# get existing workspace
workspace = Workspace.from_config()

# get existing, named dataset:
dataset = workspace.datasets['dataset_crime']

# assign name to snapshot
snapshot_name = 'snapshot_' + datetime.datetime.today().strftime('%Y%m%d%H%M%S')

# create snapshot to store profile from a remote environment
# and include copy of data
snapshot = dataset.create_snapshot(snapshot_name = snapshot_name,
                                   compute_target = remote_compute_target,
                                   create_data_snapshot = True)
```

Uma vez que os instantâneos são criados de forma assíncrona, utilize o [ `wait_for_completion()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_snapshot.datasetsnapshot?view=azure-ml-py#wait-for-completion-show-output-true--status-update-frequency-10-) método para monitorizar o processo.

```Python
# monitor process every 10 seconds
snapshot.wait_for_completion(show_output=True, status_update_frequency=10)

# return snapshot just created
dataset.get_snapshot(snapshot_name)
```

Saída:

```
Action status: Running
Action status: Running
Action status: Completed


DatasetSnapshot(Name: snapshot_20190411165420,
Dataset Id: 66cd0830-2f72-41e5-b677-d2d953f54821,
Workspace: "your_workspace_name",
Dataset definition version: 1,
Snapshot created date: 2019-05-11 17:24:00+00:00)
```

Uso [ `dataset.delete_snapshot()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#delete-snapshot-snapshot-name-) quando já não forem necessários.

## <a name="find-snapshots"></a>Encontrar instantâneos

Para obter um instantâneo existente, utilize [ `get_snapshot()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py#get-snapshot-snapshot-name-).

Para obter uma lista de sua guardados instantâneos de um determinado conjunto de dados, utilize [ `get_all_snapshots()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py#get-all-snapshots--).

```Python
# Get named snapshot for this dataset
dataset.get_snapshot(snapshot_name)

# Get all snapshots for this dataset
dataset.get_all_snapshots()
```

## <a name="get-data-and-profiles-from-snapshots"></a>Obter dados e perfis de instantâneos

Cada instantâneo gera um perfil do conjunto de dados, o que inclui as estatísticas de resumo e lhe dá a opção para guardar uma cópia dos seus dados.

### <a name="get-the-data-profile"></a>Obter o perfil de dados

Uso [ `get_profile()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py#get-profile-arguments-none--generate-if-not-exist-true--workspace-none--compute-target-none-) para acessar o perfil dos seus dados.  Pode utilizar este perfil para comparar os dados de treinamento e de produção, por exemplo.

```Python
snapshot.get_profile()
```

||Tipo|Mín.|Máx.|Contagem|Contagem de em falta|Não tem em falta contagem|Percentagem em falta|Contagem de erros|Contagem de vazia|0,1% Quantile|1% Quantile|5% Quantile|25% Quantile|50% Quantile|75% Quantile|95% Quantile|99% Quantile|99,9% Quantile|média|Desvio padrão|Variância|Assimetrias|Kurtosis
-|----|---|---|-----|-------------|-----------------|---------------|-----------|-----------|-------------|-----------|-----------|------------|------------|------------|------------|------------|--------------|----|------------------|--------|--------|--------
ID|FieldType.INTEGER|1.04986e + 07|1.05351e + 07|10.0|0.0|10.0|0.0|0.0|0.0|1.04986e + 07|1.04992e + 07|1.04986e + 07|1.05166e + 07|1.05209e + 07|1.05259e + 07|1.05351e + 07|1.05351e + 07|1.05351e + 07|1.05195e + 07|12302.7|1.51358e + 08|-0.495701|-1.02814
Número de caso|FieldType.STRING|HZ239907|HZ278872|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
Date|FieldType.DATE|2016-04-04 23:56:00+00:00|2016-04-15 17:00:00+00:00|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
Bloquear|FieldType.STRING|004XX S KILBOURN AVE|113XX S PRAIRIE AVE|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
IUCR|FieldType.INTEGER|810|1154|10.0|0.0|10.0|0.0|0.0|0.0|810|850|810|890|1136|1153|1154|1154|1154|1058.5|137.285|18847.2|-0.785501|-1.3543
Tipo de principal|FieldType.STRING|ENGANOSA PRÁTICA|ROUBO|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
Descrição|FieldType.STRING|VERIFICAÇÃO DE FALSAS|AO LONGO DE US $500|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
Descrição de localização|FieldType.STRING||INSTITUIÇÃO DE ENSINO, PÚBLICA, CRIAÇÃO|10.0|0.0|10.0|0.0|0.0|1.0||||||||||||||
Prisão|FieldType.BOOLEAN|Falso|Falso|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
Nacionais|FieldType.BOOLEAN|Falso|Falso|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
Nada|FieldType.INTEGER|531|2433|10.0|0.0|10.0|0.0|0.0|0.0|531|531|531|614|1318.5|1911|2433|2433|2433|1371.1|692.094|478994|0.105418|-1.60684
Distrito|FieldType.INTEGER|5|24|10.0|0.0|10.0|0.0|0.0|0.0|5|5|5|6|13|19|24|24|24|13.5|6.94822|48.2778|0.0930109|-1.62325
Ward|FieldType.INTEGER|1|48|10.0|0.0|10.0|0.0|0.0|0.0|1|5|1|9|22.5|40|48|48|48|24.5|16.2635|264.5|0.173723|-1.51271
Área de Comunidade|FieldType.INTEGER|4|77|10.0|0.0|10.0|0.0|0.0|0.0|4|8.5|4|24|37.5|71|77|77|77|41.2|26.6366|709.511|0.112157|-1.73379

### <a name="get-the-data-from-the-snapshot"></a>Obter os dados a partir do instantâneo

Para obter uma cópia dos dados guardados no instantâneo de um conjunto de dados, gerar um pandas DataFrame com o [ `to_pandas_dataframe()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#to-pandas-dataframe--) método.

Este método falha se uma cópia dos dados não foi pedida durante a criação do instantâneo.

```Python
snapshot.to_pandas_dataframe().head(3)
```

||ID|Número de caso|Date|Bloquear|IUCR|Tipo de principal|Descrição|Descrição de localização|Prisão|Nacionais|...|Ward|Área de Comunidade|Código do FBI|Coordenada x|Coordenada Y|Ano|Atualizado em|Latitude|Longitude|Localização
-|--|-----------|----|-----|----|------------|-----------|--------------------|------|--------|---|----|--------------|--------|------------|------------|----|----------|--------|---------|--------
0|10498554|HZ239907|2016-04-04 23:56:00|007XX E 111TH ST|1153|ENGANOSA PRÁTICA|ROUBO DE IDENTIDADES FINANCEIRAS AO LONGO DE US $ 300|OUTROS|Falso|Falso|...|9|50|11|1183356.0|1831503.0|2016|2016-05-11 15:48:00|41.692834|-87.604319|(41.692833841, -87.60431945)
1|10516598|HZ258664|2016-04-15 17:00:00|082XX S MARSHFIELD AVE|890|ROUBO|DESDE A CRIAÇÃO|RESIDÊNCIA|Falso|Falso|...|21|71|6|1166776.0|1850053.0|2016|2016-05-12 15:48:00|41.744107|-87.664494|(41.744106973, -87.664494285)
2|10519196|HZ261252|2016-04-15 10:00:00|104XX S SACRAMENTO AVE|1154|ENGANOSA PRÁTICA|US $300 DE ROUBO DE IDENTIDADES FINANCEIRAS E, EM|RESIDÊNCIA|Falso|Falso|...|19|74|11|NaN|NaN|2016|2016-05-12 15:50:00|NaN|NaN|

## <a name="next-steps"></a>Passos Seguintes

* Consulte o SDK [descrição geral](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) para padrões de design e exemplos de utilização.

* Para obter um exemplo da utilização de instantâneos de conjunto de dados, consulte a [blocos de notas de exemplo](https://aka.ms/dataset-tutorial).
