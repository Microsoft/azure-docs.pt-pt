---
title: Construir soluções integradas
description: Ferramentas de solução e parceiros que se integram com uma piscina SQL dedicada na Azure Synapse Analytics.
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
ms.openlocfilehash: 9f92128266c41912868f6ab74abaa2d374c6d236
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93324495"
---
# <a name="integrate-other-services-with-a-dedicated-sql-pool-in-azure-synapse-analytics"></a>Integre outros serviços com uma piscina SQL dedicada na Azure Synapse Analytics.

A capacidade dedicada de piscina SQL dentro do Azure Synapse Analytics permite que os utilizadores se integrem com muitos dos outros serviços em Azure. Utilizando o Synapse SQL, pode criar um armazém de dados através do seu recurso de piscina SQL dedicado, que pode então utilizar vários serviços adicionais, alguns dos quais incluem:

* Power BI
* Azure Data Factory
* Azure Machine Learning
* Azure Stream Analytics

Para mais informações sobre serviços de integração em todo o Azure, reveja o artigo [parceiros de Integração.](sql-data-warehouse-partner-data-integration.md)

## <a name="power-bi"></a>Power BI

A integração do Power BI permite combinar o poder de computação de um armazém de dados com o relatório dinâmico e visualização do Power BI. Atualmente, a integração do Power BI inclui:

* **Direct Connect** : Uma ligação mais avançada com o pushdown lógico contra um armazém de dados a forrado com piscina SQL dedicada. A propagação fornece uma análise mais rápida numa escala maior.
* **Abrir em Power BI** : O botão 'Open in Power BI' transmite informações de exemplo ao Power BI para uma forma simplificada de ligar.

Para obter mais informações, consulte [Integrar-se com o Power BI,](sql-data-warehouse-get-started-visualize-with-power-bi.md)ou a [documentação Power BI](https://powerbi.microsoft.com/blog/exploring-azure-sql-data-warehouse-with-power-bi/).

## <a name="azure-data-factory"></a>Azure Data Factory

A Azure Data Factory oferece aos utilizadores uma plataforma gerida para criar gasodutos complexos de extração e carga. A integração dedicada da piscina SQL com a Azure Data Factory inclui:

* **Procedimentos armazenados** : Orquestrar a execução dos procedimentos armazenados.
* **Cópia** : Utilize a ADF para mover dados para um pool DE SQL dedicado. Esta operação pode utilizar o mecanismo padrão de movimento de dados da ADF ou a PolyBase sob as capas.

Para mais informações, consulte [Integrar-se com a Azure Data Factory.](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)

## <a name="azure-machine-learning"></a>Azure Machine Learning

O Azure Machine Learning é um serviço de análise totalmente gerido, que permite criar modelos intrincados usando um grande conjunto de ferramentas preditivas. O pool SQL dedicado é suportado como uma fonte e destino para estes modelos, e tem a seguinte funcionalidade:

* **Leia Os Dados:** Conduza os modelos à escala utilizando o T-SQL contra uma piscina SQL dedicada.
* **Escrever Dados:** Comprometa as alterações de qualquer modelo de volta para uma piscina SQL dedicada.

Para obter mais informações, consulte [Integrar-se com a Azure Machine Learning.](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)

## <a name="azure-stream-analytics"></a>Azure Stream Analytics

O Azure Stream Analytics é uma infraestrutura complexa e totalmente gerida para o processamento e consumo de dados de eventos gerados a partir do Azure Event Hub.  A integração com o pool DE SQL dedicado permite que os dados de streaming sejam efetivamente processados e armazenados ao lado de dados relacionais que permitam uma análise mais profunda e mais avançada.  

* **Saída de Emprego:** Envie a produção dos trabalhos stream Analytics diretamente para uma piscina SQL dedicada.

Para obter mais informações, consulte [Integrar-se com a Azure Stream Analytics](sql-data-warehouse-integrate-azure-stream-analytics.md).
