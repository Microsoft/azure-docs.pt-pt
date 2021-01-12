---
title: Ligar um Data Factory ao Azure Purview
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
ms.openlocfilehash: 7dc05c88416bb2a23221029bc04c506271a86652
ms.sourcegitcommit: 48e5379c373f8bd98bc6de439482248cd07ae883
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/12/2021
ms.locfileid: "98108352"
---
# <a name="connect-data-factory-to-azure-purview-preview"></a>Ligue a Fábrica de Dados à Azure Purview (Pré-visualização)
[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Este artigo explicará como ligar a fábrica de dados ao Azure Purview e como reportar a linhagem de dados das atividades ADF Copiar dados, fluxo de dados e executar pacote SSIS.

## <a name="connect-data-factory-to-azure-purview"></a>Ligue a fábrica de dados à Azure Purview
O Azure Purview é um novo serviço de cloud para uso pelos utilizadores de dados que gerem centralmente a governação de dados em todos os seus dados, abrangendo ambientes em nuvem e on-prem. Pode ligar a sua fábrica de dados ao Azure Purview e a ligação permite-lhe alavancar o Azure Purview para capturar dados de linhagem do pacote Copy, Data flow e Execute SSIS. Para saber como registar a fábrica de dados em Azure Purview, consulte [Como ligar a Azure Data Factory e a Azure Purview](https://docs.microsoft.com/azure/purview/how-to-link-azure-data-factory). 

## <a name="report-lineage-data-to-azure-purview"></a>Reportar dados de linhagem à Azure Purview
Quando os clientes executam a atividade de copy, data flow ou executar pacote SSIS na Azure Data Factory, os clientes podem obter a relação de dependência e ter uma visão geral de alto nível de todo o processo de fluxo de trabalho entre fontes de dados e destino.
Para obter a linhagem da Azure Data Factory, consulte a linhagem da [fábrica de dados.](https://docs.microsoft.com/azure/purview/how-to-link-azure-data-factory#supported-azure-data-factory-activities)

## <a name="next-steps"></a>Passos seguintes
[Guia do utilizador da linhagem do catálogo](https://docs.microsoft.com/azure/purview/catalog-lineage-user-guide)

[Tutorial: Empurrar dados de linhagem da Fábrica de Dados para a Azure Purview](turorial-push-lineage-to-purview.md)
