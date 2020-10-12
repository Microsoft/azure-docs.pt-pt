---
title: Visão geral das consultas de registo no Azure Monitor Microsoft Docs
description: Responde a questões comuns relacionadas com consultas de registo e faz com que você começa a usá-las.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/19/2019
ms.openlocfilehash: 5a47de51a90d926b47f6b465d6c10111407da9c6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90033171"
---
# <a name="overview-of-log-queries-in-azure-monitor"></a>Visão geral das consultas de registo no Azure Monitor
As consultas de registo ajudam-no a aproveitar totalmente o valor dos dados recolhidos nos [Registos do Monitor Azure](../platform/data-platform-logs.md). Uma linguagem de consulta poderosa permite-lhe juntar dados de várias tabelas, agregar grandes conjuntos de dados e realizar operações complexas com código mínimo. Praticamente qualquer pergunta pode ser respondida e a análise realizada desde que os dados de suporte sejam recolhidos, e você entende como construir a consulta correta.

Algumas funcionalidades no Azure Monitor, como [insights](../insights/insights-overview.md) e [soluções,](../monitor-reference.md) processam dados de registo sem o expor às consultas subjacentes. Para aproveitar totalmente outras funcionalidades do Azure Monitor, deve entender como as consultas são construídas e como pode usá-las para analisar interativamente dados em Registos do Monitor Azure.

Use este artigo como ponto de partida para aprender sobre consultas de registo no Azure Monitor. Responde a questões comuns e fornece ligações a outra documentação que fornece mais detalhes e lições.

## <a name="how-can-i-learn-how-to-write-queries"></a>Como posso aprender a escrever consultas?
Se quiser saltar para as coisas, pode começar com os seguintes tutoriais:

- [Começa com o Log Analytics no Azure Monitor](get-started-portal.md).
- [Começa com consultas de registo no Azure Monitor](get-started-queries.md).

Assim que tiver o básico em baixo, caminhe por várias lições usando os seus próprios dados ou dados do nosso ambiente de demonstração começando com: 

- [Trabalhe com cordas em consultas de log do Azure Monitor](string-operations.md)
 
## <a name="what-language-do-log-queries-use"></a>Que linguagem usam as consultas de registo?
Os Registos do Monitor Azure baseiam-se no [Azure Data Explorer,](/azure/data-explorer)e as consultas de registo são escritas utilizando a mesma linguagem de consulta Kusto (KQL). Esta é uma linguagem rica projetada para ser fácil de ler e autor, e você deve ser capaz de começar a usá-lo com a mínima orientação.

Consulte [a documentação KQL do Azure Data Explorer](/azure/kusto/query) para obter documentação completa sobre o KQL e referência sobre as diferentes funções disponíveis.<br>
Consulte [Começar com consultas de log no Azure Monitor](get-started-queries.md) para uma rápida passagem do idioma utilizando dados de Registos do Monitor Azure.
Consulte [as diferenças linguísticas de consulta de registo do Azure Monitor](data-explorer-difference.md) para pequenas diferenças na versão do KQL utilizada pelo Azure Monitor.

## <a name="what-data-is-available-to-log-queries"></a>Que dados estão disponíveis para registar consultas?
Todos os dados recolhidos nos Registos do Monitor Azure estão disponíveis para recuperar e analisar em consultas de registo. Diferentes fontes de dados escreverão os seus dados em diferentes tabelas, mas pode incluir várias tabelas numa única consulta para analisar dados em várias fontes. Quando se constrói uma consulta, começa-se por determinar quais as tabelas que tem os dados que procura. Consulte [os Registos de Monitores Azure](logs-structure.md) para obter uma explicação de como os dados são estruturados.

## <a name="what-does-a-log-query-look-like"></a>Como é uma consulta de registo?
Uma consulta pode ser tão simples como um único nome de mesa para recuperar todos os registos daquela tabela:

```Kusto
Syslog
```

Ou pode filtrar para registos particulares, resumi-los e visualizar os resultados num gráfico:

```
SecurityEvent
| where TimeGenerated > ago(7d)
| where EventID == 4625
| summarize count() by Computer, bin(TimeGenerated, 1h)
| render timechart 
```

Para uma análise mais complexa, poderá obter dados de várias tabelas usando uma junção para analisar os resultados em conjunto.

