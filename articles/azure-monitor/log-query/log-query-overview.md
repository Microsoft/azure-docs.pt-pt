---
title: Visão geral das consultas de log no Azure Monitor | Microsoft Docs
description: Responde a perguntas comuns relacionadas a consultas de log e você começa a usá-las.
services: log-analytics
author: bwren
ms.service: log-analytics
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: bwren
ms.openlocfilehash: 89633d77a6270b5c34cd9b4f52bc7286f84b1976
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68827304"
---
# <a name="overview-of-log-queries-in-azure-monitor"></a>Visão geral das consultas de log no Azure Monitor
As consultas de log ajudam você a aproveitar totalmente o valor dos dados coletados nos [logs de Azure monitor](../platform/data-platform-logs.md). Uma linguagem de consulta eficiente permite unir dados de várias tabelas, agregar grandes conjuntos de dados e executar operações complexas com o mínimo de código. Praticamente qualquer pergunta pode ser respondida e a análise realizada desde que os dados de suporte tenham sido coletados e você entenda como construir a consulta correta.

Alguns recursos em Azure Monitor como [informações](../insights/insights-overview.md) e [soluções](../insights/solutions-inventory.md) processam dados de log sem expor você às consultas subjacentes. Para aproveitar totalmente outros recursos do Azure Monitor, você deve entender como as consultas são construídas e como usá-las para analisar interativamente os dados nos logs do Azure Monitor.

Use este artigo como um ponto de partida para aprender sobre consultas de log no Azure Monitor. Ele responde a perguntas comuns e fornece links para outras documentações que fornecem mais detalhes e lições.

## <a name="how-can-i-learn-how-to-write-queries"></a>Como posso aprender a escrever consultas?
Se você quiser ir diretamente para as coisas, poderá começar com os seguintes tutoriais:

- Comece a usar o [log Analytics no Azure monitor](get-started-portal.md).
- [Introdução às consultas de log no Azure monitor](get-started-queries.md).

Depois de obter as noções básicas, percorra várias lições usando seus próprios dados ou dados de nosso ambiente de demonstração, começando com: 

- [Trabalhar com cadeias de caracteres em consultas de log de Azure Monitor](string-operations.md)
 
## <a name="what-language-do-log-queries-use"></a>Que linguagem as consultas de log usam?
Os logs de Azure Monitor baseiam-se no [Azure data Explorer](/azure/data-explorer)e as consultas de log são escritas usando a mesma linguagem de consulta KUSTO (KQL). Essa é uma linguagem avançada criada para ser fácil de ler e criar e você deve ser capaz de começar a usá-la com diretrizes mínimas.

Consulte a [documentação do Azure data Explorer KQL](/azure/kusto/query) para obter a documentação completa sobre o KQL e a referência em funções diferentes disponíveis.<br>
Consulte Introdução [às consultas de log no Azure monitor](get-started-queries.md) para obter uma rápida explicação do idioma usando dados de Logs de Azure monitor.
Veja [Azure monitor diferenças na linguagem de consulta de log](data-explorer-difference.md) para pequenas diferenças na versão do KQL usada pelo Azure monitor.

## <a name="what-data-is-available-to-log-queries"></a>Quais dados estão disponíveis para registrar consultas?
Todos os dados coletados em logs de Azure Monitor estão disponíveis para recuperar e analisar em consultas de log. Diferentes fontes de dados gravarão seus dados em tabelas diferentes, mas você pode incluir várias tabelas em uma única consulta para analisar dados em várias fontes. Ao criar uma consulta, você começa determinando quais tabelas têm os dados que está procurando e, portanto, deve ter pelo menos um entendimento básico de como os dados em logs de Azure Monitor são estruturados.

