---
title: Blocos de notas do Jupyter de exemplo com um conjunto de dados aberto NOAA
titleSuffix: Azure Open Datasets
description: Utilize blocos de notas do Jupyter exemplo para conjuntos de dados do Azure abrir para aprender a carregar conjuntos de dados abertos e usá-los para enriquecer os dados de demonstração. Técnicas incluem a utilização do Spark e Pandas para processar os dados.
ms.service: open-datasets
ms.topic: sample
author: cjgronlund
ms.author: cgronlun
ms.date: 05/02/2019
ms.openlocfilehash: b62a2690e5879e45a14d0b06a38e8c5171dda14e
ms.sourcegitcommit: 4891f404c1816ebd247467a12d7789b9a38cee7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/08/2019
ms.locfileid: "65442326"
---
# <a name="example-jupyter-notebooks-show-how-to-enrich-data-with-open-datasets"></a>Blocos de notas do Jupyter exemplo mostram como enriquecer os dados com conjuntos de dados aberto 
Os blocos de notas do Jupyter de exemplo para conjuntos de dados do Azure abrir mostram-lhe como carregar conjuntos de dados abertos e usá-los para enriquecer os dados de demonstração. Técnicas incluem a utilização do Apache Spark e Pandas para processar os dados.

>[!IMPORTANT]
>Ao trabalhar num ambiente de não-Spark, conjuntos de dados aberto permite a transferência apenas de um mês de dados cada vez com determinadas classes para evitar MemoryError com grandes conjuntos de dados.

## <a name="load-noaa-integrated-surface-database-isd-data"></a>Carregar dados da NOAA integrada superfície da base de dados (ISD) 
|Bloco de Notas        | Descrição                                    |
|----------------|------------------------------------------------|
|[Carregar um mês recente dos dados meteorológicos para um dataframe de Pandas](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-access/02-weather-to-pandas-dataframe.ipynb) | Saiba como carregar dados meteorológicos históricos para sua dataframe Pandas favorito. |
|[Carregar um mês recente dos dados meteorológicos para um dataframe do Spark](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-access/01-weather-to-spark-dataframe.ipynb) | Saiba como carregar dados meteorológicos históricos para seus favorito dataframe do Spark.  |

## <a name="join-demo-data-with-noaa-isd-data"></a>Junte-se a dados de demonstração com dados da NOAA ISD 
|Bloco de Notas        | Descrição                                    |
|----------------|------------------------------------------------|
|[Junte-se a dados de demonstração com dados de Meteorologia - Pandas](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-join/02-weather-join-in-pandas.ipynb) | Junte-se um conjunto de dados de demonstração de 1 mês de locais de sensor com leituras de Meteorologia num Pandas dataframe.  |
|[Junte-se a dados de demonstração com dados de Meteorologia – Spark](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-join/01-weather-join-in-spark.ipynb) | Junte-se um conjunto de dados de demonstração de locais de sensor com leituras de Meteorologia num dataframe do Spark. |

## <a name="join-nyc-taxi-data-with-noaa-isd-data"></a>Junte-se a dados de táxis de NYC com dados da NOAA ISD 
|Bloco de Notas        | Descrição                                    |
|----------------|------------------------------------------------|
|[Dados de viagens de táxis enriquecidos com dados meteorológicos - Pandas](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-join/04-nyc-taxi-join-weather-in-pandas.ipynb) | Carregar dados de táxis verde NYC (mais de 1 mês) e enriquecê-lo com dados de Meteorologia num Pandas dataframe. Este exemplo substitui o método `get_pandas_limit` e faz o balanceamento de desempenho de carregamento de dados com a quantidade de dados.|
|[Dados de viagens de táxis enriquecidos com dados meteorológicos – Spark](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-join/03-nyc-taxi-join-weather-in-spark.ipynb) | Carregar dados de táxis verde NYC e enriquecê-lo com dados de Meteorologia, no Spark dataframe.  |

## <a name="next-steps"></a>Passos Seguintes

* [Tutorial: Regressão de modelagem com aprendizagem automática e um conjunto de dados aberto](tutorial-opendatasets-automl.md)
* [Python SDK para conjuntos de dados abertos](https://aka.ms/open-datasets-api)
* [Catálogo de conjuntos de dados aberto do Azure](https://azure.microsoft.com/services/open-datasets/catalog/)
