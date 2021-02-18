---
title: Otimizar consultas de log no Azure Monitor
description: Melhores práticas para otimizar consultas de log no Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/30/2019
ms.openlocfilehash: 53f189921a44d63d7e344fb733519661f5b17dc6
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100619581"
---
# <a name="optimize-log-queries-in-azure-monitor"></a>Otimizar consultas de log no Azure Monitor
O Azure Monitor Logs utiliza [o Azure Data Explorer (ADX)](/azure/data-explorer/) para armazenar dados de registo e executar consultas para analisar esses dados. Cria, gere e mantém os clusters ADX para si, e otimiza-os para a sua carga de trabalho de análise de registo. Quando você faz uma consulta, é otimizado, e encaminhado para o cluster ADX apropriado que armazena os dados do espaço de trabalho. Tanto os Registos monitores Azure como o Azure Data Explorer utilizam muitos mecanismos automáticos de otimização de consultas. Embora as otimizações automáticas proporcionem um impulso significativo, existem alguns casos em que pode melhorar drasticamente o seu desempenho de consulta. Este artigo explica as considerações de desempenho e várias técnicas para corrigi-las.

A maioria das técnicas são comuns a consultas que são executadas diretamente em Azure Data Explorer e Azure Monitor Logs, embora existam várias considerações únicas de Registos do Monitor Azure que são discutidas aqui. Para obter mais dicas de otimização do Azure Data Explorer, consulte [as melhores práticas de consulta](/azure/kusto/query/best-practices).

Consultas otimizadas:

- Corra mais rápido, reduza a duração total da execução da consulta.
- Ter menores hipóteses de ser estrangulado ou rejeitado.

Deve prestar especial atenção às consultas que são usadas para utilização recorrente e rebentada, tais como dashboards, alertas, Aplicações Lógicas e Power BI. O impacto de uma consulta ineficaz nestes casos é substancial.

## <a name="query-performance-pane"></a>Painel de desempenho de consulta
Depois de fazer uma consulta no Log Analytics, clique na seta para baixo acima dos resultados da consulta para ver o painel de desempenho da consulta que mostra os resultados de vários indicadores de desempenho para a consulta. Estes indicadores de desempenho são descritos na secção seguinte.

![Painel de desempenho de consulta](media/query-optimization/query-performance-pane.png)


## <a name="query-performance-indicators"></a>Indicadores de desempenho de consulta

Estão disponíveis os seguintes indicadores de desempenho de consulta para cada consulta que for executada:

