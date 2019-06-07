---
title: Visualização de dados do Azure Data Explorer
description: Saiba mais sobre as diferentes formas em que pode visualizar os seus dados do Explorador de dados do Azure
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 85c37b6d626fc9942f5df956e738431d2727d282
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/04/2019
ms.locfileid: "66481838"
---
# <a name="data-visualization-with-azure-data-explorer"></a>Visualização de dados com o Explorador de dados do Azure 

Explorador de dados do Azure é um serviço de exploração de dados rápida e altamente escalável para dados de telemetria e de registo que são utilizados para criar soluções de análise complexas para grandes quantidades de dados. O Explorador de dados do Azure integra-se com várias ferramentas de visualização, para que possa visualizar os seus dados e partilhar os resultados na sua organização. Estes dados podem ser transformados em informações acionáveis para causar impacto no seu negócio.

Relatórios e visualização de dados é uma etapa crítica no processo de análise de dados. O Explorador de dados do Azure suporta muitos serviços de BI, pelo que pode utilizar aquele que melhor se adequa a seu cenário e o orçamento.

* Visualizações do Explorador de dados do Azure: Usando a linguagem de consulta de Kusto a [ `render operator` ](/azure/kusto/query/renderoperator) oferece vários tipos de visualização para descrever os resultados da consulta. Visualizações de consulta são úteis na deteção de anomalias e previsão, aprendizagem automática e muito mais.

* [Power BI](https://powerbi.microsoft.com): O Explorador de dados do Azure fornece a capacidade de ligar ao Power BI através de vários métodos: 

  * [Conector nativo do Power BI incorporado](/azure/data-explorer/power-bi-connector)

  * [Importar de consulta do Explorador de dados do Azure para o Power BI](/azure/data-explorer/power-bi-imported-query)
 
  * [Consulta SQL](/azure/data-explorer/power-bi-sql-query).

* [Microsoft Excel](https://products.office.com/excel): O Explorador de dados do Azure fornece a capacidade de ligar ao Excel usando o conector do Excel nativo incorporado ou importar uma consulta a partir do Explorador de dados do Azure para o Excel.

* [Grafana](https://grafana.com): O Grafana fornece um plug-in do Explorador de dados do Azure que lhe permite visualizar os dados a partir do Explorador de dados do Azure. [Configurar o Explorador de dados do Azure como uma origem de dados para o Grafana e, em seguida, visualize os dados](/azure/data-explorer/grafana)

* [Sisense](https://www.sisense.com): O Explorador de dados do Azure fornece a capacidade de ligar ao Sisense com o conector JDBC. [Configurar o Explorador de dados do Azure como uma origem de dados para Sisense e, em seguida, visualize os dados](/azure/data-explorer/sisense).

* [Tableau](https://www.tableau.com): O Explorador de dados do Azure fornece a capacidade para ligar ao Tableau com o [conector do ODBC e visualize os dados no Tableau](/azure/data-explorer/connect-odbc).

* [Qlik](https://www.qlik.com): O Explorador de dados do Azure fornece a capacidade para ligar ao Qlik com o [conector do ODBC](/azure/data-explorer/connect-odbc).