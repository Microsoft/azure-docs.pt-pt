---
title: Exemplo Cadernos Jupyter usando dados NOAA
titleSuffix: Azure Open Datasets
description: Use exemplo os cadernos Jupyter para conjuntos de dados abertos Azure para aprender a carregar conjuntos de dados abertos e usá-los para enriquecer dados de demonstração. As técnicas incluem o uso de Spark e Pandas para processar dados.
ms.service: open-datasets
ms.topic: sample
author: cjgronlund
ms.author: cgronlun
ms.date: 05/06/2020
ms.openlocfilehash: eddfcc36c6440ce155d7b9d81031db449cfa8d2b
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96492442"
---
# <a name="example-jupyter-notebooks-show-how-to-enrich-data-with-open-datasets"></a>Exemplo Os cadernos jupyter mostram como enriquecer dados com conjuntos de dados abertos 
O exemplo Os cadernos jupyter para Azure Open Datasets mostram-lhe como carregar conjuntos de dados abertos e usá-los para enriquecer dados de demonstração. As técnicas incluem o uso de Apache Spark e Pandas para processar dados.

>[!IMPORTANT]
>Ao trabalhar num ambiente não-Spark, o Open Datasets permite o descarregamento de apenas um mês de dados de cada vez com determinadas classes, de modo a evitar o MemoryError com grandes conjuntos de dados.

## <a name="load-noaa-integrated-surface-database-isd-data"></a>Carregar dados integrados da Base de Dados de Superfície (ISD) da NOAA 
|Bloco de Notas        | Descrição                                    |
|----------------|------------------------------------------------|
|[Carregue um mês recente de dados meteorológicos num dataframe do Pandas](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-access/02-weather-to-pandas-dataframe.ipynb) | Saiba como carregar dados meteorológicos históricos no seu dataframe favorito dos Pandas. |
|[Carregue um mês recente de dados meteorológicos num dataframe spark](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-access/01-weather-to-spark-dataframe.ipynb) | Saiba como carregar dados meteorológicos históricos no seu dataframe favorito da Spark.  |

## <a name="join-demo-data-with-noaa-isd-data"></a>Junte os dados de demonstração com os dados do ISD DA NOAA 
|Bloco de Notas        | Descrição                                    |
|----------------|------------------------------------------------|
|[Junte os dados da demonstração com os dados meteorológicos - Pandas](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-join/02-weather-join-in-pandas.ipynb) | Junte-se a um conjunto de dados de demonstração de 1 mês de locais de sensores com leituras meteorológicas num dataframe de Pandas.  |
|[Junte dados de demonstração com dados meteorológicos - Faísca](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-join/01-weather-join-in-spark.ipynb) | Junte-se a um conjunto de dados de demonstração de locais de sensores com leituras meteorológicas num dataframe spark. |

## <a name="join-nyc-taxi-data-with-noaa-isd-data"></a>Junte-se aos dados do táxi de NYC com os dados do ISD DA NOAA 
|Bloco de Notas        | Descrição                                    |
|----------------|------------------------------------------------|
|[Dados da viagem de táxi enriquecidos com dados meteorológicos - Pandas](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-join/04-nyc-taxi-join-weather-in-pandas.ipynb) | Carregue os dados do táxi verde nyc (mais de 1 mês) e enriqueça-os com dados meteorológicos num dataframe pandas. Este exemplo sobrepõe-se ao método `get_pandas_limit` e equilibra o desempenho da carga de dados com a quantidade de dados.|
|[Dados da viagem de táxi enriquecidos com dados meteorológicos – Faísca](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-join/03-nyc-taxi-join-weather-in-spark.ipynb) | Carregue os dados do táxi verde nyc e enriqueça-os com dados meteorológicos, no dataframe spark.  |

## <a name="next-steps"></a>Passos seguintes

* [Tutorial: Modelação de regressão com machine learning automatizado e um conjunto de dados aberto](../machine-learning/tutorial-auto-train-models.md?context=azure%2fopen-datasets%2fcontext%2fopen-datasets-context)
* [Python SDK para conjuntos de dados abertos](/python/api/azureml-opendatasets/azureml.opendatasets)
* [Catálogo do Azure Open Datasets](https://azure.microsoft.com/services/open-datasets/catalog/)
* [Criar conjunto de dados de aprendizagem de máquinas Azure a partir do Conjunto de Dados Abertos](how-to-create-azure-machine-learning-dataset-from-open-dataset.md)
