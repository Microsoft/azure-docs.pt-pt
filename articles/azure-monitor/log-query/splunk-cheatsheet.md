---
title: Splunk para Azure Monitor consulta de log | Microsoft Docs
description: Ajuda para usuários que estão familiarizados com Splunk em consultas de log de Azure Monitor de aprendizado.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/21/2018
ms.author: bwren
ms.openlocfilehash: 03a0d755cf6d099f07a7c6d853e1d747908eec05
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2019
ms.locfileid: "72177641"
---
# <a name="splunk-to-azure-monitor-log-query"></a>Splunk para Azure Monitor consulta de log

Este artigo destina-se a ajudar os usuários que estão familiarizados com o Splunk a aprender a linguagem de consulta do Kusto para gravar consultas de log em Azure Monitor. São feitas comparações diretas entre os dois para entender as principais diferenças e também as semelhanças em que você pode aproveitar seu conhecimento existente.

## <a name="structure-and-concepts"></a>Estrutura e conceitos

A tabela a seguir compara conceitos e estruturas de dados entre Splunk e logs de Azure Monitor.

 | Conceito  | Splunk | Azure Monitor |  Comentário
 | --- | --- | --- | ---
 | Unidade de implantação  | em |  em |  Azure Monitor permite consultas entre clusters arbitrárias. Splunk não. |
 | Caches de dados |  buckets  |  Políticas de retenção e cache |  Controla o período e o nível de cache para os dados. Essa configuração afeta diretamente o desempenho de consultas e o custo da implantação. |
 | Partição lógica de dados  |  index  |  database  |  Permite a separação lógica dos dados. Ambas as implementações permitem uniões e junção entre essas partições. |
 | Metadados de eventos estruturados | N/A | table |  Splunk não tem o conceito exposto à linguagem de pesquisa de metadados de eventos. Os logs de Azure Monitor têm o conceito de uma tabela, que tem colunas. Cada instância de evento é mapeada para uma linha. |
 | Registro de dados | event | fila |  Somente a terminologia é alterada. |
 | Atributo de registro de dados | Campo |  Coluna |  No Azure Monitor, isso é predefinido como parte da estrutura da tabela. No Splunk, cada evento tem seu próprio conjunto de campos. |
 | Digita | tipo de dados |  tipo de dados |  Azure Monitor tipos de texto são mais explícitos, pois eles são definidos nas colunas. Ambos têm a capacidade de trabalhar dinamicamente com tipos de dados e um conjunto equivalente aproximadamente de tipos de dado, incluindo suporte a JSON. |
 | Consulta e pesquisa  | Procurando | query |  Os conceitos são essencialmente os mesmos entre Azure Monitor e Splunk. |
 | Tempo de ingestão de eventos | Hora do sistema | ingestion_time() |  No Splunk, cada evento Obtém um carimbo de data/hora do sistema do momento em que o evento foi indexado. No Azure Monitor, você pode definir uma política chamada ingestion_time que expõe uma coluna do sistema que pode ser referenciada por meio da função ingestion_time (). |

## <a name="functions"></a>Functions

A tabela a seguir especifica funções no Azure Monitor que são equivalentes às funções Splunk.

|Splunk | Azure Monitor |Comentário
|---|---|---
|strcat | strcat()| uma |
|split  | split() | uma |
|que     | IFF ()   | uma |
|número totais | todouble()<br>tolong()<br>toint() | uma |
|canto superior<br>canto |toupper()<br>tolower()|uma |
| Substitua | Replace () | uma<br> Observe também que, enquanto `replace()` usa três parâmetros em ambos os produtos, os parâmetros são diferentes. |
| subst | Subcadeia de caracteres () | uma<br>Observe também que o Splunk usa índices baseados em um. Índices com base em zero do Azure Monitor Notes. |
| ToLower |  tolower() | uma |
| ToUpper | toupper() | uma |
| às | corresponde ao Regex |  2  |
| Regex | corresponde ao Regex | Em Splunk, `regex` é um operador. No Azure Monitor, é um operador relacional. |
| searchmatch | == | Em Splunk, `searchmatch` permite pesquisar a cadeia de caracteres exata.
| megabit | Rand ()<br>Rand (n) | A função de Splunk retorna um número de zero a 2<sup>31</sup>-1. Azure Monitor ' retorna um número entre 0,0 e 1,0, ou se um parâmetro fornecido, entre 0 e n-1.
| agora | now() | uma
| relative_time | totimespan() | uma<br>No Azure Monitor, o equivalente de relative_time (datetimeVal, offsetVal) do Splunk é datetimeVal + ToTimeSpan (offsetVal).<br>Por exemplo, <code>search &#124; eval n=relative_time(now(), "-1d@d")</code> se torna <code>...  &#124; extend myTime = now() - totimespan("1d")</code>.

