---
title: Exemplo, cadernos Jupyter usando dados da NOAA
titleSuffix: Azure Open Datasets
description: Use exemplo de cadernos Jupyter para Azure Open Datasets para aprender a carregar conjuntos de dados abertos e usá-los para enriquecer dados de demonstração. As técnicas incluem a utilização de Spark e Pandas para processar dados.
ms.service: open-datasets
ms.topic: sample
author: cjgronlund
ms.author: cgronlun
ms.date: 11/04/2019
ms.openlocfilehash: 8b96a35db91a282be1fb5e4c6143e6bd0a0203f2
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "73606149"
---
# <a name="example-jupyter-notebooks-show-how-to-enrich-data-with-open-datasets"></a>Exemplo, os cadernos jupyter mostram como enriquecer dados com Conjuntos de Dados Abertos 
Os cadernos Jupyter para O Conjunto de Dados Abertos do Azure mostram-lhe como carregar conjuntos de dados abertos e usá-los para enriquecer dados de demonstração. As técnicas incluem a utilização de Apache Spark e Pandas para processar dados.

>[!IMPORTANT]
>Ao trabalhar num ambiente não-Spark, o Open Datasets permite descarregar apenas um mês de dados de cada vez com determinadas classes, de modo a evitar o Error memory com grandes conjuntos de dados.

## <a name="load-noaa-integrated-surface-database-isd-data"></a>Carregar dados integrados da Base de Dados de Superfície (ISD) da NOAA 
|Bloco de Notas        | Descrição                                    |
|----------------|------------------------------------------------|
|[Carregue um mês recente de dados meteorológicos num quadro de dados do Pandas](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-access/02-weather-to-pandas-dataframe.ipynb) | Saiba como carregar dados meteorológicos históricos no seu dataframe favorito dos Pandas. |
|[Carregue um mês recente de dados meteorológicos num quadro de dados da Spark](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-access/01-weather-to-spark-dataframe.ipynb) | Saiba como carregar dados meteorológicos históricos no seu dataframe favorito da Spark.  |

## <a name="join-demo-data-with-noaa-isd-data"></a>Junte-se aos dados de demonstração com dados da ISD da NOAA 
|Bloco de Notas        | Descrição                                    |
|----------------|------------------------------------------------|
|[Junte-se aos dados da demonstração com dados meteorológicos - Pandas](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-join/02-weather-join-in-pandas.ipynb) | Junte-se a um conjunto de dados de demonstração de 1 mês de locais de sensores com leituras meteorológicas num quadro de dados pandas.  |
|[Junte-se aos dados da demonstração com dados meteorológicos – Spark](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-join/01-weather-join-in-spark.ipynb) | Junte-se a um conjunto de dados de demonstração de localizações de sensores com leituras meteorológicas num quadro de dados spark. |

## <a name="join-nyc-taxi-data-with-noaa-isd-data"></a>Junte-se aos dados de táxi de NYC com dados da NOAA ISD 
|Bloco de Notas        | Descrição                                    |
|----------------|------------------------------------------------|
|[Dados da viagem de táxi enriquecidos com dados meteorológicos - Pandas](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-join/04-nyc-taxi-join-weather-in-pandas.ipynb) | Carregue os dados do táxi verde nyc (mais de 1 mês) e enriqueça-os com dados meteorológicos num quadro de dados do Pandas. Este exemplo sobrepõe-se ao método `get_pandas_limit` e equilibra o desempenho da carga de dados com a quantidade de dados.|
|[Dados da viagem de táxi enriquecidos com dados meteorológicos - Faísca](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-join/03-nyc-taxi-join-weather-in-spark.ipynb) | Carregue os dados do táxi verde nyc e enriqueça-os com dados meteorológicos, em Spark dataframe.  |

## <a name="next-steps"></a>Passos seguintes

* [Tutorial: Modelo de regressão com machine learning automatizado e um conjunto de dados aberto](/azure/machine-learning/service/tutorial-auto-train-models?context=azure/open-datasets/context/open-datasets-context)
* [Python SDK para conjuntos de dados abertos](/python/api/azureml-opendatasets/azureml.opendatasets?view=azure-ml-py)
* [Catálogo do Azure Open Datasets](https://azure.microsoft.com/services/open-datasets/catalog/)
