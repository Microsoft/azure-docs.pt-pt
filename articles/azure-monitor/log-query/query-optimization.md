---
title: Otimizar consultas de log no Monitor Azure
description: Boas práticas para otimizar consultas de log no Monitor Azure.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/30/2019
ms.openlocfilehash: 9ae0aec6b87a746ed1f141dcf98f599acd20ab3a
ms.sourcegitcommit: 602e6db62069d568a91981a1117244ffd757f1c2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2020
ms.locfileid: "82864254"
---
# <a name="optimize-log-queries-in-azure-monitor"></a>Otimizar consultas de log no Monitor Azure
O Azure Monitor Logs utiliza o [Azure Data Explorer (ADX)](/azure/data-explorer/) para armazenar dados de registo e executar consultas para analisar esses dados. Cria, gere e mantém os clusters ADX para si e otimiza-os para a sua carga de trabalho de análise de registo. Quando executa uma consulta, está otimizada e encaminhada para o cluster ADX apropriado que armazena os dados do espaço de trabalho. Tanto o Azure Monitor Logs como o Azure Data Explorer utilizam muitos mecanismos automáticos de otimização de consultas. Embora as otimizações automáticas ofereçam um impulso significativo, são em alguns casos em que pode melhorar drasticamente o seu desempenho de consulta. Este artigo explica as considerações de desempenho e várias técnicas para corrigi-las.

A maioria das técnicas são comuns a consultas que são executadas diretamente no Azure Data Explorer e no Azure Monitor Logs, embora existam várias considerações únicas do Azure Monitor Logs que são discutidas aqui. Para mais dicas de otimização do Azure Data Explorer, consulte [as melhores práticas](/azure/kusto/query/best-practices)da Consulta.

Consultas otimizadas irão:

- Corra mais rápido, reduza a duração total da execução da consulta.
- Ter menores hipóteses de ser estrangulado ou rejeitado.

Deve dar especial atenção às consultas que são usadas para uso recorrente e rebentado, tais como dashboards, alertas, Aplicações Lógicas e Power BI. O impacto de uma consulta ineficaz nestes casos é substancial.

## <a name="query-performance-pane"></a>Painel de desempenho de consulta
Depois de fazer uma consulta no Log Analytics, clique na seta para baixo acima dos resultados da consulta para visualizar o painel de desempenho da consulta que mostra os resultados de vários indicadores de desempenho para a consulta. Estes indicadores de desempenho são descritos na secção seguinte.

![Painel de desempenho de consulta](media/query-optimization/query-performance-pane.png)


## <a name="query-performance-indicators"></a>Indicadores de desempenho de consulta

Os seguintes indicadores de desempenho de consulta estão disponíveis para cada consulta que seja executada:

