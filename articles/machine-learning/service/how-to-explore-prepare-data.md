---
title: Explorar e transformar dados (classe DataSet)
titleSuffix: Azure Machine Learning service
description: Explorar dados usando estatísticas de resumo e preparar dados por meio de limpeza de dados, transformação e engenharia de recursos
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 05/23/2019
ms.openlocfilehash: f680a1cb15edf0141897c74da3b7c7afa01acae0
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/31/2019
ms.locfileid: "68699126"
---
# <a name="explore-and-prepare-data-with-the-dataset-class-preview"></a>Explorar e preparar dados com a classe DataSet (visualização)

Saiba como explorar e preparar dados com o pacote azureml-DataSets no SDK do [Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py). A classe [DataSet](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py) (visualização) permite que você explore e prepare seus dados fornecendo funções como: amostragem, estatísticas de resumo e transformações inteligentes. As etapas de transformação são salvas nas [definições do conjunto](how-to-manage-dataset-definitions.md) de recursos com a capacidade de lidar com vários arquivos grandes de esquemas diferentes de maneira altamente escalonável.

> [!Important]
> Algumas classes de DataSet (versão prévia) têm dependências no pacote [azureml-dataprep](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py) (GA). Embora as funções de transformação possam ser feitas diretamente com as [funções de preparação de dados](how-to-transform-data.md)do GA'ed, recomendamos que os wrappers de pacote do DataSet descritos neste artigo se você estiver criando uma nova solução. Azure Machine Learning conjuntos de dados (versão prévia) permitem que você não apenas transforme os mesmos, mas também os [dados de instantâneo](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_snapshot.datasetsnapshot?view=azure-ml-py) e armazene as definições de conjunto do dados com [controle de versão](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset?view=azure-ml-py). Conjuntos de dados é a próxima versão do data Prep SDK, oferecendo funcionalidade expandida para o gerenciamento de conjuntos de dados em soluções de ia.

## <a name="prerequisites"></a>Pré-requisitos

Para explorar e preparar seus dados, você precisará de:

