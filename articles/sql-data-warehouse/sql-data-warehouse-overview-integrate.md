---
title: Crie soluções integradas
description: Ferramentas e parceiros com soluções que se integram ao Azure SQL Data Warehouse.
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: integration
ms.date: 04/17/2018
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 6e159e0e254ae8b2515515dfaeb2c514e0f25e0b
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685635"
---
# <a name="integrate-other-services-with-sql-data-warehouse"></a>Integrar outros serviços com o SQL Data Warehouse
Além de sua funcionalidade principal, SQL Data Warehouse permite que os usuários se integrem a muitos dos outros serviços no Azure. Alguns desses serviços incluem:

* Power BI
* Azure Data Factory
* Azure Machine Learning
* Azure Stream Analytics

SQL Data Warehouse continua a integrar com mais serviços no Azure e mais [parceiros de integração](sql-data-warehouse-partner-data-integration.md).

## <a name="power-bi"></a>Power BI
Power BI integração permite combinar a potência de computação do SQL Data Warehouse com a visualização e o relatório dinâmico de Power BI. Atualmente, a integração do Power BI inclui:

* Conexão **direta**: uma conexão mais avançada com aplicação lógica contra SQL data warehouse. A aplicação fornece uma análise mais rápida em uma escala maior.
* **Abrir no Power bi**: o botão ' abrir no Power Bi ' passa informações da instância para Power bi de uma maneira simplificada de se conectar.

Para obter mais informações, consulte [integrar com Power bi](sql-data-warehouse-get-started-visualize-with-power-bi.md)ou a [documentação Power bi](https://powerbi.microsoft.com/blog/exploring-azure-sql-data-warehouse-with-power-bi/).

## <a name="azure-data-factory"></a>Azure Data Factory
Azure Data Factory fornece aos usuários uma plataforma gerenciada para criar pipelines de extração e carregamento complexos. A integração do SQL Data Warehouse com o Azure Data Factory inclui:

* **Procedimentos armazenados**: orquestrar a execução de procedimentos armazenados em SQL data warehouse.
* **Copiar**: Use o ADF para mover dados para SQL data warehouse. Essa operação pode usar o mecanismo de movimentação de dados padrão do ADF ou polybase nos bastidores. 

Para obter mais informações, consulte [integrar com Azure data Factory](https://docs.microsoft.com/azure/data-factory/load-azure-sql-data-warehouse?toc=/azure/sql-data-warehouse/toc.json).

## <a name="azure-machine-learning"></a>Azure Machine Learning
Azure Machine Learning é um serviço de análise totalmente gerenciado, que permite criar modelos complexos usando um grande conjunto de ferramentas preditivas. SQL Data Warehouse tem suporte como origem e destino para esses modelos com a seguinte funcionalidade:

* **Ler dados:** Impulsionar modelos em escala usando o T-SQL em relação ao SQL Data Warehouse.
* **Gravar dados:** Confirme as alterações de qualquer modelo de volta para SQL Data Warehouse.

Para obter mais informações, consulte [integrar com Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md).

## <a name="azure-stream-analytics"></a>Azure Stream Analytics
Azure Stream Analytics é uma infraestrutura complexa e totalmente gerenciada para processar e consumir dados de eventos gerados do hub de eventos do Azure.  A integração com o SQL Data Warehouse permite que os dados de streaming sejam efetivamente processados e armazenados juntamente com dados relacionais, permitindo uma análise mais profunda e mais avançada.  

* **Saída do trabalho:** Enviar a saída de trabalhos do Stream Analytics diretamente para o SQL Data Warehouse.

Para obter mais informações, consulte [integrar com Azure Stream Analytics](sql-data-warehouse-integrate-azure-stream-analytics.md).