- [CPU Total](#total-cpu): Computação geral utilizada para processar a consulta em todos os nós computacional. Representa o tempo utilizado para computação, análise e recolha de dados. 

- [Dados utilizados para consulta processada](#data-used-for-processed-query): Dados globais que foram acedidos para processar a consulta. Influenciado pelo tamanho da tabela-alvo, o tempo de utilização, os filtros aplicados e o número de colunas referenciadas.

- [Período de tempo da consulta processada](#time-span-of-the-processed-query): O fosso entre os dados mais recentes e os mais antigos que foram acedidos para processar a consulta. Influenciado pelo intervalo de tempo explícito especificado para a consulta.

- [Idade dos dados processados](#age-of-processed-data): A diferença entre agora e os dados mais antigos que foram acedidos para processar a consulta. Influencia muito a eficiência da obtenção de dados.

- [Número de espaços de trabalho](#number-of-workspaces): Quantos espaços de trabalho foram acedidos durante o processamento de consulta devido à seleção implícita ou explícita.

- [Número de regiões](#number-of-regions): Quantas regiões foram acedidas durante o processamento de consultas com base na seleção implícita ou explícita dos espaços de trabalho. As consultas multi-regiões são muito menos eficientes e os indicadores de desempenho apresentam uma cobertura parcial.

- [Paralelismo](#parallelism): Indica quanto o sistema foi capaz de executar esta consulta em múltiplos nós. Relevante apenas para consultas que têm elevado consumo de CPU. Influenciado pelo uso de funções específicas e operadores.


## <a name="total-cpu"></a>Total CPU
O CPU de computação real que foi investido para processar esta consulta em todos os nós de processamento de consulta. Uma vez que a maioria das consultas são executadas em um grande número de nós, este será geralmente muito maior do que a duração que a consulta realmente levou a executar. 

A consulta que utiliza mais de 100 segundos de CPU é considerada uma consulta que consome recursos excessivos. A consulta que utiliza mais de 1.000 segundos de CPU é considerada uma consulta abusiva e pode ser estrangulada.

O tempo de processamento de consultas é gasto em:
- Recuperação de dados – a recuperação de dados antigos consumirá mais tempo do que a recuperação de dados recentes.
- Processamento de dados – lógica e avaliação dos dados. 

Para além do tempo gasto nos nós de processamento de consultas, existe tempo adicional que é gasto pelos Registos do Monitor Azure para: autenticar o utilizador e verificar se estão autorizados a aceder a estes dados, localizar a loja de dados, analisar a consulta e alocar os nós de processamento de consulta. Desta vez não está incluído no tempo total de consulta cpu.

### <a name="early-filtering-of-records-prior-of-using-high-cpu-functions"></a>Filtragem precoce dos registos antes da utilização de funções de CPU elevadas

Alguns dos comandos e funções de consulta são pesados no seu consumo de CPU. Isto é especialmente verdade para comandos que analisam JSON e XML ou extraem expressões regulares complexas. Tal análise pode acontecer explicitamente através de funções [parse_json ou](/azure/kusto/query/parsejsonfunction) [parse_xml()](/azure/kusto/query/parse-xmlfunction) ou implicitamente quando se refere a colunas dinâmicas.

Estas funções consomem CPU proporcionalmente ao número de linhas que estão a processar. A otimização mais eficiente é adicionar onde as condições no início da consulta que podem filtrar o maior número possível de registos antes da função intensiva da CPU ser executada.

Por exemplo, as seguintes consultas produzem exatamente o mesmo resultado, mas a segunda é de longe a mais eficiente como [a](/azure/kusto/query/whereoperator) condição antes de analisar exclui muitos registos:

```Kusto
//less efficient
SecurityEvent
| extend Details = parse_xml(EventData)
| extend FilePath = tostring(Details.UserData.RuleAndFileData.FilePath)
| extend FileHash = tostring(Details.UserData.RuleAndFileData.FileHash)
| where FileHash != "" and FilePath !startswith "%SYSTEM32"  // Problem: irrelevant results are filtered after all processing and parsing is done
| summarize count() by FileHash, FilePath
```
```Kusto
//more efficient
SecurityEvent
| where EventID == 8002 //Only this event have FileHash
| where EventData !has "%SYSTEM32" //Early removal of unwanted records
| extend Details = parse_xml(EventData)
| extend FilePath = tostring(Details.UserData.RuleAndFileData.FilePath)
| extend FileHash = tostring(Details.UserData.RuleAndFileData.FileHash)
| where FileHash != "" and FilePath !startswith "%SYSTEM32"  // exact removal of results. Early filter is not accurate enough
| summarize count() by FileHash, FilePath
| where FileHash != "" // No need to filter out %SYSTEM32 here as it was removed before
```

### <a name="avoid-using-evaluated-where-clauses"></a>Evite utilizar a avaliação de onde as cláusulas

As consultas que contêm [onde](/azure/kusto/query/whereoperator) as cláusulas numa coluna avaliada e não em colunas fisicamente presentes no conjunto de dados perdem eficiência. A filtragem em colunas avaliadas impede algumas otimizações do sistema quando grandes conjuntos de dados são tratados.
Por exemplo, as seguintes consultas produzem exatamente o mesmo resultado, mas a segunda é mais eficiente como [a](/azure/kusto/query/whereoperator) condição se refere a coluna incorporada

```Kusto
//less efficient
Syslog
| extend Msg = strcat("Syslog: ",SyslogMessage)
| where  Msg  has "Error"
| count 
```
```Kusto
//more efficient
Syslog
| where  SyslogMessage  has "Error"
| count 
```

Em alguns casos, a coluna avaliada é criada implicitamente pelo motor de processamento de consultas, uma vez que a filtragem é feita não apenas no campo:
```Kusto
//less efficient
SecurityEvent
| where tolower(Process) == "conhost.exe"
| count 
```
```Kusto
//more efficient
SecurityEvent
| where Process =~ "conhost.exe"
| count 
```




### <a name="use-effective-aggregation-commands-and-dimensions-in-summarize-and-join"></a>Use comandos e dimensões de agregação eficazes em resumir e juntar

Enquanto alguns comandos de agregação como [max()](/azure/kusto/query/max-aggfunction), [sum()](/azure/kusto/query/sum-aggfunction)( [count()](/azure/kusto/query/count-aggfunction)e [avg()](/azure/kusto/query/avg-aggfunction) têm baixo impacto cpU devido à sua lógica, outros são mais complexos e incluem heurísticas e estimativas que permitem que sejam executados eficientemente. Por exemplo, [o dcount()](/azure/kusto/query/dcount-aggfunction) utiliza o algoritmo HyperLogLog para fornecer uma estimativa próxima à contagem distinta de grandes conjuntos de dados sem realmente contar cada valor; as funções percentil estão a fazer aproximações semelhantes usando o algoritmo percentil de classificação mais próximo. Vários dos comandos incluem parâmetros opcionais para reduzir o seu impacto. Por exemplo, a função [makeset()](/azure/kusto/query/makeset-aggfunction) tem um parâmetro opcional para definir o tamanho máximo definido, que afeta significativamente o CPU e a memória.

[Junte](/azure/kusto/query/joinoperator?pivots=azuremonitor) e [resumir](/azure/kusto/query/summarizeoperator) os comandos pode causar uma alta utilização do CPU quando estão a processar um grande conjunto de dados. A sua complexidade está diretamente relacionada com o número de valores possíveis, referidos como *cardinalidade,* das colunas que estão a usar como o `by` in summarize ou como os atributos de união. Para explicação e otimização de juntar e resumir, consulte os seus artigos de documentação e dicas de otimização.

Por exemplo, as seguintes consultas produzem exatamente o mesmo resultado porque **o CounterPath** é sempre um para um mapeado para **o ContraName** e **o ObjectName**. A segunda é mais eficiente, uma vez que a dimensão da agregação é menor:

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

O consumo de CPU também pode ser impactado quando as condições ou colunas estendidas requerem computação intensiva. Todas as comparações de cordas triviais tais como [igual ==](/azure/kusto/query/datatypes-string-operators) e [começacom](/azure/kusto/query/datatypes-string-operators) têm aproximadamente o mesmo impacto cpU, enquanto jogos de texto avançados têm mais impacto. Especificamente, o operador [tem](/azure/kusto/query/datatypes-string-operators) um operador mais eficiente que o operador [contém.](/azure/kusto/query/datatypes-string-operators) Devido às técnicas de manuseamento de cordas, é mais eficiente procurar cordas que sejam mais compridas do que quatro caracteres do que cordas curtas.

Por exemplo, as seguintes consultas produzem resultados semelhantes, dependendo da política de nomeação de computador, mas a segunda é mais eficiente:

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
> Este indicador apresenta apenas CPU do cluster imediato. Em consultas multi-regiões, representaria apenas uma das regiões. Em consulta de vários espaços de trabalho, pode não incluir todos os espaços de trabalho.

### <a name="avoid-full-xml-and-json-parsing-when-string-parsing-works"></a>Evite análises completas de XML e JSON quando a análise das cordas funcionar
A análise completa de um objeto XML ou JSON pode consumir elevados recursos de CPU e memória. Em muitos casos, quando apenas um ou dois parâmetros são necessários e os objetos XML ou JSON são simples, é mais fácil analisá-los como cordas usando o operador de [parse](/azure/kusto/query/parseoperator) ou [outras técnicas de análise de texto](./parse-text.md). O aumento de desempenho será mais significativo à medida que o número de registos no objeto XML ou JSON aumenta. É essencial quando o número de registos atinge dezenas de milhões.

Por exemplo, a seguinte consulta devolverá exatamente os mesmos resultados que as consultas acima sem efetuar a análise completa do XML. Note que faz algumas suposições na estrutura de ficheiros XML, como o elemento FilePath vem depois do FileHash e nenhum deles tem atributos. 

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

Um fator crítico no processamento da consulta é o volume de dados que é digitalizado e utilizado para o processamento de consultas. O Azure Data Explorer utiliza otimizações agressivas que reduzem drasticamente o volume de dados em comparação com outras plataformas de dados. Ainda assim, existem fatores críticos na consulta que podem impactar o volume de dados que é usado.

A consulta que processa mais de 2.000KB de dados é considerada uma consulta que consome recursos excessivos. A consulta que está a processar mais de 20.000KB de dados é considerada uma consulta abusiva e pode ser estrangulada.

Nos Registos do Monitor Azure, a coluna **TimeGenerated** é utilizada como forma de indexar os dados. Restringir os valores **timeGenerated** para um intervalo o mais estreito possível fará uma melhoria significativa no desempenho da consulta, limitando significativamente a quantidade de dados que tem de ser processado.

### <a name="avoid-unnecessary-use-of-search-and-union-operators"></a>Evitar o uso desnecessário de operadores de pesquisa e sindicato

Outro fator que aumenta os dados que são processados é o uso de um grande número de tabelas. Isto geralmente acontece quando `search *` e `union *` os comandos são usados. Estes comandos forçam o sistema a avaliar e digitalizar dados de todas as tabelas do espaço de trabalho. Em alguns casos, pode haver centenas de mesas no espaço de trabalho. Tente evitar o máximo possível usando "search *" ou qualquer pesquisa sem a digitalizar para uma mesa específica.

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

### <a name="add-early-filters-to-the-query"></a>Adicione filtros iniciais à consulta

Outro método para reduzir o volume de dados é ter [onde](/azure/kusto/query/whereoperator) as condições no início da consulta. A plataforma Azure Data Explorer inclui uma cache que lhe permite saber quais as divisórias que incluem dados que são relevantes para uma condição específica. Por exemplo, se uma consulta contiver, `where EventID == 4624` então distribuiria a consulta apenas a nós que manuseiam divisórias com eventos correspondentes.

As seguintes consultas de exemplo produzem exatamente o mesmo resultado, mas a segunda é mais eficiente:

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

### <a name="avoid-multiple-scans-of-same-source-data-using-conditional-aggregation-functions-and-materialize-function"></a>Evite múltiplas análises de dados de mesma origem utilizando funções de agregação condicional e materialize a função
Quando uma consulta tem várias sub-consultas que são fundidas usando operadores de junta ou união, cada sub-consulta digitaliza toda a fonte separadamente e, em seguida, funde os resultados. Isto múltipla o número de vezes que os dados são digitalizados - fator crítico em conjuntos de dados muito grandes.

Uma técnica para evitar isto é utilizando as funções de agregação condicional. A maioria das funções de agregação que são [utilizadas](/azure/data-explorer/kusto/query/summarizeoperator#list-of-aggregation-functions) no operador resumido tem uma versão condicionada que lhe permite utilizar um único operador sumário com múltiplas condições. 

Por exemplo, as seguintes consultas mostram o número de eventos de login e o número de eventos de execução de processos para cada conta. Devolvem os mesmos resultados, mas o primeiro é digitalizar os dados duas vezes, o segundo só uma vez:

```Kusto
//Scans the SecurityEvent table twice and perform expensive join
SecurityEvent
| where EventID == 4624 //Login event
| summarize LoginCount = count() by Account
| join 
(
    SecurityEvent
    | where EventID == 4688 //Process execution event
    | summarize ExecutionCount = count(), ExecutedProcesses = make_set(Process) by Account
) on Account
```

```Kusto
//Scan only once with no join
SecurityEvent
| where EventID == 4624 or EventID == 4688 //early filter
| summarize LoginCount = countif(EventID == 4624), ExecutionCount = countif(EventID == 4688), ExecutedProcesses = make_set_if(Process,EventID == 4688)  by Account
```

Outro caso em que as sub-consultas são desnecessárias é a pré-filtragem para o operador de [parse](/azure/data-explorer/kusto/query/parseoperator?pivots=azuremonitor) para se certificar de que processa apenas registos que correspondam a um padrão específico. Isto é desnecessário, uma vez que o operador de parse e outros operadores similares retornam resultados vazios quando o padrão não corresponde. Aqui estão duas consultas que devolvem exatamente os mesmos resultados enquanto a segunda consulta de dados de verificação apenas uma vez. Na segunda consulta, cada comando de parse é relevante apenas para os seus eventos. O operador de extensão mostra depois como se referir à situação de dados vazios.

```Kusto
//Scan SecurityEvent table twice
union(
SecurityEvent
| where EventID == 8002 
| parse EventData with * "<FilePath>" FilePath "</FilePath>" * "<FileHash>" FileHash "</FileHash>" *
| distinct FilePath
),(
SecurityEvent
| where EventID == 4799
| parse EventData with * "CallerProcessName\">" CallerProcessName1 "</Data>" * 
| distinct CallerProcessName1
)
```

```Kusto
//Single scan of the SecurityEvent table
SecurityEvent
| where EventID == 8002 or EventID == 4799
| parse EventData with * "<FilePath>" FilePath "</FilePath>" * "<FileHash>" FileHash "</FileHash>" * //Relevant only for event 8002
| parse EventData with * "CallerProcessName\">" CallerProcessName1 "</Data>" *  //Relevant only for event 4799
| extend FilePath = iif(isempty(CallerProcessName1),FilePath,"")
| distinct FilePath, CallerProcessName1
```

Quando o acima referido não permite evitar a utilização de sub-consultas, outra técnica é sugerir ao motor de consulta que existe um único dado de origem utilizado em cada um deles utilizando a [função de materialização.](/azure/data-explorer/kusto/query/materializefunction?pivots=azuremonitor) Isto é útil quando os dados de origem vêm de uma função que é usada várias vezes dentro da consulta. Materializar é eficaz quando a saída da sub-consulta é muito menor do que a entrada. O motor de consulta cache e reutilizar a saída em todas as ocorrências.



### <a name="reduce-the-number-of-columns-that-is-retrieved"></a>Reduzir o número de colunas que são recuperadas

Uma vez que o Azure Data Explorer é uma loja de dados colunar, a recuperação de cada coluna é independente das outras. O número de colunas que são recuperadas influencia diretamente o volume global de dados. Deve incluir apenas as colunas na saída que são necessárias [resumindo](/azure/kusto/query/summarizeoperator) os resultados ou [projetando](/azure/kusto/query/projectoperator) as colunas específicas. O Azure Data Explorer tem várias otimizações para reduzir o número de colunas recuperadas. Se determinar que uma coluna não é necessária, por exemplo, se não for referenciada no comando [resumido,](/azure/kusto/query/summarizeoperator) não a recuperará.

Por exemplo, a segunda consulta pode processar três vezes mais dados, uma vez que precisa de ir buscar não uma coluna, mas três:

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

## <a name="time-span-of-the-processed-query"></a>Período de tempo da consulta processada

Todos os registos em Registos monitores Azure são divididos de acordo com a coluna **TimeGenerated.** O número de divisórias acedidas está diretamente relacionado com o período de tempo. Reduzir o intervalo de tempo é a forma mais eficiente de assegurar uma rápida execução de consultas.

Consulta com tempo de mais de 15 dias é considerada uma consulta que consome recursos excessivos. Consulta com tempo de mais de 90 dias é considerada uma consulta abusiva e pode ser estrangulada.

O intervalo de tempo pode ser definido utilizando o seletor de intervalo de tempo no ecrã Log Analytics, conforme descrito no [âmbito de consulta de Log e intervalo de tempo no Azure Monitor Log Analytics](../log-query/scope.md#time-range). Este é o método recomendado, uma vez que o intervalo de tempo selecionado é passado para o backend utilizando os metadados de consulta. 

Um método alternativo é incluir explicitamente uma condição [em](/azure/kusto/query/whereoperator) que a condição do **TempoGerado** na consulta. Deve utilizar este método pois assegura que o tempo é fixado, mesmo quando a consulta é utilizada a partir de uma interface diferente.
Deve certificar-se de que todas as partes da consulta possuem filtros **TimeGenerated.** Quando uma consulta tem sub-consultas que recolhem dados de várias tabelas ou da mesma tabela, cada uma tem de incluir a sua própria [condição.](/azure/kusto/query/whereoperator)

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

Outro exemplo para esta falha é quando se executa a filtragem do âmbito do tempo logo após uma [união](/azure/kusto/query/unionoperator?pivots=azuremonitor) sobre várias mesas. Ao realizar a união, cada sub-consulta deve ser examinado. Pode usar [uma declaração de let](/azure/kusto/query/letstatement) para garantir a consistência da deteção.

Por exemplo, a seguinte consulta irá digitalizar todos os dados nas tabelas *Heartbeat* e *Perf,* e não apenas no último dia:

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

### <a name="time-span-measurement-limitations"></a>Limitações de medição de tempo

A medição é sempre maior do que o tempo real especificado. Por exemplo, se o filtro na consulta for de 7 dias, o sistema pode digitalizar 7,5 ou 8,1 dias. Isto porque o sistema está a dividir os dados em pedaços de tamanho variável. Para garantir que todos os registos relevantes são digitalizados, digitaliza toda a partição que pode cobrir várias horas e até mais de um dia.

Existem vários casos em que o sistema não pode fornecer uma medição precisa do intervalo de tempo. Isto acontece na maioria dos casos em que a consulta se estende menos de um dia ou em consultas multi-workspace.


> [!IMPORTANT]
> Este indicador apresenta apenas dados processados no cluster imediato. Em consultas multi-regiões, representaria apenas uma das regiões. Em consulta de vários espaços de trabalho, pode não incluir todos os espaços de trabalho.

## <a name="age-of-processed-data"></a>Idade dos dados processados
O Azure Data Explorer utiliza vários níveis de armazenamento: em memória, discos SSD locais e Blobs Azure muito mais lentos. Quanto mais recentes os dados, maior é a possibilidade de ser armazenado num nível mais performante com menor latência, reduzindo a duração da consulta e CPU. Além dos dados em si, o sistema também tem uma cache para metadados. Quanto mais velhos os dados, menos hipóteses os seus metadados estarão em cache.

A consulta de que processa dados com mais de 14 dias é considerada uma consulta que consome recursos excessivos.


Embora algumas consultas exijam o uso de dados antigos, há casos em que dados antigos são usados por engano. Isto acontece quando as consultas são executadas sem fornecer intervalo de tempo nos seus meta-dados e nem todas as referências de tabela incluem filtro na coluna **TimeGenerated.** Nestes casos, o sistema irá digitalizar todos os dados que são armazenados nessa tabela. Quando a retenção de dados é longa, pode abranger intervalos de longo tempo e, portanto, dados tão antigos quanto o período de retenção de dados.

Tais casos podem ser, por exemplo:

- Não definir o intervalo de tempo no Log Analytics com uma sub-consulta que não é limitada. Veja o exemplo acima.
- Utilização da API sem os parâmetros opcionais do intervalo de tempo.
- Usando um cliente que não force um intervalo de tempo como o conector Power BI.

Consulte exemplos e notas na secção perversa, uma vez que também são relevantes neste caso.

## <a name="number-of-regions"></a>Número de regiões
Existem várias situações em que uma única consulta pode ser executada em diferentes regiões:

- Quando vários espaços de trabalho são explicitamente listados, e estão localizados em diferentes regiões.
- Quando uma consulta de recursos está a recolher dados e os dados são armazenados em múltiplos espaços de trabalho que estão localizados em diferentes regiões.

A execução de consultas entre regiões requer que o sistema serialize e transfira no backend grandes pedaços de dados intermédios que são geralmente muito maiores do que os resultados finais da consulta. Também limita a capacidade do sistema de realizar otimizações, heurísticas e caches de utilização.
Se não houver uma verdadeira razão para digitalizar todas estas regiões, deve ajustar o âmbito de aplicação para abranger menos regiões. Se o âmbito de recursos for minimizado, mas ainda assim muitas regiões forem utilizadas, isso poderá acontecer devido a uma má configuração. Por exemplo, os registos de auditoria e as definições de diagnóstico são enviados para diferentes espaços de trabalho em diferentes regiões ou existem múltiplas configurações de diagnóstico. 

A consulta que abrange mais de 3 regiões é considerada uma consulta que consome recursos excessivos. A consulta que abrange mais de 6 regiões é considerada uma consulta abusiva e pode ser estrangulada.

> [!IMPORTANT]
> Quando uma consulta é efetuada em várias regiões, a CPU e as medições de dados não serão precisas e representarão a medição apenas numa das regiões.

## <a name="number-of-workspaces"></a>Número de espaços de trabalho
Os espaços de trabalho são recipientes lógicos que são usados para segregar e administrar dados de registos. O backend otimiza as colocações de espaço de trabalho em aglomerados físicos dentro da região selecionada.

A utilização de múltiplos espaços de trabalho pode resultar de: 

- Onde vários espaços de trabalho estão explicitamente listados.
- Quando uma consulta de âmbito de recursos está a recolher dados e os dados são armazenados em vários espaços de trabalho.
 
A execução transversal e transversal de consultas requer que o sistema serialize e transfira no backend grandes pedaços de dados intermédios que são geralmente muito maiores do que os resultados finais da consulta. Também limita a capacidade do sistema de realizar otimizações, heurísticas e caches usando.

A consulta que abrange mais de 5 espaços de trabalho é considerada uma consulta que consome recursos excessivos. As consultas não podem ir até mais de 100 espaços de trabalho.

> [!IMPORTANT]
> Em alguns cenários multi-workspace, o CPU e as medições de dados não serão precisos e representarão a medição apenas para poucos dos espaços de trabalho.

## <a name="parallelism"></a>Paralelismo
O Azure Monitor Logs está a usar grandes aglomerados de Azure Data Explorer para executar consultas, e estes clusters variam em escala, potencialmente chegando a dezenas de nós computacional. O sistema escala automaticamente os clusters de acordo com a lógica e capacidade de colocação do espaço de trabalho.

Para executar eficientemente uma consulta, é dividida e distribuída para nómadas de computação com base nos dados necessários para o seu processamento. Há algumas situações em que o sistema não pode fazê-lo de forma eficiente. Isto pode levar a uma longa duração da consulta. 

Os comportamentos de consulta que podem reduzir o paralelismo incluem:

- Utilização de funções de serialização e janela, tais como o [operador de serialização,](/azure/kusto/query/serializeoperator) [o próximo()](/azure/kusto/query/nextfunction) [prev()](/azure/kusto/query/prevfunction)e as funções de [linha.](/azure/kusto/query/rowcumsumfunction) As funções de análise de séries temporárias e de análise de utilizadores podem ser utilizadas em alguns destes casos. A serialização ineficiente também pode acontecer se os seguintes operadores não forem utilizados no final da consulta: [intervalo](/azure/kusto/query/rangeoperator), [ordenar](/azure/kusto/query/sortoperator), [encomendar,](/azure/kusto/query/orderoperator) [topo,](/azure/kusto/query/topoperator) [top-hitters,](/azure/kusto/query/tophittersoperator) [getschema](/azure/kusto/query/getschemaoperator).
-    A utilização da função de agregação [dcount()](/azure/kusto/query/dcount-aggfunction) força o sistema a ter cópia central dos valores distintos. Quando a escala de dados for elevada, considere usar os parâmetros opcionais da função dcount para reduzir a precisão.
-    Em muitos casos, o operador de [união](/azure/kusto/query/joinoperator?pivots=azuremonitor) reduz o paralelismo geral. Examine a mistura como alternativa quando o desempenho é problemático.
-    Nas consultas de âmbito de recursos, os controlos de Pré-execução de Kubernetes RBAC ou Azure RBAC podem permanecer em situações em que há um número muito grande de atribuições de papel Azure. Isto pode levar a controlos mais longos que resultariam num paraleloismo mais baixo. Por exemplo, uma consulta é executada numa subscrição onde existem milhares de recursos e cada recurso tem muitas atribuições de funções ao nível dos recursos, não no grupo de subscrição ou recursos.
-    Se uma consulta estiver a processar pequenos pedaços de dados, o seu paralelismo será baixo, uma vez que o sistema não o espalhará por muitos nós computacional.



## <a name="next-steps"></a>Passos seguintes

- [Documentação de referência para a língua de consulta kusto.](/azure/kusto/query/)
