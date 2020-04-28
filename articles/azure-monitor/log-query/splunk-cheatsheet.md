---
title: Splunk para consulta de log Do Monitor Azure [ Splunk to Azure Monitor log consulta ] Microsoft Docs
description: Ajuda para os utilizadores que estão familiarizados com splunk na aprendizagem de consultas de registo do Monitor Azure.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/21/2018
ms.openlocfilehash: 6346055f1169bfa533d5dbfe441ecf27fb0d78a7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75397751"
---
# <a name="splunk-to-azure-monitor-log-query"></a>Splunk para consulta de log Do Monitor Azure

Este artigo destina-se a ajudar os utilizadores que estão familiarizados com o Splunk a aprender a linguagem de consulta kusto a escrever consultas de registo no Monitor Azure. São feitas comparações diretas entre os dois para compreender as principais diferenças e também as semelhanças em que pode alavancar o seu conhecimento existente.

## <a name="structure-and-concepts"></a>Estrutura e conceitos

A tabela seguinte compara conceitos e estruturas de dados entre os registos Splunk e Azure Monitor.

 | Conceito  | Splunk | Azure Monitor |  Comentário
 | --- | --- | --- | ---
 | Unidade de implantação  | cluster |  cluster |  O Monitor Azure permite consultas arbitrárias de clusters cruzados. Splunk não. |
 | Caches de dados |  baldes  |  Políticas de caching e retenção |  Controla o período e o nível de cache para os dados. Esta definição impacta diretamente o desempenho das consultas e do custo da implantação. |
 | Partição lógica de dados  |  índice  |  base de dados  |  Permite a separação lógica dos dados. Ambas as implementações permitem aos sindicatos e juntem-se a estas divisórias. |
 | Metadados estruturados de eventos | N/D | tabela |  A Splunk não tem o conceito exposto à linguagem de pesquisa dos metadados do evento. Os registos do Monitor Azure têm o conceito de uma tabela, que tem colunas. Cada instância de evento é mapeada para uma linha. |
 | Registo de dados | evento | linha |  A terminologia só muda. |
 | Atributo de registo de dados | campo |  coluna |  No Monitor Azure, este é predefinido como parte da estrutura da tabela. Em Splunk, cada evento tem o seu próprio conjunto de campos. |
 | Tipos | tipo de dados |  tipo de dados |  Os tipos de dados do Monitor Azure são mais explícitos à medida que são definidos nas colunas. Ambos têm a capacidade de trabalhar dinamicamente com tipos de dados e um conjunto aproximadamente equivalente de tipos de dados, incluindo suporte JSON. |
 | Consulta e pesquisa  | pesquisar | consulta |  Os conceitos são essencialmente os mesmos entre o Monitor Azure e o Splunk. |
 | Tempo de ingestão de eventos | Tempo do sistema | ingestion_time() |  Em Splunk, cada evento obtém um carimbo de tempo do tempo que o evento foi indexado. No Monitor Azure, pode definir uma política chamada ingestion_time que expõe uma coluna do sistema que pode ser referenciada através da função ingestion_time(). |

## <a name="functions"></a>Funções

O quadro seguinte especifica funções no Monitor Azure que são equivalentes às funções Splunk.

|Splunk | Azure Monitor |Comentário
|---|---|---
|strcat | strcat()| (1) |
|dividir  | split() | (1) |
|if     | iff()   | (1) |
|número | todouble()<br>tolong()<br>toint() | (1) |
|superior<br>menor |toupper()<br>tolower()|(1) |
| substituir | replace() | (1)<br> Note também `replace()` que, embora leve três parâmetros em ambos os produtos, os parâmetros são diferentes. |
| substr | substring() | (1)<br>Note também que Splunk usa índices de base única. O Monitor Azure regista índices de base zero. |
| tolower |  tolower() | (1) |
| toia | toupper() | (1) |
| jogo | jogos regex |  (2)  |
| regex | jogos regex | Em Splunk, `regex` é um operador. No Monitor Azure, é um operador relacional. |
| searchmatch | == | Em Splunk, `searchmatch` permite procurar a corda exata.
| aleatório | rand()<br>rand(n) | A função de Splunk devolve um número de zero a 2<sup>31-1.</sup> O Azure Monitor devolve um número entre 0,0 e 1.0, ou se for fornecido um parâmetro, entre 0 e n-1.
| agora | now() | (1)
| relative_time | totimespan() | (1)<br>No Monitor Azure, o equivalente da Splunk a relative_time (dataval, offsetVal) é dataVal + totimespan (offsetVal).<br>Por <code>search &#124; eval n=relative_time(now(), "-1d@d")</code> exemplo, <code>...  &#124; extend myTime = now() - totimespan("1d")</code>torna-se .

