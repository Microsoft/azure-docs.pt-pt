---
title: 'Tutorial do modelo de regressão: Preparar dados'
titleSuffix: Azure Machine Learning service
description: Na primeira parte deste tutorial, você aprenderá a preparar dados em Python para a modelagem de regressão usando o SDK do Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: MayMSFT
ms.author: sihhu
ms.reviewer: trbye
ms.date: 07/16/2019
ms.custom: seodec18
ms.openlocfilehash: 55bece47ad2a9965e5137ad720631d9b5f5add48
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/17/2019
ms.locfileid: "68297874"
---
# <a name="tutorial-prepare-data-for-regression-modeling"></a>Tutorial: Preparar dados para modelagem de regressão

Neste tutorial, você aprenderá a preparar dados para a modelagem de regressão usando o [pacote de preparação de dados](https://aka.ms/data-prep-sdk) do [SDK do Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py). Você executa várias transformações para filtrar e combinar dois conjuntos diferentes de dados de táxi de NYC.

Este tutorial é a **primeira parte de uma série composta por duas partes**. Depois de concluir a série de tutoriais, você pode prever o custo de uma viagem de táxi treinando um modelo em recursos de dados. Esses recursos incluem o dia e a hora de retirada, o número de passageiros e o local de retirada.

Neste tutorial:

> [!div class="checklist"]
> * Configure um ambiente Python e importe pacotes.
> * Carregue dois conjuntos de valores com nomes de campo diferentes.
> * Limpar dados para remover anomalias.
> * Transforme dados usando transformações inteligentes para criar novos recursos.
> * Salve o objeto Dataflow para usar em um modelo de regressão.

## <a name="prerequisites"></a>Pré-requisitos

Pule para [configurar seu ambiente de desenvolvimento](#start) para ler as etapas do bloco de anotações ou use as instruções abaixo para obter o bloco de anotações e executá-lo em Azure notebooks ou em seu próprio servidor de notebook. Para executar o bloco de anotações, você precisará de:

* Um servidor de notebook Python 3,6 com o seguinte instalado:
    * O `azureml-dataprep` pacote do SDK do Azure Machine Learning
* O bloco de notas do tutorial

* Usar um [servidor de bloco de anotações de nuvem em seu espaço de trabalho](#azure) 
* Use [seu próprio servidor de notebook](#server)

### <a name="azure"></a>Usar um servidor de bloco de anotações de nuvem em seu espaço de trabalho

É fácil começar com seu próprio servidor de notebook baseado em nuvem. O SDK do Azure Machine Learning para Python já está instalado e configurado para você depois de criar esse recurso de nuvem.

[!INCLUDE [aml-azure-notebooks](../../../includes/aml-azure-notebooks.md)]

* Depois de iniciar a página da Web do bloco de anotações, execute o bloco de anotações **tutoriais/regression-part1-data-Prep. ipynb** .

### <a name="server"></a>Use seu próprio servidor do Jupyter Notebook

Utilize estes passos para criar um servidor de bloco de notas Jupyter local no seu computador.  Depois de concluir as etapas, execute o bloco de anotações **tutoriais/regression-part1-data-Prep. ipynb** .

1. Conclua as etapas de instalação em [Azure Machine Learning início rápido do Python](setup-create-workspace.md#sdk) para criar um ambiente Miniconda e instalar o SDK.  Fique à vontade para ignorar a seção **criar um espaço de trabalho** , se desejar, mas você precisará dela para a [parte 2](tutorial-auto-train-models.md) desta série de tutoriais.
1. O `azureml-dataprep` pacote é instalado automaticamente quando você instala o SDK do.
1. Clone o [repositório do GitHub](https://aka.ms/aml-notebooks).

    ```
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Inicie o servidor de blocos de notas a partir do diretório clonado.

    ```shell
    jupyter notebook
    ```

## <a name="start"></a>Configurar seu ambiente de desenvolvimento

Toda a configuração para o seu trabalho de desenvolvimento pode ser feita num bloco de notas Python. A instalação inclui as seguintes ações:

* Instalar o SDK
* Importação de pacotes Python

### <a name="install-and-import-packages"></a>Instalar e importar pacotes

Use o seguinte para instalar os pacotes necessários se você ainda não os tiver.

```shell
pip install "azureml-dataprep[pandas]>=1.1.0,<1.2.0"
```

Importe o pacote.

```python
import azureml.dataprep as dprep
```

> [!IMPORTANT]
> Certifique-se de instalar a versão mais recente do pacote azureml. dataprep. Este tutorial não funcionará com o número de versão inferior ao 1.1.0

## <a name="load-data"></a>Carregar dados

Baixe dois conjuntos de dados de táxi de NYC diferentes em objetos de Dataflow. Os conjuntos de valores têm campos um pouco diferentes. O `auto_read_file()` método reconhece automaticamente o tipo de arquivo de entrada.

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

> [!Note]
> A URL neste mesmo exemplo não é uma URL completa. Em vez disso, ele se refere à pasta demo no BLOB. A URL completa para alguns dos dados é https://dprepdata.blob.core.windows.net/demo/green-small/green_tripdata_2013-08.csv

Um `Dataflow` objeto é semelhante a um dataframe e representa uma série de operações imutáveis avaliadas lentamente nos dados. As operações podem ser adicionadas invocando os diferentes métodos de transformação e filtragem disponíveis. O resultado da adição de uma operação a `Dataflow` um é sempre um `Dataflow` novo objeto.

## <a name="cleanse-data"></a>Limpar dados

Agora, você preenche algumas variáveis com transformações de atalho para aplicar a todos os fluxos de fluxo de os. A `drop_if_all_null` variável é usada para excluir registros onde todos os campos são nulos. A `useful_columns` variável contém uma matriz de descrições de coluna que são mantidas em cada Dataflow.

```python
all_columns = dprep.ColumnSelector(term=".*", use_regex=True)
drop_if_all_null = [all_columns, dprep.ColumnRelationship(dprep.ColumnRelationship.ALL)]
useful_columns = [
    "cost", "distance", "dropoff_datetime", "dropoff_latitude", "dropoff_longitude",
    "passengers", "pickup_datetime", "pickup_latitude", "pickup_longitude", "store_forward", "vendor"
]
```

Primeiro, você trabalha com os dados de táxis verdes para colocá-los em uma forma válida que pode ser combinada com os dados de táxis amarelos. Chame as `replace_na()`funções `drop_nulls()`, e `keep_columns()` usando as variáveis de transformação de atalho que você criou. Além disso, renomeie todas as colunas no dataframe para corresponder aos nomes `useful_columns` na variável.


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

Execute as mesmas etapas de transformação nos dados de táxi amarelo. Essas funções garantem que os dados nulos sejam removidos do conjunto de dados, o que ajudará a aumentar a precisão do modelo de aprendizado de máquina.

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

Chame a `append_rows()` função nos dados de táxi verde para acrescentar os dados de táxi amarelo. Um novo dataframe combinado é criado.

```python
combined_df = green_df.append_rows([yellow_df])
```

### <a name="convert-types-and-filter"></a>Converter tipos e filtro

Examine a recolha e entrega coordena as estatísticas de resumo para ver como os dados são distribuídos. Primeiro, defina um `TypeConverter` objeto para alterar os campos de latitude e longitude para o tipo decimal. Em seguida, chame `keep_columns()` a função para restringir a saída somente aos campos de latitude e longitude e, em seguida `get_profile()` , chame a função. Essas chamadas de função criam uma exibição condensada do Dataflow para apenas mostrar os campos lat/long, o que torna mais fácil avaliar as coordenadas ausente ou fora do escopo.


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



Na saída de estatísticas de resumo, você vê que há coordenadas e coordenadas ausentes que não estão na cidade de Nova York (isso é determinado pela análise subjetiva). Filtrar coordenadas de locais que estão fora da borda da cidade. Encadear os comandos de filtro de `filter()` coluna dentro da função e definir os limites mínimo e máximo para cada campo. Em seguida, `get_profile()` chame a função novamente para verificar a transformação.


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

Examinar o perfil de dados para o `store_forward` coluna. Esse campo é um sinalizador booliano que `Y` ocorre quando o táxi não tem uma conexão com o servidor após a viagem e, portanto, precisou armazenar os dados de viagem na memória e, posteriormente, encaminhá-los ao servidor quando conectado.


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



Observe que a saída do perfil de dados `store_forward` na coluna mostra que os dados estão inconsistentes e que há valores ausentes ou nulos. Use as `replace()` funções `fill_nulls()` e para substituir esses valores pela cadeia de caracteres "N":


```python
replaced_stfor_vals_df = latlong_filtered_df.replace(columns="store_forward", find="0", replace_with="N").fill_nulls("store_forward", "N")
```

Execute a `replace` função `distance` no campo. A função reformata valores de distância que são rotulados incorretamente como `.00`e preenche quaisquer nulos com zeros. Converter o `distance` campo para um formato numérico. Esses pontos de dados incorretos são provavelmente anomalias no sistema de coleta de dados no táxi de táxi.


```python
replaced_distance_vals_df = replaced_stfor_vals_df.replace(columns="distance", find=".00", replace_with=0).fill_nulls("distance", 0)
replaced_distance_vals_df = replaced_distance_vals_df.to_number(["distance"])
```

Divida os valores de data e hora de recebimento e chegada nas respectivas colunas Date e time. Use a `split_column_by_example()` função para fazer a divisão. Nesse caso, o parâmetro opcional `example` `split_column_by_example()` da função é omitido. Portanto, a função determina automaticamente onde dividir com base nos dados.


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

Renomeie as colunas geradas pela `split_column_by_example()` função para usar nomes significativos.

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

Chame a `get_profile()` função para ver as estatísticas de resumo completas após todas as etapas de limpeza.

```python
renamed_col_df.get_profile()
```

## <a name="transform-data"></a>Transformar dados

Divida o recebimento e a data de chegada mais no dia da semana, no dia do mês e nos valores de mês. Para obter o dia do valor da semana, use a `derive_column_by_example()` função. A função usa um parâmetro de matriz de objetos de exemplo que definem os dados de entrada e a saída preferida. A função determina automaticamente sua transformação preferida. Para as colunas de tempo de retirada e chegada, divida a hora em hora, minuto e segundo usando a `split_column_by_example()` função sem nenhum parâmetro de exemplo.

Depois de gerar os novos recursos, use a `drop_columns()` função para excluir os campos originais, já que os recursos gerados recentemente são preferidos. Renomeie o restante dos campos para usar descrições significativas.

A transformação dos dados dessa maneira para criar novos recursos baseados em tempo melhorará a precisão do modelo de aprendizado de máquina. Por exemplo, a geração de um novo recurso para o dia da semana ajudará a estabelecer uma relação entre o dia da semana e o preço de táxi, que geralmente é mais caro em determinados dias da semana devido à alta demanda.


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

Observe que os dados mostram que os componentes de data e hora de retirada e chegada produzidos a partir das transformações derivadas estão corretos. Descarte as `pickup_datetime` colunas `dropoff_datetime` e porque elas não são mais necessárias (recursos de tempo granular, como hora, minuto e segundo, são mais úteis para o treinamento do modelo).


```python
processed_df = transformed_features_df.drop_columns(columns=["pickup_datetime", "dropoff_datetime"])
```

Utilize a funcionalidade de inferência de tipo para verificar automaticamente o tipo de dados de cada campo e exibir os resultados de inferência de tipos.


```python
type_infer = processed_df.builders.set_column_types()
type_infer.learn()
type_infer
```

A saída resultante do `type_infer` é a seguinte.

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

Os resultados da inferência parecem corretos com base nos dados. Agora, aplique as conversões de tipo ao Dataflow.


```python
type_converted_df = type_infer.to_dataflow()
type_converted_df.get_profile()
```

Antes de empacotar o Dataflow, execute dois filtros finais no conjunto de dados. Para eliminar pontos de dados capturados incorretamente, filtre o Dataflow nos registros em `cost` que `distance` os valores de variável e sejam maiores que zero. Esta etapa melhorará significativamente a precisão do modelo de aprendizado de máquina, pois os pontos de dados com um custo ou distância zero representam as principais exceções que lançam a precisão da previsão.

```python
final_df = type_converted_df.filter(dprep.col("distance") > 0)
final_df = final_df.filter(dprep.col("cost") > 0)
```

Agora você tem um objeto Dataflow totalmente transformado e preparado para usar em um modelo de aprendizado de máquina. O SDK inclui a funcionalidade de serialização de objeto, que é usada conforme mostrado no código a seguir.

```python
import os

file_path = os.path.join(os.getcwd(), "dflows.dprep")
final_df.save(file_path)
```

## <a name="clean-up-resources"></a>Limpar recursos

Para continuar com a parte dois do tutorial, você precisa do arquivo **dflows. dprep** no diretório atual.

Se você não planeja continuar para a parte dois, exclua o arquivo **dflows. dprep** no diretório atual. Exclua esse arquivo se você estiver executando a execução localmente ou em [Azure notebooks](https://notebooks.azure.com/).

## <a name="next-steps"></a>Passos seguintes

Na primeira parte deste tutorial,:

> [!div class="checklist"]
> * Configure seu ambiente de desenvolvimento.
> * Conjuntos de dados carregados e limpos.
> * Transformações inteligentes usadas para prever sua lógica com base em um exemplo.
> * Conjuntos de valores mesclados e empacotados para o treinamento do Machine Learning.

Você está pronto para usar os dados de treinamento na parte dois do tutorial:

> [!div class="nextstepaction"]
> [Tutorial (parte dois): Treinar o modelo de regressão](tutorial-auto-train-models.md)
