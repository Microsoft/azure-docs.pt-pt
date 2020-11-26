---
title: Começa com consultas de registo no Azure Monitor Microsoft Docs
description: Este artigo fornece um tutorial para começar a escrever consultas de log no Azure Monitor.
ms.subservice: logs
ms.topic: tutorial
author: bwren
ms.author: bwren
ms.date: 10/24/2019
ms.openlocfilehash: a949c9b34e299e0dc4eccbb62f4b4ebb38d6ccb9
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96186716"
---
# <a name="get-started-with-log-queries-in-azure-monitor"></a>Introdução às consultas de registos no Azure Monitor

> [!NOTE]
> Pode trabalhar neste exercício no seu próprio ambiente se estiver a recolher dados de pelo menos uma máquina virtual. Se não, então use o nosso [ambiente Demo,](https://ms.portal.azure.com/#blade/Microsoft_Azure_Monitoring_Logs/DemoLogsBlade)que inclui muitos dados da amostra.  Se já sabe como consultar em KQL, mas apenas precisa de criar rapidamente consultas úteis com base no tipo de recursos, consulte o [painel de consultas de exemplo guardado.](./example-queries.md)

Neste tutorial você aprenderá a escrever consultas de log no Azure Monitor. Vai ensinar-te a:

- Compreender a estrutura de consulta
- Ordenar resultados de consulta
- Resultados da consulta do filtro
- Especificar um intervalo de tempo
- Selecione quais os campos a incluir nos resultados
- Definir e utilizar campos personalizados
- Resultados agregados e de grupo

Para obter um tutorial sobre a utilização do Log Analytics no portal Azure, consulte [Começar com a Azure Monitor Log Analytics](./log-analytics-tutorial.md).<br>
Para obter mais detalhes sobre as consultas de registo no Azure Monitor, consulte [a visão geral das consultas de registo no Azure Monitor](log-query-overview.md).

Acompanhe uma versão em vídeo deste tutorial abaixo:

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE42pGX]

## <a name="writing-a-new-query"></a>Escrever uma nova consulta

As consultas podem começar com um nome de mesa ou com o comando *de pesquisa.* Deve começar com um nome de mesa, uma vez que define uma margem clara para a consulta e melhora o desempenho da consulta e a relevância dos resultados.

> [!NOTE]
> A linguagem de consulta Kusto utilizada pelo Azure Monitor é sensível às maiúsculas e minúsculas. Normalmente, as palavras-chave da linguagem são escritas em letras minúsculas. Quando utilizar nomes de tabelas ou colunas numa consulta, certifique-se de que utiliza a caixa correta, como indicado no painel de esquemas.

### <a name="table-based-queries"></a>Consultas à base de mesa

O Azure Monitor organiza os dados de registo em tabelas, cada uma composta por várias colunas. Todas as tabelas e colunas são mostradas no painel de esquemas em Log Analytics no portal Analytics. Identifique uma tabela em que esteja interessado e, em seguida, dê uma olhada em alguns dados:

```Kusto
SecurityEvent
| take 10
```

A consulta acima apresentada devolve 10 resultados da tabela *SecurityEvent,* sem ordem específica. Esta é uma forma muito comum de dar uma olhada numa mesa e compreender a sua estrutura e conteúdo. Vamos examinar como é construído:

* A consulta começa com o nome de mesa *SecurityEvent* - esta parte define o âmbito da consulta.
* O caractere do tubo separa os comandos, pelo que a saída do primeiro na entrada do seguinte comando. Pode adicionar qualquer número de elementos canalizados.
* Seguindo o tubo está o comando **de tomada,** que devolve um número específico de registos arbitrários da tabela.

Poderíamos, na verdade, fazer a consulta mesmo sem acrescentar `| take 10` - que ainda seria válido, mas poderia devolver até 10.000 resultados.

### <a name="search-queries"></a>Consultas de pesquisa

As consultas de pesquisa são menos estruturadas e geralmente mais adequadas para encontrar registos que incluam um valor específico em qualquer uma das suas colunas:

```Kusto
search in (SecurityEvent) "Cryptographic"
| take 10
```

Esta consulta procura registos na tabela *SecurityEvent* que contenham a frase "Criptográfica". Desses registos, 10 discos serão devolvidos e exibidos. Se omitirmos a `in (SecurityEvent)` peça e `search "Cryptographic"` corrermos, a procura passará por todas *as* mesas, o que demoraria mais tempo e seria menos eficiente.

