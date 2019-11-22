---
title: Introdução às consultas de log no Azure Monitor | Microsoft Docs
description: Este artigo fornece um tutorial para começar a escrever consultas de log no Azure Monitor.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: tutorial
author: bwren
ms.author: bwren
ms.date: 10/24/2019
ms.openlocfilehash: 0503ebc7a585210f95eb58b0f11ae1dd0a835264
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74278612"
---
# <a name="get-started-with-log-queries-in-azure-monitor"></a>Introdução às consultas de log no Azure Monitor

> [!NOTE]
> Você pode trabalhar com este exercício em seu próprio ambiente se estiver coletando dados de pelo menos uma máquina virtual. Caso contrário, use nosso [ambiente de demonstração](https://portal.loganalytics.io/demo), que inclui muitos dados de exemplo.

Neste tutorial, você aprenderá a escrever consultas de log em Azure Monitor. Ele ensinará como:

- Entender a estrutura de consulta
- Classificar resultados da consulta
- Filtrar resultados da consulta
- Especificar um intervalo de tempo
- Selecionar quais campos incluir nos resultados
- Definir e usar campos personalizados
- Agregar e resultados de grupo

Para obter um tutorial sobre como usar Log Analytics no portal do Azure, consulte Introdução [ao Azure Monitor log Analytics](get-started-portal.md).<br>
Para obter mais detalhes sobre as consultas de log no Azure Monitor, consulte [visão geral das consultas de log no Azure monitor](log-query-overview.md).

Siga junto com uma versão de vídeo deste tutorial abaixo:

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE42pGX]

## <a name="writing-a-new-query"></a>Escrevendo uma nova consulta
As consultas podem começar com um nome de tabela ou o comando de *pesquisa* . Você deve começar com um nome de tabela, pois ele define um escopo claro para a consulta e melhora o desempenho da consulta e a relevância dos resultados.

> [!NOTE]
> A linguagem de consulta Kusto usada pelo Azure Monitor diferencia maiúsculas de minúsculas. Palavras-chave de idioma normalmente são escritas em letras minúsculas. Ao usar nomes de tabelas ou colunas em uma consulta, certifique-se de usar o caso correto, conforme mostrado no painel esquema.

### <a name="table-based-queries"></a>Consultas baseadas em tabela
Azure Monitor organiza os dados de log em tabelas, cada uma composta por várias colunas. Todas as tabelas e colunas são mostradas no painel esquema em Log Analytics no portal de análise. Identifique uma tabela na qual você esteja interessado e dê uma olhada em um pouco de dados:

```Kusto
SecurityEvent
| take 10
```

A consulta mostrada acima retorna 10 resultados da tabela *SecurityEvent* , em nenhuma ordem específica. Essa é uma maneira muito comum de dar uma olhada em uma tabela e entender sua estrutura e conteúdo. Vamos examinar como ele é criado:

* A consulta começa com o nome de tabela *SecurityEvent* -essa parte define o escopo da consulta.
* O caractere de pipe (|) separa os comandos, portanto, a saída do primeiro na entrada do comando a seguir. Você pode adicionar qualquer número de elementos canalizados.
* Seguir o pipe é o comando **Take** , que retorna um número específico de registros arbitrários da tabela.

Na verdade, poderíamos executar a consulta mesmo sem adicionar `| take 10`-isso ainda seria válido, mas poderia retornar até 10.000 resultados.

### <a name="search-queries"></a>Consultas de pesquisa
As consultas de pesquisa são menos estruturadas e geralmente mais adequadas para localizar registros que incluem um valor específico em qualquer uma de suas colunas:

```Kusto
search in (SecurityEvent) "Cryptographic"
| take 10
```

Essa consulta pesquisa a tabela *SecurityEvent* em busca de registros que contenham a frase "criptografia". Desses registros, 10 registros serão retornados e exibidos. Se omitirmos a parte `in (SecurityEvent)` e apenas executar `search "Cryptographic"`, a pesquisa passará por *todas as* tabelas, o que levaria mais tempo e será menos eficiente.

> [!WARNING]
> As consultas de pesquisa são normalmente mais lentas do que as consultas baseadas em tabela, pois precisam processar mais dados. 

