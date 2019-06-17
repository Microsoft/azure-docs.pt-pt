---
title: Análise - a pesquisa poderosa e a ferramenta de consulta do Azure Application Insights | Documentos da Microsoft
description: 'Descrição geral da análise, a ferramenta de pesquisa poderosas de diagnóstico do Application Insights. '
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 0a2f6011-5bcf-47b7-8450-40f284274b24
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 02/02/2019
ms.author: mbullwin
ms.openlocfilehash: f5819194e7967b5921f34223cad299752460de30
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66255648"
---
# <a name="analytics-in-application-insights"></a>Análise no Application Insights
A análise é a ferramenta de pesquisa e consulta poderosa da [Application Insights](app-insights-overview.md). A análise é uma ferramenta da web para que nenhuma configuração é necessária.
Se já tiver configurado o Application Insights para uma das suas aplicações, em seguida, pode analisar dados da sua aplicação, abrindo a análise do painel de descrição geral da sua aplicação.

![Abrir portal.azure.com, abra o recurso do Application Insights e clique em Analytics.](./media/analytics/001.png)

Também pode utilizar o [recreio de análise](https://go.microsoft.com/fwlink/?linkid=859557) que é um ambiente de demonstração gratuito com uma grande quantidade de dados de exemplo.

## <a name="relation-to-azure-monitor-logs"></a>Registos de relação para o Azure Monitor
O Application Insights analytics baseia-se no [Explorador de dados do Azure](/azure/data-explorer) como o Azure Monitor faz e também utiliza o [linguagem de consulta de Kusto](/azure/kusto/query). Ele usa o mesmo [portal do log analytics](../log-query/get-started-portal.md) como registos do Azure Monitor, embora os seus dados são armazenados numa partição separada.

Não é possível acessar dados numa área de trabalho do Log Analytics diretamente do analytics do Application Insights, nem pode acessar diretamente os dados da aplicação do log analytics. Para poder consultar os dois conjuntos de dados em conjunto, escrever um [consulta do log analytics](../log-query/log-query-overview.md) e o uso do [app() expressão](../log-query/app-expression.md) para aceder a dados de aplicação.


## <a name="query-data-in-analytics"></a>Dados de consulta do Analytics
Uma consulta típica começa com um nome de tabela, seguido de uma série de *operadores* separados por `|`.
Por exemplo, vamos descobrir quantas solicitações a nossa aplicação recebida a partir de diferentes países/regiões, durante as últimas 3 horas:
```AIQL
requests
| where timestamp > ago(3h)
| summarize count() by client_CountryOrRegion
| render piechart
```

Vamos começar com o nome da tabela *pedidos* e adicionar elementos de pipe, conforme necessário.  Em primeiro lugar, definimos um filtro de tempo para rever apenas os registos das últimas 3 horas.
Em seguida, Contamos o número de registos por país (que é encontrar os dados da coluna *client_CountryOrRegion*). Por fim, vamos renderizar os resultados num gráfico circular.
<br>

![Resultados da consulta](./media/analytics/030.png)

A linguagem tem muitos recursos atraentes:

* [Filtro](/azure/kusto/query/whereoperator) telemetria da aplicação não processados por qualquer campos, incluindo as propriedades personalizadas e as métricas.
* [Junte-se a](/azure/kusto/query/joinoperator) várias tabelas - fazer a correlação de pedidos com vistas de página, chamadas de dependências, exceções e rastreios de registos.
* Poderosa estatística [agregações](/azure/kusto/query/summarizeoperator).
* Visualizações de imediatas e poderosas.
* [REST API](https://dev.applicationinsights.io/) que pode usar para executar consultas através de programação, por exemplo do PowerShell.

O [completa de referência de linguagem](https://go.microsoft.com/fwlink/?linkid=856079) fornece detalhes sobre cada comando suportado e atualiza regularmente.

## <a name="next-steps"></a>Passos Seguintes
* Comece com o [Analytics portal](https://go.microsoft.com/fwlink/?linkid=856587)
* Introdução ao [escrever consultas](https://go.microsoft.com/fwlink/?linkid=856078)
* Reveja a [Roteiro para SQL-os utilizadores](https://aka.ms/sql-analytics) para traduções dos idiomas mais comuns.
* Teste de unidade de análise em nosso [recreio](https://analytics.applicationinsights.io/demo) se a aplicação não está a enviar dados para o Application Insights ainda.
* Veja a [vídeo introdutório](https://applicationanalytics-media.azureedge.net/home_page_video.mp4).
