---
title: Construir soluções integradas
description: Ferramentas de solução e parceiros que se integram com uma piscina Sinapse SQL.
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
ms.openlocfilehash: 16cfdfb475ce21ed4b51dc9140e59df701363f27
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85201009"
---
# <a name="integrate-other-services-with-a-synapse-sql-pool-data-warehouse"></a>Integrar outros serviços com um pool Sinaapse SQL (armazém de dados)

A capacidade de piscina SQL dentro do Azure Synapse Analytics permite que os utilizadores se integrem com muitos dos outros serviços em Azure. Utilizando o Synapse SQL, pode criar um armazém de dados através do seu recurso de pool SQL, que pode então utilizar vários serviços adicionais, alguns dos quais incluem:

* Power BI
* Azure Data Factory
* Azure Machine Learning
* Azure Stream Analytics

Para mais informações sobre serviços de integração em todo o Azure, reveja o artigo [parceiros de Integração.](sql-data-warehouse-partner-data-integration.md)

## <a name="power-bi"></a>Power BI

A integração do Power BI permite combinar o poder de computação de um armazém de dados com o relatório dinâmico e visualização do Power BI. Atualmente, a integração do Power BI inclui:

* **Ligação Direta**: Uma ligação mais avançada com o empurrão lógico contra um armazém de dados previsto com piscina SQL. A propagação fornece uma análise mais rápida numa escala maior.
* **Abrir em Power BI**: O botão 'Open in Power BI' transmite informações de exemplo ao Power BI para uma forma simplificada de ligar.

Para obter mais informações, consulte [Integrar-se com o Power BI,](sql-data-warehouse-get-started-visualize-with-power-bi.md)ou a [documentação Power BI](https://powerbi.microsoft.com/blog/exploring-azure-sql-data-warehouse-with-power-bi/).

## <a name="azure-data-factory"></a>Azure Data Factory

A Azure Data Factory oferece aos utilizadores uma plataforma gerida para criar gasodutos complexos de extração e carga. A integração da piscina SQL com a Azure Data Factory inclui:

* **Procedimentos armazenados**: Orquestrar a execução dos procedimentos armazenados.
* **Cópia:** Utilize a ADF para mover dados para o pool SQL. Esta operação pode utilizar o mecanismo padrão de movimento de dados da ADF ou a PolyBase sob as capas.

Para mais informações, consulte [Integrar-se com a Azure Data Factory.](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)

## <a name="azure-machine-learning"></a>Azure Machine Learning

O Azure Machine Learning é um serviço de análise totalmente gerido, que permite criar modelos intrincados usando um grande conjunto de ferramentas preditivas. A piscina SQL é suportada como uma fonte e destino para estes modelos, e tem a seguinte funcionalidade:

* **Leia Os Dados:** Conduza os modelos à escala utilizando o T-SQL contra a piscina SQL.
* **Escrever Dados:** Comprometa alterações de qualquer modelo de volta para piscina SQL.

Para obter mais informações, consulte [Integrar-se com a Azure Machine Learning.](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)

## <a name="azure-stream-analytics"></a>Azure Stream Analytics

O Azure Stream Analytics é uma infraestrutura complexa e totalmente gerida para o processamento e consumo de dados de eventos gerados a partir do Azure Event Hub.  A integração com o pool SQL permite que os dados de streaming sejam efetivamente processados e armazenados ao lado de dados relacionais que permitam uma análise mais profunda e mais avançada.  

* **Saída de Emprego:** Envie a produção dos trabalhos stream Analytics diretamente para a piscina SQL.

Para obter mais informações, consulte [Integrar-se com a Azure Stream Analytics](sql-data-warehouse-integrate-azure-stream-analytics.md).
