---
title: Consulta de registo splunk para Azure Monitor Microsoft Docs
description: Ajuda para utilizadores que estejam familiarizados com o Splunk na aprendizagem de consultas de registo do Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/21/2018
ms.openlocfilehash: 00fdaf93553c97112c67caa66cb2246756b63c33
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86207482"
---
# <a name="splunk-to-azure-monitor-log-query"></a>Consulta de registo splunk para Azure Monitor

Este artigo destina-se a ajudar os utilizadores que estão familiarizados com a Splunk a aprender a língua de consulta kusto para escrever consultas de log no Azure Monitor. São feitas comparações diretas entre os dois para compreender as principais diferenças e também semelhanças onde pode alavancar os seus conhecimentos existentes.

## <a name="structure-and-concepts"></a>Estrutura e conceitos

A tabela a seguir compara conceitos e estruturas de dados entre os registos splunk e Azure Monitor.

 | Conceito  | Splunk | Azure Monitor |  Comentário
 | --- | --- | --- | ---
 | Unidade de implantação  | cluster |  cluster |  O Azure Monitor permite consultas arbitrárias de clusters cruzados. Splunk não. |
 | Caches de dados |  baldes  |  Política de caching e retenção |  Controla o período e o nível de caching para os dados. Esta definição impacta diretamente o desempenho das consultas e o custo da implantação. |
 | Partição lógica de dados  |  índice  |  base de dados  |  Permite a separação lógica dos dados. Ambas as implementações permitem aos sindicatos e juntam-se a estas divisórias. |
 | Metadados de eventos estruturados | N/D | table |  Splunk não tem o conceito exposto à linguagem de pesquisa de metadados de eventos. Os registos do Monitor Azure têm o conceito de tabela, que tem colunas. Cada instância do evento é mapeada para uma linha. |
 | Registo de dados | evento | row |  A terminologia só muda. |
 | Atributo de registo de dados | campo |  coluna |  No Azure Monitor, este é predefinido como parte da estrutura da mesa. Em Splunk, cada evento tem o seu próprio conjunto de campos. |
 | Tipos | tipo de dados |  tipo de dados |  Os tipos de dados do Monitor Azure são mais explícitos à medida que são definidos nas colunas. Ambos têm a capacidade de trabalhar dinamicamente com tipos de dados e um conjunto de tipos de dados aproximadamente equivalentes, incluindo suporte JSON. |
 | Consulta e pesquisa  | pesquisar | consulta |  Os conceitos são essencialmente os mesmos entre o Azure Monitor e o Splunk. |
 | Hora da ingestão de eventos | Tempo do sistema | ingestion_time() |  Em Splunk, cada evento obtém um relógio do sistema da época em que o evento foi indexado. No Azure Monitor, pode definir uma política chamada ingestion_time que expõe uma coluna do sistema que pode ser referenciada através da função ingestion_time(). |

## <a name="functions"></a>Funções

A tabela seguinte especifica funções no Monitor Azure que são equivalentes às funções splunk.

|Splunk | Azure Monitor |Comentário
|---|---|---
|strcat | strcat()| (1) |
|dividir  | split() | (1) |
|if     | iff()   | (1) |
|tonumer | todouble()<br>tolong()<br>toint() | (1) |
|superior<br>mais baixo |toupper()<br>tolower()|(1) |
| substituir | replace() | (1)<br> Note também que, embora `replace()` tenha três parâmetros em ambos os produtos, os parâmetros são diferentes. |
| substr | substring() | (1)<br>Note também que Splunk usa índices de uma base. O Monitor Azure regista índices baseados em zero. |
| tolower |  tolower() | (1) |
| toupper | toupper() | (1) |
| combinar | corresponde ao regex |  (2)  |
| regex | corresponde ao regex | Em Splunk, `regex` é um operador. No Azure Monitor, é um operador relacional. |
| searchmatch | == | Em Splunk, `searchmatch` permite procurar a corda exata.
| aleatório | rand()<br>rand(n) | A função de Splunk devolve um número de zero a 2<sup>31</sup>-1. O Azure Monitor devolve um número entre 0,0 e 1.0, ou se for fornecido um parâmetro, entre 0 e n-1.
| agora | now() | (1)
| relative_time | totimespan() | (1)<br>No Azure Monitor, o equivalente de Splunk a relative_time (datetimeVal, offsetVal) é datatimeVal + totimespan (offsetVal).<br>Por exemplo, <code>search &#124; eval n=relative_time(now(), "-1d@d")</code> torna-se <code>...  &#124; extend myTime = now() - totimespan("1d")</code> .