> [!WARNING]
> As consultas de pesquisa são tipicamente mais lentas do que as consultas baseadas em tabelas porque têm de processar mais dados. 

## <a name="sort-and-top"></a>Ordenar e topo
Embora **a tomada** seja útil para obter alguns registos, os resultados são selecionados e exibidos em nenhuma ordem particular. Para obter uma visão ordenada, pode **ordenar** pela coluna preferida:

```Kusto
SecurityEvent   
| sort by TimeGenerated desc
```

Isso pode, no entanto, devolver demasiados resultados e também pode levar algum tempo. A consulta acima classifica *toda a* tabela SecurityEvent pela coluna TimeGenerated. Em seguida, o portal Analytics limita o ecrã para mostrar apenas 10.000 registos. Esta abordagem não é, naturalmente, a ideal.

A melhor maneira de obter apenas os últimos 10 registos é usar o **topo**, que classifica toda a tabela no lado do servidor e, em seguida, devolve os registos de topo:

```Kusto
SecurityEvent
| top 10 by TimeGenerated
```

Descer é a ordem de triagem padrão, por isso normalmente omitimos o argumento **desc.** A saída será assim:

![Top 10](media/get-started-queries/top10.png)


## <a name="where-filtering-on-a-condition"></a>Quando: filtragem numa condição
Os filtros, conforme indicado pelo seu nome, filtram os dados por uma condição específica. Esta é a forma mais comum de limitar os resultados da consulta à informação relevante.

Para adicionar um filtro a uma consulta, utilize o operador **onde** o operador se segue de uma ou mais condições. Por exemplo, a seguinte consulta devolve apenas registos *SecurityEvent* onde _o nível_ é igual a _8_:

```Kusto
SecurityEvent
| where Level == 8
```

Ao escrever condições de filtro, pode utilizar as seguintes expressões:

| Expressão | Descrição | Exemplo |
|:---|:---|:---|
| == | Verificar igualdade<br>(sensível a casos) | `Level == 8` |
| =~ | Verificar igualdade<br>(caso-insensível) | `EventSourceName =~ "microsoft-windows-security-auditing"` |
| !=, <> | Verifique a desigualdade<br>(ambas as expressões são idênticas) | `Level != 4` |
| *e*, *ou* | Obrigatório entre condições| `Level == 16 or CommandLine != ""` |

Para filtrar por várias condições, pode utilizar **e:**

```Kusto
SecurityEvent
| where Level == 8 and EventID == 4672
```

ou tubo múltiplo **onde** elementos um após o outro:

```Kusto
SecurityEvent
| where Level == 8 
| where EventID == 4672
```
    
> [!NOTE]
> Os valores podem ter diferentes tipos, por isso pode ser necessário lanhá-los para realizar a comparação no tipo correto. Por exemplo, a coluna SecurityEvent *Level* é do tipo String, pelo que deve lanhá-la para um tipo numérico, como *int* ou *long*, antes de poder utilizar operadores numéricos nele: `SecurityEvent | where toint(Level) >= 10`

## <a name="specify-a-time-range"></a>Especificar um intervalo de tempo

### <a name="time-picker"></a>Apanhador de tempo

O apanhador de tempo está ao lado do botão Executar e indica que estamos a consultar apenas registos das últimas 24 horas. Este é o intervalo de tempo padrão aplicado a todas as consultas. Para obter apenas registos da última hora, selecione _Última hora_ e faça a consulta novamente.

![Selecionador de hora](media/get-started-queries/timepicker.png)


### <a name="time-filter-in-query"></a>Filtro de tempo na consulta

Também pode definir o seu próprio intervalo de tempo adicionando um filtro de tempo à consulta. É melhor colocar o filtro de tempo imediatamente após o nome da mesa: 

```Kusto
SecurityEvent
| where TimeGenerated > ago(30m) 
| where toint(Level) >= 10
```

No filtro de tempo acima  `ago(30m)` significa "há 30 minutos" pelo que esta consulta só devolve registos dos últimos 30 minutos. Outras unidades de tempo incluem dias (2d), minutos (25m) e segundos (10s).


## <a name="project-and-extend-select-and-compute-columns"></a>Projeto e Extensão: selecione e computa as colunas

Utilize **o projeto** para selecionar colunas específicas para incluir nos resultados:

```Kusto
SecurityEvent 
| top 10 by TimeGenerated 
| project TimeGenerated, Computer, Activity
```