(1) em Splunk, a função é invocada com o operador `eval`. No Azure Monitor, ele é usado como parte de `extend` ou `project`.<br>(2) em Splunk, a função é invocada com o operador `eval`. No Azure Monitor, ele pode ser usado com o operador `where`.


## <a name="operators"></a>Operadores

As seções a seguir fornecem exemplos de como usar operadores diferentes entre Splunk e Azure Monitor.

> [!NOTE]
> Para a finalidade do exemplo a seguir, a _regra_ de campo Splunk é mapeada para uma tabela em Azure monitor e o carimbo de data/hora padrão de Splunk é mapeado para a coluna _ingestion_time ()_ do log Analytics.

### <a name="search"></a>Pesquisa
No Splunk, você pode omitir a palavra-chave `search` e especificar uma cadeia de caracteres sem aspas. No Azure Monitor você deve iniciar cada consulta com `find`, uma cadeia de caracteres sem aspas é um nome de coluna e o valor de pesquisa deve ser uma cadeia de caracteres entre aspas. 

| |  | |
|:---|:---|:---|
| Splunk | **procurando** | <code>search Session.Id="c8894ffd-e684-43c9-9125-42adc25cd3fc" earliest=-24h</code> |
| Azure Monitor | **find** | <code>find Session.Id=="c8894ffd-e684-43c9-9125-42adc25cd3fc" and ingestion_time()> ago(24h)</code> |
| | |

### <a name="filter"></a>Filtro
Azure Monitor consultas de log iniciam de um conjunto de resultados tabulares em que o filtro. No Splunk, a filtragem é a operação padrão no índice atual. Você também pode usar o operador `where` em Splunk, mas isso não é recomendado.

| |  | |
|:---|:---|:---|
| Splunk | **procurando** | <code>Event.Rule="330009.2" Session.Id="c8894ffd-e684-43c9-9125-42adc25cd3fc" _indextime>-24h</code> |
| Azure Monitor | **posição** | <code>Office_Hub_OHubBGTaskError<br>&#124; where Session_Id == "c8894ffd-e684-43c9-9125-42adc25cd3fc" and ingestion_time() > ago(24h)</code> |
| | |


### <a name="getting-n-eventsrows-for-inspection"></a>Obtendo n eventos/linhas para inspeção 
Azure Monitor consultas de log também dão suporte a `take` como um alias para `limit`. No Splunk, se os resultados forem ordenados, `head` retornará os primeiros n resultados. No Azure Monitor, o limite não é ordenado, mas retorna as primeiras n linhas que são encontradas.

| |  | |
|:---|:---|:---|
| Splunk | **principal** | <code>Event.Rule=330009.2<br>&#124; head 100</code> |
| Azure Monitor | **limit** | <code>Office_Hub_OHubBGTaskError<br>&#124; limit 100</code> |
| | |



### <a name="getting-the-first-n-eventsrows-ordered-by-a-fieldcolumn"></a>Obtendo os primeiros n eventos/linhas ordenados por um campo/coluna
Para resultados finais, em Splunk você usa `tail`. Em Azure Monitor você pode especificar a direção da ordem com `asc`.

| |  | |
|:---|:---|:---|
| Splunk | **principal** |  <code>Event.Rule="330009.2"<br>&#124; sort Event.Sequence<br>&#124; head 20</code> |
| Azure Monitor | **Início** | <code>Office_Hub_OHubBGTaskError<br>&#124; top 20 by Event_Sequence</code> |
| | |




### <a name="extending-the-result-set-with-new-fieldscolumns"></a>Estendendo o conjunto de resultados com novos campos/colunas
Splunk também tem uma função `eval`, que não deve ser comparável com o operador `eval`. O operador `eval` em Splunk e o operador `extend` no Azure Monitor dão suporte apenas a funções escalares e operadores aritméticos.

| |  | |
|:---|:---|:---|
| Splunk | **eval** |  <code>Event.Rule=330009.2<br>&#124; eval state= if(Data.Exception = "0", "success", "error")</code> |
| Azure Monitor | **extend** | <code>Office_Hub_OHubBGTaskError<br>&#124; extend state = iif(Data_Exception == 0,"success" ,"error")</code> |
| | |