(1) Em Splunk, a função é invocada com o `eval` operador. No Azure Monitor, é utilizado como parte `extend` ou `project` .<br>(2) Em Splunk, a função é invocada com o `eval` operador. No Azure Monitor, pode ser utilizado com o `where` operador.


## <a name="operators"></a>Operadores

As seguintes secções dão exemplos de utilização de diferentes operadores entre o Splunk e o Azure Monitor.

> [!NOTE]
> Para o seguinte exemplo, a _regra_ de campo splunk mapeia para uma tabela no Azure Monitor e os mapas de tempos padrão de Splunk para a coluna Logs Analytics _ingestion_time()._

### <a name="search"></a>Pesquisa
Em Splunk, pode omitir a `search` palavra-chave e especificar uma corda não citada. No Azure Monitor deve iniciar cada consulta com `find` , uma corda não citada é um nome de coluna, e o valor de procuração deve ser uma corda citada. 

| | Operador | Exemplo |
|:---|:---|:---|
| **Splunk** | **pesquisar** | <code>search Session.Id="c8894ffd-e684-43c9-9125-42adc25cd3fc" earliest=-24h</code> |
| **Azure Monitor** | **encontrar** | <code>find Session.Id=="c8894ffd-e684-43c9-9125-42adc25cd3fc" and ingestion_time()> ago(24h)</code> |


### <a name="filter"></a>Filtrar
As consultas de registo do Azure Monitor partem de um conjunto de resultados tabulares onde o filtro. Em Splunk, a filtragem é a operação predefinida no índice atual. Também pode utilizar `where` o operador em Splunk, mas não é recomendado.

| | Operador | Exemplo |
|:---|:---|:---|
| **Splunk** | **pesquisar** | <code>Event.Rule="330009.2" Session.Id="c8894ffd-e684-43c9-9125-42adc25cd3fc" _indextime>-24h</code> |
| **Azure Monitor** | **onde** | <code>Office_Hub_OHubBGTaskError<br>&#124; where Session_Id == "c8894ffd-e684-43c9-9125-42adc25cd3fc" and ingestion_time() > ago(24h)</code> |

### <a name="getting-n-eventsrows-for-inspection"></a>Obtenção de n eventos/linhas para inspeção 
As consultas de registo do Azure Monitor também `take` suportam como `limit` pseudónimo. Em Splunk, se os resultados forem ordenados, `head` devolverá os primeiros n resultados. No Azure Monitor, o limite não é encomendado, mas devolve as primeiras linhas n que são encontradas.

| | Operador | Exemplo |
|:---|:---|:---|
| **Splunk** | **cabeça** | <code>Event.Rule=330009.2<br>&#124; head 100</code> |
| **Azure Monitor** | **limite** | <code>Office_Hub_OHubBGTaskError<br>&#124; limit 100</code> |

### <a name="getting-the-first-n-eventsrows-ordered-by-a-fieldcolumn"></a>Obtenção dos primeiros eventos/linhas n ordenados por um campo/coluna
Para obter resultados inferiores, em Splunk `tail` utilize. No Azure Monitor pode especificar a direção de encomenda com `asc` .

| | Operador | Exemplo |
|:---|:---|:---|
| **Splunk** | **cabeça** |  <code>Event.Rule="330009.2"<br>&#124; sort Event.Sequence<br>&#124; head 20</code> |
| **Azure Monitor** | **Início** | <code>Office_Hub_OHubBGTaskError<br>&#124; top 20 by Event_Sequence</code> |

### <a name="extending-the-result-set-with-new-fieldscolumns"></a>Alargamento do conjunto de resultados com novos campos/colunas
Splunk também tem uma `eval` função, que não deve ser comparável com o `eval` operador. Tanto o `eval` operador em Splunk como o operador no `extend` Azure Monitor suportam apenas funções de escalar e operadores aritméticos.

| | Operador | Exemplo |
|:---|:---|:---|
| **Splunk** | **eval** |  <code>Event.Rule=330009.2<br>&#124; eval state= if(Data.Exception = "0", "success", "error")</code> |
| **Azure Monitor** | **estender** | <code>Office_Hub_OHubBGTaskError<br>&#124; extend state = iif(Data_Exception == 0,"success" ,"error")</code> |

### <a name="rename"></a>Mudar o Nome 
O Azure Monitor utiliza o `project-rename` operador para mudar o nome de um campo. `project-rename` permite que a consulta tire partido de quaisquer índices pré-construídos para um campo. Splunk tem um `rename` operador para fazer o mesmo.

