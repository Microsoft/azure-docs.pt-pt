---
title: Explore e preparar dados (classe do conjunto de dados)
titleSuffix: Azure Machine Learning service
description: Explorar dados através de estatísticas de resumo e preparar dados através de limpezas de dados, transformação e engenharia de funcionalidades
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 05/02/19
ms.openlocfilehash: f9087d1fda7574043879983e31d7b608dbe58798
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65204977"
---
# <a name="explore-and-prepare-data-with-the-dataset-class-preview"></a>Explore e preparar dados com a classe de conjunto de dados (pré-visualização)

Saiba como a exploração e preparar dados com o [SDK do Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py). O [conjunto de dados](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py) classe (pré-visualização) permite-lhe explorar e prepare os seus dados, fornecendo funções, tais como: amostragem, estatísticas de resumo e transformações inteligentes. Passos de transformação são guardados num [definições do conjunto de dados](how-to-manage-dataset-definitions.md) com a capacidade de lidar com vários arquivos grandes com esquemas diferentes de uma maneira altamente escalonável.

> [!Important]
> Algumas classes de conjunto de dados (pré-visualização) têm dependências no SDK de preparação de dados (GA). Embora as funções de transformação podem ser feitas diretamente com o GA'ed [as funções do SDK de preparação de dados](how-to-transform-data.md), recomendamos que os invólucros de pacote do conjunto de dados descritos neste artigo, se estiver a criar uma nova solução. O Azure Machine Learning conjuntos de dados (pré-visualização) permitem que não só transformar os seus dados, mas também [dados de instantâneos](how-to-create-dataset-snapshots.md) e armazenar [definições do conjunto de dados com a versão](how-to-manage-dataset-definitions.md). Conjuntos de dados é a próxima versão do SDK do preparação de dados, oferecendo funcionalidade expandida para o gerenciamento de soluções de ia de conjuntos de dados.

## <a name="prerequisites"></a>Pré-requisitos