### <a name="rename"></a>Mudar o Nome 
Azure Monitor usa o operador `project-rename` para renomear um campo. `project-rename` permite que a consulta aproveite todos os índices criados previamente para um campo. Splunk tem um operador `rename` para fazer o mesmo.

| |  | |
|:---|:---|:---|
| Splunk | **rename** |  <code>Event.Rule=330009.2<br>&#124; rename Date.Exception as execption</code> |
| Azure Monitor | **projeto-renomear** | <code>Office_Hub_OHubBGTaskError<br>&#124; project-rename exception = Date_Exception</code> |
| | |




### <a name="format-resultsprojection"></a>Formatar resultados/projeção
Splunk não parece ter um operador semelhante a `project-away`. Você pode usar a interface do usuário para filtrar campos ausentes.

| |  | |
|:---|:---|:---|
| Splunk | **tabela** |  <code>Event.Rule=330009.2<br>&#124; table rule, state</code> |
| Azure Monitor | **project**<br>**project-away** | <code>Office_Hub_OHubBGTaskError<br>&#124; project exception, state</code> |
| | |



### <a name="aggregation"></a>Agregação
Consulte as [agregações em Azure monitor consultas de log](aggregations.md) para as diferentes funções de agregação.

| |  | |
|:---|:---|:---|
| Splunk | **Estatísticas** |  <code>search (Rule=120502.*)<br>&#124; stats count by OSEnv, Audience</code> |
| Azure Monitor | **summarize** | <code>Office_Hub_OHubBGTaskError<br>&#124; summarize count() by App_Platform, Release_Audience</code> |
| | |



### <a name="join"></a>Associar
O Join In Splunk tem limitações significativas. A subconsulta tem um limite de 10000 resultados (definido no arquivo de configuração de implantação) e há um número limitado de tipos de junção.

| |  | |
|:---|:---|:---|
| Splunk | **join** |  <code>Event.Rule=120103* &#124; stats by Client.Id, Data.Alias \| join Client.Id max=0 [search earliest=-24h Event.Rule="150310.0" Data.Hresult=-2147221040]</code> |
| Azure Monitor | **join** | <code>cluster("OAriaPPT").database("Office PowerPoint").Office_PowerPoint_PPT_Exceptions<br>&#124; where  Data_Hresult== -2147221040<br>&#124; join kind = inner (Office_System_SystemHealthMetadata<br>&#124; summarize by Client_Id, Data_Alias)on Client_Id</code>   |
| | |



### <a name="sort"></a>Ordenar
Em Splunk, para classificar em ordem crescente, você deve usar o operador `reverse`. Azure Monitor também dá suporte à definição de onde colocar os valores nulos, no início ou no final.

| |  | |
|:---|:---|:---|
| Splunk | **sort** |  <code>Event.Rule=120103<br>&#124; sort Data.Hresult<br>&#124; reverse</code> |
| Azure Monitor | **ordenar por** | <code>Office_Hub_OHubBGTaskError<br>&#124; order by Data_Hresult,  desc</code> |
| | |



### <a name="multivalue-expand"></a>Expansão de vários múltiplos
Esse é um operador semelhante em Splunk e Azure Monitor.

| |  | |
|:---|:---|:---|
| Splunk | **mvexpand** |  `mvexpand foo` |
| Azure Monitor | **mvexpand** | `mvexpand foo` |
| | |




### <a name="results-facets-interesting-fields"></a>Facetas de resultados, campos interessantes
Em Log Analytics no portal do Azure, somente a primeira coluna é exposta. Todas as colunas estão disponíveis por meio da API.

| |  | |
|:---|:---|:---|
| Splunk | **fields** |  <code>Event.Rule=330009.2<br>&#124; fields App.Version, App.Platform</code> |
| Azure Monitor | **facets** | <code>Office_Excel_BI_PivotTableCreate<br>&#124; facet by App_Branch, App_Version</code> |
| | |




### <a name="de-duplicate"></a>Eliminação de duplicação
Você pode usar `summarize arg_min()` em vez de inverter a ordem em que o registro é escolhido.

| |  | |
|:---|:---|:---|
| Splunk | **eliminação** |  <code>Event.Rule=330009.2<br>&#124; dedup device_id sortby -batterylife</code> |
| Azure Monitor | **resumir ARG_MAX ()** | <code>Office_Excel_BI_PivotTableCreate<br>&#124; summarize arg_max(batterylife, *) by device_id</code> |
| | |




## <a name="next-steps"></a>Passos seguintes

- Percorra uma lição sobre as [consultas de log de gravação em Azure monitor](get-started-queries.md).