| | Operador | Exemplo |
|:---|:---|:---|
| **Splunk** | **mudar de nome** |  <code>Event.Rule=330009.2<br>&#124; rename Date.Exception as execption</code> |
| **Azure Monitor** | **projeto-renomeado** | <code>Office_Hub_OHubBGTaskError<br>&#124; project-rename exception = Date_Exception</code> |

### <a name="format-resultsprojection"></a>Resultados do formato/Projeção
Splunk não parece ter um operador semelhante a `project-away` . Pode utilizar a UI para filtrar campos afastados.

| | Operador | Exemplo |
|:---|:---|:---|
| **Splunk** | **table** |  <code>Event.Rule=330009.2<br>&#124; table rule, state</code> |
| **Azure Monitor** | **projeto**<br>**projeto-away** | <code>Office_Hub_OHubBGTaskError<br>&#124; project exception, state</code> |

### <a name="aggregation"></a>Agregação
Consulte as [agregações em consultas de registo do Monitor Azure](aggregations.md) para as diferentes funções de agregação.

| | Operador | Exemplo |
|:---|:---|:---|
| **Splunk** | **stats** |  <code>search (Rule=120502.*)<br>&#124; stats count by OSEnv, Audience</code> |
| **Azure Monitor** | **resumir** | <code>Office_Hub_OHubBGTaskError<br>&#124; summarize count() by App_Platform, Release_Audience</code> |


### <a name="join"></a>Associar
A join in Splunk tem limitações significativas. O subquery tem um limite de 10000 resultados (definido no ficheiro de configuração de implementação), e há um número limitado de sabores de junção.

| | Operador | Exemplo |
|:---|:---|:---|
| **Splunk** | **juntar-se** |  <code>Event.Rule=120103* &#124; stats by Client.Id, Data.Alias \| join Client.Id max=0 [search earliest=-24h Event.Rule="150310.0" Data.Hresult=-2147221040]</code> |
| **Azure Monitor** | **juntar-se** | <code>cluster("OAriaPPT").database("Office PowerPoint").Office_PowerPoint_PPT_Exceptions<br>&#124; where  Data_Hresult== -2147221040<br>&#124; join kind = inner (Office_System_SystemHealthMetadata<br>&#124; summarize by Client_Id, Data_Alias)on Client_Id</code>   |

### <a name="sort"></a>Ordenar
Em Splunk, para ordenar por ordem ascendente, deve utilizar o `reverse` operador. O Azure Monitor também apoia a definição de onde colocar nulos, no início ou no fim.

| | Operador | Exemplo |
|:---|:---|:---|
| **Splunk** | **tipo** |  <code>Event.Rule=120103<br>&#124; sort Data.Hresult<br>&#124; reverse</code> |
| **Azure Monitor** | **ordem por** | <code>Office_Hub_OHubBGTaskError<br>&#124; order by Data_Hresult,  desc</code> |

### <a name="multivalue-expand"></a>Expansão multivalue
Trata-se de um operador semelhante tanto no Splunk como no Azure Monitor.

| | Operador | Exemplo |
|:---|:---|:---|
| **Splunk** | **mvexpand** |  `mvexpand foo` |
| **Azure Monitor** | **mvexpand** | `mvexpand foo` |

### <a name="results-facets-interesting-fields"></a>Facetas de resultados, campos interessantes
No Log Analytics no portal Azure, apenas a primeira coluna está exposta. Todas as colunas estão disponíveis através da API.

| | Operador | Exemplo |
|:---|:---|:---|
| **Splunk** | **campos** |  <code>Event.Rule=330009.2<br>&#124; fields App.Version, App.Platform</code> |
| **Azure Monitor** | **facetas** | <code>Office_Excel_BI_PivotTableCreate<br>&#124; facet by App_Branch, App_Version</code> |

### <a name="de-duplicate"></a>Des duplicado
Em vez disso, pode utilizar `summarize arg_min()` para inverter a ordem de que o registo é escolhido.

| | Operador | Exemplo |
|:---|:---|:---|
| **Splunk** | **dedup** |  <code>Event.Rule=330009.2<br>&#124; dedup device_id sortby -batterylife</code> |
| **Azure Monitor** | **resumir arg_max()** | <code>Office_Excel_BI_PivotTableCreate<br>&#124; summarize arg_max(batterylife, *) by device_id</code> |

## <a name="next-steps"></a>Passos seguintes

- Passe por uma lição sobre as [consultas de registo de escrita no Azure Monitor](get-started-queries.md).
