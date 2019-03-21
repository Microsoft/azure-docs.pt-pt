---
title: 'Escrita: o SDK de Python de preparação de dados'
titleSuffix: Azure Machine Learning service
description: Saiba mais sobre a escrita de dados com o SDK do Azure Machine Learning Data Prep. Pode escrever os dados em qualquer ponto num fluxo de dados e aos ficheiros em qualquer um dos nossos localizações suportadas (sistema de arquivos local, armazenamento de Blobs do Azure e armazenamento do Azure Data Lake).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: cforbe
author: cforbe
manager: cgronlun
ms.reviewer: jmartens
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 5cad83c6b8ca11fe45a2b29dc115c340d6e16361
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58014640"
---
# <a name="write-and-configure-data-using-azure-machine-learning"></a>Escrever e configurar os dados com o Azure Machine Learning

Neste artigo, vai aprender diferentes métodos para escrever dados com o [SDK Python do Azure Machine Learning Data Prep](https://aka.ms/data-prep-sdk) e como configurar esses dados para a experimentação com o [do Azure Machine Learning SDK para Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).  Dados de saída podem ser escritos em qualquer um fluxo de dados. Escritas são adicionadas como passos ao fluxo de dados resultante e estes passos são executados sempre que os dados de execuções de fluxo. Dados são gravados para vários ficheiros de partição para permitir que escritas paralelas.

Uma vez que existem sem limites de quantos escrever passos existem num pipeline, pode adicionar facilmente passos adicionais de escrita para obter os resultados intermediários para resolução de problemas ou para outros pipelines.

Sempre que executar uma etapa de escrita, ocorre uma solicitação completa dos dados no fluxo de dados. Por exemplo, um fluxo de dados com três passos de escrita irá ler e processar todos os registos no conjunto de dados três vezes.

## <a name="supported-data-types-and-location"></a>Tipos de dados suportados e localização

São suportados os seguintes formatos de arquivo
-   Ficheiros delimitados (CSV, TSV, etc.)
-   Ficheiros parquet

Utilizar o SDK de Python de preparação de dados do Azure Machine Learning, pode escrever dados para:
+ um sistema de ficheiros local
+ Armazenamento de Blobs do Azure
+ Armazenamento do Azure Data Lake

## <a name="spark-considerations"></a>Considerações sobre o Spark

Ao executar um fluxo de dados no Spark, deve escrever para uma pasta vazia. Tentar executar uma gravação para um resulta de pasta existente numa falha. Certificar-se de que sua pasta de destino está vazia ou utilizar uma localização de destino diferente para cada execução, ou a gravação irá falhar.

## <a name="monitoring-write-operations"></a>Monitorização de operações de escrita

Para sua comodidade, um arquivo de sentinel com êxito o nome é gerado quando uma gravação for concluída. Sua presença ajuda a identificar quando uma gravação de intermediária for concluído sem ter de esperar por todo o pipeline concluir.

## <a name="example-write-code"></a>Código de escrita de exemplo

Neste exemplo, comece por carregar dados para um fluxo de dados com `auto_read_file()`. Reutilizar esses dados com diferentes formatos.

```python
import azureml.dataprep as dprep
t = dprep.auto_read_file('./data/fixed_width_file.txt')
t = t.to_number('Column3')
t.head(5)
```

Exemplo de saída:

| | Column1 | Column2 | Column3 | Column4 | Column5 | Column6 | Column7 | Column8 | Column9 |
| -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- |
|0| 10000.0 | 99999.0 | Nenhuma | NO | NO | ENRS | NaN | NaN | NaN |   
|1| 10003.0 | 99999.0 | Nenhuma | NO | NO | ENSO | NaN | NaN | NaN |   
|2| 10010.0 | 99999.0 | Nenhuma | NO | JN | ENJA | 70933.0 | -8667.0 | 90,0 |
|3| 10013.0 | 99999.0 | Nenhuma | NO | NO |      | NaN | NaN | NaN |
|4| 10014.0 | 99999.0 | Nenhuma | NO | NO | ENSO | 59783.0 | 5350.0 |  500.0|

### <a name="delimited-file-example"></a>Exemplo de ficheiro delimitado

O seguinte código utiliza a [ `write_to_csv()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#write-to-csv-directory-path--destinationpath--separator--str--------na--str----na---error--str----error------azureml-dataprep-api-dataflow-dataflow) função para escrever dados num arquivo delimitado.

```python
# Create a new data flow using `write_to_csv` 
write_t = t.write_to_csv(directory_path=dprep.LocalFileOutput('./test_out/'))

# Run the data flow to begin the write operation.
write_t.run_local()

written_files = dprep.read_csv('./test_out/part-*')
written_files.head(5)
```

Exemplo de saída:

| | Column1 | Column2 | Column3 | Column4 | Column5 | Column6 | Column7 | Column8 | Column9 |
| -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- |
|0| 10000.0 | 99999.0 | ERRO | NO | NO | ENRS | NaN    | NaN | NaN |   
|1| 10003.0 | 99999.0 | ERRO | NO | NO | ENSO |    NaN | NaN | NaN |   
|2| 10010.0 | 99999.0 | ERRO | NO | JN | ENJA |    70933.0 | -8667.0 | 90,0 |
|3| 10013.0 | 99999.0 | ERRO | NO | NO |     | NaN | NaN | NaN |
|4| 10014.0 | 99999.0 | ERRO | NO | NO | ENSO |    59783.0 | 5350.0 |  500.0|

Na saída do anterior, vários erros aparecem nas colunas numéricas por causa de números que não foram analisados corretamente. Quando escritos para CSV, os valores nulos são substituídos com a cadeia de caracteres "ERROR" por predefinição.

Adicione os parâmetros como parte da sua escrita chamarem e especifique uma cadeia de caracteres a utilizar para representar valores nulos.

```python
write_t = t.write_to_csv(directory_path=dprep.LocalFileOutput('./test_out/'), 
                         error='BadData',
                         na='NA')
write_t.run_local()
written_files = dprep.read_csv('./test_out/part-*')
written_files.head(5)
```

O código anterior produz esta saída:

| | Column1 | Column2 | Column3 | Column4 | Column5 | Column6 | Column7 | Column8 | Column9 |
| -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- |
|0| 10000.0 | 99999.0 | BadData | NO | NO | ENRS | NaN  | NaN | NaN |   
|1| 10003.0 | 99999.0 | BadData | NO | NO | ENSO |  NaN | NaN | NaN |   
|2| 10010.0 | 99999.0 | BadData | NO | JN | ENJA |  70933.0 | -8667.0 | 90,0 |
|3| 10013.0 | 99999.0 | BadData | NO | NO |   | NaN | NaN | NaN |
|4| 10014.0 | 99999.0 | BadData | NO | NO | ENSO |  59783.0 | 5350.0 |  500.0|

### <a name="parquet-file-example"></a>Exemplo de ficheiro parquet

Semelhante à `write_to_csv()`, o [ `write_to_parquet()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#write-to-parquet-file-path--typing-union--destinationpath--nonetype----none--directory-path--typing-union--destinationpath--nonetype----none--single-file--bool---false--error--str----error---row-groups--int---0-----azureml-dataprep-api-dataflow-dataflow) função devolve um novo fluxo de dados com um passo de Parquet, que é executado quando as execuções de fluxo de dados de escrita.

```python
write_parquet_t = t.write_to_parquet(directory_path=dprep.LocalFileOutput('./test_parquet_out/'),
error='MiscreantData')
```

Execute o fluxo de dados para iniciar a operação de escrita.

```python
write_parquet_t.run_local()

written_parquet_files = dprep.read_parquet_file('./test_parquet_out/part-*')
written_parquet_files.head(5)
```

O código anterior produz esta saída:

|   | Column1 | Column2 | Column3 | Column4 | Column5 | Column6 | Column7 | Column8 | Column9 |
| -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- |-------- |
|0| 10000.0 | 99999.0 | MiscreantData | NO | NO | ENRS | MiscreantData | MiscreantData | MiscreantData |
|1| 10003.0 | 99999.0 | MiscreantData | NO | NO | ENSO | MiscreantData | MiscreantData | MiscreantData |   
|2| 10010.0 | 99999.0 | MiscreantData | NO| JN| ENJA|   70933.0|    -8667.0 |90,0|
|3| 10013.0 | 99999.0 | MiscreantData | NO| NO| |   MiscreantData|    MiscreantData|    MiscreantData|
|4| 10014.0 | 99999.0 | MiscreantData | NO| NO| ENSO|   59783.0|    5350.0| 500.0|

## <a name="configure-data-for-automated-machine-learning-training"></a>Configurar dados de treinamento de aprendizado de máquina automatizada

Passar o ficheiro de dados recentemente gravadas num [ `AutoMLConfig` ](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py#automlconfig) objeto em preparação para a formação de aprendizado de máquina automatizada. 

O exemplo de código seguinte ilustra como converter o seu fluxo de dados para um dataframe Pandas e dividi-lo posteriormente, para conjuntos de dados de preparação e teste para treinamento de aprendizado de máquina automatizada.

```Python
from azureml.train.automl import AutoMLConfig
from sklearn.model_selection import train_test_split

dflow = dprep.auto_read_file(path="")
X_dflow = dflow.keep_columns([feature_1,feature_2, feature_3])
y_dflow = dflow.keep_columns("target")

X_df = X_dflow.to_pandas_dataframe()
y_df = y_dflow.to_pandas_dataframe()

X_train, X_test, y_train, y_test = train_test_split(X_df, y_df, test_size=0.2, random_state=223)

# flatten y_train to 1d array
y_train.values.flatten()

#configure 
automated_ml_config = AutoMLConfig(task = 'regression',
                               X = X_train.values,  
                   y = y_train.values.flatten(),
                   iterations = 30,
                       Primary_metric = "AUC_weighted",
                       n_cross_validation = 5
                       )

```

Se não necessitar de quaisquer passos de preparação de dados intermediários como no exemplo anterior, pode passar o seu fluxo de dados diretamente no `AutoMLConfig`.

```Python
automated_ml_config = AutoMLConfig(task = 'regression', 
                   X = X_dflow,   
                   y = y_dflow, 
                   iterations = 30, 
                   Primary_metric = "AUC_weighted",
                   n_cross_validation = 5
                   )
```

## <a name="next-steps"></a>Passos Seguintes
* Consulte o SDK [descrição geral](https://aka.ms/data-prep-sdk) para padrões de design e exemplos de utilização 
* Consulte o automatizada de machine learning [tutorial](tutorial-auto-train-models.md) para obter um exemplo de modelo de regressão