---
title: O que são conjuntos de dados abertos? Conjuntos de dados públicos com curadoria
titleSuffix: Azure Open Datasets
description: Conheça os Conjuntos de Dados Abertos do Azure, conjuntos de dados curados do domínio público, como o tempo, o recenseamento, as férias e a localização para enriquecer soluções preditivas.
ms.service: open-datasets
ms.topic: overview
author: cjgronlund
ms.author: cgronlun
ms.date: 05/06/2020
ms.openlocfilehash: cc7d3a7ba5927730a68b704d61101a9dba447f2f
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96493972"
---
# <a name="what-are-azure-open-datasets-and-how-can-you-use-them"></a>Quais são os conjuntos de dados Azure Open e como pode usá-los?

[Os conjuntos de dados Azure Open](https://azure.microsoft.com/services/open-datasets/) são conjuntos de dados públicos com curadoria que pode utilizar para adicionar funcionalidades específicas de cenários a soluções de machine learning para modelos mais precisos. Os Conjuntos de Dados Abertos estão na nuvem no Microsoft Azure e estão integrados no Azure Machine Learning e prontamente disponíveis para Azure Databricks e Machine Learning Studio (clássico). Também pode aceder aos conjuntos de dados através de APIs e usá-los em outros produtos, como Power BI e Azure Data Factory.

Os conjuntos de dados incluem dados de domínio público para o tempo, recenseamento, feriados, segurança pública e localização que o ajudam a treinar modelos de aprendizagem automática e a enriquecer soluções preditivas. Também pode partilhar os seus conjuntos de dados públicos em Conjuntos de Dados Azure Open. 

![Componentes Azure Open Datasets](./media/overview-what-are-open-datasets/open-datasets-components.png)

## <a name="curated-prepared-datasets"></a>Conjuntos de dados curados e preparados
Conjuntos de dados públicos abertos com curadoria em Azure Open Datasets são otimizados para consumo em fluxos de trabalho de aprendizagem automática. 

Para ver todos os conjuntos de dados disponíveis, aceda ao [Catálogo Azure Open Datasets](https://azure.microsoft.com/services/open-datasets/catalog/).

Os cientistas de dados passam muitas vezes a maior parte do seu tempo a limpar e a preparar dados para análises avançadas. Os Conjuntos de Dados Abertos são copiados para a nuvem Azure e pré-processados para economizar tempo. A intervalos regulares, os dados são retirados das fontes, tais como por uma ligação FTP à Administração Oceânica e Atmosférica Nacional (NOAA). Em seguida, os dados são analisados em um formato estruturado, e depois enriquecidos como apropriado com características como código POSTAL ou localização da estação meteorológica mais próxima.

Os conjuntos de dados são co-mostados com computação em nuvem em Azure, facilitando o acesso e manipulação.  

Seguem-se exemplos de conjuntos de dados disponíveis. 

### <a name="weather-data"></a>Dados meteorológicos
 
|Conjunto de dados         | Notebooks     | Descrição                                    |
|----------------|---------------|------------------------------------------------|
|[Dados integrados de superfície da NOAA (ISD)](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/) | [Azure Notebooks](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/?tab=data-access#AzureNotebooks) <br> [Azure Databricks](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/?tab=data-access#AzureDatabricks) | Dados meteorológicos por hora a nível mundial da NOAA com a melhor cobertura espacial na América do Norte, Europa, Austrália e partes da Ásia. Atualizado diariamente. |
|[Sistema de Previsão Global DA NOAA (GFS)](https://azure.microsoft.com/services/open-datasets/catalog/noaa-global-forecast-system/) | [Azure Notebooks](https://azure.microsoft.com/services/open-datasets/catalog/noaa-global-forecast-system/?tab=data-access#AzureNotebooks) <br> [Azure Databricks](https://azure.microsoft.com/services/open-datasets/catalog/noaa-global-forecast-system/?tab=data-access#AzureDatabricks) | 15 dias de previsão do tempo por hora da NOAA. Atualizado diariamente. |

### <a name="calendar-data"></a>Dados do calendário

|Conjunto de dados         | Notebooks     | Descrição                                    |
|----------------|---------------|------------------------------------------------|
|[Feriados](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays/) | [Azure Notebooks](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays/?tab=data-access#AzureNotebooks) <br> [Azure Databricks](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays/?tab=data-access#AzureDatabricks) | Dados de feriados públicos a nível mundial, abrangendo 41 países ou regiões entre 1970 e 2099. Inclui o país e se a maioria das pessoas pagou folgas. |

## <a name="access-to-datasets"></a>Acesso aos conjuntos de dados  
Com uma conta Azure, pode aceder a conjuntos de dados abertos utilizando código ou através da interface de serviço Azure. Os dados estão equipados com recursos de computação em nuvem Azure para utilização na sua solução de aprendizagem automática.  

Os Conjuntos de Dados Abertos estão disponíveis através da UI e SDK de aprendizagem automática Azure. O Open Datasets também fornece cadernos Azure e cadernos Azure Databricks que pode usar para ligar dados ao Azure Machine Learning e aos Dados Azure Databricks. Os conjuntos de dados também podem ser acedidos através de um Python SDK. 

No entanto, não precisa de uma conta Azure para aceder a Conjuntos de Dados Abertos; você pode acessá-los a partir de qualquer ambiente Python com ou sem Faísca.

## <a name="request-or-contribute-datasets"></a>Solicitar ou contribuir conjuntos de dados

Se não conseguir encontrar os dados que pretende, envie-nos um e-mail para [solicitar um conjunto de dados](mailto:aod@microsoft.com?Subject=Request%20dataset%3A%20%3Creplace%20with%20dataset%20name%3E&Body=%0AYour%20name%20and%20institution%3A%20%0A%0ADataset%20name%3A%0A%20%0ADataset%20description%3A%20%0A%3Cfill%20in%20a%20brief%20description%20and%20share%20any%20web%20links%20of%20the%20dataset%3E%20%0A%0ADataset%20size%3A%20%0A%3Chow%20much%20space%20does%20the%20dataset%20need%20today%20and%20how%20much%20is%20it%20expected%20to%20grow%20each%20year%3E%20%0A%0ADataset%20file%20formats%3A%20%0A%3Ccurrent%20dataset%20file%20formats%2C%20and%20optionally%2C%20any%20formats%20that%20the%20dataset%20must%20be%20transformed%20to%20for%20easy%20access%3E%0A%0ALicense%3A%20%0A%3Cwhat%20is%20the%20license%20or%20terms%20and%20conditions%20governing%20the%20distribution%20of%20this%20dataset%3E%0A%0AUse%20cases%3A%20%0A%3CExplain%20some%20common%20use%20of%20the%20dataset.%20E.g.%20weather%20dataset%20can%20be%20useful%20in%20demand%20forecasting%20and%20predictive%20maintenance%20scenarios%3E%20%0A%0AAny%20additional%20information%20you%20want%20us%20to%20know%3A%0A) ou contribuir com um conjunto de [dados](mailto:aod@microsoft.com?Subject=Contribute%20dataset%3A%20%3Creplace%20with%20dataset%20name%3E&Body=%0AYour%20name%20and%20institution%3A%20%0A%0ADataset%20name%3A%0A%20%0ADataset%20description%3A%20%0A%3Cfill%20in%20a%20brief%20description%20and%20share%20any%20web%20links%20of%20the%20dataset%3E%20%0A%0ADataset%20size%3A%20%0A%3Chow%20much%20space%20does%20the%20dataset%20need%20today%20and%20how%20much%20is%20it%20expected%20to%20grow%20each%20year%3E%20%0A%0ADataset%20file%20formats%3A%20%0A%3Ccurrent%20dataset%20file%20formats%2C%20and%20optionally%2C%20any%20formats%20that%20the%20dataset%20must%20be%20transformed%20to%20for%20easy%20access%3E%0A%0ALicense%3A%20%0A%3Cwhat%20is%20the%20license%20or%20terms%20and%20conditions%20governing%20the%20distribution%20of%20this%20dataset%3E%0A%0AUse%20cases%3A%20%0A%3CExplain%20some%20common%20use%20of%20the%20dataset.%20E.g.%20weather%20dataset%20can%20be%20useful%20in%20demand%20forecasting%20and%20predictive%20maintenance%20scenarios%3E%20%0A%0AAny%20additional%20information%20you%20want%20us%20to%20know%3A%0A). 

## <a name="next-steps"></a>Passos seguintes
* [Caderno de amostras](samples.md)
* [Tutorial: Modelação de regressão com dados de táxi de NY](../machine-learning/tutorial-auto-train-models.md?context=azure%2fopen-datasets%2fcontext%2fopen-datasets-context)
* [Python SDK para conjuntos de dados abertos](/python/api/azureml-opendatasets/)
