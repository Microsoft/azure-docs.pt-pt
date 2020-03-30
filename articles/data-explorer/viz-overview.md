---
title: Visualização de dados do Azure Data Explorer
description: Saiba mais sobre as diferentes formas de visualizar os seus dados do Azure Data Explorer
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/30/2020
ms.openlocfilehash: 7b1c7825beb769b610d661cb9644fc3f3919d548
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79139067"
---
# <a name="data-visualization-with-azure-data-explorer"></a>Visualização de dados com O Explorador de Dados Azure 

O Azure Data Explorer é um serviço de exploração de dados rápido e altamente escalável para dados de log e telemetria que é usado para construir soluções de análise complexas para grandes quantidades de dados. O Azure Data Explorer integra-se com várias ferramentas de visualização, para que possa visualizar os seus dados e partilhar os resultados em toda a sua organização. Estes dados podem ser transformados em insights atuais para causar impacto no seu negócio.

A visualização e relatório de dados é um passo crítico no processo de análise de dados. O Azure Data Explorer suporta muitos serviços bi para que possa utilizar aquele que melhor se adequa ao seu cenário e orçamento.

## <a name="kusto-query-language-visualizations"></a>Visualizações linguísticas de consulta kusto

A linguagem [`render operator`](/azure/kusto/query/renderoperator) de consulta Kusto oferece várias visualizações, tais como mesas, gráficos de tartes e gráficos de barras para retratar resultados de consulta. As visualizações de consultas são úteis na deteção e previsão de anomalias, aprendizagem automática, e muito mais.

## <a name="power-bi"></a>Power BI

O Azure Data Explorer fornece a capacidade de ligar ao [Power BI](https://powerbi.microsoft.com) utilizando vários métodos: 

  * [Conector Power BI nativo incorporado](/azure/data-explorer/power-bi-connector)

  * [Consulta importado do Azure Data Explorer para o Power BI](/azure/data-explorer/power-bi-imported-query)
 
  * [Consulta SQL](/azure/data-explorer/power-bi-sql-query)

## <a name="microsoft-excel"></a>Microsoft Excel

O Azure Data Explorer fornece a capacidade de ligar ao [Microsoft Excel](https://products.office.com/excel) utilizando o [conector Excel nativo incorporado,](excel-connector.md)ou [importar uma consulta](excel-blank-query.md) do Azure Data Explorer para o Excel.

## <a name="grafana"></a>Grafana

[Grafana](https://grafana.com) fornece um plugin Azure Data Explorer que lhe permite visualizar dados do Azure Data Explorer. [Configura o Azure Data Explorer como fonte de dados para grafana e,](/azure/data-explorer/grafana)em seguida, visualiza os dados . 

## <a name="kibana"></a>Kibana

O Azure Data Explorer fornece a capacidade de ligar à [Kibana (página Discover)](https://www.elastic.co/guide/en/kibana/6.8/discover.html) utilizando o K2Bridge, um conector de código aberto. [Configura o Azure Data Explorer como fonte de dados para a Kibana e,](/azure/data-explorer/k2bridge)em seguida, visualiza os dados .

## <a name="odbc-connector"></a>Conector ODBC

O Azure Data Explorer fornece um [conector de conectividade de base de dados aberta (ODBC)](connect-odbc.md) para que qualquer aplicação que suporte a ODBC possa ligar-se ao Azure Data Explorer.

## <a name="tableau"></a>Tableau

O Azure Data Explorer fornece a capacidade de ligar ao [Tableau](https://www.tableau.com) utilizando o [conector ODBC](/azure/data-explorer/connect-odbc) e, em seguida, [visualizar os dados em Tableau](tableau.md).

## <a name="qlik"></a>Qlik

O Azure Data Explorer fornece a capacidade de se conectar a [Qlik](https://www.qlik.com) usando o [conector ODBC](/azure/data-explorer/connect-odbc) e, em seguida, criar dashboards Qlik Sense e visualizar os dados. Utilizando o seguinte vídeo, pode aprender a visualizar os dados do Azure Data Explorer com a Qlik. 

> [!VIDEO https://www.youtube.com/embed/nhWIiBwxjjU]  

## <a name="sisense"></a>Sisense

O Azure Data Explorer fornece a capacidade de ligar ao [Sisense](https://www.sisense.com) utilizando o conector JDBC. [Configura o Azure Data Explorer como fonte de dados para o Sisense e,](/azure/data-explorer/sisense)em seguida, visualiza os dados .

## <a name="redash"></a>Redash

Pode usar o [Redash](https://redash.io/) para construir dashboards e visualizar dados. [Configurar o Azure Data Explorer como fonte de dados para o Redash e, em seguida, visualizar os dados](/azure/data-explorer/redash).