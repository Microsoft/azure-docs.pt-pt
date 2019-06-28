---
title: Descrição geral do registo de consultas no Azure Monitor | Documentos da Microsoft
description: Respostas a perguntas comuns relacionados com de registos de consulta e obtém a começar a usá-los.
services: log-analytics
author: bwren
ms.service: log-analytics
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: bwren
ms.openlocfilehash: 7605bf36c41c5b1276d29076173efd52409afaa9
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/21/2019
ms.locfileid: "67310348"
---
# <a name="overview-of-log-queries-in-azure-monitor"></a>Descrição geral das consultas de registo no Azure Monitor
Consultas de registo ajudá-lo a aproveitar totalmente o valor dos dados recolhidos nas [registos do Azure Monitor](../platform/data-platform-logs.md). Uma linguagem de consulta poderosa permite-lhe associar dados de várias tabelas, Agregar grandes conjuntos de dados e executar operações complexas com pouco código. Praticamente qualquer pergunta pode ser respondida e análise realizada, desde que tenham sido recolhidos os dados de suporte e compreender como construir a consulta correta.

Algumas funcionalidades no Azure Monitor, como [insights](../insights/insights-overview.md) e [soluções](../insights/solutions-inventory.md) processar dados de registo sem lhe expor as consultas subjacentes. Para aproveitar totalmente os outros recursos do Azure Monitor, deve compreender como as consultas são construídas e como pode usá-los para analisar dados nos registos de Monitor do Azure interativamente.

Utilize este artigo como ponto de partida para saber mais sobre consultas de registo no Azure Monitor. Ele responde a perguntas comuns e fornece links para outras documentações que fornece mais detalhes e as lições.

## <a name="how-can-i-learn-how-to-write-queries"></a>Como posso saber como escrever consultas?
Se quiser entrar de cabeça em coisas, pode iniciar com os tutoriais seguintes:

- [Introdução ao Log Analytics no Azure Monitor](get-started-portal.md).
- [Introdução às consultas de registo no Azure Monitor](get-started-queries.md).

Depois de ter as noções básicas para baixo, percorra várias lições usando seus próprios dados ou dados de nosso ambiente de demonstração, começando com: 

- [Trabalhar com cadeias de caracteres em consultas de registo do Azure Monitor](string-operations.md)
 
## <a name="what-language-do-log-queries-use"></a>Que idioma iniciar a utilização de consultas?
Registos de Monitor do Azure baseia-se no [Explorador de dados do Azure](/azure/data-explorer), e consultas de registo são escritas com a mesma linguagem de consulta de Kusto (KQL). Esta é uma linguagem avançada concebida para ser fácil de ler e autor e deve poder começar a utilizar com o mínimo de orientação.

Ver [documentação do Azure Data Explorer KQL](/azure/kusto/query) para toda a documentação sobre KQL e referência em diferentes funções disponíveis.<br>
Ver [introdução às consultas de registo no Azure Monitor](get-started-queries.md) para uma passo a passo rápido da linguagem com dados de registos de Monitor do Azure.
Ver [diferenças de linguagem de consulta de registo do Azure Monitor](data-explorer-difference.md) para pequenas diferenças na versão do KQL utilizado pelo Azure Monitor.

## <a name="what-data-is-available-to-log-queries"></a>Os dados que estão disponíveis para consultas de registo?
Todos os dados recolhidos nos Logs de Monitor do Azure está disponível para recuperar e analisar de consultas de registo. Diferentes origens de dados irão escrever seus dados em tabelas diferentes, mas pode incluir várias tabelas numa consulta única para analisar dados de várias origens. Quando cria uma consulta, comece por determinar que tabelas contêm os dados que está procurando, para que deve ter, pelo menos, um entendimento básico de como os dados nos registos do Azure Monitor são estruturados.

