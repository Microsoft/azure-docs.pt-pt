---
title: O que são conjuntos de valores abertos? Conjuntos de coletados públicos
titleSuffix: Azure Open Datasets (preview)
description: Saiba mais sobre os conjuntos de valores abertos do Azure (visualização), conjuntos de informações do domínio público que estão prontos para uso nas soluções de aprendizado de máquina e análise. DataSets incluem dados públicos como clima, censo, feriados e local para ajudá-lo a enriquecer soluções preditivas.
ms.service: open-datasets
ms.topic: overview
author: cjgronlund
ms.author: cgronlun
ms.date: 05/02/2019
ms.openlocfilehash: 7fa8562c466c81ebc05360bf06cdde1ee29bed9e
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68598983"
---
# <a name="what-are-azure-open-datasets-preview-and-how-can-you-use-them"></a>O que são conjuntos de valores abertos do Azure (versão prévia) e como você pode usá-los?

Os conjuntos de itens [abertos do Azure](https://azure.microsoft.com/services/open-datasets/) são conjuntos de informações públicos estruturados que você pode usar para adicionar recursos específicos do cenário a soluções de aprendizado de máquina para modelos mais precisos. Os conjuntos de itens abertos estão na nuvem em Microsoft Azure e estão prontamente disponíveis para Azure Databricks, Machine Learning serviço e Machine Learning Studio. Você também pode acessar os conjuntos de os por meio de APIs e usá-los em outros produtos, como Power BI e Azure Data Factory.

DataSets incluem dados de domínio público para clima, censo, feriados, segurança pública e local que ajudam você a treinar modelos de aprendizado de máquina e enriquecer soluções preditivas. Você também pode compartilhar seus conjuntos de valores públicos nos conjuntos de valores abertos do Azure. 

![Componentes do Azure Open DataSets](./media/overview-what-are-open-datasets/open-datasets-components.png)

## <a name="curated-prepared-datasets"></a>Conjuntos de coletados e preparados
Os conjuntos de valores públicos abertos estruturados nos conjuntos de valores abertos do Azure são otimizados para consumo em fluxos de trabalho de aprendizado de máquina. 

Os cientistas de dados geralmente passam a maior parte do tempo de limpeza e preparação de dados para análise avançada. Os conjuntos de valores abertos são copiados para a nuvem do Azure e pré-processados para poupar tempo. Em intervalos regulares, os dados são extraídos das fontes, como por uma conexão de FTP para a NOAA (National Oceanic and atmosférica Administration), analisados em um formato estruturado e, em seguida, aprimorados conforme apropriado com recursos como CEP ou local do a estação do clima mais próxima.

Os conjuntos de itens são cohospedados com computação em nuvem no Azure, facilitando o acesso e a manipulação.  

Veja a seguir exemplos de conjuntos de valores disponíveis. 

### <a name="weather-data"></a>Dados meteorológicos
 
|Conjunto de dados         | Blocos de notas     | Descrição                                    |
|----------------|---------------|------------------------------------------------|
|[NOAA de dados de superfície integrada (ISD)](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/) | [Azure Notebooks](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/?tab=data-access#AzureNotebooks) <br> [Azure Databricks](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/?tab=data-access#AzureDatabricks) | Dados de clima mundiais por hora do NOAA com a melhor cobertura espacial em América do Norte, Europa, Austrália e partes da Ásia. Atualizado diariamente. |
|[GFS (NOAA global pregnóstico System)](https://azure.microsoft.com/services/open-datasets/catalog/noaa-global-forecast-system/) | [Azure Notebooks](https://azure.microsoft.com/services/open-datasets/catalog/noaa-global-forecast-system/?tab=data-access#AzureNotebooks) <br> [Azure Databricks](https://azure.microsoft.com/services/open-datasets/catalog/noaa-global-forecast-system/?tab=data-access#AzureDatabricks) | dados de previsão do clima por hora dos EUA de 15 dias do NOAA. Atualizado diariamente. |

### <a name="calendar-data"></a>Dados de calendário

|Conjunto de dados         | Blocos de notas     | Descrição                                    |
|----------------|---------------|------------------------------------------------|
|[Feriados públicos](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays/) | [Azure Notebooks](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays/?tab=data-access#AzureNotebooks) <br> [Azure Databricks](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays/?tab=data-access#AzureDatabricks) | Dados de Natal públicos em todo o mundo, cobrindo 41 países ou regiões de 1970 a 2099. Inclui o país e se a maioria das pessoas pagou o tempo limite. |

## <a name="access-to-datasets"></a>Acesso a conjuntos de os  
Com uma conta do Azure, você pode acessar conjuntos de valores abertos usando código ou por meio da interface de serviço do Azure. Os dados são colocados com recursos de computação na nuvem do Azure para uso em sua solução de aprendizado de máquina.  

Os conjuntos de dados abertos fornecem Azure Notebooks e Azure Databricks notebooks que você pode usar para conectar dados a Azure Machine Learning serviço e Azure Databricks. Os conjuntos de linhas também podem ser acessados por meio de um SDK do Python. 

No entanto, você não precisa de uma conta do Azure para acessar conjuntos de os abertos; Você pode acessá-los de qualquer ambiente Python sem ou sem o Spark.

## <a name="request-or-contribute-datasets"></a>Solicitar ou contribuir com conjuntos de os

Se você não encontrar os dados desejados, envie-nos um email para [solicitar um DataSet](mailto:aod@microsoft.com?Subject=Request%20dataset%3A%20%3Creplace%20with%20dataset%20name%3E&Body=%0AYour%20name%20and%20institution%3A%20%0A%0ADataset%20name%3A%0A%20%0ADataset%20description%3A%20%0A%3Cfill%20in%20a%20brief%20description%20and%20share%20any%20web%20links%20of%20the%20dataset%3E%20%0A%0ADataset%20size%3A%20%0A%3Chow%20much%20space%20does%20the%20dataset%20need%20today%20and%20how%20much%20is%20it%20expected%20to%20grow%20each%20year%3E%20%0A%0ADataset%20file%20formats%3A%20%0A%3Ccurrent%20dataset%20file%20formats%2C%20and%20optionally%2C%20any%20formats%20that%20the%20dataset%20must%20be%20transformed%20to%20for%20easy%20access%3E%0A%0ALicense%3A%20%0A%3Cwhat%20is%20the%20license%20or%20terms%20and%20conditions%20governing%20the%20distribution%20of%20this%20dataset%3E%0A%0AUse%20cases%3A%20%0A%3CExplain%20some%20common%20use%20of%20the%20dataset.%20E.g.%20weather%20dataset%20can%20be%20useful%20in%20demand%20forecasting%20and%20predictive%20maintenance%20scenarios%3E%20%0A%0AAny%20additional%20information%20you%20want%20us%20to%20know%3A%0A) ou [contribuir com um DataSet](mailto:aod@microsoft.com?Subject=Contribute%20dataset%3A%20%3Creplace%20with%20dataset%20name%3E&Body=%0AYour%20name%20and%20institution%3A%20%0A%0ADataset%20name%3A%0A%20%0ADataset%20description%3A%20%0A%3Cfill%20in%20a%20brief%20description%20and%20share%20any%20web%20links%20of%20the%20dataset%3E%20%0A%0ADataset%20size%3A%20%0A%3Chow%20much%20space%20does%20the%20dataset%20need%20today%20and%20how%20much%20is%20it%20expected%20to%20grow%20each%20year%3E%20%0A%0ADataset%20file%20formats%3A%20%0A%3Ccurrent%20dataset%20file%20formats%2C%20and%20optionally%2C%20any%20formats%20that%20the%20dataset%20must%20be%20transformed%20to%20for%20easy%20access%3E%0A%0ALicense%3A%20%0A%3Cwhat%20is%20the%20license%20or%20terms%20and%20conditions%20governing%20the%20distribution%20of%20this%20dataset%3E%0A%0AUse%20cases%3A%20%0A%3CExplain%20some%20common%20use%20of%20the%20dataset.%20E.g.%20weather%20dataset%20can%20be%20useful%20in%20demand%20forecasting%20and%20predictive%20maintenance%20scenarios%3E%20%0A%0AAny%20additional%20information%20you%20want%20us%20to%20know%3A%0A). 

## <a name="next-steps"></a>Passos Seguintes
* [Bloco de anotações de exemplo](samples.md)
* [Tutorial: Modelagem de regressão com dados de táxi de NY](tutorial-opendatasets-automl.md)
* [SDK do Python para conjuntos de valores abertos](/python/api/azureml-opendatasets/?view=azure-ml-py)