## <a name="sort-and-top"></a>Classificar e superior
Embora o **Take** seja útil para obter alguns registros, os resultados são selecionados e exibidos sem nenhuma ordem específica. Para obter uma exibição ordenada, você pode **classificar** pela coluna preferencial:

```Kusto
SecurityEvent   
| sort by TimeGenerated desc
```

Isso pode retornar muitos resultados, mas também pode levar algum tempo. A consulta acima classifica *a* tabela SecurityEvent inteira pela coluna TimeGenerated. Em seguida, o portal de análise limita a exibição para mostrar apenas 10.000 registros. Essa abordagem é, obviamente, não ideal.

A melhor maneira de obter apenas os 10 registros mais recentes é usar **Top**, que classifica a tabela inteira no lado do servidor e retorna os principais registros:

```Kusto
SecurityEvent
| top 10 by TimeGenerated
```

Decrescente é a ordem de classificação padrão, portanto, normalmente omitemos o argumento **desc** . A saída terá a seguinte aparência:

![10 principais](media/get-started-queries/top10.png)


## <a name="where-filtering-on-a-condition"></a>Em que: filtragem em uma condição
Filtros, conforme indicado pelo nome, filtre os dados por uma condição específica. Essa é a maneira mais comum de limitar os resultados da consulta a informações relevantes.

Para adicionar um filtro a uma consulta, use o operador **Where** seguido por uma ou mais condições. Por exemplo, a consulta a seguir retorna apenas registros *SecurityEvent* em que _Level_ é igual a _8_:

```Kusto
SecurityEvent
| where Level == 8
```

Ao escrever condições de filtro, você pode usar as seguintes expressões:

| Expressão | Descrição | Exemplo |
|:---|:---|:---|
| == | Verificar igualdade<br>(diferencia maiúsculas de minúsculas) | `Level == 8` |
| =~ | Verificar igualdade<br>(não diferencia maiúsculas de minúsculas) | `EventSourceName =~ "microsoft-windows-security-auditing"` |
| !=, <> | Verificar desigualdade<br>(as duas expressões são idênticas) | `Level != 4` |
| *e*, *ou* | Necessário entre as condições| `Level == 16 or CommandLine != ""` |

Para filtrar por várias condições, você pode usar o **e**o:

```Kusto
SecurityEvent
| where Level == 8 and EventID == 4672
```

ou redirecionar **vários dos** elementos um após o outro:

```Kusto
SecurityEvent
| where Level == 8 
| where EventID == 4672
```
    
> [!NOTE]
> Os valores podem ter tipos diferentes, portanto, talvez seja necessário convertê-los para executar a comparação no tipo correto. Por exemplo, a coluna de *nível* SecurityEvent é do tipo cadeia de caracteres, portanto, você deve convertê-la em um tipo numérico, como *int* ou *Long*, antes de poder usar operadores numéricos nele: `SecurityEvent | where toint(Level) >= 10`

## <a name="specify-a-time-range"></a>Especificar um intervalo de tempo

### <a name="time-picker"></a>Seletor de hora
O seletor de tempo está ao lado do botão Executar e indica que estamos consultando somente os registros das últimas 24 horas. Esse é o intervalo de tempo padrão aplicado a todas as consultas. Para obter somente os registros da última hora, selecione _última hora_ e execute a consulta novamente.

![Selecionador de hora](media/get-started-queries/timepicker.png)


### <a name="time-filter-in-query"></a>Filtro de tempo na consulta
Você também pode definir seu próprio intervalo de tempo adicionando um filtro de tempo à consulta. É melhor inserir o filtro de tempo imediatamente após o nome da tabela: 

```Kusto
SecurityEvent
| where TimeGenerated > ago(30m) 
| where toint(Level) >= 10
```

No filtro de tempo acima `ago(30m)` significa "30 minutos atrás" para que essa consulta retorne apenas os registros dos últimos 30 minutos. Outras unidades de tempo incluem dias (2D), minutos (25m) e segundos (10s).


## <a name="project-and-extend-select-and-compute-columns"></a>Project e extend: selecionar e calcular colunas
Use o **projeto** para selecionar colunas específicas a serem incluídas nos resultados:

```Kusto
SecurityEvent 
| top 10 by TimeGenerated 
| project TimeGenerated, Computer, Activity
```