- [CpU total](#total-cpu): A computação geral usada para processar a consulta em todos os nós de computação. Representa o tempo usado para computação, análise e recolha de dados. 

- [Dados utilizados para consulta processada](#data-used-for-processed-query): Dados globais que foram acedidos para processar a consulta. Influenciado pelo tamanho da tabela-alvo, tempo utilizado, filtros aplicados e pelo número de colunas referenciadas.

- [Tempo da consulta processada](#time-span-of-the-processed-query): O fosso entre os dados mais recentes e os mais antigos que foi acedido para processar a consulta. Influenciado pelo intervalo de tempo explícito especificado para a consulta.

- [Idade dos dados processados](#age-of-processed-data): O fosso entre agora e os dados mais antigos que foi acedido para processar a consulta. Influencia fortemente a eficiência da recolha de dados.

- [Número de espaços de trabalho](#number-of-workspaces): Quantos espaços de trabalho foram acedidos durante o processamento de consulta devido à seleção implícita ou explícita.

- [Número de regiões](#number-of-regions): Quantas regiões foram acedidas durante o processamento de consultas baseadas na seleção implícita ou explícita de espaços de trabalho. As consultas multi-regiões são muito menos eficientes e os indicadores de desempenho apresentam cobertura parcial.

- [Paralelismo](#parallelism): Indica o quanto o sistema foi capaz de executar esta consulta em vários nós. Relevante apenas para consultas que tenham um elevado consumo de CPU. Influenciado pela utilização de funções específicas e operadores.


## <a name="total-cpu"></a>Total CPU
O atual CPU computacional que foi investido para processar esta consulta em todos os nós de processamento de consulta. Uma vez que a maioria das consultas são executadas em grande número de nós, isso geralmente será muito maior do que a duração que a consulta realmente levou para executar. 

O tempo de processamento de consulta é gasto em:
- Recuperação de dados – a recuperação de dados antigos consumirá mais tempo do que a recuperação de dados recentes.
- Tratamento de dados – lógica e avaliação dos dados. 

Para além do tempo gasto nos nós de processamento de consulta, há tempo adicional que é gasto pela Azure Monitor Logs para: autenticar o utilizador e verificar se estão autorizados a aceder a esses dados, localizar a loja de dados, analisar a consulta e alocar os nós de processamento de consulta. Este tempo não está incluído no tempo total de CPU da consulta.

### <a name="early-filtering-of-records-prior-of-using-high-cpu-functions"></a>Filtração antecipada de registos antes da utilização de funções de CPU elevadas

Alguns dos comandos e funções de consulta são pesados no seu consumo de CPU. Isto é especialmente verdade para comandos que analisam JSON e XML ou extraem expressões regulares complexas. Essa análise pode acontecer explicitamente através de funções [parse_json()](/azure/kusto/query/parsejsonfunction) ou [parse_xml()](/azure/kusto/query/parse-xmlfunction) ou implicitamente quando se refere a colunas dinâmicas.

Estas funções consomem CPU proporcionalmente ao número de linhas que estão a processar. A otimização mais eficiente é adicionar onde as condições no início da consulta podem filtrar o maior número possível de registos antes da execução da função intensiva do CPU.

Por exemplo, as seguintes consultas produzem exatamente o mesmo resultado, mas a segunda é de longe a mais eficiente, uma vez que a condição [antes](/azure/kusto/query/whereoperator) de analisar exclui muitos registos:

```Kusto
//less efficient
SecurityEvent
| extend Details = parse_xml(EventData)
| extend FilePath = tostring(Details.UserData.RuleAndFileData.FilePath)
| extend FileHash = tostring(Details.UserData.RuleAndFileData.FileHash)
| summarize count() by FileHash, FilePath
| where FileHash != "" and FilePath !startswith "%SYSTEM32"  // Problem: irrelevant results are filtered after all processing and parsing is done
```
```Kusto
//more efficient
SecurityEvent
| where EventID == 8002 //Only this event have FileHash
| where EventData !has "%SYSTEM32" //Early removal of unwanted records
| extend Details = parse_xml(EventData)
| extend FilePath = tostring(Details.UserData.RuleAndFileData.FilePath)
| extend FileHash = tostring(Details.UserData.RuleAndFileData.FileHash)
| summarize count() by FileHash, FilePath
| where FileHash != "" // No need to filter out %SYSTEM32 here as it was removed before
```

### <a name="avoid-using-evaluated-where-clauses"></a>Evite utilizar as cláusulas avaliadas

Consultas que contêm [onde](/azure/kusto/query/whereoperator) as cláusulas numa coluna avaliada e não em colunas que estão fisicamente presentes no conjunto de dados perdem eficiência. A filtragem em colunas avaliadas impede algumas otimizações do sistema quando grandes conjuntos de dados são tratados.
Por exemplo, as seguintes consultas produzem exatamente o mesmo resultado, mas a segunda é mais eficiente do que a condição [onde](/azure/kusto/query/whereoperator) a condição se refere à coluna incorporada

```Kusto
//less efficient
Heartbeat 
| extend IPRegion = iif(RemoteIPLongitude  < -94,"WestCoast","EastCoast")
| where IPRegion == "WestCoast"
| summarize count() by Computer
```
```Kusto
//more efficient
Heartbeat 
| where RemoteIPLongitude  < -94
| extend IPRegion = iif(RemoteIPLongitude  < -94,"WestCoast","EastCoast")
| summarize count() by Computer
```

### <a name="use-effective-aggregation-commands-and-dimensions-in-summarize-and-join"></a>Use comandos e dimensões de agregação eficazes em resumo e junte-se

Embora alguns comandos de agregação como [max()](/azure/kusto/query/max-aggfunction) [() ()](/azure/kusto/query/sum-aggfunction) [() () count ()](/azure/kusto/query/count-aggfunction)e [avg()](/azure/kusto/query/avg-aggfunction) têm baixo impacto cpu devido à sua lógica, outros são mais complexos e incluem heurística e estimativas que lhes permitem ser executados de forma eficiente. Por exemplo, o [dcount()](/azure/kusto/query/dcount-aggfunction) utiliza o algoritmo HyperLogLog para fornecer uma estimativa próxima da contagem distinta de grandes conjuntos de dados sem realmente contar cada valor; as funções percentil estão a fazer aproximações semelhantes usando o algoritmo de percentil de classificação mais próximo. Vários dos comandos incluem parâmetros opcionais para reduzir o seu impacto. Por exemplo, a função [makeset()](/azure/kusto/query/makeset-aggfunction) tem um parâmetro opcional para definir o tamanho máximo definido, que afeta significativamente o CPU e a memória.

[A adesão](/azure/kusto/query/joinoperator?pivots=azuremonitor) e [resumo](/azure/kusto/query/summarizeoperator) dos comandos pode causar uma elevada utilização do CPU quando estão a processar um grande conjunto de dados. A sua complexidade está diretamente relacionada com o número de valores possíveis, referidos como *cardinalidade,* das colunas que estão a usar como resumo `by` ou como atributos de adesão. Para explicação e otimização de juntar e resumir, consulte os seus artigos de documentação e dicas de otimização.

Por exemplo, as seguintes consultas produzem exatamente o mesmo resultado porque o **CounterPath** está sempre mapeado para **contra-nome** e **objectnome**. A segunda é mais eficiente, uma vez que a dimensão de agregação é menor:

```Kusto
//less efficient
Perf
| summarize avg(CounterValue) 
by CounterName, CounterPath, ObjectName
```
```Kusto
//make the group expression more compact improve the performance
Perf
| summarize avg(CounterValue), any(CounterName), any(ObjectName) 
by CounterPath
```

O consumo de CPU também pode ser impactado por condições ou colunas estendidas que exijam computação intensiva. Todas as comparações triviais de cordas, como [igual ==](/azure/kusto/query/datatypes-string-operators) e [começa com](/azure/kusto/query/datatypes-string-operators) o mesmo impacto cpu, enquanto os jogos de texto avançados têm mais impacto. Especificamente, o operador [tem](/azure/kusto/query/datatypes-string-operators) mais eficiente que o operador [contém.](/azure/kusto/query/datatypes-string-operators) Devido às técnicas de manuseamento de cordas, é mais eficiente procurar cordas que sejam mais compridas do que quatro caracteres do que cordas curtas.

Por exemplo, as seguintes consultas produzem resultados semelhantes, dependendo da política de nomeação por computador, mas a segunda é mais eficiente:

```Kusto
//less efficient – due to filter based on contains
Heartbeat
| where Computer contains "Production" 
| summarize count() by ComputerIP 
```
```Kusto
//less efficient – due to filter based on extend
Heartbeat
| extend MyComputer = Computer
| where MyComputer startswith "Production" 
| summarize count() by ComputerIP 
```
```Kusto
//more efficient
Heartbeat
| where Computer startswith "Production" 
| summarize count() by ComputerIP 
```

> [!NOTE]
> Este indicador apresenta apenas CPU do cluster imediato. Em consultas multi-regiões, representaria apenas uma das regiões. Em consulta multi-workspace, pode não incluir todos os espaços de trabalho.

### <a name="avoid-full-xml-and-json-parsing-when-string-parsing-works"></a>Evite a análise completa do XML e da JSON quando a análise de cordas funciona
A análise completa de um objeto XML ou JSON pode consumir elevados recursos de CPU e memória. Em muitos casos, quando apenas um ou dois parâmetros são necessários e os objetos XML ou JSON são simples, é mais fácil analisá-los como cordas usando o [operador de parse](/azure/kusto/query/parseoperator) ou [outras técnicas](/azure/azure-monitor/log-query/parse-text)de análise de texto . O aumento de desempenho será mais significativo à medida que o número de registos no objeto XML ou JSON aumenta. É essencial quando o número de registos atinge dezenas de milhões.

Por exemplo, a seguinte consulta devolverá exatamente os mesmos resultados que as consultas acima sem realizar a análise completa do XML. Note que faz algumas suposições sobre a estrutura de ficheiros XML, como o elemento FilePath vem depois do FileHash e nenhum deles tem atributos. 

```Kusto
//even more efficient
SecurityEvent
| where EventID == 8002 //Only this event have FileHash
| where EventData !has "%SYSTEM32" //Early removal of unwanted records
| parse EventData with * "<FilePath>" FilePath "</FilePath>" * "<FileHash>" FileHash "</FileHash>" *
| summarize count() by FileHash, FilePath
| where FileHash != "" // No need to filter out %SYSTEM32 here as it was removed before
```


## <a name="data-used-for-processed-query"></a>Dados utilizados para consulta processada

Um fator crítico no processamento da consulta é o volume de dados que é digitalizado e utilizado para o processamento de consulta. O Azure Data Explorer utiliza otimizações agressivas que reduzem drasticamente o volume de dados em comparação com outras plataformas de dados. Ainda assim, existem fatores críticos na consulta que podem afetar o volume de dados que é usado.

Nos Registos do Monitor Azure, a coluna **TimeGenerated** é usada como forma de indexar os dados. Restringir os valores **gerados** pelo tempo para uma gama tão estreita quanto possível fará uma melhoria significativa no desempenho da consulta, limitando significativamente a quantidade de dados que tem de ser processado.

### <a name="avoid-unnecessary-use-of-search-and-union-operators"></a>Evitar utilização desnecessária de operadores de busca e sindicatos

Outro fator que aumenta os dados que está a ser processado é a utilização de um grande número de tabelas. Isto geralmente `search *` `union *` acontece quando e comandos são usados. Estes comandos forçam o sistema a avaliar e digitalizar dados de todas as tabelas do espaço de trabalho. Em alguns casos, pode haver centenas de mesas no espaço de trabalho. Tente evitar o máximo possível usando "search *" ou qualquer pesquisa sem espumreê-lo para uma tabela específica.

Por exemplo, as seguintes consultas produzem exatamente o mesmo resultado, mas a última é de longe a mais eficiente:

```Kusto
// This version scans all tables though only Perf has this kind of data
search "Processor Time" 
| summarize count(), avg(CounterValue)  by Computer
```
```Kusto
// This version scans all strings in Perf tables – much more efficient
Perf
| search "Processor Time" 
| summarize count(), avg(CounterValue)  by Computer
```
```Kusto
// This is the most efficient version 
Perf 
| where CounterName == "% Processor Time"  
| summarize count(), avg(CounterValue)  by Computer
```

### <a name="add-early-filters-to-the-query"></a>Adicione filtros precoces à consulta

Outro método para reduzir o volume de dados é ter [onde](/azure/kusto/query/whereoperator) as condições no início da consulta. A plataforma Azure Data Explorer inclui uma cache que lhe permite saber quais as divisórias que incluem dados relevantes para um determinado local onde condição. Por exemplo, se uma `where EventID == 4624` consulta contiver, distribuiria a consulta apenas a nós que lidam com divisórias com eventos correspondentes.

As seguintes consultas de exemplo produzem exatamente o mesmo resultado, mas o segundo é mais eficiente:

```Kusto
//less efficient
SecurityEvent
| summarize LoginSessions = dcount(LogonGuid) by Account
```
```Kusto
//more efficient
SecurityEvent
| where EventID == 4624 //Logon GUID is relevant only for logon event
| summarize LoginSessions = dcount(LogonGuid) by Account
```

### <a name="reduce-the-number-of-columns-that-is-retrieved"></a>Reduzir o número de colunas que são recuperadas

Uma vez que o Azure Data Explorer é uma loja de dados colunaar, a recuperação de cada coluna é independente das outras. O número de colunas recuperadas influencia diretamente o volume global de dados. Só deve incluir as colunas na saída que são necessárias através da [sumção](/azure/kusto/query/summarizeoperator) dos resultados ou da [projeção](/azure/kusto/query/projectoperator) das colunas específicas. O Azure Data Explorer tem várias otimizações para reduzir o número de colunas recuperadas. Se determinar que uma coluna não é necessária, por exemplo, se não for referenciada no comando de [resumo,](/azure/kusto/query/summarizeoperator) não a recuperará.

Por exemplo, a segunda consulta pode processar três vezes mais dados, uma vez que precisa de buscar não uma coluna, mas três:

```Kusto
//Less columns --> Less data
SecurityEvent
| summarize count() by Computer  
```
```Kusto
//More columns --> More data
SecurityEvent
| summarize count(), dcount(EventID), avg(Level) by Computer  
```

## <a name="time-span-of-the-processed-query"></a>Tempo da consulta processada

Todos os registos em Registos do Monitor Azure são divididos de acordo com a coluna **TimeGenerated.** O número de divisórias acedidas está diretamente relacionado com o período de tempo. Reduzir o intervalo de tempo é a forma mais eficiente de assegurar uma rápida execução de consultas.

O intervalo de tempo pode ser definido utilizando o seletor de intervalo de tempo no ecrã Log Analytics, conforme descrito no âmbito de consulta de registo e na gama de [tempo no Azure Monitor Log Analytics](scope.md#time-range). Este é o método recomendado, uma vez que o intervalo de tempo selecionado é passado para o backend usando os metadados de consulta. 

Um método alternativo é incluir explicitamente uma condição [em que](/azure/kusto/query/whereoperator) o **Tempo Gerado** na consulta. Deve utilizar este método pois assegura que o tempo é fixado, mesmo quando a consulta é usada a partir de uma interface diferente.
Deve certificar-se de que todas as partes da consulta têm filtros **TimeGenerateed.** Quando uma consulta tem sub-consultas que recolhadados de várias tabelas ou da mesma tabela, cada uma tem de incluir a sua [própria](/azure/kusto/query/whereoperator) condição.

### <a name="make-sure-all-sub-queries-have-timegenerated-filter"></a>Certifique-se de que todas as sub-consultas têm filtro TimeGenerated

Por exemplo, na seguinte consulta, enquanto a tabela **Perf** será digitalizada apenas para o último dia, a tabela **Heartbeat** será digitalizada para toda a sua história, que pode ser até dois anos:

```Kusto
Perf
| where TimeGenerated > ago(1d)
| summarize avg(CounterValue) by Computer, CounterName
| join kind=leftouter (
    Heartbeat
    //No time span filter in this part of the query
    | summarize IPs = makeset(ComputerIP, 10) by  Computer
) on Computer
```

Um caso comum em que tal erro ocorre é quando [arg_max()](/azure/kusto/query/arg-max-aggfunction) é usado para encontrar a ocorrência mais recente. Por exemplo:

```Kusto
Perf
| where TimeGenerated > ago(1d)
| summarize avg(CounterValue) by Computer, CounterName
| join kind=leftouter (
    Heartbeat
    //No time span filter in this part of the query
    | summarize arg_max(TimeGenerated, *), min(TimeGenerated)   
by Computer
) on Computer
```

Isto pode ser facilmente corrigido adicionando um filtro de tempo na consulta interna:

```Kusto
Perf
| where TimeGenerated > ago(1d)
| summarize avg(CounterValue) by Computer, CounterName
| join kind=leftouter (
    Heartbeat
    | where TimeGenerated > ago(1d) //filter for this part
    | summarize arg_max(TimeGenerated, *), min(TimeGenerated)   
by Computer
) on Computer
```

Outro exemplo para esta falha é quando se realiza a filtragem do intervalo de tempo logo após uma [união](/azure/kusto/query/unionoperator?pivots=azuremonitor) sobre várias mesas. Ao realizar a união, cada sub-consulta deve ser consultada. Pode utilizar a declaração de [let](/azure/kusto/query/letstatement) para garantir a consistência do scoping.

Por exemplo, a seguinte consulta irá digitalizar todos os dados nas tabelas *Heartbeat* e *Perf,* e não apenas no último dia 1:

```Kusto
Heartbeat 
| summarize arg_min(TimeGenerated,*) by Computer
| union (
    Perf 
    | summarize arg_min(TimeGenerated,*) by Computer) 
| where TimeGenerated > ago(1d)
| summarize min(TimeGenerated) by Computer
```

Esta consulta deve ser corrigida da seguinte forma:

```Kusto
let MinTime = ago(1d);
Heartbeat 
| where TimeGenerated > MinTime
| summarize arg_min(TimeGenerated,*) by Computer
| union (
    Perf 
    | where TimeGenerated > MinTime
    | summarize arg_min(TimeGenerated,*) by Computer) 
| summarize min(TimeGenerated) by Computer
```

### <a name="time-span-measurement-limitations"></a>Limitações de medição do tempo

A medição é sempre maior do que o tempo real especificado. Por exemplo, se o filtro na consulta for de 7 dias, o sistema pode digitalizar 7,5 ou 8,1 dias. Isto porque o sistema está dividindo os dados em pedaços em tamanho variável. Para garantir que todos os registos relevantes são digitalizados, verifica toda a partição que pode cobrir várias horas e até mais de um dia.

Existem vários casos em que o sistema não consegue fornecer uma medição precisa do intervalo de tempo. Isto acontece na maioria dos casos em que a consulta é inferior a um dia ou em consultas multi-workspace.


> [!IMPORTANT]
> Este indicador apresenta apenas dados tratados no cluster imediato. Em consultas multi-regiões, representaria apenas uma das regiões. Em consulta multi-workspace, pode não incluir todos os espaços de trabalho.

## <a name="age-of-processed-data"></a>Idade dos dados processados
O Azure Data Explorer utiliza vários níveis de armazenamento: em memória, discos SSD locais e blobs Azure muito mais lentos. Quanto mais recentes forem os dados, maior é a possibilidade de ser armazenado num nível mais performante com menor latência, reduzindo a duração da consulta e cpu. Além dos dados em si, o sistema também tem uma cache para metadados. Quanto mais velhos forem os dados, menos hipóteses os seus metadados estarão em cache.

Embora algumas consultas exijam o uso de dados antigos, há casos em que dados antigos são usados por engano. Isto acontece quando as consultas são executadas sem fornecer intervalo de tempo nos seus metadados e nem todas as referências de tabela incluem filtro na coluna **TimeGenerated.** Nestes casos, o sistema irá digitalizar todos os dados armazenados nessa tabela. Quando a retenção de dados é longa, pode abranger intervalos de longo prazo e, portanto, dados tão antigos como o período de retenção de dados.

Estes casos podem ser, por exemplo:

- Não definir o intervalo de tempo no Log Analytics com uma sub-consulta que não seja limitada. Veja o exemplo acima.
- Utilização da API sem o intervalo de tempo de parâmetros opcionais.
- Usando um cliente que não força um intervalo de tempo como o conector Power BI.

Consulte exemplos e notas na secção perversa, uma vez que também são relevantes neste caso.

## <a name="number-of-regions"></a>Número de regiões
Existem várias situações em que uma única consulta pode ser executada em diferentes regiões:

- Quando vários espaços de trabalho estão explicitamente listados, e estão localizados em diferentes regiões.
- Quando uma consulta com recursos está a recolher dados e os dados são armazenados em vários espaços de trabalho que estão localizados em diferentes regiões.

A execução de consultas transversais requer que o sistema se serialize e transfira no backend grandes pedaços de dados intermédios que são geralmente muito maiores do que os resultados finais da consulta. Também limita a capacidade do sistema de realizar otimizações, heurísticas e utilizar caches.
Se não houver uma verdadeira razão para digitalizar todas estas regiões, deve ajustar o âmbito de aplicação, de modo a cobrir menos regiões. Se o âmbito dos recursos for minimizado, mas ainda muitas regiões são utilizadas, pode acontecer devido a uma configuração errada. Por exemplo, os registos de auditoria e as configurações de diagnóstico são enviados para diferentes espaços de trabalho em diferentes regiões ou existem múltiplas configurações de diagnóstico. 

> [!IMPORTANT]
> Quando uma consulta é feita em várias regiões, o CPU e as medições de dados não serão precisas e representarão a medição apenas numa das regiões.

## <a name="number-of-workspaces"></a>Número de espaços de trabalho
Espaços de trabalho são recipientes lógicos que são usados para segregar e administrar dados de registos. O backend otimiza as colocações no espaço de trabalho em clusters físicos dentro da região selecionada.

A utilização de vários espaços de trabalho pode resultar de: 

- Onde vários espaços de trabalho estão explicitamente listados.
- Quando uma consulta com recursos está a recolher dados e os dados são armazenados em vários espaços de trabalho.
 
A execução transversal e transversal de consultas requer que o sistema se serialize e transfira no backend grandes pedaços de dados intermédios que são geralmente muito maiores do que os resultados finais da consulta. Também limita a capacidade do sistema de realizar otimizações, heurísticas e utilização de caches.

> [!IMPORTANT]
> Em alguns cenários multi-workspace, o CPU e as medições de dados não serão precisas e representarão a medição apenas para poucos dos espaços de trabalho.

## <a name="parallelism"></a>Paralelismo
O Azure Monitor Logs está a usar grandes clusters de Azure Data Explorer para executar consultas, e estes clusters variam em escala, potencialmente chegando a dezenas de nós de computação. O sistema escala automaticamente os clusters de acordo com a lógica e capacidade de colocação do espaço de trabalho.

Para executar uma consulta eficientemente, é dividida e distribuída para calcular nós com base nos dados necessários para o seu processamento. Há algumas situações em que o sistema não pode fazê-lo de forma eficiente. Isto pode levar a uma longa duração da consulta. 

Comportamentos de consulta que podem reduzir o paralelismo incluem:

- Utilização de funções de serialização e janela, tais como o [operador serializar,](/azure/kusto/query/serializeoperator) [o próximo()](/azure/kusto/query/nextfunction), [o prev()](/azure/kusto/query/prevfunction)e as funções da [linha.](/azure/kusto/query/rowcumsumfunction) Séries temporais e funções de análise de utilizadores podem ser usadas em alguns destes casos. A serialização ineficiente também pode ocorrer se os seguintes operadores não forem utilizados no final da consulta: [gama](/azure/kusto/query/rangeoperator), [ordenar,](/azure/kusto/query/sortoperator) [ordenar,](/azure/kusto/query/orderoperator) [top,](/azure/kusto/query/topoperator) [top-hitters,](/azure/kusto/query/tophittersoperator) [getschema](/azure/kusto/query/getschemaoperator).
-    O uso da função de agregação [de dcount()](/azure/kusto/query/dcount-aggfunction) força o sistema a ter cópia central dos valores distintos. Quando a escala de dados for elevada, considere utilizar os parâmetros opcionais da função de contagem para reduzir a precisão.
-    Em muitos casos, o operador [de adesão](/azure/kusto/query/joinoperator?pivots=azuremonitor) reduz o paralelismo geral. Examine a shuffle juntar-se como uma alternativa quando o desempenho é problemático.
-    Nas consultas de âmbito de recursos, os controlos rBAC pré-execução podem permanecer em situações em que há um número muito elevado de atribuições rbac. Isto pode levar a controlos mais longos que resultariam num paralelismo mais baixo. Por exemplo, uma consulta é executada numa subscrição onde existem milhares de recursos e cada recurso tem muitas atribuições de funções no nível de recursos, não no grupo de subscrição ou recursos.
-    Se uma consulta estiver a processar pequenos pedaços de dados, o seu paralelismo será baixo, uma vez que o sistema não irá espalhá-lo por muitos nós de computação.



## <a name="next-steps"></a>Passos seguintes

- [Documentação de referência para a linguagem de consulta kusto](/azure/kusto/query/).