Ver [origens de Logs de Monitor do Azure](../platform/data-platform-logs.md#sources-of-azure-monitor-logs), para uma lista de dados de diferentes origens que preencher os Logs de Monitor do Azure.<br>
Ver [estrutura de Logs de Monitor do Azure](logs-structure.md) para obter uma explicação de como os dados são estruturados.

## <a name="what-does-a-log-query-look-like"></a>O que o aspeto de uma consulta de registo?
Uma consulta pode ser tão simples como um nome de tabela única para obter todos os registos da tabela:

```Kusto
Syslog
```

Ou poderia filtrar registos específico, resumi-los e visualizar os resultados num gráfico:

```
SecurityEvent
| where TimeGenerated > ago(7d)
| where EventID == 4625
| summarize count() by Computer, bin(TimeGenerated, 1h)
| render timechart 
```

Para uma análise mais complexa, poderá obter dados de várias tabelas utilizando uma associação para analisar os resultados em conjunto.

```Kusto
app("ContosoRetailWeb").requests
| summarize count() by bin(timestamp,1hr)
| join kind= inner (Perf
    | summarize avg(CounterValue) 
      by bin(TimeGenerated,1hr))
on $left.timestamp == $right.TimeGenerated
```
Mesmo se não estiver familiarizado com KQL, deve ser, pelo menos, descobrir a lógica básica que está a ser utilizada por estas consultas. Eles começam com o nome de uma tabela e, em seguida, adicionar vários comandos para filtrar e processar dados. Uma consulta pode utilizar qualquer número de comandos e pode escrever consultas mais complexas, como se familiarizar com os comandos KQL diferentes disponíveis.

Ver [introdução às consultas de registo no Azure Monitor](get-started-queries.md) para um tutorial sobre as consultas de registo que apresenta o idioma e as funções comuns,.<br>


## <a name="what-is-log-analytics"></a>O que é o Log Analytics?
O log Analytics é a principal ferramenta no portal do Azure para escrever consultas de registo e análise interativamente os seus resultados. Mesmo que uma consulta de registo é utilizada em outro lugar no Azure Monitor, normalmente, irá escrever e testar a consulta primeiro utilizar o Log Analytics.

Pode iniciar o Log Analytics a vários locais no portal do Azure. O âmbito dos dados disponíveis para o Log Analytics é determinado pelo como iniciá-la. Ver [âmbito de consulta](scope.md) para obter mais detalhes.

- Selecione **registos** partir a **Azure Monitor** menu ou **áreas de trabalho do Log Analytics** menu.
- Selecione **Analytics** partir a **descrição geral** página de um aplicativo do Application Insights.
- Selecione **registos** no menu de um recurso do Azure.

![Log Analytics](media/log-query-overview/log-analytics.png)

Ver [introdução ao Log Analytics no Azure Monitor](get-started-portal.md) para um tutorial passo a passo do Log Analytics, que apresenta vários de seus recursos.

## <a name="where-else-are-log-queries-used"></a>Onde são usadas as consultas de registo?
Para além de trabalhar interativamente com consultas de registo e seus resultados no Log Analytics, as áreas no Azure Monitor, onde irá utilizar consultas incluem o seguinte:

- **Regras de alerta.** [Regras de alerta](../platform/alerts-overview.md) identificar proativamente os problemas dos dados na sua área de trabalho.  Cada regra de alerta baseia-se uma pesquisa de registos que está a ser executada automaticamente em intervalos regulares.  Os resultados são inspecionados para determinar se um alerta deve ser criado.
- **Dashboards.** Pode afixar os resultados de qualquer consulta numa [dashboard do Azure](../learn/tutorial-logs-dashboards.md) que permitem que visualize os dados de registos e de métricas em conjunto e, opcionalmente, partilhar com outros utilizadores do Azure.
- **Modos de exibição.**  Pode criar visualizações de dados a serem incluídos nos dashboards do utilizador com [estruturador de vistas](../platform/view-designer.md).  Consultas de registo que fornecem os dados utilizados pelo [mosaicos](../platform/view-designer-tiles.md) e [partes de visualização](../platform/view-designer-parts.md) em cada vista.  
- **Exportar.**  Quando importa dados de registo do Azure Monitor para o Excel ou [Power BI](../platform/powerbi.md), cria uma consulta de registo para definir os dados para exportar.
- **PowerShell.** Pode executar um script do PowerShell a partir de uma linha de comando ou um runbook da automatização do Azure que utiliza [Get-AzOperationalInsightsSearchResults](/powershell/module/az.operationalinsights/get-azoperationalinsightssearchresult) para recuperar dados de registo do Azure Monitor.  Este cmdlet requer uma consulta para determinar os dados a obter.
- **API de Logs de Monitor do Azure.**  O [API do Azure Monitor registos](../platform/alerts-overview.md) permite que qualquer cliente de REST API recuperar dados de registo de área de trabalho.  O pedido de API inclui uma consulta que é executada no Azure Monitor para determinar os dados a obter.


## <a name="next-steps"></a>Passos Seguintes
- Percorrer um [tutorial sobre como utilizar o Log Analytics no portal do Azure](get-started-portal.md).
- Percorrer um [tutorial sobre como escrever consultas](get-started-queries.md).