O exemplo anterior gera essa saída:

![Resultados do projeto de consulta](media/get-started-queries/project.png)

Você também pode usar o **Project** para renomear colunas e definir novas. O exemplo a seguir usa o projeto para fazer o seguinte:

* Selecione apenas o *computador* e as colunas originais *geradas* .
* Renomeie a coluna *atividade* como *EventDetails*.
* Crie uma nova coluna chamada *EventCode*. A função **substring ()** é usada para obter apenas os quatro primeiros caracteres do campo atividade.


```Kusto
SecurityEvent
| top 10 by TimeGenerated 
| project Computer, TimeGenerated, EventDetails=Activity, EventCode=substring(Activity, 0, 4)
```

**estender** mantém todas as colunas originais no conjunto de resultados e define as adicionais. A consulta a seguir usa **Extend** para adicionar a coluna *EventCode* . Observe que essa coluna pode não ser exibida no final da tabela e, nesse caso, você precisaria expandir os detalhes de um registro para exibi-lo.

```Kusto
SecurityEvent
| top 10 by TimeGenerated
| extend EventCode=substring(Activity, 0, 4)
```

## <a name="summarize-aggregate-groups-of-rows"></a>Resumir: agregar grupos de linhas
Use **resumir** para identificar grupos de registros, de acordo com uma ou mais colunas, e aplique agregações a eles. O uso mais comum de **resumete** é *Count*, que retorna o número de resultados em cada grupo.

A consulta a seguir revisa todos os registros de *desempenho* da última hora, agrupa-os por *objectname*e conta os registros em cada grupo: 
```Kusto
Perf
| where TimeGenerated > ago(1h)
| summarize count() by ObjectName
```

Às vezes, faz sentido definir grupos por várias dimensões. Cada combinação exclusiva desses valores define um grupo separado:

```Kusto
Perf
| where TimeGenerated > ago(1h)
| summarize count() by ObjectName, CounterName
```

Outro uso comum é executar cálculos matemáticos ou estatísticos em cada grupo. Por exemplo, o seguinte calcula o *valor* médio para cada computador:

```Kusto
Perf
| where TimeGenerated > ago(1h)
| summarize avg(CounterValue) by Computer
```

Infelizmente, os resultados dessa consulta não têm sentido, pois misturamos diferentes contadores de desempenho. Para tornar isso mais significativo, devemos calcular a média separadamente para cada combinação de *CounterName* e *Computer*:

```Kusto
Perf
| where TimeGenerated > ago(1h)
| summarize avg(CounterValue) by Computer, CounterName
```

### <a name="summarize-by-a-time-column"></a>Resumir por uma coluna de tempo
O agrupamento de resultados também pode ser baseado em uma coluna de tempo ou em outro valor contínuo. Simplesmente Resumindo `by TimeGenerated` o criaria grupos para cada milissegundo durante o intervalo de tempo, já que esses são valores exclusivos. 

Para criar grupos com base em valores contínuos, é melhor dividir o intervalo em unidades gerenciáveis usando **bin**. A consulta a seguir analisa os registros de *desempenho* que medem a memória livre (*MBytes disponíveis*) em um computador específico. Ele calcula o valor médio de cada período de 1 hora nos últimos 7 dias:

```Kusto
Perf 
| where TimeGenerated > ago(7d)
| where Computer == "ContosoAzADDS2" 
| where CounterName == "Available MBytes" 
| summarize avg(CounterValue) by bin(TimeGenerated, 1h)
```

Para tornar a saída mais clara, selecione para exibi-la como um gráfico de tempo, mostrando a memória disponível ao longo do tempo:

![Memória de consulta ao longo do tempo](media/get-started-queries/chart.png)



## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre como usar dados de cadeia de caracteres em uma consulta de log com o [trabalho com cadeias em consultas de log Azure monitor](string-operations.md).
- Saiba mais sobre como agregar dados em uma consulta de log com [agregações avançadas em consultas de log de Azure monitor](advanced-aggregations.md).
- Saiba como unir dados de várias tabelas com [junções em Azure monitor consultas de log](joins.md).
- Obtenha a documentação sobre toda a linguagem de consulta Kusto na [referência de linguagem KQL](/azure/kusto/query/).