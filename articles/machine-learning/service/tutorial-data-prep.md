---
title: 'Tutorial do modelo de regressão: Preparar dados'
titleSuffix: Azure Machine Learning service
description: A primeira parte deste tutorial, saiba como preparar dados no Python para regressão de modelagem com o SDK do Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: sihhu
ms.author: MayMSFT
ms.reviewer: trbye
ms.date: 02/04/2019
ms.custom: seodec18
ms.openlocfilehash: ca49cb65331373832b00b8489d731d51e8a8e004
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58182919"
---
# <a name="tutorial-prepare-data-for-regression-modeling"></a>Tutorial: Preparar dados para a modelação de regressão

Neste tutorial, irá aprender a preparar dados para regressão de modelagem, utilizando o [do Azure Machine Learning dados de preparação do SDK para Python](https://aka.ms/data-prep-sdk). Executar várias transformações para filtrar e combinar os dois conjuntos de dados de táxis NYC diferentes.

Este tutorial é a **primeira parte de uma série composta por duas partes**. Depois de concluir a série de tutoriais, pode prever o custo de uma viagem de táxis ao preparar um modelo sobre os recursos de dados. Esses recursos incluem o dia de recolha e tempo, o número de passageiros e o local de recebimento.

Neste tutorial:

> [!div class="checklist"]
> * Configurar um ambiente de Python e importe pacotes.
> * Carregar dois conjuntos de dados com nomes de campos diferentes.
> * Limpar dados para remover a anomalias.
> * Transforme dados com transformações inteligentes para criar novos recursos.
> * Guarde o seu objeto de fluxo de dados para usar num modelo de regressão.

## <a name="prerequisites"></a>Pré-requisitos

Avance para o [configurar o ambiente de desenvolvimento](#start) para ler os passos de bloco de notas ou utilize as instruções abaixo para obter o bloco de notas e executá-lo em blocos de notas do Azure ou no seu próprio servidor de bloco de notas. Para executar o bloco de notas, terá de:

* Um servidor de bloco de notas do Python 3.6 com o seguinte instalado:
    * O Azure Machine Learning a preparação de dados SDK para Python
* O bloco de notas do tutorial

Obter todos os estes pré-requisitos a partir de qualquer uma das seções abaixo.

* Utilize [blocos de notas do Azure](#azure)
* Utilize [seu próprio servidor de bloco de notas](#server)

### <a name="azure"></a>Utilize blocos de notas do Azure: Blocos de notas do Jupyter gratuitos na cloud

É fácil começar com blocos de notas do Azure! O SDK do Azure Machine Learning Data Prep já está instalado e configurado para si no [blocos de notas do Azure](https://notebooks.azure.com/). A instalação e as futuras atualizações são geridas automaticamente por meio de serviços do Azure.

Depois de concluir os passos abaixo, execute o **tutoriais/regressão-part1-data-prep.ipynb** bloco de notas no seu **introdução** projeto.

[!INCLUDE [aml-azure-notebooks](../../../includes/aml-azure-notebooks.md)]

### <a name="server"></a>Utilizar o seu próprio servidor de bloco de notas do Jupyter

Utilize estes passos para criar um servidor de bloco de notas Jupyter local no seu computador.  Depois de concluir os passos, execute o **tutoriais/regressão-part1-data-prep.ipynb** bloco de notas.

1. Concluir o [guia de início rápido do Azure Machine Learning Python](quickstart-create-workspace-with-python.md) para criar um ambiente de Miniconda.  Fique à vontade ignorar a **criar uma área de trabalho** secção se desejar, mas irá precisar dele para [parte 2](tutorial-auto-train-models.md) desta série de tutoriais.
1. Instalar o SDK de preparação de dados no seu ambiente utilizando `pip install azureml-dataprep`.
1. Clone o [repositório do GitHub](https://aka.ms/aml-notebooks).

    ```
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Inicie o servidor de blocos de notas a partir do diretório clonado.

    ```shell
    jupyter notebook

## <a name="start"></a>Set up your development environment

All the setup for your development work can be accomplished in a Python notebook. Setup includes the following actions:

* Install the SDK
* Import Python packages

### Install and import packages

Use the following to install necessary packages if you don't already have them.

```shell
pip install azureml-dataprep
```

Importe o SDK.

```python
import azureml.dataprep as dprep
```

## <a name="load-data"></a>Carregar dados

Baixe os dois conjuntos de dados de táxis NYC diferentes em objetos de fluxo de dados. Os conjuntos de dados têm campos de um pouco diferentes. O `auto_read_file()` método reconhece automaticamente o tipo de ficheiro de entrada.

```python
from IPython.display import display
dataset_root = "https://dprepdata.blob.core.windows.net/demo"

green_path = "/".join([dataset_root, "green-small/*"])
yellow_path = "/".join([dataset_root, "yellow-small/*"])

green_df_raw = dprep.read_csv(path=green_path, header=dprep.PromoteHeadersMode.GROUPED)
# auto_read_file automatically identifies and parses the file type, which is useful when you don't know the file type.
yellow_df_raw = dprep.auto_read_file(path=yellow_path)

display(green_df_raw.head(5))
display(yellow_df_raw.head(5))
```

A `Dataflow` objeto é semelhante a um pacote de dados e representa uma série de operações imutáveis, ociosamente avaliada nos dados. Operações podem ser adicionadas ao invocar a transformação diferente e filtragem de métodos disponíveis. O resultado da adição de uma operação para um `Dataflow` sempre é uma nova `Dataflow` objeto.

## <a name="cleanse-data"></a>Limpar dados

Agora preenche algumas variáveis com transformações de atalho para aplicar a todos os fluxos de dados. O `drop_if_all_null` variável é utilizada para eliminar registos em que todos os campos são nulos. O `useful_columns` variável contém uma matriz de descrições das colunas que são mantidas em cada fluxo de dados.

```python
all_columns = dprep.ColumnSelector(term=".*", use_regex=True)
drop_if_all_null = [all_columns, dprep.ColumnRelationship(dprep.ColumnRelationship.ALL)]
useful_columns = [
    "cost", "distance", "dropoff_datetime", "dropoff_latitude", "dropoff_longitude",
    "passengers", "pickup_datetime", "pickup_latitude", "pickup_longitude", "store_forward", "vendor"
]
```

Em primeiro lugar, trabalhar com os dados de táxis verde para obtê-la para um formato válido que pode ser combinado com os dados de táxis amarelo. Chamar o `replace_na()`, `drop_nulls()`, e `keep_columns()` funções utilizando o atalho transformar variáveis que criou. Além disso, mudar o nome de todas as colunas no pacote de dados de acordo com os nomes no `useful_columns` variável.


```python
green_df = (green_df_raw
    .replace_na(columns=all_columns)
    .drop_nulls(*drop_if_all_null)
    .rename_columns(column_pairs={
        "VendorID": "vendor",
        "lpep_pickup_datetime": "pickup_datetime",
        "Lpep_dropoff_datetime": "dropoff_datetime",
        "lpep_dropoff_datetime": "dropoff_datetime",
        "Store_and_fwd_flag": "store_forward",
        "store_and_fwd_flag": "store_forward",
        "Pickup_longitude": "pickup_longitude",
        "Pickup_latitude": "pickup_latitude",
        "Dropoff_longitude": "dropoff_longitude",
        "Dropoff_latitude": "dropoff_latitude",
        "Passenger_count": "passengers",
        "Fare_amount": "cost",
        "Trip_distance": "distance"
     })
    .keep_columns(columns=useful_columns))
green_df.head(5)
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
      <th>Fornecedor</th>
      <th>pickup_datetime</th>
      <th>dropoff_datetime</th>
      <th>store_forward</th>
      <th>pickup_longitude</th>
      <th>pickup_latitude</th>
      <th>dropoff_longitude</th>
      <th>dropoff_latitude</th>
      <th>passageiros</th>
      <th>distância</th>
      <th>custo</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2</td>
      <td>2013-08-01 08:14:37</td>
      <td>2013-08-01 09:09:06</td>
      <td>N</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>,00</td>
      <td>21.25</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>2013-08-01 09:00 13:</td>
      <td>2013-08-01 38: UTC+11:00</td>
      <td>N</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>2</td>
      <td>,00</td>
      <td>74.5</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>2013-08-01 09:00 48:</td>
      <td>2013-08-01 09:00 49:</td>
      <td>N</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>,00</td>
      <td>1</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2</td>
      <td>2013-08-01 10:38:35</td>
      <td>2013-08-01 10:38:51</td>
      <td>N</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>,00</td>
      <td>3.25</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2</td>
      <td>2013-08-01 11:51:45</td>
      <td>2013-08-01 12:03:52</td>
      <td>N</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>,00</td>
      <td>8.5</td>
    </tr>
  </tbody>
</table>
</div>

Execute os mesmos passos de transformação nos dados de táxis amarelo. Estas funções Certifique-se de que os dados nulos são removidos do conjunto de dados, o que irá ajudar a aumentar a aprendizagem precisão do modelo.

```python
yellow_df = (yellow_df_raw
    .replace_na(columns=all_columns)
    .drop_nulls(*drop_if_all_null)
    .rename_columns(column_pairs={
        "vendor_name": "vendor",
        "VendorID": "vendor",
        "vendor_id": "vendor",
        "Trip_Pickup_DateTime": "pickup_datetime",
        "tpep_pickup_datetime": "pickup_datetime",
        "Trip_Dropoff_DateTime": "dropoff_datetime",
        "tpep_dropoff_datetime": "dropoff_datetime",
        "store_and_forward": "store_forward",
        "store_and_fwd_flag": "store_forward",
        "Start_Lon": "pickup_longitude",
        "Start_Lat": "pickup_latitude",
        "End_Lon": "dropoff_longitude",
        "End_Lat": "dropoff_latitude",
        "Passenger_Count": "passengers",
        "passenger_count": "passengers",
        "Fare_Amt": "cost",
        "fare_amount": "cost",
        "Trip_Distance": "distance",
        "trip_distance": "distance"
    })
    .keep_columns(columns=useful_columns))
yellow_df.head(5)
```

Chamar o `append_rows()` função aos dados de táxis verde para acrescentar os dados de táxis amarelo. É criado um novo pacote de dados combinado.

```python
combined_df = green_df.append_rows([yellow_df])
```

### <a name="convert-types-and-filter"></a>Converter tipos e filtro

Examine a recolha e entrega coordena as estatísticas de resumo para ver como os dados são distribuídos. Em primeiro lugar, defina um `TypeConverter` objeto para alterar os campos de latitude e longitude para o tipo decimal. Em seguida, chame o `keep_columns()` função para restringir a saída para apenas os campos de latitude e longitude e, em seguida, chame o `get_profile()` função. Essas chamadas de função criar uma exibição condensada do fluxo de dados para mostrar apenas os campos de lat e longos, o que torna mais fácil avaliar em falta ou coordenadas de fora do escopo.


```python
decimal_type = dprep.TypeConverter(data_type=dprep.FieldType.DECIMAL)
combined_df = combined_df.set_column_types(type_conversions={
    "pickup_longitude": decimal_type,
    "pickup_latitude": decimal_type,
    "dropoff_longitude": decimal_type,
    "dropoff_latitude": decimal_type
})
combined_df.keep_columns(columns=[
    "pickup_longitude", "pickup_latitude",
    "dropoff_longitude", "dropoff_latitude"
]).get_profile()
```




<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Type</th>
      <th>Mín.</th>
      <th>Máx.</th>
      <th>Contagem</th>
      <th>Contagem de em falta</th>
      <th>Não tem em falta contagem</th>
      <th>Percentagem em falta</th>
      <th>Contagem de erros</th>
      <th>Contagem de vazia</th>
      <th>0,1% Quantile</th>
      <th>1% Quantile</th>
      <th>5% Quantile</th>
      <th>25% Quantile</th>
      <th>50% Quantile</th>
      <th>75% Quantile</th>
      <th>95% Quantile</th>
      <th>99% Quantile</th>
      <th>99,9% Quantile</th>
      <th>Desvio padrão</th>
      <th>média</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>pickup_longitude</th>
      <td>FieldType.DECIMAL</td>
      <td>-115.179337</td>
      <td>0.000000</td>
      <td>7722.0</td>
      <td>0.0</td>
      <td>7722.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>-88.114046</td>
      <td>-73.961840</td>
      <td>-73.961964</td>
      <td>-73.947693</td>
      <td>-73.922097</td>
      <td>-73.846670</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>18.792672</td>
      <td>-68.833579</td>
    </tr>
    <tr>
      <th>pickup_latitude</th>
      <td>FieldType.DECIMAL</td>
      <td>0.000000</td>
      <td>40.919121</td>
      <td>7722.0</td>
      <td>0.0</td>
      <td>7722.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>40.682889</td>
      <td>40.675541</td>
      <td>40.721075</td>
      <td>40.756159</td>
      <td>40.803909</td>
      <td>40.849406</td>
      <td>40.870681</td>
      <td>40.891244</td>
      <td>10.345967</td>
      <td>37.936742</td>
    </tr>
    <tr>
      <th>dropoff_longitude</th>
      <td>FieldType.DECIMAL</td>
      <td>-115.179337</td>
      <td>0.000000</td>
      <td>7722.0</td>
      <td>0.0</td>
      <td>7722.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>-87.699611</td>
      <td>-73.984734</td>
      <td>-73.985777</td>
      <td>-73.956250</td>
      <td>-73.928948</td>
      <td>-73.866208</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>18.696526</td>
      <td>-68.896978</td>
    </tr>
    <tr>
      <th>dropoff_latitude</th>
      <td>FieldType.DECIMAL</td>
      <td>0.000000</td>
      <td>41.008934</td>
      <td>7722.0</td>
      <td>0.0</td>
      <td>7722.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>40.662763</td>
      <td>40.654851</td>
      <td>40.717821</td>
      <td>40.756534</td>
      <td>40.784688</td>
      <td>40.852437</td>
      <td>40.879289</td>
      <td>40.937291</td>
      <td>10.290780</td>
      <td>37.963774</td>
    </tr>
  </tbody>
</table>



A partir da saída de estatísticas de resumo, ver que existem em falta coordenadas e que não estão na cidade de nova York (isto é determinado a partir de análise subjetiva). Filtre as coordenadas de locais que estão fora do limite de cidade. O filtro de coluna de cadeia os comandos no `filter()` de função e defina os limites mínimos e máximo para cada campo. Em seguida, chame o `get_profile()` novamente para verificar a transformação.


```python
latlong_filtered_df = (combined_df
    .drop_nulls(
        columns=["pickup_longitude", "pickup_latitude", "dropoff_longitude", "dropoff_latitude"],
        column_relationship=dprep.ColumnRelationship(dprep.ColumnRelationship.ANY)
    )
    .filter(dprep.f_and(
        dprep.col("pickup_longitude") <= -73.72,
        dprep.col("pickup_longitude") >= -74.09,
        dprep.col("pickup_latitude") <= 40.88,
        dprep.col("pickup_latitude") >= 40.53,
        dprep.col("dropoff_longitude") <= -73.72,
        dprep.col("dropoff_longitude") >= -74.09,
        dprep.col("dropoff_latitude") <= 40.88,
        dprep.col("dropoff_latitude") >= 40.53
    )))
latlong_filtered_df.keep_columns(columns=[
    "pickup_longitude", "pickup_latitude",
    "dropoff_longitude", "dropoff_latitude"
]).get_profile()
```




<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Type</th>
      <th>Mín.</th>
      <th>Máx.</th>
      <th>Contagem</th>
      <th>Contagem de em falta</th>
      <th>Não tem em falta contagem</th>
      <th>Percentagem em falta</th>
      <th>Contagem de erros</th>
      <th>Contagem de vazia</th>
      <th>0,1% Quantile</th>
      <th>1% Quantile</th>
      <th>5% Quantile</th>
      <th>25% Quantile</th>
      <th>50% Quantile</th>
      <th>75% Quantile</th>
      <th>95% Quantile</th>
      <th>99% Quantile</th>
      <th>99,9% Quantile</th>
      <th>Desvio padrão</th>
      <th>média</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>pickup_longitude</th>
      <td>FieldType.DECIMAL</td>
      <td>-74.078156</td>
      <td>-73.736481</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>-74.076314</td>
      <td>-73.962542</td>
      <td>-73.962893</td>
      <td>-73.948975</td>
      <td>-73.927856</td>
      <td>-73.866662</td>
      <td>-73.830438</td>
      <td>-73.823160</td>
      <td>-73.769750</td>
      <td>0.048711</td>
      <td>-73.913865</td>
    </tr>
    <tr>
      <th>pickup_latitude</th>
      <td>FieldType.DECIMAL</td>
      <td>40.575485</td>
      <td>40.879852</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>40.632884</td>
      <td>40.713105</td>
      <td>40.711600</td>
      <td>40.721403</td>
      <td>40.758142</td>
      <td>40.805145</td>
      <td>40.848855</td>
      <td>40.867567</td>
      <td>40.877690</td>
      <td>0.048348</td>
      <td>40.765226</td>
    </tr>
    <tr>
      <th>dropoff_longitude</th>
      <td>FieldType.DECIMAL</td>
      <td>-74.085747</td>
      <td>-73.720871</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>-74.078828</td>
      <td>-73.985650</td>
      <td>-73.985813</td>
      <td>-73.959041</td>
      <td>-73.936681</td>
      <td>-73.884846</td>
      <td>-73.815507</td>
      <td>-73.776697</td>
      <td>-73.733471</td>
      <td>0.055961</td>
      <td>-73.920718</td>
    </tr>
    <tr>
      <th>dropoff_latitude</th>
      <td>FieldType.DECIMAL</td>
      <td>40.583530</td>
      <td>40.879734</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>40.597741</td>
      <td>40.695376</td>
      <td>40.695115</td>
      <td>40.727549</td>
      <td>40.758160</td>
      <td>40.788378</td>
      <td>40.850372</td>
      <td>40.867968</td>
      <td>40.878586</td>
      <td>0.050462</td>
      <td>40.759487</td>
    </tr>
  </tbody>
</table>

### <a name="split-and-rename-columns"></a>Dividir e mudar o nome de colunas

Examinar o perfil de dados para o `store_forward` coluna. Este campo é um sinalizador booleano, que é `Y` quando a táxis não tinha uma ligação para o servidor após a viagem e, então, precisaram que armazenar os dados da viagem na memória e reencaminhá-lo mais tarde para o servidor quando estiver ligado.


```python
latlong_filtered_df.keep_columns(columns='store_forward').get_profile()
```




<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Type</th>
      <th>Mín.</th>
      <th>Máx.</th>
      <th>Contagem</th>
      <th>Contagem de em falta</th>
      <th>Não tem em falta contagem</th>
      <th>Percentagem em falta</th>
      <th>Contagem de erros</th>
      <th>Contagem de vazia</th>
      <th>0,1% Quantile</th>
      <th>1% Quantile</th>
      <th>5% Quantile</th>
      <th>25% Quantile</th>
      <th>50% Quantile</th>
      <th>75% Quantile</th>
      <th>95% Quantile</th>
      <th>99% Quantile</th>
      <th>99,9% Quantile</th>
      <th>Desvio padrão</th>
      <th>média</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>store_forward</th>
      <td>FieldType.STRING</td>
      <td>N</td>
      <td>S</td>
      <td>7059.0</td>
      <td>99.0</td>
      <td>6960.0</td>
      <td>0.014025</td>
      <td>0.0</td>
      <td>0.0</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
  </tbody>
</table>



Tenha em atenção que o perfil de dados de saída no `store_forward` coluna mostra que os dados é inconsistentes e estiverem faltando ou valores de nulo. Utilize o `replace()` e `fill_nulls()` funções para substitua estes valores de cadeia "N":


```python
replaced_stfor_vals_df = latlong_filtered_df.replace(columns="store_forward", find="0", replace_with="N").fill_nulls("store_forward", "N")
```

Executar o `replace` funcionar no `distance` campo. A função reformata valores de distância, que estão incorretamente identificadas como `.00`e preenche qualquer nulos com zeros. Converter o `distance` campo para um formato numérico. Esses pontos de dados incorretos são provavelmente anomalias no sistema de recolha de dados no cabs a táxis.


```python
replaced_distance_vals_df = replaced_stfor_vals_df.replace(columns="distance", find=".00", replace_with=0).fill_nulls("distance", 0)
replaced_distance_vals_df = replaced_distance_vals_df.to_number(["distance"])
```

Divida os valores de datetime de recolha e de redução nas respectiva data e as colunas de tempo. Utilize o `split_column_by_example()` função para fazer a divisão. Neste caso, o opcional `example` parâmetro do `split_column_by_example()` função for omitida. Por conseguinte, a função automaticamente determina onde divididas com base nos dados.


```python
time_split_df = (replaced_distance_vals_df
    .split_column_by_example(source_column="pickup_datetime")
    .split_column_by_example(source_column="dropoff_datetime"))
time_split_df.head(5)
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
      <th>Fornecedor</th>
      <th>pickup_datetime</th>
      <th>pickup_datetime_1</th>
      <th>pickup_datetime_2</th>
      <th>dropoff_datetime</th>
      <th>dropoff_datetime_1</th>
      <th>dropoff_datetime_2</th>
      <th>store_forward</th>
      <th>pickup_longitude</th>
      <th>pickup_latitude</th>
      <th>dropoff_longitude</th>
      <th>dropoff_latitude</th>
      <th>passageiros</th>
      <th>distância</th>
      <th>custo</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2</td>
      <td>2013-08-01 17: 22:00</td>
      <td>2013-08-01</td>
      <td>17: 22:00</td>
      <td>2013-08-01 17: 22:00</td>
      <td>2013-08-01</td>
      <td>17: 22:00</td>
      <td>N</td>
      <td>-73.937767</td>
      <td>40.758480</td>
      <td>-73.937767</td>
      <td>40.758480</td>
      <td>1</td>
      <td>0.0</td>
      <td>2.5</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>2013-08-01 17:24:00</td>
      <td>2013-08-01</td>
      <td>17:24:00</td>
      <td>2013-08-01 17:25:00</td>
      <td>2013-08-01</td>
      <td>17:25:00</td>
      <td>N</td>
      <td>-73.937927</td>
      <td>40.757843</td>
      <td>-73.937927</td>
      <td>40.757843</td>
      <td>1</td>
      <td>0.0</td>
      <td>2.5</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>2013-08-06 06:51:19</td>
      <td>2013-08-06</td>
      <td>06:51:19</td>
      <td>2013-08-06 06:51:36</td>
      <td>2013-08-06</td>
      <td>06:51:36</td>
      <td>N</td>
      <td>-73.937721</td>
      <td>40.758404</td>
      <td>-73.937721</td>
      <td>40.758369</td>
      <td>1</td>
      <td>0.0</td>
      <td>3.3</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2</td>
      <td>2013-08-06 13:26:34</td>
      <td>2013-08-06</td>
      <td>13:26:34</td>
      <td>2013-08-06 13:26:57</td>
      <td>2013-08-06</td>
      <td>13:26:57</td>
      <td>N</td>
      <td>-73.937691</td>
      <td>40.758419</td>
      <td>-73.937790</td>
      <td>40.758358</td>
      <td>1</td>
      <td>0.0</td>
      <td>3.3</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2</td>
      <td>2013-08-06 13:27:53</td>
      <td>2013-08-06</td>
      <td>13:27:53</td>
      <td>2013-08-06 13:28:08</td>
      <td>2013-08-06</td>
      <td>13:28:08</td>
      <td>N</td>
      <td>-73.937805</td>
      <td>40.758396</td>
      <td>-73.937775</td>
      <td>40.758450</td>
      <td>1</td>
      <td>0.0</td>
      <td>3.3</td>
    </tr>
  </tbody>
</table>
</div>

Mudar o nome as colunas geradas pelo `split_column_by_example()` função a utilizar nomes significativos.

```python
renamed_col_df = (time_split_df
    .rename_columns(column_pairs={
        "pickup_datetime_1": "pickup_date",
        "pickup_datetime_2": "pickup_time",
        "dropoff_datetime_1": "dropoff_date",
        "dropoff_datetime_2": "dropoff_time"
    }))
renamed_col_df.head(5)
```

Chamar o `get_profile()` função para ver as estatísticas de resumo completa passos de limpeza Afinal de contas.

```python
renamed_col_df.get_profile()
```

## <a name="transform-data"></a>Transformar dados

Divida as datas recolha e de redução ainda mais para o dia da semana, dia do mês e valores de mês. Para obter o dia do valor de semana, utilize o `derive_column_by_example()` função. A função aceita um parâmetro de matriz de objetos de exemplo que definem os dados de entrada e saída preferencial. A função determina automaticamente a sua transformação preferencial. Para as colunas de tempo de recolha e de redução, dividir o tempo na hora, minuto e segundo, utilizando o `split_column_by_example()` função sem parâmetros de exemplo.

Depois de gerar os novos recursos, utilize o `drop_columns()` função para eliminar os campos originais que os recursos gerados recentemente sejam preferenciais. Mudar o nome o resto dos campos para utilizar descrições significativas.

Transformar os dados desta forma para criar novas funcionalidades baseadas no tempo melhorará a aprendizagem precisão do modelo. Por exemplo, gerar um novo recurso para o dia da semana ajudará a estabelecer uma relação entre o dia da semana e o preço de Europeia táxis, que é muitas vezes mais caro em determinados dias da semana devido à elevada procura.


```python
transformed_features_df = (renamed_col_df
    .derive_column_by_example(
        source_columns="pickup_date",
        new_column_name="pickup_weekday",
        example_data=[("2009-01-04", "Sunday"), ("2013-08-22", "Thursday")]
    )
    .derive_column_by_example(
        source_columns="dropoff_date",
        new_column_name="dropoff_weekday",
        example_data=[("2013-08-22", "Thursday"), ("2013-11-03", "Sunday")]
    )

    .split_column_by_example(source_column="pickup_time")
    .split_column_by_example(source_column="dropoff_time")
    # The following two calls to split_column_by_example reference the column names generated from the previous two calls.
    .split_column_by_example(source_column="pickup_time_1")
    .split_column_by_example(source_column="dropoff_time_1")
    .drop_columns(columns=[
        "pickup_date", "pickup_time", "dropoff_date", "dropoff_time",
        "pickup_date_1", "dropoff_date_1", "pickup_time_1", "dropoff_time_1"
    ])

    .rename_columns(column_pairs={
        "pickup_date_2": "pickup_month",
        "pickup_date_3": "pickup_monthday",
        "pickup_time_1_1": "pickup_hour",
        "pickup_time_1_2": "pickup_minute",
        "pickup_time_2": "pickup_second",
        "dropoff_date_2": "dropoff_month",
        "dropoff_date_3": "dropoff_monthday",
        "dropoff_time_1_1": "dropoff_hour",
        "dropoff_time_1_2": "dropoff_minute",
        "dropoff_time_2": "dropoff_second"
    }))

transformed_features_df.head(5)
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
      <th>Fornecedor</th>
      <th>pickup_datetime</th>
      <th>pickup_weekday</th>
      <th>pickup_hour</th>
      <th>pickup_minute</th>
      <th>pickup_second</th>
      <th>dropoff_datetime</th>
      <th>dropoff_weekday</th>
      <th>dropoff_hour</th>
      <th>dropoff_minute</th>
      <th>dropoff_second</th>
      <th>store_forward</th>
      <th>pickup_longitude</th>
      <th>pickup_latitude</th>
      <th>dropoff_longitude</th>
      <th>dropoff_latitude</th>
      <th>passageiros</th>
      <th>distância</th>
      <th>custo</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2</td>
      <td>2013-08-01 17: 22:00</td>
      <td>Quinta-feira</td>
      <td>17</td>
      <td>22</td>
      <td>00</td>
      <td>2013-08-01 17: 22:00</td>
      <td>Quinta-feira</td>
      <td>17</td>
      <td>22</td>
      <td>00</td>
      <td>N</td>
      <td>-73.937767</td>
      <td>40.758480</td>
      <td>-73.937767</td>
      <td>40.758480</td>
      <td>1</td>
      <td>0.0</td>
      <td>2.5</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>2013-08-01 17:24:00</td>
      <td>Quinta-feira</td>
      <td>17</td>
      <td>24</td>
      <td>00</td>
      <td>2013-08-01 17:25:00</td>
      <td>Quinta-feira</td>
      <td>17</td>
      <td>25</td>
      <td>00</td>
      <td>N</td>
      <td>-73.937927</td>
      <td>40.757843</td>
      <td>-73.937927</td>
      <td>40.757843</td>
      <td>1</td>
      <td>0.0</td>
      <td>2.5</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>2013-08-06 06:51:19</td>
      <td>Terça-feira</td>
      <td>06</td>
      <td>51</td>
      <td>19</td>
      <td>2013-08-06 06:51:36</td>
      <td>Terça-feira</td>
      <td>06</td>
      <td>51</td>
      <td>36</td>
      <td>N</td>
      <td>-73.937721</td>
      <td>40.758404</td>
      <td>-73.937721</td>
      <td>40.758369</td>
      <td>1</td>
      <td>0.0</td>
      <td>3.3</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2</td>
      <td>2013-08-06 13:26:34</td>
      <td>Terça-feira</td>
      <td>13</td>
      <td>26</td>
      <td>34</td>
      <td>2013-08-06 13:26:57</td>
      <td>Terça-feira</td>
      <td>13</td>
      <td>26</td>
      <td>57</td>
      <td>N</td>
      <td>-73.937691</td>
      <td>40.758419</td>
      <td>-73.937790</td>
      <td>40.758358</td>
      <td>1</td>
      <td>0.0</td>
      <td>3.3</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2</td>
      <td>2013-08-06 13:27:53</td>
      <td>Terça-feira</td>
      <td>13</td>
      <td>27</td>
      <td>53</td>
      <td>2013-08-06 13:28:08</td>
      <td>Terça-feira</td>
      <td>13</td>
      <td>28</td>
      <td>08</td>
      <td>N</td>
      <td>-73.937805</td>
      <td>40.758396</td>
      <td>-73.937775</td>
      <td>40.758450</td>
      <td>1</td>
      <td>0.0</td>
      <td>3.3</td>
    </tr>
  </tbody>
</table>
</div>

Tenha em atenção que o mostra dados que os componentes data e hora de recolha e de redução produzidos a partir das transformações derivadas está correto. Remover os `pickup_datetime` e `dropoff_datetime` colunas porque já não são necessários (funcionalidades de tempo granular, como hora, minuto e segundo, são mais úteis para treinamento de modelo).


```python
processed_df = transformed_features_df.drop_columns(columns=["pickup_datetime", "dropoff_datetime"])
```

Utilize a funcionalidade de inferência de tipo para verificar automaticamente o tipo de dados de cada campo e exibir os resultados de inferência de tipos.


```python
type_infer = processed_df.builders.set_column_types()
type_infer.learn()
type_infer
```

A saída resultante de `type_infer` é o seguinte.

    Column types conversion candidates:
    'pickup_weekday': [FieldType.STRING],
    'pickup_hour': [FieldType.DECIMAL],
    'pickup_minute': [FieldType.DECIMAL],
    'pickup_second': [FieldType.DECIMAL],
    'dropoff_hour': [FieldType.DECIMAL],
    'dropoff_minute': [FieldType.DECIMAL],
    'dropoff_second': [FieldType.DECIMAL],
    'store_forward': [FieldType.STRING],
    'pickup_longitude': [FieldType.DECIMAL],
    'dropoff_longitude': [FieldType.DECIMAL],
    'passengers': [FieldType.DECIMAL],
    'distance': [FieldType.DECIMAL],
    'vendor': [FieldType.STRING],
    'dropoff_weekday': [FieldType.STRING],
    'pickup_latitude': [FieldType.DECIMAL],
    'dropoff_latitude': [FieldType.DECIMAL],
    'cost': [FieldType.DECIMAL]

Os resultados de inferência de tipos a aparência corretos com base nos dados. Agora, aplica as conversões de tipo para o fluxo de dados.


```python
type_converted_df = type_infer.to_dataflow()
type_converted_df.get_profile()
```

Antes de empacotar o fluxo de dados, execute dois filtros finais no conjunto de dados. Para eliminar os pontos de dados capturada de forma incorreta, filtrar o fluxo de dados nos registos em que tanto o `cost` e `distance` valores das variáveis são maiores que zero. Este passo poderá aprimorar significativamente de machine learning a precisão do modelo, porque o custam de pontos de dados com um zero ou distância representam principais valores atípicos que jogar fora a exatidão da previsão.

```python
final_df = type_converted_df.filter(dprep.col("distance") > 0)
final_df = final_df.filter(dprep.col("cost") > 0)
```

Agora tem um objeto de fluxo de dados totalmente transformados e preparado para usar num modelo de machine learning. O SDK inclui funcionalidade de serialização de objeto, o que é utilizada conforme mostrado no código a seguir.

```python
import os
file_path = os.path.join(os.getcwd(), "dflows.dprep")

package = dprep.Package([final_df])
package.save(file_path)
```

## <a name="clean-up-resources"></a>Limpar recursos

Para continuar com a segunda parte do tutorial, terá do **dflows.dprep** ficheiro no diretório atual.

Se não quiser continuar para a parte dois, elimine o **dflows.dprep** ficheiro no seu diretório atual. Eliminar este ficheiro, se estiver a executar a execução localmente ou na [blocos de notas do Azure](https://notebooks.azure.com/).

## <a name="next-steps"></a>Passos Seguintes

Na primeira parte deste tutorial,:

> [!div class="checklist"]
> * Configure o ambiente de desenvolvimento.
> * Conjuntos de dados limpos e carregá-lo.
> * Utilizado transformações inteligentes para prever a lógica com base num exemplo.
> * Em pacotes e intercalados conjuntos de dados de treinamento do machine learning.

Está pronto para utilizar os dados de treinamento na segunda parte do tutorial:

> [!div class="nextstepaction"]
> [Tutorial (parte dois): Preparar o modelo de regressão](tutorial-auto-train-models.md)