Para explorar e prepare os seus dados, terá de:

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma conta gratuita antes de começar. Experimente o [uma versão gratuita ou paga do serviço Azure Machine Learning](https://aka.ms/AMLFree) hoje mesmo.

* Uma área de trabalho de serviço do Azure Machine Learning. Ver [criar uma área de trabalho do serviço do Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/setup-create-workspace).

* O Azure Machine Learning SDK para Python (versão 1.0.21 ou posterior). Para instalar ou atualizar para a versão mais recente do SDK, consulte [instalar ou atualizar o SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

* Preparação de dados para o Azure Machine Learning SDK. Para instalar ou atualizar para a versão mais recente, consulte [instalar ou atualizar o SDK de preparação de dados](https://docs.microsoft.com/python/api/overview/azure/dataprep/intro?view=azure-dataprep-py#install).

* Transferir os ficheiros de exemplo a seguir, juntamente com os exemplos: [crime.csv](https://dprepdata.blob.core.windows.net/dataset-sample-files/crime.csv) e [city.json](https://dprepdata.blob.core.windows.net/dataset-sample-files/city.json).

## <a name="sampling"></a>Amostragem

Dê um exemplo dos seus dados para obter uma compreensão inicial da sua arquitetura de dados e conteúdo. Neste momento, o [ `sample()` ](https://docs.microsoft.com//python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py#sample-sample-strategy--arguments-) estratégias de amostragem de Top N simples aleatório e Stratified suporta o método da classe do conjunto de dados.

```Python
from azureml.core.dataset import Dataset
import random

# create an in-memory Dataset from a local file
dataset = Dataset.auto_read_files('./data/crime.csv')

# create seed for Simple Random and Stratified sampling
seed = random.randint(0, 4294967295)
```

### <a name="top-n-sample"></a>Exemplo de principais N

Amostragem de Top N, os primeiros n registos do seu conjunto de dados são seu exemplo. Isso é útil se estiver a tentar apenas ter uma idéia de que sua aparência de registos de dados, como ou para ver quais campos são nos seus dados.

```Python
top_n_sample_dataset = dataset.sample('top_n', {'n': 5})
top_n_sample_dataset.to_pandas_dataframe()
```

||ID|Número de caso|Date|Bloquear|IUCR|Tipo de principal|...|
-|--|-----------|----|-----|----|------------|---
0|10498554|HZ239907|4/4/2016 23:56|007XX E 111TH ST|1153|ENGANOSA PRÁTICA|...
1|10516598|HZ258664|4/15/2016 17:00|082XX S MARSHFIELD AVE|890|ROUBO|...
2|10519196|HZ261252|4/15/2016 10:00|104XX S SACRAMENTO AVE|1154|ENGANOSA PRÁTICA|...
3|10519591|HZ261534|4/15/2016 9:00|113XX S PRAIRIE AVE|1120|ENGANOSA PRÁTICA|...
4|10534446|HZ277630|4/15/2016 10:00|055XX N KEDZIE AVE|890|ROUBO|...

### <a name="simple-random-sample"></a>Amostra aleatória Simple

Numa amostragem aleatória simples, cada membro da população de dados tem hipóteses de que está a ser selecionada como uma parte do exemplo. Na `simple_random` estratégia de exemplo, os registos do seu conjunto de dados são selecionada com base na probabilidade especificada e devolve um conjunto de dados modificado. O parâmetro de seed é opcional.

```Python
simple_random_sample_dataset = dataset.sample('simple_random', {'probability':0.3, 'seed': seed})
simple_random_sample_dataset.to_pandas_dataframe()
```

||ID|Número de caso|Date|Bloquear|IUCR|Tipo de principal|...|
-|--|-----------|----|-----|----|------------|---
0|10516598|HZ258664|4/15/2016 17:00|082XX S MARSHFIELD AVE|890|ROUBO|...
1|10519196|HZ261252|4/15/2016 10:00|104XX S SACRAMENTO AVE|1154|ENGANOSA PRÁTICA|...
2|10534446|HZ277630|4/15/2016 10:00|055XX N KEDZIE AVE|890|ROUBO|...

### <a name="stratified-sample"></a>Exemplo stratified

Exemplos de stratified Certifique-se de que determinados grupos de uma população são representados no exemplo. Na `stratified` estratégia de amostragem, a população é dividida em strata ou subgrupos, com base nas similaridades, e registos são selecionados aleatoriamente entre cada strata, de acordo com os pesos de strata indicados pelo `fractions` parâmetro.

No exemplo a seguir, que cada registo de grupo pelas colunas especificadas e inclui o registo disse com base nas informações de peso strata X no `fractions`. Se não for especificado um strata ou o registo não pode ser agrupado, o peso de predefinido a amostra é 0.

```Python
# take 50% of records with `Primary Type` as `THEFT` and 20% of records with `Primary Type` as `DECEPTIVE PRACTICE` into sample Dataset
fractions = {}
fractions[('THEFT',)] = 0.5
fractions[('DECEPTIVE PRACTICE',)] = 0.2

sample_dataset = dataset.sample('stratified', {'columns': ['Primary Type'], 'fractions': fractions, 'seed': seed})

sample_dataset.to_pandas_dataframe()
```

||ID|Número de caso|Date|Bloquear|IUCR|Tipo de principal|...|
-|--|-----------|----|-----|----|------------|---
0|10516598|HZ258664|4/15/2016 17:00|082XX S MARSHFIELD AVE|890|ROUBO|...
1|10534446|HZ277630|4/15/2016 10:00|055XX N KEDZIE AVE|890|ROUBO|...
2|10535059|HZ278872|4/15/2016 4:30|004XX S KILBOURN AVE|810|ROUBO|...

## <a name="explore-with-summary-statistics"></a>Explore com estatísticas de resumo

 Detetar anomalias, falta de valores, ou contagem de erros com o [ `get_profile()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#get-profile-arguments-none--generate-if-not-exist-true--workspace-none--compute-target-none-) método. Essa função obtém o perfil e estatísticas de resumo dos seus dados, que por sua vez ajuda a determinam as operações de preparação de dados necessários para aplicar.

```Python
dataset.get_profile()
```

||Type|Mín.|Máx.|Contagem|Contagem de em falta|Não tem em falta contagem|Percentagem em falta|Contagem de erros|Contagem de vazia|0,1% Quantile|1% Quantile|5% Quantile|25% Quantile|50% Quantile|75% Quantile|95% Quantile|99% Quantile|99,9% Quantile|média|Desvio padrão|Variância|Assimetrias|Kurtosis
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
Código do FBI|FieldType.INTEGER|6|11|10.0|0.0|10.0|0.0|0.0|0.0|6|6|6|6|11|11|11|11|11|9.4|2.36643|5.6|-0.702685|-1.59582
Coordenada X|FieldType.INTEGER|1.16309e + 06|1.18336e + 06|10.0|7.0|3.0|0.7|0.0|0.0|1.16309e + 06|1.16309e + 06|1.16309e + 06|1.16401e + 06|1.16678e + 06|1.17921e + 06|1.18336e + 06|1.18336e + 06|1.18336e + 06|1.17108e + 06|10793.5|1.165e + 08|0.335126|-2.33333
Coordenada Y|FieldType.INTEGER|1.8315e + 06|1.908e + 06|10.0|7.0|3.0|0.7|0.0|0.0|1.8315e + 06|1.8315e + 06|1.8315e + 06|1.83614e + 06|1.85005e + 06|1.89352e + 06|1.908e + 06|1.908e + 06|1.908e + 06|1.86319e + 06|39905.2|1.59243e + 09|0.293465|-2.33333
Ano|FieldType.INTEGER|2016|2016|10.0|0.0|10.0|0.0|0.0|0.0|2016|2016|2016|2016|2016|2016|2016|2016|2016|2016|0|0|NaN|NaN
Atualizado em|FieldType.DATE|2016-05-11 15:48:00+00:00|2016-05-27 15:45:00+00:00|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
Latitude|FieldType.DECIMAL|41.6928|41.9032|10.0|7.0|3.0|0.7|0.0|0.0|41.6928|41.6928|41.6928|41.7057|41.7441|41.8634|41.9032|41.9032|41.9032|41.78|0.109695|0.012033|0.292478|-2.33333
Longitude|FieldType.DECIMAL|-87.6764|-87.6043|10.0|7.0|3.0|0.7|0.0|0.0|-87.6764|-87.6764|-87.6764|-87.6734|-87.6645|-87.6194|-87.6043|-87.6043|-87.6043|-87.6484|0.0386264|0.001492|0.344429|-2.33333
Location|FieldType.STRING||(41.903206037, -87.676361925)|10.0|0.0|10.0|0.0|0.0|7.0||||||||||||||

## <a name="impute-missing-values"></a>Impute valores em falta

Nos conjuntos de dados, os valores nulos, do NaN e valores que não contêm nenhum conteúdo são considerados valores em falta. Estes podem afetar o desempenho dos modelos de aprendizagem ou levar a conclusões inválidos. Imputation é uma abordagem comum para abordar os valores em falta e é útil quando tem uma alta porcentagem de em falta valor registos que simplesmente não pode eliminar.

O perfil de conjunto de dados gerados na secção anterior, Vemos que `Latitude` e `Longitude` colunas têm uma alta porcentagem de valores em falta. Neste exemplo, vamos calcular a média e impute valores em falta para essas duas colunas.

Primeiro, obtenha a definição mais recentes do conjunto de dados com [ `get_definition()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#get-definition-version-id-none-) e diminuir os dados com [ `keep_columns()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#keep-columns-columns--multicolumnselection-----azureml-dataprep-api-dataflow-dataflow), por isso, podemos ver apenas as colunas que queremos para endereço.

```Python
from azureml.core.dataset import Dataset
import azureml.dataprep as dprep

# get the latest definition of Dataset
ds_def = dataset.get_definition()

# keep useful columns for this example
ds_def = ds_def.keep_columns(['ID', 'Arrest', 'Latitude', 'Longitude'])
ds_def.head(3)
```

||ID|Prisão| Latitude|Longitude|
-|---------|-----|---------|----------|
|0|10498554|Falso|41.692834|-87.604319|
|1|10516598|Falso| 41.744107 |-87.664494|
|2|10519196|Falso| NaN|NaN|

Em seguida, verifique os `MEAN` valor de uso de coluna de latitude a [ `summarize()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#summarize-summary-columns--typing-union-typing-list-azureml-dataprep-api-dataflow-summarycolumnsvalue---nonetype----none--group-by-columns--typing-union-typing-list-str---nonetype----none--join-back--bool---false--join-back-columns-prefix--typing-union-str--nonetype----none-----azureml-dataprep-api-dataflow-dataflow) função. Esta função aceita uma matriz de colunas no `group_by_columns` parâmetro para especificar o nível de agregação. O `summary_columns` parâmetro aceita o `SummaryColumnsValue` função, que especifica o nome da coluna atual, o novo nome de campo calculado, e o `SummaryFunction` para executar.

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
|0|Falso|41.780049|

Assim que podemos verificar os valores para impute, utilize [ `ImputeMissingValuesBuilder` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.imputemissingvaluesbuilder?view=azure-dataprep-py) para obter uma expressão fixa que imputes as colunas com qualquer um de um calculada `MIN`, `MAX`, `MEAN` valor, ou um `CUSTOM` valor. Quando `group_by_columns` for especificado, valores em falta vão ser imputed ao grupo com `MIN`, `MAX`, e `MEAN` calculado por grupo.

O [ `ImputeColumnArguments` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.imputecolumnarguments?view=azure-dataprep-pyfunction) aceita uma cadeia de caracteres column_id e um `ReplaceValueFunction` para especificar o tipo de impute. Para o valor de longitude em falta, impute-lo com-87 com base nos dados de conhecimento externos.

```Python
# impute with MEAN
impute_mean = dprep.ImputeColumnArguments(column_id = 'Latitude',
                                          impute_function = dprep.ReplaceValueFunction.MEAN)

# impute with custom value -87
impute_custom = dprep.ImputeColumnArguments(column_id='Longitude',
                                            custom_impute_value=-87)
```

Impute passos podem ser encadeados num `ImputeMissingValuesBuilder` objeto usando o [ `Builders` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.builders?view=azure-dataprep-py) classe função [ `impute_missing_values()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.builders?view=azure-dataprep-py#impute-missing-values-impute-columns--typing-list-azureml-dataprep-api-builders-imputecolumnarguments----none--group-by-columns--typing-union-typing-list-str---nonetype----none-----azureml-dataprep-api-builders-imputemissingvaluesbuilder). O `impute_columns` parâmetro aceita uma matriz de `ImputeColumnArguments` objetos.

```Python
# get instance of ImputeMissingValuesBuilder
impute_builder = ds_def.builders.impute_missing_values(impute_columns=[impute_mean, impute_custom],
                                                   group_by_columns=['Arrest'])
```

Chamar o [ `learn()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.imputemissingvaluesbuilder?view=azure-dataprep-py#learn------none) funcionar para armazenar os passos de impute e aplicá-las a um objeto de fluxo de dados utilizando [ `to_dataflow()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.imputemissingvaluesbuilder?view=azure-dataprep-py#to-dataflow------azureml-dataprep-api-dataflow-dataflow).

```Python
impute_builder.learn()
ds_def = impute_builder.to_dataflow()
ds_def.head(3)
```

Conforme mostrado na seguinte tabela de saída, a latitude em falta foi imputed com o `MEAN` valor de `Arrest==False` grupo e a longitude em falta foi imputed com-87.

||ID|Prisão|Latitude|Longitude
-|---------|-----|---------|----------
0|10498554|Falso|41.692834|-87.604319
1|10516598|Falso|41.744107|-87.664494
2|10519196|Falso|41.780049|-87.000000

Atualizar a definição do conjunto de dados, [ `update_definition()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py#update-definition-definition--definition-update-message-) para manter os passos de transformação executada.

```Python
dataset = dataset.update_definition(ds_def, 'Impute Missing')
dataset.head(3)
```

||ID|Prisão|Latitude|Longitude
-|---------|-----|---------|----------
0|10498554|Falso|41.692834|-87.604319
1|10516598|Falso|41.744107|-87.664494
2|10519196|Falso|41.780049|-87.000000

## <a name="create-assertion-rules"></a>Criar regras de asserção

Com freqüência, os dados de nós Trabalhamos com durante a limpeza e preparação de dados é apenas um subconjunto do total de dados que precisamos para produção. Como resultado, algumas das suposições que fazemos como parte do nosso processo de limpeza podem se tornar falsa. Por exemplo, num conjunto de dados que atualiza continuamente, uma coluna que originalmente continha apenas números dentro de um determinado intervalo pode conter uma vasta gama de valores em execuções posteriores. Estes erros, muitas vezes, resultam em pipelines interrompidas ou dados incorretos.

Suporte de conjuntos de dados, criação de asserções sobre os dados, sendo estas avaliadas conforme o pipeline executa. Essas declarações permitem-na verificar que nosso pressupostos relacionados com os dados continuam a ser precisos e, quando não, para lidar com falhas em conformidade.

Por exemplo, se quiser restringir `Latitude` e `Longitude` valores no conjunto de dados a intervalos numéricos específicos, o [ `assert_value()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#assert-value-columns--multicolumnselection--expression--azureml-dataprep-api-expressions-expression--policy--azureml-dataprep-api-engineapi-typedefinitions-assertpolicy----assertpolicy-errorvalue--1---error-code--str----assertionfailed------azureml-dataprep-api-dataflow-dataflow) método garante que sempre é esse o caso.

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
ID|FieldType.INTEGER|1.04986e + 07|1.05351e + 07|10.0|0.0|10.0|0.0|0.0|0.0|1.04986e + 07|1.04992e + 07|1.04986e + 07|1.05166e + 07|1.05209e + 07|1.05259e + 07|1.05351e + 07|1.05351e + 07|1.05351e + 07|1.05195e + 07|12302.7|1.51358e + 08|-0.495701|-1.02814
Prisão|FieldType.BOOLEAN|Falso|Falso|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
Latitude|FieldType.DECIMAL|41.6928|41.9032|10.0|0.0|10.0|0.0|0.0|0.0|41.6928|41.7185|41.6928|41.78|41.78|41.78|41.9032|41.9032|41.9032|41.78|0.0517107|0.002674|0.837593|1,05
Longitude|FieldType.INTEGER|-87|-87|10.0|0.0|10.0|0.0|3.0|0.0|-87|-87|-87|-87|-87|-87|-87|-87|-87|-87|0|0|NaN|NaN

O perfil, verá que o `Error Count` para o `Longitude` coluna é 3. O código a seguir filtra o conjunto de dados, obtém o erro e vê o valor que o faz com que a asserção falha. A partir daqui, ajuste o seu código e limpar os dados adequadamente.

```Python
from azureml.dataprep import col

ds_error = ds_def.filter(col('Longitude').is_error())
error = ds_error.head(10)['Longitude'][0]

print(error.originalValue)
```

    -87.60431945

## <a name="derive-columns-by-example"></a>Derivar de colunas por exemplo

Uma das ferramentas mais avançadas para conjuntos de dados é a capacidade de derivar de colunas com exemplos de resultados desejados. Isto permite-lhe dar um exemplo, o SDK para que ele pode gerar o código para alcançar as transformações pretendidas.

```Python
from azureml.core.dataset import Dataset

# create an in-memory Dataset from a local file
dataset = Dataset.auto_read_files('./data/crime.csv')
dataset.head(3)
```

||ID|Número de caso|Date|Bloquear|...|
-|---------|-----|---------|----|---
0|10498554|HZ239907|2016-04-04 23:56:00|007XX E 111TH ST|...
1|10516598|HZ258664|2016-04-15 17:00:00|082XX S MARSHFIELD AVE|...
2|10519196|HZ261252|2016-04-15 10:00:00|104XX S SACRAMENTO AVE|...

Digamos que precisa transformar o formato de data e hora para ' 2016-04-04 10 PM - 12 AM ". Na [ `derive_column_by_example()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#derive-column-by-example-source-columns--sourcecolumns--new-column-name--str--example-data--exampledata-----azureml-dataprep-api-dataflow-dataflow) argumento, fornecer exemplos de sua saída desejado no `example_data` parâmetro neste formato: *(saída original, saída desejada)*.

O código a seguir fornece dois exemplos de saída desejado, ("2016-04-04 23:56:00", "2016-04-04 10 PM-12 AM") e ("2016 a 04-15 17:00:00", "2016 a 04-15 PM de 4-6 PM")

```Python
ds_def = dataset.get_definition()
ds_def = ds_def.derive_column_by_example(
        source_columns = "Date",
        new_column_name = "Date_Time_Range",
        example_data = [("2016-04-04 23:56:00", "2016-04-04 10PM-12AM"), ("2016-04-15 17:00:00", "2016-04-15 4PM-6PM")]
    )
ds_def.keep_columns(['ID','Date','Date_Time_Range']).head(3)
```

Na tabela a seguir, tenha em atenção que uma nova coluna, Date_Time_Range contém registos no formato especificado.

||ID|Date|Date_Time_Range
-|--------|-----|----
0|10498554|2016-04-04 23:56:00|2016-04-04 10 PM-12 AM
1|10516598|2016-04-15 17:00:00|2016 A 04-15 PM DE 4-6 PM
2|10519196|2016-04-15 10:00:00|2016 A 04-15 10 AM - 12 PM

```Python
# update Dataset definition to keep the transformation steps performed.
dataset = dataset.update_definition(ds_def, 'Derive Date_Time_Range')
```

## <a name="fuzzy-groupings"></a>Agrupamentos difusas

Quando colete os dados de origens diferentes que poderá encontrar variações na ortografia, uso ou abreviaturas das mesmas entidades. Padronizar e reconciliar essas variantes com a funcionalidade de agrupamento difusa ou texto clustering, o SDK automaticamente.

Por exemplo, a coluna `inspections.business.city` contém várias formas do nome de cidade "Francisco San".

```Python
from azureml.core.dataset import Dataset

# create an in-memory Dataset from a local json file
dataset = Dataset.auto_read_files('./data/city.json')
dataset.head(5)
```

||inspections.business.business_id|inspections.business_name|inspections.business.address|inspections.business.city|...|
-|-----|-------------------------|------------|--|---
0|16162|Foods indianos do guia de introdução-N-Ezee|3861 24th St|SF|...
1|67565|Rei de Cafe macarrão tailandês|1541 TARAVAL St|SAN FRANCISCO|...
2|67565|Rei de Cafe macarrão tailandês|1541 TARAVAL St|SAN FRANCISCO|...
3|68701|Grindz|832 clement St|SF|...
4|69186|Fornecendo Premier & de eventos, Inc.|1255 22nd St|S.F.|...

Vamos utilizar o [ `fuzzy_group_column()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#fuzzy-group-column-source-column--str--new-column-name--str--similarity-threshold--float---0-8--similarity-score-column-name--str---none-----azureml-dataprep-api-dataflow-dataflow) método para adicionar uma coluna com a forma canónica detetada automaticamente de "San Francisco". Os argumentos `source_column` e `new_column_name` são necessários. Também tem a opção de:

* Criar uma nova coluna, `similarity_score_column_name`, que mostra a pontuação de semelhança entre cada par de valores originais e canónicos.

* Fornecer um `similarity_threshold`, que é a pontuação de semelhança mínima para os valores ser agrupados em conjunto.

```Python
# get the latest Dataset definition
ds_def = dataset.get_definition()
ds_def = ds_def.fuzzy_group_column(source_column='inspections.business.city',
                                       new_column_name='city_grouped',
                                       similarity_threshold=0.8,
                                       similarity_score_column_name='similarity_score')
ds_def.head(5)
```

||inspections.business.business_id|inspections.business_name|inspections.business.address|inspections.business.city|city_grouped|similarity_score|...|
-|-----|-------------------------|------------|--|---|---|---
0|16162|Foods indianos do guia de introdução-N-Ezee|3861 24th St|SF|San Francisco|0.814806|...
1|67565|Rei de Cafe macarrão tailandês|1541 TARAVAL St|SAN FRANCISCO|San Francisco|1.000000|...
2|67565|Rei de Cafe macarrão tailandês|1541 TARAVAL St|SAN FRANCISCO|San Francisco|1.000000|...
3|68701|Grindz|832 clement St|SF|San Francisco|0.814806|...
4|69186|Fornecendo Premier & de eventos, Inc.|1255 22nd St|S.F.|San Francisco|0.814806|...

Na definição do conjunto de dados resultante, todas as variações diferentes de representar "San Francisco" nos dados foram normalizadas para a mesma cadeia, "San Francisco".

Guardar este passo de agrupamento difusa para a definição de conjunto de dados mais recentes com `update_definition()`.

```Python
dataset = dataset.update_definition(ds_def, 'fuzzy grouping')
```

## <a name="next-steps"></a>Passos Seguintes

* [Gerir o ciclo de vida de definições do conjunto de dados de](how-to-manage-dataset-definitions.md).

* Consulte o automatizada de machine learning [tutorial](tutorial-auto-train-models.md) para obter um exemplo de modelo de regressão.

* Consulte o SDK [descrição geral](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) para padrões de design e exemplos de utilização.

* Para obter um exemplo da utilização de conjuntos de dados, consulte a [blocos de notas de exemplo](https://aka.ms/dataset-tutorial).