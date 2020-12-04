---
title: Ligue uma fábrica de dados ao Azure Purview
description: Saiba como ligar uma Fábrica de Dados ao Azure Purview
services: data-factory
ms.author: lle
author: lrtoyou1223
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom:
- seo-lt-2019
- references_regions
ms.date: 12/3/2020
ms.openlocfilehash: 94b2ed8a25ca5cc837f6677dea1c0bbb54225fcd
ms.sourcegitcommit: c4246c2b986c6f53b20b94d4e75ccc49ec768a9a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/04/2020
ms.locfileid: "96603350"
---
# <a name="connect-data-factory-to-azure-purview-preview"></a>Ligue a Fábrica de Dados à Azure Purview (Pré-visualização)
[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Este artigo explicará como ligar a fábrica de dados ao Azure Purview e como reportar a linhagem de dados das atividades ADF Copiar dados, fluxo de dados e executar pacote SSIS.

## <a name="connect-data-factory-to-azure-purview"></a>Ligue a fábrica de dados à Azure Purview
O Azure Purview é um novo serviço de cloud para uso pelos utilizadores de dados que gerem centralmente a governação de dados em todos os seus dados, abrangendo ambientes em nuvem e on-prem. Pode ligar a sua fábrica de dados ao Azure Purview e a ligação permite-lhe alavancar o Azure Purview para capturar dados de linhagem do pacote Copy, Data flow e Execute SSIS. Para saber como registar a fábrica de dados em Azure Purview, consulte [Como ligar a Azure Data Factory e a Azure Purview](https://docs.microsoft.com/azure/purview/how-to-link-azure-data-factory). 

## <a name="report-lineage-data-to-azure-purview"></a>Reportar dados de linhagem à Azure Purview
Quando os clientes executam a atividade de copy, data flow ou executar pacoteS SSIS na fábrica de dados Azure, os clientes podem obter a relação de dependência e ter uma visão geral de alto nível de todo o processo de fluxo de trabalho entre fontes de dados e destino.
Para saber como recolher a linhagem da fábrica de dados Azure, consulte a [linhagem da fábrica de dados.](https://docs.microsoft.com/azure/purview/how-to-link-azure-data-factory#supported-azure-data-factory-activities)

## <a name="next-steps"></a>Próximos passos
[Guia do utilizador da linhagem do catálogo](https://docs.microsoft.com/azure/purview/catalog-lineage-user-guide)

[Tutorial: Empurrar dados de linhagem da Fábrica de Dados para a Azure Purview](turorial-push-lineage-to-purview.md)