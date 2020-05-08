---
title: O que são conjuntos de dados abertos? Conjuntos de dados públicos com curadoria
titleSuffix: Azure Open Datasets
description: Conheça o Azure Open Datasets, conjuntos de dados com curadoria do domínio público, como meteorologia, recenseamento, férias e localização para enriquecer soluções preditivas.
ms.service: open-datasets
ms.topic: overview
author: cjgronlund
ms.author: cgronlun
ms.date: 05/06/2020
ms.openlocfilehash: 24f5a671076a44db382ac37e2f6532e5f0f2a3ae
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/08/2020
ms.locfileid: "82930391"
---
# <a name="what-are-azure-open-datasets-and-how-can-you-use-them"></a>O que são Conjuntos de Dados Abertos Azure e como pode usá-los?

[Os Conjuntos de Dados Abertos do Azure](https://azure.microsoft.com/services/open-datasets/) são conjuntos de dados públicos com curadoria que pode utilizar para adicionar funcionalidades específicas ao cenário a soluções de aprendizagem automática para modelos mais precisos. Os Conjuntos de Dados Abertos estão na nuvem do Microsoft Azure e estão integrados no Azure Machine Learning e prontamente disponíveis para o Azure Databricks e Machine Learning Studio (clássico). Também pode aceder aos conjuntos de dados através de APIs e usá-los em outros produtos, como power BI e Azure Data Factory.

Os conjuntos de dados incluem dados de domínio público para meteorologia, recenseamento, férias, segurança pública e localização que o ajudam a treinar modelos de aprendizagem automática e a enriquecer soluções preditivas. Também pode partilhar os seus conjuntos de dados públicos em Conjuntos de Dados Abertos Do Azure. 

![Componentes azure Open Datasets](./media/overview-what-are-open-datasets/open-datasets-components.png)

## <a name="curated-prepared-datasets"></a>Conjuntos de dados curados e preparados
Conjuntos de dados públicos abertos com curadoria em Conjuntos de Dados Abertos Do Azure são otimizados para consumo em fluxos de trabalho de aprendizagem automática. 

Para ver todos os conjuntos de dados disponíveis, vá ao Catálogo de Conjuntos de [Dados Abertos do Azure](https://azure.microsoft.com/services/open-datasets/catalog/).

Os cientistas de dados passam frequentemente a maior parte do seu tempo a limpar e a preparar dados para análises avançadas. Os Conjuntos de Dados Abertos são copiados para a nuvem Azure e pré-processados para economizar tempo. Em intervalos regulares, os dados são retirados das fontes, como por exemplo por uma ligação FTP à Administração Oceânica e Atmosférica Nacional (NOAA). Em seguida, os dados são analisados num formato estruturado e, em seguida, enriquecidos conforme apropriado com funcionalidades como código POSTAL ou localização da estação meteorológica mais próxima.

Os conjuntos de dados são coorganizados com computação em nuvem em Azure, facilitando o acesso e manipulação.  

Seguem-se exemplos de conjuntos de dados disponíveis. 

### <a name="weather-data"></a>Dados meteorológicos
 
|Conjunto de dados         | Notebooks     | Descrição                                    |
|----------------|---------------|------------------------------------------------|
|[Dados de superfície integrados da NOAA (ISD)](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/) | [Azure Notebooks](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/?tab=data-access#AzureNotebooks) <br> [Azure Databricks](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/?tab=data-access#AzureDatabricks) | Dados meteorológicos de hora a hora a nível mundial da NOAA com a melhor cobertura espacial na América do Norte, Europa, Austrália e partes da Ásia. Atualizado diariamente. |
|[Noaa Global Forecast System (GFS)](https://azure.microsoft.com/services/open-datasets/catalog/noaa-global-forecast-system/) | [Azure Notebooks](https://azure.microsoft.com/services/open-datasets/catalog/noaa-global-forecast-system/?tab=data-access#AzureNotebooks) <br> [Azure Databricks](https://azure.microsoft.com/services/open-datasets/catalog/noaa-global-forecast-system/?tab=data-access#AzureDatabricks) | Dados de previsão meteorológica de 15 dias por hora da NOAA. Atualizado diariamente. |

### <a name="calendar-data"></a>Dados do calendário

|Conjunto de dados         | Notebooks     | Descrição                                    |
|----------------|---------------|------------------------------------------------|
|[Feriados](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays/) | [Azure Notebooks](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays/?tab=data-access#AzureNotebooks) <br> [Azure Databricks](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays/?tab=data-access#AzureDatabricks) | Dados mundiais de feriados, abrangendo 41 países ou regiões entre 1970 e 2099. Inclui o país e se a maioria das pessoas pagou férias. |

## <a name="access-to-datasets"></a>Acesso a conjuntos de dados  
Com uma conta Azure, pode aceder a conjuntos de dados abertos utilizando código ou através da interface de serviço Azure. Os dados são co-localizados com recursos de computação em nuvem Azure para uso na sua solução de machine learning.  

Os Conjuntos de Dados Abertos estão disponíveis através do Azure Machine Learning UI e SDK. Os Conjuntos de Dados Abertos também fornecem cadernos Azure e Azure Databricks que pode utilizar para ligar dados a Azure Machine Learning e Azure Databricks. Os conjuntos de dados também podem ser acedidos através de um SDK Python. 

No entanto, não precisa de uma conta Azure para aceder a Conjuntos de Dados Abertos; pode acessá-los a partir de qualquer ambiente Python com ou sem Faísca.

## <a name="request-or-contribute-datasets"></a>Solicitar ou contribuir conjuntos de dados

Se não encontrar os dados que deseja, envie-nos um e-mail para [solicitar um conjunto](mailto:aod@microsoft.com?Subject=Request%20dataset%3A%20%3Creplace%20with%20dataset%20name%3E&Body=%0AYour%20name%20and%20institution%3A%20%0A%0ADataset%20name%3A%0A%20%0ADataset%20description%3A%20%0A%3Cfill%20in%20a%20brief%20description%20and%20share%20any%20web%20links%20of%20the%20dataset%3E%20%0A%0ADataset%20size%3A%20%0A%3Chow%20much%20space%20does%20the%20dataset%20need%20today%20and%20how%20much%20is%20it%20expected%20to%20grow%20each%20year%3E%20%0A%0ADataset%20file%20formats%3A%20%0A%3Ccurrent%20dataset%20file%20formats%2C%20and%20optionally%2C%20any%20formats%20that%20the%20dataset%20must%20be%20transformed%20to%20for%20easy%20access%3E%0A%0ALicense%3A%20%0A%3Cwhat%20is%20the%20license%20or%20terms%20and%20conditions%20governing%20the%20distribution%20of%20this%20dataset%3E%0A%0AUse%20cases%3A%20%0A%3CExplain%20some%20common%20use%20of%20the%20dataset.%20E.g.%20weather%20dataset%20can%20be%20useful%20in%20demand%20forecasting%20and%20predictive%20maintenance%20scenarios%3E%20%0A%0AAny%20additional%20information%20you%20want%20us%20to%20know%3A%0A) de dados ou contribuir com um conjunto de [dados](mailto:aod@microsoft.com?Subject=Contribute%20dataset%3A%20%3Creplace%20with%20dataset%20name%3E&Body=%0AYour%20name%20and%20institution%3A%20%0A%0ADataset%20name%3A%0A%20%0ADataset%20description%3A%20%0A%3Cfill%20in%20a%20brief%20description%20and%20share%20any%20web%20links%20of%20the%20dataset%3E%20%0A%0ADataset%20size%3A%20%0A%3Chow%20much%20space%20does%20the%20dataset%20need%20today%20and%20how%20much%20is%20it%20expected%20to%20grow%20each%20year%3E%20%0A%0ADataset%20file%20formats%3A%20%0A%3Ccurrent%20dataset%20file%20formats%2C%20and%20optionally%2C%20any%20formats%20that%20the%20dataset%20must%20be%20transformed%20to%20for%20easy%20access%3E%0A%0ALicense%3A%20%0A%3Cwhat%20is%20the%20license%20or%20terms%20and%20conditions%20governing%20the%20distribution%20of%20this%20dataset%3E%0A%0AUse%20cases%3A%20%0A%3CExplain%20some%20common%20use%20of%20the%20dataset.%20E.g.%20weather%20dataset%20can%20be%20useful%20in%20demand%20forecasting%20and%20predictive%20maintenance%20scenarios%3E%20%0A%0AAny%20additional%20information%20you%20want%20us%20to%20know%3A%0A). 

## <a name="next-steps"></a>Passos seguintes
* [Caderno de amostras](samples.md)
* [Tutorial: Modelo de regressão com dados de táxi de NY](/azure/machine-learning/tutorial-auto-train-models?context=azure/open-datasets/context/open-datasets-context)
* [Python SDK para conjuntos de dados abertos](/python/api/azureml-opendatasets/?view=azure-ml-py)
