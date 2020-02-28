---
title: Visão geral das consultas de log no Monitor Azure [ Monitor De acesso à sessão de freguesia de Azure] Microsoft Docs
description: Responde a questões comuns relacionadas com consultas de registo e faz com que as utilize.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/19/2019
ms.openlocfilehash: 54a6f875bc33d24d412d2424c634d1019b4af399
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77670122"
---
# <a name="overview-of-log-queries-in-azure-monitor"></a>Visão geral das consultas de log no Monitor Azure
As consultas de registo ajudam-no a alavancar totalmente o valor dos dados recolhidos nos [Registos do Monitor do Azure](../platform/data-platform-logs.md). Uma linguagem de consulta poderosa permite-lhe juntar dados de várias tabelas, agregar grandes conjuntos de dados e realizar operações complexas com código mínimo. Praticamente qualquer pergunta pode ser respondida e a análise realizada desde que os dados de suporte sejam recolhidos, e você entende como construir a consulta certa.

Algumas funcionalidades no Monitor Azure, tais como [insights](../insights/insights-overview.md) e [soluções](../insights/solutions-inventory.md) processam dados de registo sem o expor às consultas subjacentes. Para alavancar totalmente outras funcionalidades do Monitor Azure, deve compreender como as consultas são construídas e como pode utilizá-las para analisar interativamente dados em Registos do Monitor Do Azure.

Utilize este artigo como ponto de partida para aprender sobre consultas de log no Monitor Azure. Responde a perguntas comuns e fornece ligações a outra documentação que fornece mais detalhes e lições.

## <a name="how-can-i-learn-how-to-write-queries"></a>Como posso aprender a escrever consultas?
Se quiser saltar para as coisas, pode começar com os seguintes tutoriais:

- [Inicie com Log Analytics no Monitor Azure](get-started-portal.md).
- [Começar com consultas de log no Monitor Azure.](get-started-queries.md)

Assim que tiver o básico para baixo, caminhe por várias lições usando os seus próprios dados ou dados do nosso ambiente de demonstração começando com: 

- [Trabalhe com cordas em consultas de registo do Monitor Azure](string-operations.md)
 
## <a name="what-language-do-log-queries-use"></a>Que linguagem as consultas de registo usam?
Os Registos do Monitor Azure baseiam-se no [Azure Data Explorer,](/azure/data-explorer)e as consultas de registo são escritas utilizando a mesma linguagem de consulta Kusto (KQL). Esta é uma linguagem rica projetada para ser fácil de ler e autor, e você deve ser capaz de começar a usá-lo com o mínimo de orientação.

Consulte a [documentação do Azure Data Explorer KQL](/azure/kusto/query) para obter documentação completa sobre a KQL e referência sobre as diferentes funções disponíveis.<br>
Consulte Começar com consultas de [log no Monitor Azure](get-started-queries.md) para uma rápida passagem pelo idioma utilizando dados de Registos do Monitor Do Azure.
Consulte [as diferenças linguísticas](data-explorer-difference.md) de consulta de registo do Azure Monitor para pequenas diferenças na versão do KQL utilizada pelo Azure Monitor.

## <a name="what-data-is-available-to-log-queries"></a>Que dados estão disponíveis para consultas de registo?
Todos os dados recolhidos nos Registos do Monitor Do Azure estão disponíveis para recuperar e analisar em consultas de registo. Diferentes fontes de dados escreverão os seus dados para diferentes tabelas, mas pode incluir várias tabelas numa única consulta para analisar dados através de várias fontes. Quando se constrói uma consulta, começa-se por determinar quais as tabelas que procura, pelo que deve ter pelo menos uma compreensão básica de como os dados em Registos do Monitor Do Azure são estruturados.

