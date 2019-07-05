---
title: Visualização de dados do Azure Data Explorer
description: Saiba mais sobre as diferentes formas em que pode visualizar os seus dados do Explorador de dados do Azure
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/30/2019
ms.openlocfilehash: d1c73d8eb65ed5d67d5250b4a3bca3b80450001e
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/03/2019
ms.locfileid: "67536729"
---
# <a name="data-visualization-with-azure-data-explorer"></a>Visualização de dados com o Explorador de dados do Azure 

Explorador de dados do Azure é um serviço de exploração de dados rápida e altamente escalável para dados de telemetria e de registo que são utilizados para criar soluções de análise complexas para grandes quantidades de dados. O Explorador de dados do Azure integra-se com várias ferramentas de visualização, para que possa visualizar os seus dados e partilhar os resultados na sua organização. Estes dados podem ser transformados em informações acionáveis para causar impacto no seu negócio.

Relatórios e visualização de dados é uma etapa crítica no processo de análise de dados. O Explorador de dados do Azure suporta muitos serviços de BI, pelo que pode utilizar aquele que melhor se adequa a seu cenário e o orçamento.

## <a name="kusto-query-language-visualizations"></a>Visualizações de linguagem de consulta de Kusto

A linguagem de consulta de Kusto [ `render operator` ](/azure/kusto/query/renderoperator) oferece várias visualizações, como tabelas, gráficos circulares e gráficos de barras para descrever os resultados da consulta. Visualizações de consulta são úteis na deteção de anomalias e previsão, aprendizagem automática e muito mais.

## <a name="power-bi"></a>Power BI

O Explorador de dados do Azure fornece a capacidade para ligar à [Power BI](https://powerbi.microsoft.com) usando vários métodos: 

  * [Conector nativo do Power BI incorporado](/azure/data-explorer/power-bi-connector)

  * [Importar de consulta do Explorador de dados do Azure para o Power BI](/azure/data-explorer/power-bi-imported-query)
 
  * [Consulta SQL](/azure/data-explorer/power-bi-sql-query)

## <a name="microsoft-excel"></a>Microsoft Excel

O Explorador de dados do Azure fornece a capacidade para ligar à [Microsoft Excel](https://products.office.com/excel) usando nativa incorporada conector do Excel ou importar uma consulta a partir do Explorador de dados do Azure para o Excel.

## <a name="grafana"></a>Grafana

[O Grafana](https://grafana.com) fornece um plug-in do Explorador de dados do Azure que lhe permite visualizar os dados a partir do Explorador de dados do Azure. [Configurar o Explorador de dados do Azure como uma origem de dados para o Grafana e, em seguida, visualize os dados](/azure/data-explorer/grafana). 

## <a name="odbc-connector"></a>Conector ODBC

O Explorador de dados do Azure fornece um [conector de conectividade ODBC (Open Database)](connect-odbc.md) para qualquer aplicação que suporte o ODBC pode ligar-se ao Azure Data Explorer.

## <a name="tableau"></a>Tableau

O Explorador de dados do Azure fornece a capacidade de ligar ao [Tableau](https://www.tableau.com) utilizando o [conector do ODBC](/azure/data-explorer/connect-odbc) e, em seguida [visualize os dados no Tableau](tableau.md).

## <a name="qlik"></a>Qlik

O Explorador de dados do Azure fornece a capacidade para ligar à [Qlik](https://www.qlik.com) utilizando o [conector do ODBC](/azure/data-explorer/connect-odbc) e, em seguida, criar Qlik Sense dashboards e visualizar os dados. Utilizar o seguinte vídeo, pode aprender visualizar dados de Explorador de dados do Azure com o Qlik. 

> [!VIDEO https://www.youtube.com/embed/nhWIiBwxjjU]  

## <a name="sisense"></a>Sisense

O Explorador de dados do Azure fornece a capacidade para ligar à [Sisense](https://www.sisense.com) através do conector JDBC. [Configurar o Explorador de dados do Azure como uma origem de dados para Sisense e, em seguida, visualize os dados](/azure/data-explorer/sisense).