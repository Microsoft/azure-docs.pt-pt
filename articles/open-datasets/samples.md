---
title: Exemplo de notebooks Jupyter usando um conjunto de NOAA aberto
titleSuffix: Azure Open Datasets
description: Use os notebooks Jupyter de exemplo para conjuntos de dados abertos do Azure para aprender a carregar conjuntos de dados abertos e usá-los para enriquecer os data de demonstração. As técnicas incluem o uso do Spark e do pandas para processar dados.
ms.service: open-datasets
ms.topic: sample
author: cjgronlund
ms.author: cgronlun
ms.date: 11/04/2019
ms.openlocfilehash: 2ccdb7adcae40d22a36fbfb47fba6e4c87b3349a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73496574"
---
# <a name="example-jupyter-notebooks-show-how-to-enrich-data-with-open-datasets"></a>Os notebooks Jupyter de exemplo mostram como enriquecer os dados com DataSets abertos 
Os blocos de anotações de exemplo do Jupyter para conjuntos de dados abertos do Azure mostram como carregar conjuntos de dados abertos e usá-los para enriquecer o dado da demonstração. As técnicas incluem o uso de Apache Spark e pandas para processar dados.

>[!IMPORTANT]
>Ao trabalhar em um ambiente não Spark, os conjuntos de dados abertos permitem o download de apenas um mês de dado por vez com determinadas classes para evitar o MemoryError com grandes DataSets.

## <a name="load-noaa-integrated-surface-database-isd-data"></a>Carregar dados do NOAA Integrated Surface Database (ISD) 
|Bloco de Notas        | Descrição                                    |
|----------------|------------------------------------------------|
|[Carregar um mês recente de dados meteorológicos em um dataframe do pandas](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-access/02-weather-to-pandas-dataframe.ipynb) | Saiba como carregar dados meteorológicos históricos em seu dataframe do pandas favorito. |
|[Carregar um mês recente de dados meteorológicos em um dataframe do Spark](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-access/01-weather-to-spark-dataframe.ipynb) | Saiba como carregar dados meteorológicos históricos em seu dataframe do Spark favorito.  |

## <a name="join-demo-data-with-noaa-isd-data"></a>Unir dados de demonstração com dados do NOAA ISD 
|Bloco de Notas        | Descrição                                    |
|----------------|------------------------------------------------|
|[Unir dados de demonstração com o clima data-pandas](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-join/02-weather-join-in-pandas.ipynb) | Junte-se a um conjunto de uma demonstração de 1 mês de locais de sensor com leituras de clima em um dataframe do pandas.  |
|[Unir dados de demonstração com dados meteorológicos – Spark](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-join/01-weather-join-in-spark.ipynb) | Ingresse em um conjunto de uma demonstração de locais de sensor com leituras de clima em um dataframe do Spark. |

## <a name="join-nyc-taxi-data-with-noaa-isd-data"></a>Unir dados de táxi de NYC com dados do NOAA ISD 
|Bloco de Notas        | Descrição                                    |
|----------------|------------------------------------------------|
|[Dados de corrida de táxi aprimorados com o clima data-pandas](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-join/04-nyc-taxi-join-weather-in-pandas.ipynb) | Carregue dados de táxi de NYC verde (mais de 1 mês) e aprimore-os com dados meteorológicos em um dataframe do pandas. Este exemplo substitui o método `get_pandas_limit` e equilibra o desempenho de carregamento de dados com a quantidade de dados.|
|[Dados de corrida de táxi aprimorados com dados meteorológicos – Spark](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-join/03-nyc-taxi-join-weather-in-spark.ipynb) | Carregue dados de táxis NYC e aprimore-os com dados meteorológicos, no data frame do Spark.  |

## <a name="next-steps"></a>Passos seguintes

* [Tutorial: modelagem de regressão com o Machine Learning automatizado e um conjunto de informações aberto](tutorial-opendatasets-automl.md)
* [SDK do Python para conjuntos de valores abertos](/python/api/azureml-opendatasets/azureml.opendatasets?view=azure-ml-py)
* [Catálogo de conjuntos de itens abertos do Azure](https://azure.microsoft.com/services/open-datasets/catalog/)