Consulte [fontes de logs de Azure monitor](../platform/data-platform-logs.md#sources-of-azure-monitor-logs)para obter uma lista de diferentes fontes de dados que preenchem Azure monitor logs.<br>
Consulte [estrutura de logs de Azure monitor](logs-structure.md) para obter uma explicação de como os dados são estruturados.

## <a name="what-does-a-log-query-look-like"></a>Como é a aparência de uma consulta de log?
Uma consulta pode ser tão simples quanto um único nome de tabela para recuperar todos os registros dessa tabela:

```Kusto
Syslog
```

Ou poderia filtrar registros específicos, resumi-los e visualizar os resultados em um gráfico:

```
SecurityEvent
| where TimeGenerated > ago(7d)
| where EventID == 4625
| summarize count() by Computer, bin(TimeGenerated, 1h)
| render timechart 
```

Para uma análise mais complexa, você pode recuperar dados de várias tabelas usando uma junção para analisar os resultados juntos.

```Kusto
app("ContosoRetailWeb").requests
| summarize count() by bin(timestamp,1hr)
| join kind= inner (Perf
    | summarize avg(CounterValue) 
      by bin(TimeGenerated,1hr))
on $left.timestamp == $right.TimeGenerated
```
Mesmo que você não esteja familiarizado com o KQL, você deve ser capaz de, pelo menos, descobrir a lógica básica que está sendo usada por essas consultas. Eles começam com o nome de uma tabela e, em seguida, adicionam vários comandos para filtrar e processar esses dados. Uma consulta pode usar qualquer número de comandos, e você pode escrever consultas mais complexas conforme se familiarizar com os diferentes comandos do KQL disponíveis.

Consulte Introdução [às consultas de log no Azure monitor](get-started-queries.md) para obter um tutorial sobre consultas de log que introduzem a linguagem e as funções comuns,.<br>


## <a name="what-is-log-analytics"></a>O que é o Log Analytics?
Log Analytics é a principal ferramenta na portal do Azure para gravar consultas de log e analisar seus resultados interativamente. Mesmo que uma consulta de log seja usada em outro lugar na Azure Monitor, você normalmente escreverá e testará a consulta primeiro usando Log Analytics.

Você pode iniciar o Log Analytics de vários locais na portal do Azure. O escopo dos dados disponíveis para Log Analytics é determinado pelo modo como você o inicia. Consulte [escopo da consulta](scope.md) para obter mais detalhes.

- Selecione **logs** no menu **Azure monitor** ou no menu **log Analytics espaços de trabalho** .
- Selecione **análise** na página **visão geral** de um aplicativo Application insights.
- Selecione **logs** no menu de um recurso do Azure.

![Log Analytics](media/log-query-overview/log-analytics.png)

Confira [introdução ao log Analytics no Azure monitor](get-started-portal.md) para obter um tutorial explicativo de log Analytics que apresenta vários de seus recursos.

## <a name="where-else-are-log-queries-used"></a>Onde outras consultas de log são usadas?
Além de trabalhar interativamente com consultas de log e seus resultados em Log Analytics, as áreas em Azure Monitor em que você usará consultas incluem o seguinte:

- **Regras de alerta.** [As regras de alerta](../platform/alerts-overview.md) identificam proativamente os problemas dos dados em seu espaço de trabalho.  Cada regra de alerta é baseada em uma pesquisa de log que é executada automaticamente em intervalos regulares.  Os resultados são inspecionados para determinar se um alerta deve ser criado.
- **Painéis.** Você pode fixar os resultados de qualquer consulta em um [painel do Azure](../learn/tutorial-logs-dashboards.md) que permita que você visualize dados de log e métricas juntos e, opcionalmente, compartilhe com outros usuários do Azure.
- **Modos de exibição.**  Você pode criar visualizações de dados a serem incluídas nos painéis do usuário com o [Designer de exibição](../platform/view-designer.md).  As consultas de log fornecem os dados usados por [peças de visualização](../platform/view-designer-parts.md) e [blocos](../platform/view-designer-tiles.md) em cada exibição.  
- **Exportar.**  Ao importar dados de log de Azure Monitor para o Excel ou [Power bi](../platform/powerbi.md), você cria uma consulta de log para definir os dados a serem exportados.
- **PowerShell.** Você pode executar um script do PowerShell de uma linha de comando ou um runbook de automação do Azure que usa [Get-AzOperationalInsightsSearchResults](/powershell/module/az.operationalinsights/get-azoperationalinsightssearchresult) para recuperar dados de log de Azure monitor.  Esse cmdlet requer uma consulta para determinar os dados a serem recuperados.
- **API de logs de Azure Monitor.**  A [API de logs de Azure monitor](https://dev.loganalytics.io) permite que qualquer cliente da API REST recupere dados de log do espaço de trabalho.  A solicitação de API inclui uma consulta que é executada em relação a Azure Monitor para determinar os dados a serem recuperados.


## <a name="next-steps"></a>Passos seguintes
- Percorra um [tutorial sobre como usar log Analytics no portal do Azure](get-started-portal.md).
- Percorra um [tutorial sobre como escrever consultas](get-started-queries.md).