* Uma subscrição do Azure. Se você não tiver uma assinatura do Azure, crie uma conta gratuita antes de começar. Experimente a [versão gratuita ou paga do serviço de Azure Machine Learning](https://aka.ms/AMLFree) hoje mesmo.

* Uma área de trabalho de serviço do Azure Machine Learning. Consulte [criar um Azure Machine Learning espaço de trabalho de serviço](https://docs.microsoft.com/azure/machine-learning/service/setup-create-workspace).

* O SDK do Azure Machine Learning para Python (versão 1.0.21 ou posterior), que inclui o pacote de conjuntos de linhas do azureml. Para instalar ou atualizar para a versão mais recente do SDK, consulte [instalar ou atualizar o SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

* O SDK do Azure Machine Learning data Prep. Para instalar ou atualizar para a versão mais recente, consulte [instalar ou atualizar o SDK de preparação de dados](https://docs.microsoft.com/python/api/overview/azure/dataprep/intro?view=azure-dataprep-py#install).

* Baixe os arquivos de exemplo a seguir junto com os exemplos: [crime. csv](https://dprepdata.blob.core.windows.net/dataset-sample-files/crime.csv) e [City. JSON](https://dprepdata.blob.core.windows.net/dataset-sample-files/city.json).

## <a name="sampling"></a>Amostragem

Obtenha um exemplo de seus dados para obter uma compreensão inicial de sua arquitetura de dados e conteúdo. Neste momento, o [`sample()`](https://docs.microsoft.com//python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py#sample-sample-strategy--arguments-) método da classe DataSet dá suporte às principais estratégias de amostragem de N, simples aleatórias e de sobreratificações.

```Python
from azureml.core.dataset import Dataset
import random

# create an in-memory Dataset from a local file
dataset = Dataset.auto_read_files('./data/crime.csv')

# create seed for Simple Random and Stratified sampling
seed = random.randint(0, 4294967295)
```

### <a name="top-n-sample"></a>Exemplo de N principais

Para a amostragem de N superior, os primeiros N registros de seu conjunto de seus conjuntos de recursos são o seu exemplo. Isso será útil se você estiver apenas tentando ter uma ideia de como os seus registros de dados se parecem ou para ver quais campos estão em seus dados.

```Python
top_n_sample_dataset = dataset.sample('top_n', {'n': 5})
top_n_sample_dataset.to_pandas_dataframe()
```

||id|Número de caso|Date|Bloquear|IUCR|Tipo de principal|...|
-|--|-----------|----|-----|----|------------|---
0|10498554|HZ239907|4/4/2016 23:56|007XX E 111TH ST|1153|PRÁTICA ENGANOSA|...
1|10516598|HZ258664|4/15/2016 17:00|082XX S MARSHFIELD AVE|890|ROUBO|...
2|10519196|HZ261252|4/15/2016 10:00|104XX S SACRAMENTO ALVAR|1154|PRÁTICA ENGANOSA|...
3|10519591|HZ261534|4/15/2016 9:00|113XX S PRAIRIE AVE|1120|PRÁTICA ENGANOSA|...
4|10534446|HZ277630|4/15/2016 10:00|055XX N KEDZIE AVE|890|ROUBO|...

### <a name="simple-random-sample"></a>Exemplo aleatório simples

Em amostragem aleatória simples, cada membro da população de dados tem uma chance igual de ser selecionado como parte do exemplo. Na estratégia de exemplo, os registros do conjunto de recursos são selecionados com base na probabilidade especificada e retorna um conjunto de registros modificado. `simple_random` O parâmetro semente é opcional.

```Python
simple_random_sample_dataset = dataset.sample('simple_random', {'probability':0.3, 'seed': seed})
simple_random_sample_dataset.to_pandas_dataframe()
```

||id|Número de caso|Date|Bloquear|IUCR|Tipo de principal|...|
-|--|-----------|----|-----|----|------------|---
0|10516598|HZ258664|4/15/2016 17:00|082XX S MARSHFIELD AVE|890|ROUBO|...
1|10519196|HZ261252|4/15/2016 10:00|104XX S SACRAMENTO ALVAR|1154|PRÁTICA ENGANOSA|...
2|10534446|HZ277630|4/15/2016 10:00|055XX N KEDZIE AVE|890|ROUBO|...

### <a name="stratified-sample"></a>Exemplo de sobreratificação

Os exemplos de sobreratificação asseguram que determinados grupos de uma população sejam representados no exemplo. Na estratégia de `fractions` exemplo,apopulaçãoédivididaemStrata,ousubgrupos,combaseemsemelhanças,eosregistrossãoselecionadosaleatoriamentedecadaStratadeacordocomospesosStrataindicados`stratified` pelo parâmetro.

No exemplo a seguir, agrupamos cada registro pelas colunas especificadas e incluimos o registro dito com base nas informações de peso do Strata `fractions`X no. Se um Strata não for especificado ou o registro não puder ser agrupado, o peso padrão para a amostra será 0.

```Python
# take 50% of records with `Primary Type` as `THEFT` and 20% of records with `Primary Type` as `DECEPTIVE PRACTICE` into sample Dataset
fractions = {}
fractions[('THEFT',)] = 0.5
fractions[('DECEPTIVE PRACTICE',)] = 0.2

sample_dataset = dataset.sample('stratified', {'columns': ['Primary Type'], 'fractions': fractions, 'seed': seed})

sample_dataset.to_pandas_dataframe()
```

||id|Número de caso|Date|Bloquear|IUCR|Tipo de principal|...|
-|--|-----------|----|-----|----|------------|---
0|10516598|HZ258664|4/15/2016 17:00|082XX S MARSHFIELD AVE|890|ROUBO|...
1|10534446|HZ277630|4/15/2016 10:00|055XX N KEDZIE AVE|890|ROUBO|...
2|10535059|HZ278872|4/15/2016 4:30|004XX S KILBOURN ALVAR|810|ROUBO|...

## <a name="explore-with-summary-statistics"></a>Explorar com estatísticas de resumo

 Detectar anomalias, valores ausentes ou contagens de erros com [`get_profile()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#get-profile-arguments-none--generate-if-not-exist-true--workspace-none--compute-target-none-) o método. Essa função obtém o perfil e as estatísticas de Resumo de seus dados, o que, por sua vez, ajuda a determinar as operações de preparação de dados necessárias a serem aplicadas.

```Python
dataset.get_profile()
```

||Type|Mín.|Máx.|Contagem|Contagem de em falta|Não tem em falta contagem|Percentagem em falta|Contagem de erros|Contagem de vazia|0,1% Quantile|1% Quantile|5% Quantile|25% Quantile|50% Quantile|75% Quantile|95% Quantile|99% Quantile|99,9% Quantile|média|Desvio padrão|Variância|Assimetrias|Kurtosis
-|----|---|---|-----|-------------|-----------------|---------------|-----------|-----------|-------------|-----------|-----------|------------|------------|------------|------------|------------|--------------|----|------------------|--------|--------|--------
id|FieldType.INTEGER|1.04986 e + 07|1.05351 e + 07|10,0|0.0|10,0|0.0|0.0|0.0|1.04986 e + 07|1.04992 e + 07|1.04986 e + 07|1.05166 e + 07|1.05209 e + 07|1.05259 e + 07|1.05351 e + 07|1.05351 e + 07|1.05351 e + 07|1.05195 e + 07|12302,7|1.51358 e + 08|-0,495701|-1, 2814
Número de caso|FieldType.STRING|HZ239907|HZ278872|10,0|0.0|10,0|0.0|0.0|0.0||||||||||||||
Date|FieldType. DATE|2016-04-04 23:56:00+00:00|2016-04-15 17:00:00+00:00|10,0|0.0|10,0|0.0|0.0|0.0||||||||||||||
Bloquear|FieldType.STRING|004XX S KILBOURN ALVAR|113XX S PRAIRIE AVE|10,0|0.0|10,0|0.0|0.0|0.0||||||||||||||
IUCR|FieldType.INTEGER|810|1154|10,0|0.0|10,0|0.0|0.0|0.0|810|850|810|890|1136|1153|1154|1154|1154|1058,5|137,285|18847,2|-0,785501|-1,3543
Tipo de principal|FieldType.STRING|PRÁTICA ENGANOSA|ROUBO|10,0|0.0|10,0|0.0|0.0|0.0||||||||||||||
Descrição|FieldType.STRING|VERIFICAÇÃO FALSA|MAIS DE $500|10,0|0.0|10,0|0.0|0.0|0.0||||||||||||||
Descrição de localização|FieldType.STRING||ESCOLA, PÚBLICO, PRÉDIO|10,0|0.0|10,0|0.0|0.0|1.0||||||||||||||
Prisão|FieldType. BOOLEAN|False|False|10,0|0.0|10,0|0.0|0.0|0.0||||||||||||||
Nacionais|FieldType. BOOLEAN|False|False|10,0|0.0|10,0|0.0|0.0|0.0||||||||||||||
Super|FieldType.INTEGER|531|2433|10,0|0.0|10,0|0.0|0.0|0.0|531|531|531|614|1318,5|1911|2433|2433|2433|1371,1|692, 94|478994|0,105418|-1,60684
Distrito|FieldType.INTEGER|5|24|10,0|0.0|10,0|0.0|0.0|0.0|5|5|5|6|13|19|24|24|24|13.5|6,94822|48,2778|0, 930109|-1,62325
Ward|FieldType.INTEGER|1|48|10,0|0.0|10,0|0.0|0.0|0.0|1|5|1|9|22.5|40|48|48|48|24.5|16,2635|264,5|0,173723|-1,51271
Área de Comunidade|FieldType.INTEGER|4|77|10,0|0.0|10,0|0.0|0.0|0.0|4|8.5|4|24|37,5|71|77|77|77|41,2|26,6366|709,511|0,112157|-1,73379
Código do FBI|FieldType.INTEGER|6|11|10,0|0.0|10,0|0.0|0.0|0.0|6|6|6|6|11|11|11|11|11|9.4|2,36643|5.6|-0,702685|-1,59582
Coordenada x|FieldType.INTEGER|1.16309 e + 06|1.18336 e + 06|10,0|7.0|3.0|0,7|0.0|0.0|1.16309 e + 06|1.16309 e + 06|1.16309 e + 06|1.16401 e + 06|1.16678 e + 06|1.17921 e + 06|1.18336 e + 06|1.18336 e + 06|1.18336 e + 06|1.17108 e + 06|10793,5|1.165 e + 08|0,335126|-2,33333
Coordenada Y|FieldType.INTEGER|1.8315 e + 06|1.908 e + 06|10,0|7.0|3.0|0,7|0.0|0.0|1.8315 e + 06|1.8315 e + 06|1.8315 e + 06|1.83614 e + 06|1.85005 e + 06|1.89352 e + 06|1.908 e + 06|1.908 e + 06|1.908 e + 06|1.86319 e + 06|39905,2|1.59243 e + 09|0,293465|-2,33333
Ano|FieldType.INTEGER|2016|2016|10,0|0.0|10,0|0.0|0.0|0.0|2016|2016|2016|2016|2016|2016|2016|2016|2016|2016|0|0|NaN|NaN
Atualizado em|FieldType. DATE|2016-05-11 15:48:00+00:00|2016-05-27 15:45:00+00:00|10,0|0.0|10,0|0.0|0.0|0.0||||||||||||||
Latitude|FieldType.DECIMAL|41,6928|41,9032|10,0|7.0|3.0|0,7|0.0|0.0|41,6928|41,6928|41,6928|41,7057|41,7441|41,8634|41,9032|41,9032|41,9032|41,78|0,109695|0, 12033|0,292478|-2,33333
Longitude|FieldType.DECIMAL|-87,6764|-87,6043|10,0|7.0|3.0|0,7|0.0|0.0|-87,6764|-87,6764|-87,6764|-87,6734|-87,6645|-87,6194|-87,6043|-87,6043|-87,6043|-87,6484|0, 386264|0, 1492|0,344429|-2,33333
Location|FieldType.STRING||(41,903206037,-87,676361925)|10,0|0.0|10,0|0.0|0.0|7.0||||||||||||||

## <a name="impute-missing-values"></a>Impute valores em falta

Em DataSets, valores nulos, NaNs e valores que não contêm nenhum conteúdo são considerados valores ausentes. Eles podem afetar o desempenho de seus modelos de aprendizado de máquina ou levar a conclusões inválidas. Imputação é uma abordagem comum para tratar valores ausentes e é útil quando você tem uma alta porcentagem de registros de valor ausente que não pode simplesmente excluir.

No perfil do conjunto de linhas gerado na seção anterior, vemos que `Latitude` as `Longitude` colunas e têm uma alta porcentagem de valores ausentes. Neste exemplo, calculamos os valores de Mean e imputar ausentes para essas duas colunas.

Primeiro, obtenha a definição mais recente do conjunto de [`get_definition()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#get-definition-version-id-none-) dados com o e o parênteses com [`keep_columns()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow#keep-columns-columns--multicolumnselection--validate-column-exists--bool---false-----azureml-dataprep-api-dataflow-dataflow), portanto, apenas exibimos as colunas que desejamos abordar.

```Python
from azureml.core.dataset import Dataset
import azureml.dataprep as dprep

# get the latest definition of Dataset
ds_def = dataset.get_definition()

# keep useful columns for this example
ds_def = ds_def.keep_columns(['ID', 'Arrest', 'Latitude', 'Longitude'])
ds_def.head(3)
```

||id|Prisão| Latitude|Longitude|
-|---------|-----|---------|----------|
|0|10498554|False|41,692834|-87,604319|
|1|10516598|False| 41,744107 |-87,664494|
|2|10519196|False| NaN|NaN|

Em seguida, verifique `MEAN` o valor da coluna latitude usando a [`summarize()`](/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-ml-py#summarize-summary-columns--typing-union-typing-list-azureml-dataprep-api-dataflow--summarycolumnsvalue---nonetype----none--group-by-columns--typing-union-typing-list-str---nonetype----none--join-back--bool---false--join-back-columns-prefix--typing-union-str--nonetype----none-----azureml-dataprep-api-dataflow-dataflow) função. Esta função aceita uma matriz de colunas no `group_by_columns` parâmetro para especificar o nível de agregação. O `summary_columns` parâmetro aceita a `SummaryColumnsValue` função, que especifica o nome da coluna atual, o novo nome do campo calculado e `SummaryFunction` o a ser executado.

```Python
lat_mean = ds_def.summarize(group_by_columns = ['Arrest'],
                            summary_columns = [dprep.SummaryColumnsValue(column_id = 'Latitude',
                                                                         summary_column_name = 'Latitude_MEAN',
                                                                         summary_function = dprep.SummaryFunction.MEAN)])
lat_mean = lat_mean.filter(lat_mean['Arrest'] == False)
lat_mean.head(1)
```

||Prisão|Latitude_MEAN|
--|-----|--------|
|0|False|41,780049|

Depois de verificarmos os valores para imputar, [`ImputeMissingValuesBuilder`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.imputemissingvaluesbuilder?view=azure-dataprep-py) use para aprender uma expressão fixa que imputes as colunas com um `CUSTOM` valor `MIN`calculado `MAX`, `MEAN` , valor ou. Quando `group_by_columns` for especificado, valores em falta vão ser imputed ao grupo com `MIN`, `MAX`, e `MEAN` calculado por grupo.

O [`ImputeColumnArguments`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.imputecolumnarguments?view=azure-dataprep-pyfunction) aceita uma cadeia de caracteres column_id e `ReplaceValueFunction` um para especificar o tipo imputar. Para o valor de longitude ausente, imputar-o com-87 com base no conhecimento externo.

```Python
# impute with MEAN
impute_mean = dprep.ImputeColumnArguments(column_id = 'Latitude',
                                          impute_function = dprep.ReplaceValueFunction.MEAN)

# impute with custom value -87
impute_custom = dprep.ImputeColumnArguments(column_id='Longitude',
                                            custom_impute_value=-87)
```

As etapas de imputar podem ser encadeadas em `ImputeMissingValuesBuilder` um objeto usando [`Builders`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.builders?view=azure-dataprep-py) a função [`impute_missing_values()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.builders?view=azure-dataprep-py#impute-missing-values-impute-columns--typing-list-azureml-dataprep-api-builders-imputecolumnarguments----none--group-by-columns--typing-union-typing-list-str---nonetype----none-----azureml-dataprep-api-builders-imputemissingvaluesbuilder)de classe. O `impute_columns` parâmetro aceita uma matriz de `ImputeColumnArguments` objetos.

```Python
# get instance of ImputeMissingValuesBuilder
impute_builder = ds_def.builders.impute_missing_values(impute_columns=[impute_mean, impute_custom],
                                                   group_by_columns=['Arrest'])
```

Chame a [`learn()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.imputemissingvaluesbuilder?view=azure-dataprep-py#learn------none) função para armazenar as etapas imputar e aplique-as a um objeto Dataflow [`to_dataflow()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.imputemissingvaluesbuilder?view=azure-dataprep-py#to-dataflow------azureml-dataprep-api-dataflow-dataflow)usando.

```Python
impute_builder.learn()
ds_def = impute_builder.to_dataflow()
ds_def.head(3)
```

Conforme mostrado na tabela de saída a seguir, o Latitude ausente foi imputados com `MEAN` o valor `Arrest==False` de Group e a longitude ausente foi imputados com-87.

||id|Prisão|Latitude|Longitude
-|---------|-----|---------|----------
0|10498554|False|41,692834|-87,604319
1|10516598|False|41,744107|-87,664494
2|10519196|False|41,780049|-87, 0

Atualize a definição de conjunto de [`update_definition()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py#update-definition-definition--definition-update-message-) um com, para manter as etapas de transformação executadas.

```Python
dataset = dataset.update_definition(ds_def, 'Impute Missing')
dataset.head(3)
```

||id|Prisão|Latitude|Longitude
-|---------|-----|---------|----------
0|10498554|False|41,692834|-87,604319
1|10516598|False|41,744107|-87,664494
2|10519196|False|41,780049|-87, 0

## <a name="create-assertion-rules"></a>Criar regras de asserção

Frequentemente, os dados com os quais trabalhamos ao limpar e preparar dados são apenas um subconjunto do total de dados de que precisamos para a produção. Como resultado, algumas das suposições que fazemos como parte de nossa limpeza podem se tornar falsas. Por exemplo, em um conjunto de dados que é atualizado continuamente, uma coluna que originalmente continha números dentro de um determinado intervalo pode conter um intervalo maior de valores em execuções posteriores. Esses erros geralmente resultam em pipelines rompidos ou dados incorretos.

Os conjuntos de dados dão suporte à criação de asserções em data, que são avaliadas à medida que o pipeline é executado. Essas asserções nos permitem verificar se nossas suposições sobre os dados continuam precisas e, quando não, para tratar as falhas de acordo.

Por exemplo, se você quiser restringir `Latitude` e `Longitude` valores em seu conjunto de informações para intervalos numéricos específicos [`assert_value()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#assert-value-columns--multicolumnselection--expression--azureml-dataprep-api-expressions-expression--policy--azureml-dataprep-api-engineapi-typedefinitions-assertpolicy----assertpolicy-errorvalue--1---error-code--str----assertionfailed------azureml-dataprep-api-dataflow-dataflow) , o método garante que sempre será o caso.

```Python
from azureml.dataprep import value
from azureml.core.dataset import Dataset

# get the latest definition of the Dataset
ds_def = dataset.get_definition()

# set assertion rules for `Latitude` and `Longitude` columns
ds_def = ds_def.assert_value('Latitude', (value <= 90) & (value >= -90), error_code='InvalidLatitude')
ds_def = ds_def.assert_value('Longitude', (value <= 180) & (value >= -87), error_code='InvalidLongitude')

ds_def.get_profile()
```

||Type|Mín.|Máx.|Contagem|Contagem de em falta|Não tem em falta contagem|Percentagem em falta|Contagem de erros|Contagem de vazia|0,1% Quantile|1% Quantile|5% Quantile|25% Quantile|50% Quantile|75% Quantile|95% Quantile|99% Quantile|99,9% Quantile|média|Desvio padrão|Variância|Assimetrias|Kurtosis
-|----|---|---|-----|-------------|-----------------|---------------|-----------|-----------|-------------|-----------|-----------|------------|------------|------------|------------|------------|--------------|----|------------------|--------|--------|--------
id|FieldType.INTEGER|1.04986 e + 07|1.05351 e + 07|10,0|0.0|10,0|0.0|0.0|0.0|1.04986 e + 07|1.04992 e + 07|1.04986 e + 07|1.05166 e + 07|1.05209 e + 07|1.05259 e + 07|1.05351 e + 07|1.05351 e + 07|1.05351 e + 07|1.05195 e + 07|12302,7|1.51358 e + 08|-0,495701|-1, 2814
Prisão|FieldType. BOOLEAN|False|False|10,0|0.0|10,0|0.0|0.0|0.0||||||||||||||
Latitude|FieldType.DECIMAL|41,6928|41,9032|10,0|0.0|10,0|0.0|0.0|0.0|41,6928|41,7185|41,6928|41,78|41,78|41,78|41,9032|41,9032|41,9032|41,78|0, 517107|0, 2674|0,837593|1,05
Longitude|FieldType.INTEGER|-87|-87|10,0|0.0|10,0|0.0|3.0|0.0|-87|-87|-87|-87|-87|-87|-87|-87|-87|-87|0|0|NaN|NaN

No perfil, você verá que o `Error Count` para a `Longitude` coluna é 3. O código a seguir filtra o conjunto de valores, recupera o erro e vê qual valor causa a falha da asserção. A partir daqui, ajuste seu código e limpe os dados adequadamente.

```Python
from azureml.dataprep import col

ds_error = ds_def.filter(col('Longitude').is_error())
error = ds_error.head(10)['Longitude'][0]

print(error.originalValue)
```

    -87.60431945

## <a name="derive-columns-by-example"></a>Derivar colunas por exemplo

Uma das ferramentas mais avançadas para conjuntos de informações é a capacidade de derivar colunas usando exemplos de resultados desejados. Isso permite que você dê um exemplo ao SDK para que ele possa gerar código para alcançar as transformações pretendidas.

```Python
from azureml.core.dataset import Dataset

# create an in-memory Dataset from a local file
dataset = Dataset.auto_read_files('./data/crime.csv')
dataset.head(3)
```

||id|Número de caso|Date|Bloquear|...|
-|---------|-----|---------|----|---
0|10498554|HZ239907|2016-04-04 23:56:00|007XX E 111TH ST|...
1|10516598|HZ258664|2016-04-15 17:00:00|082XX S MARSHFIELD AVE|...
2|10519196|HZ261252|2016-04-15 10:00:00|104XX S SACRAMENTO ALVAR|...

Digamos que você precise transformar o formato de data e hora para ' 2016-04-04 19:10-12AM '. No argumento, forneça exemplos de sua saída desejada `example_data` no parâmetro neste formato: *(saída original, saída desejada)* . [`derive_column_by_example()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#derive-column-by-example-source-columns--sourcecolumns--new-column-name--str--example-data--exampledata-----azureml-dataprep-api-dataflow-dataflow)

O código a seguir fornece dois exemplos de saída desejada, ("2016-04-04 23:56:00", "2016-04-04 19:10-12AM") e ("2016-04-15 17:00:00", "2016-04-15 16:00-18:00")

```Python
ds_def = dataset.get_definition()
ds_def = ds_def.derive_column_by_example(
        source_columns = "Date",
        new_column_name = "Date_Time_Range",
        example_data = [("2016-04-04 23:56:00", "2016-04-04 10PM-12AM"), ("2016-04-15 17:00:00", "2016-04-15 4PM-6PM")]
    )
ds_def.keep_columns(['ID','Date','Date_Time_Range']).head(3)
```

Na tabela a seguir, observe que uma nova coluna, Date_Time_Range, contém registros no formato especificado.

||id|Date|Date_Time_Range
-|--------|-----|----
0|10498554|2016-04-04 23:56:00|2016-04-04 19:10-12AM
1|10516598|2016-04-15 17:00:00|2016-04-15 16:00-18:00
2|10519196|2016-04-15 10:00:00|2016-04-15 10H-12:00

```Python
# update Dataset definition to keep the transformation steps performed.
dataset = dataset.update_definition(ds_def, 'Derive Date_Time_Range')
```

## <a name="fuzzy-groupings"></a>Agrupamentos difuso

Ao coletar dados de fontes diferentes, você pode encontrar variações de ortografia, capitalização ou abreviações das mesmas entidades. Padronizar e reconciliar automaticamente essas variantes com a funcionalidade de agrupamento difuso do SDK ou clustering de texto.

Por exemplo, a coluna `inspections.business.city` contém várias formas do nome da cidade "San Francisco".

```Python
from azureml.core.dataset import Dataset

# create an in-memory Dataset from a local json file
dataset = Dataset.auto_read_files('./data/city.json')
dataset.head(5)
```

||inspections.business.business_id|inspeções de business_name|inspections.business.address|inspections.business.city|...|
-|-----|-------------------------|------------|--|---
0|16162|Quick-N-eZee Índico pratos|3861 24 St|IF|...
1|67565|King of tailandês macarrão Cafe|1541 TARAVAL St|SÃO FRANCISCO|...
2|67565|King of tailandês macarrão Cafe|1541 TARAVAL St|SÃO FRANCISCO|...
3|68701|Grindz|832 Clement St|IF|...
4|69186|Premier que atende & Events, Inc.|1255 22 St|S.F.|...

Vamos usar o [`fuzzy_group_column()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#fuzzy-group-column-source-column--str--new-column-name--str--similarity-threshold--float---0-8--similarity-score-column-name--str---none-----azureml-dataprep-api-dataflow-dataflow) método para adicionar uma coluna com o formato canônico detectado automaticamente de "San Francisco". Os argumentos `source_column` e `new_column_name` são obrigatórios. Você também tem a opção de:

* Crie uma nova coluna, `similarity_score_column_name`, que mostra a pontuação de similaridade entre cada par de valores originais e canônicos.

* Forneça um `similarity_threshold`, que é a pontuação de similaridade mínima para os valores que serão agrupados juntos.

```Python
# get the latest Dataset definition
ds_def = dataset.get_definition()
ds_def = ds_def.fuzzy_group_column(source_column='inspections.business.city',
                                       new_column_name='city_grouped',
                                       similarity_threshold=0.8,
                                       similarity_score_column_name='similarity_score')
ds_def.head(5)
```

||inspections.business.business_id|inspeções de business_name|inspections.business.address|inspections.business.city|city_grouped|similarity_score|...|
-|-----|-------------------------|------------|--|---|---|---
0|16162|Quick-N-eZee Índico pratos|3861 24 St|IF|São Francisco|0,814806|...
1|67565|King of tailandês macarrão Cafe|1541 TARAVAL St|SÃO FRANCISCO|São Francisco|1, 0|...
2|67565|King of tailandês macarrão Cafe|1541 TARAVAL St|SÃO FRANCISCO|São Francisco|1, 0|...
3|68701|Grindz|832 Clement St|IF|São Francisco|0,814806|...
4|69186|Premier que atende & Events, Inc.|1255 22 St|S.F.|São Francisco|0,814806|...

Na definição de conjunto de dados resultante, todas as diferentes variações de representação de "San Francisco" nos dados foram normalizadas para a mesma cadeia de caracteres, "San Francisco".

Salve essa etapa de agrupamento difuso na definição mais recente do conjunto `update_definition()`de informações com.

```Python
dataset = dataset.update_definition(ds_def, 'fuzzy grouping')
```

## <a name="next-steps"></a>Passos seguintes

* Consulte o [tutorial](tutorial-auto-train-models.md) de aprendizado de máquina automatizado para um exemplo de modelo de regressão.

* Consulte a [visão geral](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) do SDK para padrões de design e exemplos de uso.

* Para obter um exemplo de como usar conjuntos de valores, consulte os [blocos de anotações de exemplo](https://aka.ms/dataset-tutorial).
