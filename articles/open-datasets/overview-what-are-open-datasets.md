---
title: Quais são os conjuntos de dados abertos? Conjuntos de dados públicos organizados
titleSuffix: Azure Open Datasets (preview)
description: Saiba mais sobre o Azure Open conjuntos de dados (pré-visualização), organizados conjuntos de dados de domínio público que estão prontos para utilizar em soluções de machine learning e análise. Conjuntos de dados incluem dados públicos, como informações sobre o clima, recenseamento, feriados e a localização para o ajudar a melhorar as soluções de preditiva.
ms.service: open-datasets
ms.topic: overview
author: cjgronlund
ms.author: cgronlun
ms.date: 05/02/2019
ms.openlocfilehash: b01ad5a4fd8808c1e841ba2fb47ea06ece011010
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67051059"
---
# <a name="what-are-azure-open-datasets-preview-and-how-can-you-use-them"></a>O que são conjuntos de dados aberto do Azure (pré-visualização) e como pode utilizar?

[Conjuntos de dados do Azure abertos](https://azure.microsoft.com/services/open-datasets/) são organizados conjuntos de dados públicos que pode usar para adicionar recursos específicos de cenário para soluções de aprendizagem automática para modelos mais precisos. Conjuntos de dados abertos estão na cloud no Microsoft Azure e estão prontamente disponíveis para o Azure Databricks, o serviço Machine Learning e o Machine Learning Studio. Também pode aceder os conjuntos de dados através de APIs e usá-los em outros produtos, como o Power BI e o Azure Data Factory.

Conjuntos de dados incluem dados de domínio público para informações sobre o clima, censos, feriados, segurança pública e localização que o ajudam a preparar modelos de aprendizagem automática e enriquecer soluções preditiva. Também pode partilhar os conjuntos de dados públicos em conjuntos de dados aberto do Azure. 

![Componentes de Open conjuntos de dados do Azure](./media/overview-what-are-open-datasets/open-datasets-components.png)

## <a name="curated-prepared-datasets"></a>Conjuntos de dados estruturados, preparados
Organizado abertos conjuntos de dados públicos em conjuntos de dados do Azure abrir estão otimizados para consumo em fluxos de trabalho do machine learning. 

Os cientistas de dados, muitas vezes, passam a maior parte do seu tempo a limpar e preparar dados para análise avançada. Abra os conjuntos de dados são copiados para a cloud do Azure e pré-processados lhe poupar tempo. Em intervalos regulares dados são extraídos de origens, como por uma ligação de FTP para a National Oceanic and Atmospheric Administration (NOAA), analisado num formato estruturado e, em seguida, enriquecidos conforme adequado com funcionalidades como código postal ou localização do estação de Meteorologia mais próxima.

Conjuntos de dados estão cohosted com a computação em nuvem do Azure garantindo o acesso e a manipulação mais fácil.  

Seguem-se exemplos de conjuntos de dados disponíveis. 

### <a name="weather-data"></a>Dados meteorológicos
 
|Conjunto de dados         | Blocos de notas     | Descrição                                    |
|----------------|---------------|------------------------------------------------|
|[Dados do Surface NOAA integrado (ISD)](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/) | [Azure Notebooks](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/?tab=data-access#AzureNotebooks) <br> [Azure Databricks](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/?tab=data-access#AzureDatabricks) | Em todo o mundo por hora dados de meteorologia da NOAA com a melhor cobertura geográfico na América do Norte, Europa, Austrália e partes da Ásia. Atualizados diariamente. |
|[Previsão de NOAA Global sistema (GFS)](https://azure.microsoft.com/services/open-datasets/catalog/noaa-global-forecast-system/) | [Azure Notebooks](https://azure.microsoft.com/services/open-datasets/catalog/noaa-global-forecast-system/?tab=data-access#AzureNotebooks) <br> [Azure Databricks](https://azure.microsoft.com/services/open-datasets/catalog/noaa-global-forecast-system/?tab=data-access#AzureDatabricks) | 15 dias dos EUA por hora previsão meteorológica dados da NOAA. Atualizados diariamente. |

### <a name="calendar-data"></a>Dados de calendário

|Conjunto de dados         | Blocos de notas     | Descrição                                    |
|----------------|---------------|------------------------------------------------|
|[Feriados públicos](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays/) | [Azure Notebooks](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays/?tab=data-access#AzureNotebooks) <br> [Azure Databricks](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays/?tab=data-access#AzureDatabricks) | Dados de feriado público em todo o mundo, que abrange 41 países ou regiões a partir de 1970 para 2099. Inclui o país e se a maioria das pessoas tem valeu tempo. |

## <a name="access-to-datasets"></a>Acesso a conjuntos de dados  
Com uma conta do Azure, pode aceder a conjuntos de dados abertos com o código ou através do Azure service interface. Os dados estão colocalizados com recursos de computação em nuvem do Azure para utilização na sua solução de machine learning.  

Conjuntos de dados aberto fornece os blocos de notas blocos de notas do Azure e o Azure Databricks que pode utilizar para ligar os dados ao serviço Azure Machine Learning e o Azure Databricks. Conjuntos de dados também podem ser acedidos através de um SDK de Python. 

No entanto, não precisa de uma conta do Azure para aceder a conjuntos de dados abertos; pode acessá-los em qualquer ambiente de Python sem ou sem o Spark.

## <a name="request-or-contribute-datasets"></a>Pedir ou contribuir com conjuntos de dados

Se não conseguir encontrar os dados desejar, enviar-num e-mail [um conjunto de dados do pedido](mailto:aod@microsoft.com?Subject=Request%20dataset%3A%20%3Creplace%20with%20dataset%20name%3E&Body=%0AYour%20name%20and%20institution%3A%20%0A%0ADataset%20name%3A%0A%20%0ADataset%20description%3A%20%0A%3Cfill%20in%20a%20brief%20description%20and%20share%20any%20web%20links%20of%20the%20dataset%3E%20%0A%0ADataset%20size%3A%20%0A%3Chow%20much%20space%20does%20the%20dataset%20need%20today%20and%20how%20much%20is%20it%20expected%20to%20grow%20each%20year%3E%20%0A%0ADataset%20file%20formats%3A%20%0A%3Ccurrent%20dataset%20file%20formats%2C%20and%20optionally%2C%20any%20formats%20that%20the%20dataset%20must%20be%20transformed%20to%20for%20easy%20access%3E%0A%0ALicense%3A%20%0A%3Cwhat%20is%20the%20license%20or%20terms%20and%20conditions%20governing%20the%20distribution%20of%20this%20dataset%3E%0A%0AUse%20cases%3A%20%0A%3CExplain%20some%20common%20use%20of%20the%20dataset.%20E.g.%20weather%20dataset%20can%20be%20useful%20in%20demand%20forecasting%20and%20predictive%20maintenance%20scenarios%3E%20%0A%0AAny%20additional%20information%20you%20want%20us%20to%20know%3A%0A) ou [contribuir com um conjunto de dados](mailto:aod@microsoft.com?Subject=Contribute%20dataset%3A%20%3Creplace%20with%20dataset%20name%3E&Body=%0AYour%20name%20and%20institution%3A%20%0A%0ADataset%20name%3A%0A%20%0ADataset%20description%3A%20%0A%3Cfill%20in%20a%20brief%20description%20and%20share%20any%20web%20links%20of%20the%20dataset%3E%20%0A%0ADataset%20size%3A%20%0A%3Chow%20much%20space%20does%20the%20dataset%20need%20today%20and%20how%20much%20is%20it%20expected%20to%20grow%20each%20year%3E%20%0A%0ADataset%20file%20formats%3A%20%0A%3Ccurrent%20dataset%20file%20formats%2C%20and%20optionally%2C%20any%20formats%20that%20the%20dataset%20must%20be%20transformed%20to%20for%20easy%20access%3E%0A%0ALicense%3A%20%0A%3Cwhat%20is%20the%20license%20or%20terms%20and%20conditions%20governing%20the%20distribution%20of%20this%20dataset%3E%0A%0AUse%20cases%3A%20%0A%3CExplain%20some%20common%20use%20of%20the%20dataset.%20E.g.%20weather%20dataset%20can%20be%20useful%20in%20demand%20forecasting%20and%20predictive%20maintenance%20scenarios%3E%20%0A%0AAny%20additional%20information%20you%20want%20us%20to%20know%3A%0A). 

## <a name="next-steps"></a>Passos Seguintes
* [Bloco de notas de exemplo](samples.md)
* [Tutorial: Regressão de modelagem com dados de táxis NY](tutorial-opendatasets-automl.md)
* [Python SDK para conjuntos de dados abertos](https://aka.ms/open-datasets-api)