O exemplo anterior gera esta saída:

![Resultados do projeto de consulta](media/get-started-queries/project.png)

Também pode usar **o projeto** para renomear colunas e definir novas. O exemplo a seguir utiliza o projeto para fazer o seguinte:

* Selecione apenas as colunas originais *com o computador* e o *tempo.*
* Exibe a coluna *De atividade* como *EventDetails*.
* Criar uma nova coluna chamada *EventCode*. A função **sub-adiscagem()** é utilizada para obter apenas os primeiros quatro caracteres do campo Atividade.


```Kusto
SecurityEvent
| top 10 by TimeGenerated 
| project Computer, TimeGenerated, EventDetails=Activity, EventCode=substring(Activity, 0, 4)
```

**prolongar** mantém todas as colunas originais no conjunto de resultados e define as adicionais. As seguintes utilizações **estendem-se** para adicionar a coluna *EventCode.* Note que esta coluna pode não ser exibida no final dos resultados da tabela, caso em que necessitaria de expandir os detalhes de um registo para a visualizar.

```Kusto
SecurityEvent
| top 10 by TimeGenerated
| extend EventCode=substring(Activity, 0, 4)
```

## <a name="summarize-aggregate-groups-of-rows"></a>Resumo: grupos agregados de linhas
Use **resumir** para identificar grupos de registos, de acordo com uma ou mais colunas, e aplicar agregações neles. O uso mais comum de **resumo** é *a contagem*, que devolve o número de resultados em cada grupo.

A seguinte consulta analisa todos os registos *perf* da última hora, agrupa-os por *ObjectName,* e conta os registos em cada grupo: 
```Kusto
Perf
| where TimeGenerated > ago(1h)
| summarize count() by ObjectName
```

Às vezes faz sentido definir grupos por múltiplas dimensões. Cada combinação única destes valores define um grupo separado:

```Kusto
Perf
| where TimeGenerated > ago(1h)
| summarize count() by ObjectName, CounterName
```

Outro uso comum é realizar cálculos matemáticos ou estatísticos em cada grupo. Por exemplo, o seguinte calcula o *CounterValue* médio para cada computador:

```Kusto
Perf
| where TimeGenerated > ago(1h)
| summarize avg(CounterValue) by Computer
```

Infelizmente, os resultados desta consulta não têm sentido, uma vez que misturamos diferentes contadores de desempenho. Para tornar isto mais significativo, devemos calcular a média separadamente para cada combinação de *Contra-Nome* e *Computador:*

```Kusto
Perf
| where TimeGenerated > ago(1h)
| summarize avg(CounterValue) by Computer, CounterName
```

### <a name="summarize-by-a-time-column"></a>Resumir por uma coluna de tempo
Os resultados do agrupamento também podem ser baseados numa coluna temporal ou noutro valor contínuo. `by TimeGenerated`Resumindo, no entanto, criaria grupos para cada milissegundo ao longo do intervalo de tempo, uma vez que estes são valores únicos. 

Para criar grupos baseados em valores contínuos, o melhor é quebrar o alcance em unidades manejáveis usando **o caixote do lixo.** A seguinte consulta analisa registos *perf* que medem a memória gratuita *(MBytes disponíveis*) num computador específico. Calcula o valor médio de cada período de 1 hora nos últimos 7 dias:

```Kusto
Perf 
| where TimeGenerated > ago(7d)
| where Computer == "ContosoAzADDS2" 
| where CounterName == "Available MBytes" 
| summarize avg(CounterValue) by bin(TimeGenerated, 1h)
```

Para tornar a saída mais clara, seleciona para exibi-la como um gráfico temporal, mostrando a memória disponível ao longo do tempo:

![Memória de consulta ao longo do tempo](media/get-started-queries/chart.png)



## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre a utilização de dados de cadeias numa consulta de log com [Trabalho com cordas em consultas de registo do Azure Monitor](/azure/data-explorer/kusto/query/samples?&pivots=azuremonitor#string-operations).
- Saiba mais sobre agregação de dados numa consulta de log com [agregações avançadas em consultas de registo do Azure Monitor](/azure/data-explorer/write-queries#advanced-aggregations).
- Saiba como juntar dados de várias tabelas com [se juntas em consultas de registo do Azure Monitor](/azure/data-explorer/kusto/query/samples?&pivots=azuremonitor#joins).
- Obtenha documentação sobre toda a língua de consulta kusto na [referência linguística KQL](/azure/kusto/query/).