Consulte [as fontes de registos do Monitor Azure,](../platform/data-platform-logs.md#sources-of-azure-monitor-logs)para obter uma lista de diferentes fontes de dados que povoam os Registos do Monitor Azure.<br>
Consulte a [Estrutura dos Registos do Monitor Azure](logs-structure.md) para obter uma explicação de como os dados são estruturados.

## <a name="what-does-a-log-query-look-like"></a>Como é uma consulta de registo?
Uma consulta pode ser tão simples como um único nome de mesa para recuperar todos os registos dessa tabela:

```Kusto
Syslog
```

Ou pode filtrar para registos específicos, sintetizá-los e visualizar os resultados num gráfico:

```
SecurityEvent
| where TimeGenerated > ago(7d)
| where EventID == 4625
| summarize count() by Computer, bin(TimeGenerated, 1h)
| render timechart 
```

Para uma análise mais complexa, poderá obter dados de várias tabelas utilizando uma adesão para analisar os resultados em conjunto.

```Kusto
app("ContosoRetailWeb").requests
| summarize count() by bin(timestamp,1hr)
| join kind= inner (Perf
    | summarize avg(CounterValue) 
      by bin(TimeGenerated,1hr))
on $left.timestamp == $right.TimeGenerated
```
Mesmo que não esteja familiarizado com a KQL, deve ser capaz de pelo menos descobrir a lógica básica que está a ser usada por estas consultas. Começam com o nome de uma mesa e depois adicionam vários comandos para filtrar e processar esses dados. Uma consulta pode usar qualquer número de comandos, e você pode escrever consultas mais complexas à medida que se familiariza com os diferentes comandos KQL disponíveis.

Ver Começar com consultas de [log no Monitor Azure](get-started-queries.md) para um tutorial sobre consultas de log que introduz a linguagem e funções comuns, .<br>


## <a name="what-is-log-analytics"></a>O que é o Log Analytics?
O Log Analytics é a principal ferramenta no portal Azure para escrever consultas de registo e analisar interativamente os seus resultados. Mesmo que uma consulta de log seja usada em outro lugar do Monitor Azure, você normalmente escreverá e testará a consulta usando log analytics.

Pode iniciar o Log Analytics de vários locais do portal Azure. O âmbito dos dados disponíveis para o Log Analytics é determinado pela forma como os inicia. Consulte [o Âmbito](scope.md) de Consulta para mais detalhes.

- Selecione **Registos** do menu **Do Monitor Azure** ou do menu **de espaços de trabalho Do Log Analytics.**
- Selecione **Analytics** a partir da página **de visão geral** de uma aplicação Application Insights.
- Selecione **Registos** do menu de um recurso Azure.

![Log Analytics](media/log-query-overview/log-analytics.png)

Veja [o Get iniciado com o Log Analytics no Monitor Azure](get-started-portal.md) para um passe tutorial do Log Analytics que introduz várias das suas funcionalidades.

## <a name="where-else-are-log-queries-used"></a>Onde mais são usadas consultas de registo?
Além de trabalhar interativamente com consultas de registo e os seus resultados no Log Analytics, as áreas do Monitor Azure onde utilizará consultas incluem o seguinte:

- **Regras de alerta.** [As regras](../platform/alerts-overview.md) de alerta identificam proativamente problemas a partir de dados no seu espaço de trabalho.  Cada regra de alerta baseia-se numa pesquisa de registo que é executada automaticamente em intervalos regulares.  Os resultados são inspecionados para determinar se deve ser criado um alerta.
- **Tabliers.** Pode fixar os resultados de qualquer consulta num [dashboard Azure](../learn/tutorial-logs-dashboards.md) que lhe permita visualizar dados de registo e métricas e partilhar opcionalmente com outros utilizadores do Azure.
- **Modos de exibição.**  Pode criar visualizações de dados a serem incluídos nos dashboards dos utilizadores com [o View Designer](../platform/view-designer.md).  As consultas de registo fornecem os dados utilizados por [azulejos](../platform/view-designer-tiles.md) e peças de [visualização](../platform/view-designer-parts.md) em cada vista.  
- **Exportar.**  Quando importa dados de registo do Azure Monitor para excel ou [Power BI,](../platform/powerbi.md)cria uma consulta de registo para definir os dados para exportação.
- **PowerShell.** Pode executar um script PowerShell a partir de uma linha de comando ou de um livro de execução da Automatização Azure que utiliza [o Get-AzOperationalInsightsSearchResults](/powershell/module/az.operationalinsights/get-azoperationalinsightssearchresult) para recuperar dados de registo do Monitor Azure.  Este cmdlet requer uma consulta para determinar os dados para recuperar.
- **Azure Monitor Logs API.**  O [Azure Monitor Logs API](https://dev.loganalytics.io) permite que qualquer cliente rest API recupere dados de registo do espaço de trabalho.  O pedido da API inclui uma consulta que é feita contra o Monitor Azure para determinar os dados a recuperar.


## <a name="next-steps"></a>Passos seguintes
- Caminhe por um [tutorial sobre a utilização de Log Analytics no portal Azure](get-started-portal.md).
- Caminhe por um [tutorial sobre perguntas de escrita.](get-started-queries.md)
