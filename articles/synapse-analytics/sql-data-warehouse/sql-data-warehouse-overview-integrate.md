---
title: Construir soluções integradas
description: Ferramentas de solução e parceiros que se integram com uma piscina SQL dedicada (anteriormente SQL DW) em Azure Synapse Analytics.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 2afc274bf7c040eca6a83abbab24c41767f16482
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96453662"
---
# <a name="integrate-other-services-with-a-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>Integre outros serviços com uma piscina SQL dedicada (anteriormente SQL DW) em Azure Synapse Analytics.

A capacidade dedicada do pool SQL (anteriormente SQL DW) dentro do Azure Synapse Analytics permite que os utilizadores se integrem com muitos dos outros serviços em Azure. Uma piscina SQL dedicada pode utilizar vários serviços adicionais, alguns dos quais incluem:

* Power BI
* Azure Data Factory
* Azure Machine Learning
* Azure Stream Analytics

Para mais informações sobre serviços de integração em todo o Azure, reveja o artigo [parceiros de Integração.](sql-data-warehouse-partner-data-integration.md)

## <a name="power-bi"></a>Power BI

A integração do Power BI permite combinar o poder de computação de um armazém de dados com o relatório dinâmico e visualização do Power BI. Atualmente, a integração do Power BI inclui:

* **Direct Connect**: Uma ligação mais avançada com o pushdown lógico contra um armazém de dados a forrado utilizando piscina SQL dedicada (anteriormente SQL DW). A propagação fornece uma análise mais rápida numa escala maior.
* **Abrir em Power BI**: O botão 'Open in Power BI' transmite informações de exemplo ao Power BI para uma forma simplificada de ligar.

Para obter mais informações, consulte [Integrar-se com o Power BI,](sql-data-warehouse-get-started-visualize-with-power-bi.md)ou a [documentação Power BI](https://powerbi.microsoft.com/blog/exploring-azure-sql-data-warehouse-with-power-bi/).

## <a name="azure-data-factory"></a>Azure Data Factory

A Azure Data Factory oferece aos utilizadores uma plataforma gerida para criar gasodutos complexos de extração e carga. A integração dedicada do pool SQL (anteriormente SQL DW) com a Azure Data Factory inclui:

* **Procedimentos armazenados**: Orquestrar a execução dos procedimentos armazenados.
* **Cópia**: Utilize a ADF para mover dados para piscina SQL dedicada (anteriormente SQL DW). Esta operação pode utilizar o mecanismo padrão de movimento de dados da ADF ou a PolyBase sob as capas.

Para mais informações, consulte [Integrar-se com a Azure Data Factory.](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)

## <a name="azure-machine-learning"></a>Azure Machine Learning

O Azure Machine Learning é um serviço de análise totalmente gerido, que permite criar modelos intrincados usando um grande conjunto de ferramentas preditivas. A piscina SQL dedicada (anteriormente SQL DW) é suportada como uma fonte e destino para estes modelos, e tem a seguinte funcionalidade:

* **Leia Os Dados:** Acionar os modelos à escala utilizando o T-SQL contra uma piscina SQL dedicada (anteriormente SQL DW).
* **Escrever Dados:** Comprometa as alterações de qualquer modelo de volta para uma piscina SQL dedicada (anteriormente SQL DW).

Para obter mais informações, consulte [Integrar-se com a Azure Machine Learning.](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)

## <a name="azure-stream-analytics"></a>Azure Stream Analytics

O Azure Stream Analytics é uma infraestrutura complexa e totalmente gerida para o processamento e consumo de dados de eventos gerados a partir do Azure Event Hub.  A integração com o pool de SQL dedicado (anteriormente SQL DW) permite que os dados de streaming sejam efetivamente processados e armazenados ao lado de dados relacionais que permitam uma análise mais profunda e mais avançada.  

* **Saída de Emprego:** Envie a produção de trabalhos stream Analytics diretamente para uma piscina SQL dedicada (anteriormente SQL DW).

Para obter mais informações, consulte [Integrar-se com a Azure Stream Analytics](sql-data-warehouse-integrate-azure-stream-analytics.md).
