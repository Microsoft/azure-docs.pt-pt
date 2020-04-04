---
title: Construir soluções integradas
description: Soluções e parceiros que se integram com uma piscina SYnapse SQL.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 2f6e091b6e0285bea5fef9e4d0be40faec936c6b
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633131"
---
# <a name="integrate-other-services-with-a-sql-analytics-data-warehouse"></a>Integrar outros serviços com um armazém de dados SQL Analytics

A capacidade SQL Analytics dentro do Azure Synapse Analytics permite aos utilizadores integrarem-se com muitos dos outros serviços em Azure. Utilizando o SQL Analytics, pode criar um armazém de dados através do seu recurso SQL Pool, que pode então utilizar vários serviços adicionais, alguns dos quais incluem:

* Power BI
* Azure Data Factory
* Azure Machine Learning
* Azure Stream Analytics

Para obter mais informações sobre os serviços de integração em todo o Azure, reveja o artigo parceiros de [integração.](sql-data-warehouse-partner-data-integration.md)

## <a name="power-bi"></a>Power BI

A integração do Power BI permite combinar o poder computacional de um armazém de dados com o relatório dinâmico e visualização do Power BI. A integração do Power BI inclui atualmente:

* **Ligação Direta**: Uma ligação mais avançada com pushdown lógico contra um armazém de dados aprovisionado com piscina SQL. Pushdown fornece uma análise mais rápida em uma escala maior.
* **Aberto em Power BI**: O botão 'Open in Power BI' passa informações de instância para Power BI para uma forma simplificada de se ligar.

Para mais informações, consulte [Integrar com power bi](sql-data-warehouse-get-started-visualize-with-power-bi.md), ou a [documentação Power BI](https://powerbi.microsoft.com/blog/exploring-azure-sql-data-warehouse-with-power-bi/).

## <a name="azure-data-factory"></a>Azure Data Factory

A Azure Data Factory oferece aos utilizadores uma plataforma gerida para criar oleodutos complexos de extração e carga. A integração do pool SQL com a Azure Data Factory inclui:

* **Procedimentos Armazenados**: Orquestrar a execução dos procedimentos armazenados.
* **Cópia:** Utilize a ADF para mover dados para o pool SQL. Esta operação pode utilizar o mecanismo padrão de movimento de dados da ADF ou a PolyBase sob as coberturas.

Para mais informações, consulte [Integrar com a Azure Data Factory.](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)

## <a name="azure-machine-learning"></a>Azure Machine Learning

O Azure Machine Learning é um serviço de análise totalmente gerido, que permite criar modelos intrincados utilizando um grande conjunto de ferramentas preditivas. A piscina SQL é suportada como fonte e destino para estes modelos, e tem a seguinte funcionalidade:

* **Leia os dados:** Conduza os modelos em escala utilizando t-SQL contra piscina SQL.
* **Escrever Dados:** Comprometa alterações de qualquer modelo de volta para piscina SQL.

Para mais informações, consulte [Integrar com Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md).

## <a name="azure-stream-analytics"></a>Azure Stream Analytics

O Azure Stream Analytics é uma infraestrutura complexa e totalmente gerida para o processamento e consumo de dados de eventos gerados pelo Azure Event Hub.  A integração com o pool SQL permite que os dados de streaming sejam eficazmente processados e armazenados juntamente com dados relacionais permitindo uma análise mais profunda e avançada.  

* **Saída de emprego:** Envie a saída dos trabalhos da Stream Analytics diretamente para a piscina SQL.

Para mais informações, consulte [Integrar com o Azure Stream Analytics.](sql-data-warehouse-integrate-azure-stream-analytics.md)