(1) Em Splunk, a função `eval` é invocada com o operador. No Monitor Azure, é utilizado `extend` `project`como parte de ou .<br>(2) Em Splunk, a função `eval` é invocada com o operador. No Monitor Azure, pode ser `where` utilizado com o operador.


## <a name="operators"></a>Operadores

As seguintes secções dão exemplos da utilização de diferentes operadores entre o Splunk e o Monitor Azure.

> [!NOTE]
> Para efeitos do seguinte exemplo, a _regra_ do campo Splunk mapeia para uma mesa no Monitor Azure e os mapas padrão de carimbo de tempo de Splunk para a coluna Logs Analytics _ingestion_time()._

### <a name="search"></a>Pesquisa
Em Splunk, pode omiti-la a `search` palavra-chave e especificar uma corda não citada. No Monitor Azure deve iniciar `find`cada consulta com , uma corda não citada é um nome de coluna, e o valor de procura deve ser uma cadeia citada. 

| |  | |
|:---|:---|:---|
| Splunk | **pesquisa** | <code>search Session.Id="c8894ffd-e684-43c9-9125-42adc25cd3fc" earliest=-24h</code> |
| Azure Monitor | **encontrar** | <code>find Session.Id=="c8894ffd-e684-43c9-9125-42adc25cd3fc" and ingestion_time()> ago(24h)</code> |
| | |

### <a name="filter"></a>Filtro
As consultas de registo do Monitor Azure partem de um conjunto de resultados tabular onde o filtro. Em Splunk, a filtragem é o funcionamento predefinido no índice atual. Também pode `where` utilizar o operador em Splunk, mas não é recomendado.

| |  | |
|:---|:---|:---|
| Splunk | **pesquisa** | <code>Event.Rule="330009.2" Session.Id="c8894ffd-e684-43c9-9125-42adc25cd3fc" _indextime>-24h</code> |
| Azure Monitor | **onde onde** | <code>Office_Hub_OHubBGTaskError<br>&#124; where Session_Id == "c8894ffd-e684-43c9-9125-42adc25cd3fc" and ingestion_time() > ago(24h)</code> |
| | |


### <a name="getting-n-eventsrows-for-inspection"></a>Obtenção n eventos/filas para inspeção 
As consultas de registo `take` do Monitor Azure também suportam como pseudónimo para `limit`. Em Splunk, se os `head` resultados forem encomendados, devolverá os primeiros resultados n. No Monitor Azure, o limite não é encomendado, mas devolve as primeiras linhas n que são encontradas.

| |  | |
|:---|:---|:---|
| Splunk | **cabeça** | <code>Event.Rule=330009.2<br>&#124; head 100</code> |
| Azure Monitor | **limite** | <code>Office_Hub_OHubBGTaskError<br>&#124; limit 100</code> |
| | |



### <a name="getting-the-first-n-eventsrows-ordered-by-a-fieldcolumn"></a>Obter os primeiros eventos/linhas de n ordenadopor um campo/coluna
Para os resultados inferiores, `tail`em Splunk utiliza-se . No Monitor Azure pode especificar `asc`a direção de encomenda com .

| |  | |
|:---|:---|:---|
| Splunk | **cabeça** |  <code>Event.Rule="330009.2"<br>&#124; sort Event.Sequence<br>&#124; head 20</code> |
| Azure Monitor | **Início** | <code>Office_Hub_OHubBGTaskError<br>&#124; top 20 by Event_Sequence</code> |
| | |




### <a name="extending-the-result-set-with-new-fieldscolumns"></a>Alargar o resultado definido com novos campos/colunas
A Splunk `eval` também tem uma função, `eval` que não deve ser comparável com o operador. Tanto `eval` o operador da Splunk como o `extend` operador do Monitor Azure apenas suportam funções escalar e operadores aritméticos.

| |  | |
|:---|:---|:---|
| Splunk | **eval** |  <code>Event.Rule=330009.2<br>&#124; eval state= if(Data.Exception = "0", "success", "error")</code> |
| Azure Monitor | **estender** | <code>Office_Hub_OHubBGTaskError<br>&#124; extend state = iif(Data_Exception == 0,"success" ,"error")</code> |
| | |


