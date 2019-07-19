---
title: 'Transformações: SDK do Python de preparação de dados'
titleSuffix: Azure Machine Learning service
description: Saiba mais sobre a transformar e limpeza de dados com o SDK do Azure Machine Learning Data Prep. Utilize métodos de transformação para adicionar colunas, filtrar indesejadas linhas ou colunas e impute valores em falta.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: jmartens
ms.date: 07/16/2019
ms.custom: seodec18
ms.openlocfilehash: 6c5d60bb51a96725f766c6b49d61ac20fb2a1b58
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/17/2019
ms.locfileid: "68297909"
---
# <a name="transform-data-with-the-azure-machine-learning-data-prep-sdk"></a>Transformar dados com o SDK de preparação de dados do Azure Machine Learning

Neste artigo, você aprenderá métodos diferentes para transformar dados usando o `azureml-dataprep` pacote. O pacote oferece funções que tornam simples a adição de colunas, o filtro de linhas ou colunas indesejadas e a imputar de valores ausentes. Consulte a documentação de referência completa para o [pacote azureml-dataprep](https://aka.ms/data-prep-sdk).

> [!Important]
> Se você estiver criando uma nova solução, experimente os [conjuntos](how-to-explore-prepare-data.md) de dados Azure Machine Learning (versão prévia) para transformar seus dados, fazer instantâneos e armazenar as definições de conjunto de dados com controle de versão. Conjuntos de dados é a próxima versão do data Prep SDK, oferecendo funcionalidade expandida para o gerenciamento de conjuntos de dados em soluções de ia. Se você usar o `azureml-dataprep` pacote para criar um fluxo de os com suas transformações em vez `azureml-datasets` de usar o pacote para criar um conjunto de um, não será possível usar instantâneos ou conjuntos de valores com controle de versão mais tarde.

Este "como" mostra exemplos para as seguintes tarefas:

- Adicionar coluna utilização de uma expressão
- [Impute valores em falta](#impute-missing-values)
- [Derivar coluna por exemplo](#derive-column-by-example)
- [Filtragem](#filtering)
- [Transformações de Python personalizadas](#custom-python-transforms)

## <a name="add-column-using-an-expression"></a>Adicionar coluna utilização de uma expressão

Inclui o SDK do Azure Machine Learning Data Prep `substring` expressões, pode utilizar para calcular um valor de colunas existentes e, em seguida, colocar esse valor numa nova coluna. Neste exemplo, carregar dados e tente adicionar colunas a esses dados de entrada.

```Python
import azureml.dataprep as dprep

# loading data
dflow = dprep.read_csv(path=r'data\crime0-10.csv')
dflow.head(3)
```

||ID|Número de caso|Date|Bloquear|IUCR|Tipo de principal|Descrição|Descrição de localização|Prisão|Nacionais|...|Ward|Área de Comunidade|Código do FBI|Coordenada x|Coordenada Y|Ano|Atualizado em|Latitude|Longitude|Localização|
|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|
|0|10140490|HY329907|05/07/2015 50: 11:00 PM|050XX N NEWLAND AVE|0820|ROUBO|US $500 E, EM|RUA|false|false|...|41|10|06|1129230|1933315|2015|12/07/2015 42:46 12:00 DE|41.973309466|-87.800174996|(41.973309466,-87.800174996)|
|1|10139776|HY329265|05/07/2015 30: 11:00 PM|011XX W MORSE AVE|0460|BATERIA|SIMPLES|RUA|false|true|...|49|1|08B|1167370|1946271|2015|12/07/2015 42:46 12:00 DE|42.008124017|-87.65955018|(42.008124017,-87.65955018)|
|2|10140270|HY329253|05/07/2015 20: 11:00 PM|121XX S FRONT AVE|0486|BATERIA|BATERIA NACIONAL SIMPLE|RUA|false|true|...|9|53|08B|||2015|12/07/2015 42:46 12:00 DE|


Utilize o `substring(start, length)` expressão para extrair o prefixo da coluna de número de caso e colocar essa cadeia de caracteres numa nova coluna, `Case Category`. Passando o `substring_expression` variável para o `expression` parâmetro cria uma nova coluna calculada que executa a expressão em cada registo.

```Python
substring_expression = dprep.col('Case Number').substring(0, 2)
case_category = dflow.add_column(new_column_name='Case Category',
                                    prior_column='Case Number',
                                    expression=substring_expression)
case_category.head(3)
```

||ID|Número de caso|Categoria de caso|Date|Bloquear|IUCR|Tipo de principal|Descrição|Descrição de localização|Prisão|Nacionais|...|Ward|Área de Comunidade|Código do FBI|Coordenada x|Coordenada Y|Ano|Atualizado em|Latitude|Longitude|Localização|
|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|------|
|0|10140490|HY329907|HY|05/07/2015 50: 11:00 PM|050XX N NEWLAND AVE|0820|ROUBO|US $500 E, EM|RUA|false|false|...|41|10|06|1129230|1933315|2015|12/07/2015 42:46 12:00 DE|41.973309466|-87.800174996|(41.973309466,-87.800174996)|
|1|10139776|HY329265|HY|05/07/2015 30: 11:00 PM|011XX W MORSE AVE|0460|BATERIA|SIMPLES|RUA|false|true|...|49|1|08B|1167370|1946271|2015|12/07/2015 42:46 12:00 DE|42.008124017|-87.65955018|(42.008124017,-87.65955018)|
|2|10140270|HY329253|HY|05/07/2015 20: 11:00 PM|121XX S FRONT AVE|0486|BATERIA|BATERIA NACIONAL SIMPLE|RUA|false|true|...|9|53|08B|||2015|12/07/2015 42:46 12:00 DE|


Utilize o `substring(start)` expressão para extrair apenas o número da coluna de número de caso e criar uma nova coluna. Convertê-lo para um tipo de dados numéricos com o `to_number()` funcionar e passar o nome da coluna de cadeia de caracteres como um parâmetro.

```Python
substring_expression2 = dprep.col('Case Number').substring(2)
case_id = dflow.add_column(new_column_name='Case Id',
                              prior_column='Case Number',
                              expression=substring_expression2)
case_id = case_id.to_number('Case Id')
```

## <a name="impute-missing-values"></a>Impute valores em falta

O SDK pode impute valores em falta nas colunas especificadas. Neste exemplo, valores de latitude e longitude de carga e, em seguida, tente impute valores em falta nos dados de entrada.

```python
import azureml.dataprep as dprep

# loading input data
dflow = dprep.read_csv(r'data\crime0-10.csv')
dflow = dflow.keep_columns(['ID', 'Arrest', 'Latitude', 'Longitude'])
dflow = dflow.to_number(['Latitude', 'Longitude'])
dflow.head(3)
```

||ID|Prisão|Latitude|Longitude|
|-----|------|-----|------|-----|
|0|10140490|false|41.973309|-87.800175|
|1|10139776|false|42.008124|-87.659550|
|2|10140270|false|NaN|NaN|

O registo de terceiro está em falta valores de latitude e longitude. Para imputar esses valores ausentes, use [`ImputeMissingValuesBuilder`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.imputemissingvaluesbuilder?view=azure-dataprep-py) para aprender uma expressão fixa. Ele pode impute as colunas com qualquer um de um calculado `MIN`, `MAX`, `MEAN` valor, ou um `CUSTOM` valor. Quando `group_by_columns` for especificado, valores em falta vão ser imputed ao grupo com `MIN`, `MAX`, e `MEAN` calculado por grupo.

Verifique o `MEAN` valor da coluna latitude usando a [`summarize()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#summarize-summary-columns--typing-union-typing-list-azureml-dataprep-api-dataflow-summarycolumnsvalue---nonetype----none--group-by-columns--typing-union-typing-list-str---nonetype----none--join-back--bool---false--join-back-columns-prefix--typing-union-str--nonetype----none-----azureml-dataprep-api-dataflow-dataflow) função. Esta função aceita uma matriz de colunas no `group_by_columns` parâmetro para especificar o nível de agregação. O `summary_columns` parâmetro aceita um `SummaryColumnsValue` chamar. Esta chamada de função Especifica o nome da coluna atual, o novo nome de campo calculado e o `SummaryFunction` para executar.

```python
dflow_mean = dflow.summarize(group_by_columns=['Arrest'],
                       summary_columns=[dprep.SummaryColumnsValue(column_id='Latitude',
                                                                 summary_column_name='Latitude_MEAN',
                                                                 summary_function=dprep.SummaryFunction.MEAN)])
dflow_mean = dflow_mean.filter(dprep.col('Arrest') == 'false')
dflow_mean.head(1)
```

||Prisão|Latitude_MEAN|
|-----|-----|----|
|0|false|41.878961|

O `MEAN` valor de latitudes parece preciso, utilize o `ImputeColumnArguments` função para impute-lo. Esta função aceita um `column_id` cadeia de caracteres e um `ReplaceValueFunction` para especificar o tipo de impute. Para o valor de longitude em falta, impute-lo com 42 com base nos dados de conhecimento externos.

Impute passos podem ser encadeados num `ImputeMissingValuesBuilder` objeto, usando a função de construtor `impute_missing_values()`. O `impute_columns` parâmetro aceita uma matriz de `ImputeColumnArguments` objetos. Chamar o `learn()` funcionar para armazenar os passos de impute e, em seguida, aplicar a um objeto de fluxo de dados utilizando `to_dataflow()`.

```python
# impute with MEAN
impute_mean = dprep.ImputeColumnArguments(column_id='Latitude',
                                          impute_function=dprep.ReplaceValueFunction.MEAN)
# impute with custom value 42
impute_custom = dprep.ImputeColumnArguments(column_id='Longitude',
                                            custom_impute_value=42)
# get instance of ImputeMissingValuesBuilder
impute_builder = dflow.builders.impute_missing_values(impute_columns=[impute_mean, impute_custom],
                                                   group_by_columns=['Arrest'])

impute_builder.learn()
dflow_imputed = impute_builder.to_dataflow()
dflow_imputed.head(3)
```

||ID|Prisão|Latitude|Longitude|
|-----|------|-----|------|-----|
|0|10140490|false|41.973309|-87.800175|
|1|10139776|false|42.008124|-87.659550|
|2|10140270|false|41.878961|42.000000|

Conforme mostrado no resultado acima, a latitude em falta foi imputed com o `MEAN` valor de `Arrest=='false'` grupo. A longitude em falta foi imputed com 42.

```python
imputed_longitude = dflow_imputed.to_pandas_dataframe()['Longitude'][2]
assert imputed_longitude == 42
```

## <a name="derive-column-by-example"></a>Derivar coluna por exemplo

Uma das ferramentas mais avançadas no SDK de Prep de dados do Azure Machine Learning é a capacidade de derivar de colunas com exemplos de resultados desejados. Isto permite-lhe dar um exemplo SDK para que ele pode gerar o código para alcançar a transformação pretendida.

```python
import azureml.dataprep as dprep
dflow = dprep.read_csv(path='https://dpreptestfiles.blob.core.windows.net/testfiles/BostonWeather.csv')
dflow.head(4)
```

||DATA|REPORTTPYE|HOURLYDRYBULBTEMPF|HOURLYRelativeHumidity|HOURLYWindSpeed|
|----|----|----|----|----|----|
|0|1/1/2015 0:54|FM-15|22|50|10|
|1|1/1/2015 1:00|FM-12|22|50|10|
|2|1/1/2015 1 minuto e 54|FM-15|22|50|10|
|3|1/1/2015 2 minutos e 54|FM-15|22|50|11|

Partem do princípio de que precisa de associar este ficheiro com um conjunto de dados em que a data e hora são em formato de "10 de Março de 2018 | 2AM -4AM '.

```python
builder = dflow.builders.derive_column_by_example(source_columns=['DATE'], new_column_name='date_timerange')
builder.add_example(source_data=dflow.iloc[1], example_value='Jan 1, 2015 12AM-2AM')
builder.preview(count=5) 
```

||DATA|date_timerange|
|----|----|----|
|0|1/1/2015 0:54|1 de Janeiro de 2015 12AM -2AM|
|1|1/1/2015 1:00|1 de Janeiro de 2015 12AM -2AM|
|2|1/1/2015 1 minuto e 54|1 de Janeiro de 2015 12AM -2AM|
|3|1/1/2015 2 minutos e 54|1 de Janeiro de 2015 2AM -4AM|
|4|1/1/2015 3 minutos e 54|1 de Janeiro de 2015 2AM -4AM|

O código acima cria primeiro um construtor para a coluna derivada. Fornece uma matriz de colunas de origem a ter em consideração (`DATE`) e um nome para a nova coluna a adicionar. Como o primeiro exemplo, passar na segunda linha (índice 1) e fornecer um valor esperado para a coluna derivada.

Finalmente, chama `builder.preview(skip=30, count=5)` e pode ver a coluna derivada junto a coluna de origem. O formato parece correto, mas apenas verá valores para a mesma data "1 de Janeiro de 2015".

Agora, passar o número de linhas que quer `skip` da parte superior para ver as linhas mais abaixo.

> [!NOTE]
> A função Preview () ignora as linhas, mas não renumera o índice de saída. No exemplo a seguir, o índice 0 na tabela corresponde ao índice 30 no fluxo de os.

```python
builder.preview(skip=30, count=5)
```

||DATE|date_timerange|
|-----|-----|-----|
|0|1/1/2015 22 minutos e 54|1 de Janeiro de 2015 10 PM - 12 AM|
|1|1/1/2015 23 minutos e 54|1 de Janeiro de 2015 10 PM - 12 AM|
|2|1/1/2015 23:59|1 de Janeiro de 2015 10 PM - 12 AM|
|3|1/2/2015 0:54|1 de Fevereiro de 2015 12AM -2AM|
|4|1/2/2015 1:00|1 de Fevereiro de 2015 12AM -2AM|

Aqui vê um problema com o programa gerado. Com base apenas num dos exemplos fornecidos acima, o programa de derivar optou por analisar a data como "Dia/mês/ano", que é que não o que deseja nesse caso. Para corrigir esse problema, direcione um índice de registro específico e forneça outro exemplo usando `add_example()` a função `builder` na variável.

```python
builder.add_example(source_data=dflow.iloc[3], example_value='Jan 2, 2015 12AM-2AM')
builder.preview(skip=30, count=5)
```

||DATE|date_timerange|
|-----|-----|-----|
|0|1/1/2015 22 minutos e 54|1 de Janeiro de 2015 10 PM - 12 AM|
|1|1/1/2015 23 minutos e 54|1 de Janeiro de 2015 10 PM - 12 AM|
|2|1/1/2015 23:59|1 de Janeiro de 2015 10 PM - 12 AM|
|3|1/2/2015 0:54|2 de Janeiro de 2015 12AM -2AM|
|4|1/2/2015 1:00|2 de Janeiro de 2015 12AM -2AM|

Agora, as linhas lidam corretamente com ' 1/2/2015 ' como ' 2 de janeiro de 2015 ', mas se você olhar além do índice 76 da coluna derivada, verá que os valores no final não têm nada na coluna derivada.

```python
builder.preview(skip=75, count=5)
```


||DATE|date_timerange|
|-----|-----|-----|
|0|1/3/2015 7:00|3 de janeiro de 2015 6h – 8:00|
|1|1/3/2015 7:54|3 de janeiro de 2015 6h – 8:00|
|2|1/29/2015 6:54|Nenhum|
|3|1/29/2015 7:00|Nenhuma|
|4|1/29/2015 7:54|Nenhuma|

```python
builder.add_example(source_data=dflow.iloc[77], example_value='Jan 29, 2015 6AM-8AM')
builder.preview(skip=75, count=5)
```

||DATE|date_timerange|
|-----|-----|-----|
|0|1/3/2015 7:00|3 de janeiro de 2015 6h – 8:00|
|1|1/3/2015 7:54|3 de janeiro de 2015 6h – 8:00|
|2|1/29/2015 6:54|29 de janeiro de 2015 6h – 8:00|
|3|1/29/2015 7:00|29 de janeiro de 2015 6h – 8:00|
|4|1/29/2015 7:54|29 de janeiro de 2015 6h – 8:00|

 Para ver uma lista da chamada `list_examples()` de derivações de exemplo atual no objeto Builder.

```python
examples = builder.list_examples()
```

| |DATE|Exemplo|example_id|
| -------- | -------- | -------- | -------- |
|0|1/1/2015 1:00|1 de Janeiro de 2015 12AM -2AM|-1|
|1|1/2/2015 0:54|2 de Janeiro de 2015 12AM -2AM|-2|
|2|1/29/2015 20 minutos e 54|29 de Janeiro de 2015 8 PM - 10 PM|-3|


Em determinados casos, se você quiser excluir exemplos incorretos, poderá passar `example_row` do valor ou `example_id` do dataframe do pandas. Por exemplo, se você executar `builder.delete_example(example_id=-1)`, ele excluirá o primeiro exemplo de transformação.


Chame `to_dataflow()` no construtor, que retorna um fluxo de dados com as colunas derivadas desejadas adicionadas.

```python
dflow = builder.to_dataflow()
df = dflow.to_pandas_dataframe()
```

## <a name="filtering"></a>Filtragem

O SDK inclui os métodos [`drop_columns()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#drop-columns-columns--multicolumnselection-----azureml-dataprep-api-dataflow-dataflow) e [`filter()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py) para permitir que você filtre colunas ou linhas.

### <a name="initial-setup"></a>Configuração inicial

> [!Note]
> A URL neste mesmo exemplo não é uma URL completa. Em vez disso, ele se refere à pasta demo no BLOB. A URL completa para os dados é https://dprepdata.blob.core.windows.net/demo/green-small/green_tripdata_2013-08.csv

O que estamos fazendo no tutorial é carregar todos os arquivos dentro da pasta e agregar o resultado em green_df_raw e yellow_df_raw.

```python
import azureml.dataprep as dprep
from datetime import datetime
dflow = dprep.read_csv(path='https://dprepdata.blob.core.windows.net/demo/green-small/*')
dflow.head(5)
```

||lpep_pickup_datetime|Lpep_dropoff_datetime|Store_and_fwd_flag|RateCodeID|Pickup_longitude|Pickup_latitude|Dropoff_longitude|Dropoff_latitude|Passenger_count|Trip_distance|Tip_amount|Tolls_amount|Total_amount|
|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|
|0|Nenhuma|Nenhum|Nenhum|Nenhum|Nenhum|Nenhum|Nenhum|Nenhum|Nenhum|Nenhum|Nenhum|Nenhum|Nenhuma|
|1|2013-08-01 08:14:37|2013-08-01 09:09:06|N|1|0|0|0|0|1|,00|0|0|21.25|
|2|2013-08-01 09:00 13:|2013-08-01 38: UTC+11:00|N|1|0|0|0|0|2|,00|0|0|75|
|3|2013-08-01 09:00 48:|2013-08-01 09:00 49:|N|5|0|0|0|0|1|,00|0|1|2.1|
|4|2013-08-01 10:38:35|2013-08-01 10:38:51|N|1|0|0|0|0|1|,00|0|0|3.25|

### <a name="filtering-columns"></a>Filtragem de colunas

Para filtrar colunas, utilize `drop_columns()`. Esse método usa uma lista de colunas a serem descartadas ou um argumento [`ColumnSelector`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.columnselector?view=azure-dataprep-py)mais complexo chamado.

#### <a name="filtering-columns-with-list-of-strings"></a>Filtragem de colunas com a lista de cadeias de caracteres

Neste exemplo, `drop_columns()` utiliza uma lista de cadeias de caracteres. Cada cadeia de caracteres deve ser exatamente igual a coluna pretendida para remover.

```python
dflow = dflow.drop_columns(['Store_and_fwd_flag', 'RateCodeID'])
dflow.head(2)
```

||lpep_pickup_datetime|Lpep_dropoff_datetime|Pickup_longitude|Pickup_latitude|Dropoff_longitude|Dropoff_latitude|Passenger_count|Trip_distance|Tip_amount|Tolls_amount|Total_amount|
|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|
|0|Nenhuma|Nenhum|Nenhum|Nenhum|Nenhum|Nenhum|Nenhum|Nenhum|Nenhum|Nenhum|Nenhuma|
|1|2013-08-01 08:14:37|2013-08-01 09:09:06|0|0|0|0|1|,00|0|0|21.25|

#### <a name="filtering-columns-with-regex"></a>Filtragem de colunas com regex

Em alternativa, utilize o `ColumnSelector` expressão remover colunas que correspondem a uma expressão regex. Neste exemplo, que a elimine todas as colunas que correspondem à expressão `Column*|.*longitude|.*latitude`.

```python
dflow = dflow.drop_columns(dprep.ColumnSelector('Column*|.*longitud|.*latitude', True, True))
dflow.head(2)
```

||lpep_pickup_datetime|Lpep_dropoff_datetime|Passenger_count|Trip_distance|Tip_amount|Tolls_amount|Total_amount|
|-----|-----|-----|-----|-----|-----|-----|-----|
|0|Nenhuma|Nenhum|Nenhum|Nenhum|Nenhum|Nenhum|Nenhuma|
|1|2013-08-01 08:14:37|2013-08-01 09:09:06|1|,00|0|0|21.25|

## <a name="filtering-rows"></a>Filtragem de linhas

Para filtrar linhas, utilize `filter()`. Este método utiliza uma expressão de SDK do Azure Machine Learning Data Prep como um argumento e devolve um novo fluxo de dados com as linhas que a expressão é avaliada como True. As expressões são criadas através dos construtores de expressões (`col`, `f_not`, `f_and`, `f_or`) e operadores regulares (>, <>, =, < =, = =,! =).

### <a name="filtering-rows-with-simple-expressions"></a>Filtrar as linhas com expressões simples

Utilizar o construtor de expressões `col`, especifique o nome da coluna como argumento de cadeia de caracteres `col('column_name')`. Utilize esta expressão em combinação com um dos seguintes operadores padrão >, <>, =, < =, = =,! = para criar uma expressão como `col('Tip_amount') > 0`. Finalmente, passa a expressão incorporada no `filter()` função.

Neste exemplo, `dflow.filter(col('Tip_amount') > 0)` retorna um novo fluxo de dados com as linhas em que o valor de `Tip_amount` for maior que 0.

> [!NOTE] 
> `Tip_amount` em primeiro lugar é convertido em numérico, o que nos permite criar uma expressão compará-lo em relação a outros valores numéricos.

```python
dflow = dflow.to_number(['Tip_amount'])
dflow = dflow.filter(dprep.col('Tip_amount') > 0)
dflow.head(2)
```

||lpep_pickup_datetime|Lpep_dropoff_datetime|Passenger_count|Trip_distance|Tip_amount|Tolls_amount|Total_amount|
|-----|-----|-----|-----|-----|-----|-----|-----|
|0|2013-08-01 19:33:28|2013-08-01 19:35:21|5|,00|0,08|0|4.58|
|1|2013-08-05 13:16:38|2013-08-05 13:18:24|1|,00|0,30|0|3.8|

### <a name="filtering-rows-with-complex-expressions"></a>Filtrar as linhas com expressões complexas

Para filtrar usando expressões complexas, combinar uma ou mais expressões simples com dos construtores de expressões `f_not`, `f_and`, ou `f_or`.

Neste exemplo, `dflow.filter()` retorna um novo fluxo de dados com as linhas em que `'Passenger_count'` é inferior a 5 e `'Tolls_amount'` for maior que 0.

```python
dflow = dflow.to_number(['Passenger_count', 'Tolls_amount'])
dflow = dflow.filter(dprep.f_and(dprep.col('Passenger_count') < 5, dprep.col('Tolls_amount') > 0))
dflow.head(2)
```

||lpep_pickup_datetime|Lpep_dropoff_datetime|Passenger_count|Trip_distance|Tip_amount|Tolls_amount|Total_amount|
|-----|-----|-----|-----|-----|-----|-----|-----|
|0|2013-08-08 12:00 16:|2013-08-08 12:00 16:|1.0|,00|2.25|5,00|19.75|
|1|2013-08-12 14:43:53|2013-08-12 15:04:50|1.0|5.28|6.46|5.33|32.29|

Também é possível filtrar linhas combinar mais do que um construtor de expressões para criar uma expressão aninhada.

> [!NOTE]
> `lpep_pickup_datetime` e `Lpep_dropoff_datetime` primeiro são convertidos em datetime, que nos permite criar uma expressão compará-lo em relação a outros valores de datetime.

```python
dflow = dflow.to_datetime(['lpep_pickup_datetime', 'Lpep_dropoff_datetime'], ['%Y-%m-%d %H:%M:%S'])
dflow = dflow.to_number(['Total_amount', 'Trip_distance'])
mid_2013 = datetime(2013,7,1)
dflow = dflow.filter(
    dprep.f_and(
        dprep.f_or(
            dprep.col('lpep_pickup_datetime') > mid_2013,
            dprep.col('Lpep_dropoff_datetime') > mid_2013),
        dprep.f_and(
            dprep.col('Total_amount') > 40,
            dprep.col('Trip_distance') < 10)))
dflow.head(2)
```

||lpep_pickup_datetime|Lpep_dropoff_datetime|Passenger_count|Trip_distance|Tip_amount|Tolls_amount|Total_amount|
|-----|-----|-----|-----|-----|-----|-----|-----|
|0|2013-08-13 06:11:06 + 00:00|2013-08-13 GMT+06:30: 28 + 00:00|1.0|9.57|7.47|5.33|44.80|
|1|2013-08-23 12:28: mais de 20 00:00|2013-08-23 12:50:28 + 00:00|2.0|8.22|8.08|5.33|40.41|

## <a name="custom-python-transforms"></a>Transformações de Python personalizadas

Sempre existirão cenários quando a opção mais fácil para fazer uma transformação é escrever seu próprio script. O SDK fornece três pontos de extensão que pode utilizar para scripts personalizados do Python.

- Nova coluna de script
- Novo filtro de script
- Transformar a partição

Cada uma das extensões é suportada o tempo de execução vertical e horizontal. Das principais vantagens de usar esses pontos de extensão é que não é necessário extrair todos os dados para criar um quadro de dados. Seu código será executado apenas de Python personalizado como outras transformações, à escala, pela partição e, normalmente em paralelo.

### <a name="initial-data-preparation"></a>Preparação de dados inicial

Comece por carregar alguns dados do Blob do Azure.

```python
import azureml.dataprep as dprep
col = dprep.col

dflow = dprep.read_csv(path='https://dpreptestfiles.blob.core.windows.net/testfiles/read_csv_duplicate_headers.csv', skip_rows=1)
dflow.head(2)
```

| |stnam|fipst|leaid|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|------|
|0|ALABAMA|1|101710|CONDADO Hale|10171002158| |
|1|ALABAMA|1|101710|CONDADO Hale|10171002162| |

Reduza o conjunto de dados e faça algumas transformações básicas, incluindo a remoção de colunas, a substituição de valores e a conversão de tipos.

```python
dflow = dflow.keep_columns(['stnam', 'leanm10', 'ncessch', 'MAM_MTH00numvalid_1011'])
dflow = dflow.replace_na(columns=['leanm10', 'MAM_MTH00numvalid_1011'], custom_na_list='.')
dflow = dflow.to_number(['ncessch', 'MAM_MTH00numvalid_1011'])
dflow.head(2)
```

| |stnam|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|
|0|ALABAMA|CONDADO Hale|1.017100e + 10|Nenhuma|
|1|ALABAMA|CONDADO Hale|1.017100e + 10|Nenhuma|

Procure valores nulos, usando o seguinte filtro.

```python
dflow.filter(col('MAM_MTH00numvalid_1011').is_null()).head(2)
```

| |stnam|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|
|0|ALABAMA|CONDADO Hale|1.017100e + 10|Nenhuma|
|1|ALABAMA|CONDADO Hale|1.017100e + 10|Nenhuma|

### <a name="transform-partition"></a>Transformar a partição

Use [`transform_partition()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#transform-partition-script--str-----azureml-dataprep-api-dataflow-dataflow) para substituir todos os valores nulos por 0. Esse código será executado por partição, não no conjunto completo de dados de uma só vez. Isso significa que num conjunto de dados grandes, esse código pode executar em paralelo conforme o tempo de execução processa os dados, a partição por partição.

O script de Python tem de definir uma função chamada `transform()` que aceita dois argumentos, `df` e `index`. O `df` argumento será um pandas dataframe que contém os dados para a partição e o `index` argumento é um identificador exclusivo da partição. A função de transformação totalmente pode editar o pacote de dados transmitido, mas tem de devolver um dataframe. Quaisquer bibliotecas que importa o script de Python tem de existir no ambiente em que o fluxo de dados é executado.

```python
df = df.transform_partition("""
def transform(df, index):
    df['MAM_MTH00numvalid_1011'].fillna(0,inplace=True)
    return df
""")
df.head(2)
```

||stnam|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|
|0|ALABAMA|CONDADO Hale|1.017100e + 10|0.0|
|1|ALABAMA|CONDADO Hale|1.017100e + 10|0.0|

### <a name="new-script-column"></a>Nova coluna de script

Você pode usar um script Python para criar uma nova coluna que tem o nome do município e o nome do estado, e também para colocar em maiúscula o nome do estado. Para fazer isso, use o [`new_script_column()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#new-script-column-new-column-name--str--insert-after--str--script--str-----azureml-dataprep-api-dataflow-dataflow) método no fluxo de dados.

O script de Python tem de definir uma função chamada `newvalue()` que assume um argumento único `row`. O `row` argumento é um dict (`key`: nome da coluna, `val`: valor atual) e serão passados para essa função para cada linha no conjunto de dados. Esta função tem de devolver um valor a ser utilizado na nova coluna. Quaisquer bibliotecas que importa o script de Python tem de existir no ambiente em que o fluxo de dados é executado.

```python
dflow = dflow.new_script_column(new_column_name='county_state', insert_after='leanm10', script="""
def newvalue(row):
    return row['leanm10'] + ', ' + row['stnam'].title()
""")
dflow.head(2)
```

||stnam|leanm10|county_state|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|
|0|ALABAMA|CONDADO Hale|CONDADO Hale, Alabama|1.017100e + 10|0.0|
|1|ALABAMA|CONDADO Hale|CONDADO Hale, Alabama|1.017100e + 10|0.0|

### <a name="new-script-filter"></a>Novo filtro de Script

Crie uma expressão Python usando [`new_script_filter()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#new-script-filter-script--str-----azureml-dataprep-api-dataflow-dataflow) para filtrar o conjunto de dados somente para linhas em que ' Hale ' não esteja na `county_state` nova coluna. A expressão devolve `True` para manter a linha, e `False` para remover a linha.

```python
dflow = dflow.new_script_filter("""
def includerow(row):
    val = row['county_state']
    return 'Hale' not in val
""")
dflow.head(2)
```

||stnam|leanm10|county_state|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|
|0|ALABAMA|CONDADO Jefferson|CONDADO Jefferson, Alabama|1.019200e + 10|1.0|
|1|ALABAMA|CONDADO Jefferson|CONDADO Jefferson, Alabama|1.019200e + 10|0.0|

## <a name="next-steps"></a>Passos Seguintes

* Consulte o [tutorial](tutorial-data-prep.md) Azure Machine Learning data Prep SDK para obter um exemplo de como resolver um cenário específico