```Kusto
app("ContosoRetailWeb").requests
| summarize count() by bin(timestamp,1hr)
| join kind= inner (Perf
    | summarize avg(CounterValue) 
      by bin(TimeGenerated,1hr))
on $left.timestamp == $right.TimeGenerated
```
Mesmo que não esteja familiarizado com o KQL, deve ser capaz de, pelo menos, descobrir a lógica básica que está a ser usada por estas consultas. Começam com o nome de uma tabela e depois adicionam vários comandos para filtrar e processar esses dados. Uma consulta pode usar qualquer número de comandos, e você pode escrever consultas mais complexas à medida que se familiariza com os diferentes comandos KQL disponíveis.

Consulte [Começar com consultas de log no Azure Monitor](get-started-queries.md) para um tutorial em consultas de log que introduz a linguagem e as funções comuns, .<br>


## <a name="what-is-log-analytics"></a>O que é o Log Analytics?
O Log Analytics é a principal ferramenta no portal do Azure para escrever consultas de registo e analisar interativamente os seus resultados. Mesmo que uma consulta de log seja usada em outro lugar no Azure Monitor, você normalmente escreverá e testará a consulta primeiro usando Log Analytics.

Pode iniciar o Log Analytics a partir de vários locais do portal Azure. O âmbito dos dados disponíveis para o Log Analytics é determinado pela forma como o inicia. Consulte [o Âmbito de Consulta](scope.md) para mais detalhes.

- Selecione **Logs** do menu **Azure Monitor** ou do menu **de espaços de trabalho Log Analytics.**
- Selecione **Registos** na página **de visão geral** de uma aplicação 'Insights de aplicação'.
- Selecione **Registos** do menu de um recurso Azure.

![Log Analytics](media/log-query-overview/log-analytics.png)

Veja [Começar com o Log Analytics no Azure Monitor](get-started-portal.md) para uma passagem tutorial do Log Analytics que introduz várias das suas funcionalidades.

## <a name="where-else-are-log-queries-used"></a>Onde mais são usadas consultas de registo?
Além de trabalhar interativamente com consultas de log e seus resultados em Log Analytics, as áreas em Azure Monitor onde você usará consultas incluem:

- **Regras de alerta.** [As regras de alerta](../platform/alerts-overview.md) identificam proativamente problemas a partir de dados no seu espaço de trabalho.  Cada regra de alerta baseia-se numa pesquisa de registo que é executada automaticamente em intervalos regulares.  Os resultados são inspecionados para determinar se deve ser criado um alerta.
- **Os tabliers.** Pode fixar os resultados de qualquer consulta num [dashboard Azure](../learn/tutorial-logs-dashboards.md) que lhe permita visualizar dados de registo e métrica em conjunto e partilhar opcionalmente com outros utilizadores do Azure.
- **Modos de exibição.**  Pode criar visualizações de dados a incluir nos dashboards do utilizador com [o View Designer.](../platform/view-designer.md)  As consultas de registo fornecem os dados [utilizados](../platform/view-designer-tiles.md) por azulejos e [peças de visualização](../platform/view-designer-parts.md) em cada vista.  
- **Exportar.**  Quando importa dados de registo do Azure Monitor para o Excel ou [o Power BI,](../platform/powerbi.md)cria uma consulta de registo para definir os dados para exportar.
- **PowerShell.** Pode executar um script PowerShell a partir de uma linha de comando ou de um runbook Azure Automation que utiliza [o Get-AzOperationalInsightsSearchResults](/powershell/module/az.operationalinsights/get-azoperationalinsightssearchresult) para recuperar dados de registo do Azure Monitor.  Este cmdlet requer uma consulta para determinar os dados para recuperar.
- **Azure Monitor Regista API.**  A [Azure Monitor Logs API](https://dev.loganalytics.io) permite que qualquer cliente da API REST recupere dados de registo do espaço de trabalho.  O pedido da API inclui uma consulta que é executada contra o Azure Monitor para determinar os dados a recuperar.


## <a name="next-steps"></a>Passos seguintes
- Caminhe através de um [tutorial sobre a utilização do Log Analytics no portal Azure](get-started-portal.md).
- Caminhe por um [tutorial sobre consultas de escrita.](get-started-queries.md)