### <a name="rename"></a>Mudar o Nome 
O Monitor Azure utiliza o operador para mudar o `project-rename` nome de um campo. `project-rename`permite que a consulta aproveite quaisquer índices pré-construídos para um campo. A Splunk `rename` tem um operador para fazer o mesmo.

| |  | |
|:---|:---|:---|
| Splunk | **renome** |  <code>Event.Rule=330009.2<br>&#124; rename Date.Exception as execption</code> |
| Azure Monitor | **projeto-renome** | <code>Office_Hub_OHubBGTaskError<br>&#124; project-rename exception = Date_Exception</code> |
| | |




### <a name="format-resultsprojection"></a>Resultados do formato/projeção
A Splunk não parece ter um `project-away`operador semelhante ao . Pode usar a UI para filtrar campos afastados.

| |  | |
|:---|:---|:---|
| Splunk | **tabela** |  <code>Event.Rule=330009.2<br>&#124; table rule, state</code> |
| Azure Monitor | **projeto**<br>**projeto-longe** | <code>Office_Hub_OHubBGTaskError<br>&#124; project exception, state</code> |
| | |



### <a name="aggregation"></a>Agregação
Consulte as [agregações em consultas](aggregations.md) de registo do Monitor Azure para as diferentes funções de agregação.

| |  | |
|:---|:---|:---|
| Splunk | **estatísticas** |  <code>search (Rule=120502.*)<br>&#124; stats count by OSEnv, Audience</code> |
| Azure Monitor | **resumo** | <code>Office_Hub_OHubBGTaskError<br>&#124; summarize count() by App_Platform, Release_Audience</code> |
| | |



### <a name="join"></a>Associar
Juntar-se em Splunk tem limitações significativas. O subquery tem um limite de 10000 resultados (definido no ficheiro de configuração de implementação), e há um número limitado de sabores de adesão.

| |  | |
|:---|:---|:---|
| Splunk | **juntar-se** |  <code>Event.Rule=120103* &#124; stats by Client.Id, Data.Alias \| join Client.Id max=0 [search earliest=-24h Event.Rule="150310.0" Data.Hresult=-2147221040]</code> |
| Azure Monitor | **juntar-se** | <code>cluster("OAriaPPT").database("Office PowerPoint").Office_PowerPoint_PPT_Exceptions<br>&#124; where  Data_Hresult== -2147221040<br>&#124; join kind = inner (Office_System_SystemHealthMetadata<br>&#124; summarize by Client_Id, Data_Alias)on Client_Id</code>   |
| | |



### <a name="sort"></a>Ordenar
Em Splunk, para ordenar em ordem `reverse` ascendente, deve utilizar o operador. O Azure Monitor também apoia a definição de onde colocar nulos, no início ou no final.

| |  | |
|:---|:---|:---|
| Splunk | **classificar** |  <code>Event.Rule=120103<br>&#124; sort Data.Hresult<br>&#124; reverse</code> |
| Azure Monitor | **ordem por** | <code>Office_Hub_OHubBGTaskError<br>&#124; order by Data_Hresult,  desc</code> |
| | |



### <a name="multivalue-expand"></a>Expansão multivalorizar
Trata-se de um operador semelhante tanto no Splunk como no Monitor Azure.

| |  | |
|:---|:---|:---|
| Splunk | **mvexpand** |  `mvexpand foo` |
| Azure Monitor | **mvexpand** | `mvexpand foo` |
| | |




### <a name="results-facets-interesting-fields"></a>Facetas de resultados, campos interessantes
No Log Analytics no portal Azure, apenas a primeira coluna está exposta. Todas as colunas estão disponíveis através da API.

| |  | |
|:---|:---|:---|
| Splunk | **campos** |  <code>Event.Rule=330009.2<br>&#124; fields App.Version, App.Platform</code> |
| Azure Monitor | **facetas** | <code>Office_Excel_BI_PivotTableCreate<br>&#124; facet by App_Branch, App_Version</code> |
| | |




### <a name="de-duplicate"></a>De-duplicado
Pode utilizar `summarize arg_min()` para inverter a ordem de qual o registo é escolhido.

| |  | |
|:---|:---|:---|
| Splunk | **dedup** |  <code>Event.Rule=330009.2<br>&#124; dedup device_id sortby -batterylife</code> |
| Azure Monitor | **resumir arg_max()** | <code>Office_Excel_BI_PivotTableCreate<br>&#124; summarize arg_max(batterylife, *) by device_id</code> |
| | |




## <a name="next-steps"></a>Passos seguintes

- Passe por uma lição sobre as consultas de registo de [escrita no Monitor Azure.](get-started-queries.md)
