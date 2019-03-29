---
title: Analisar dados de texto nos registos do Azure Monitor | Documentos da Microsoft
description: Descreve as diferentes opções para analisar dados de registo nos registos do Azure Monitor, quando os dados são ingeridos e quando ela é recuperada numa consulta, comparando as relativas vantagens para cada um.
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: log-analytics
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/04/2018
ms.author: bwren
ms.openlocfilehash: ad4839a1b9e951a2bb206518254826a066330000
ms.sourcegitcommit: f8c592ebaad4a5fc45710dadc0e5c4480d122d6f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2019
ms.locfileid: "58620864"
---
# <a name="parse-text-data-in-azure-monitor-logs"></a>Analisar dados de texto nos registos do Azure Monitor
Alguns dados de registo recolhidos pelo Azure Monitor irão incluir várias partes de informações numa única propriedade. Analisando estes dados em várias propriedades facilitam a utilizar nas consultas. Um exemplo comum é um [log personalizado](../../log-analytics/log-analytics-data-sources-custom-logs.md) que recolhe uma entrada de registo inteira com vários valores numa única propriedade. Ao criar propriedades separadas para os valores diferentes, pode pesquisar e agregados em cada um.

Este artigo descreve as diferentes opções para analisar dados de registo no Azure Monitor, quando os dados são ingeridos e quando ela é recuperada numa consulta, comparando as relativas vantagens para cada um.


## <a name="parsing-methods"></a>Métodos de análise
Pode analisar dados em tempo de ingestão quando os dados são recolhidos ou consulta de tempo quando a analisar os dados com uma consulta. Cada estratégia tem vantagens únicas, conforme descrito abaixo.

### <a name="parse-data-at-collection-time"></a>Analisar dados em tempo de coleção
Ao analisar dados em tempo de coleção, configura [campos personalizados](../../log-analytics/log-analytics-custom-fields.md) que criar novas propriedades na tabela. As consultas não ter que incluir qualquer lógica de análise e simplesmente usar essas propriedades como qualquer outro campo na tabela.

Vantagens deste método incluem o seguinte:

- Mais fácil para consultar os dados recolhidos, uma vez que não precisa incluir analisar comandos na consulta.
- Melhor desempenho das consultas desde a consulta não precisa de efetuar a análise.
 
Desvantagens deste método incluem o seguinte:

- Tem de ser definido com antecedência. Não pode incluir dados que já tenham sido recolhidos.
- Se alterar a lógica de análise, ele só será aplicada à novos dados.
- Menos opções de análise que os disponíveis nas consultas.
- Aumenta o tempo de latência para a recolha de dados.
- Erros podem ser difícil processar.


### <a name="parse-data-at-query-time"></a>Analisar dados no momento da consulta
Quando analisar dados no momento da consulta, incluir lógica na sua consulta para analisar dados em vários campos. Não é modificada da própria tabela.

Vantagens deste método incluem o seguinte:

- Aplica-se a quaisquer dados, incluindo os dados que já tenham sido recolhidos.
- Alterações na lógica podem ser aplicadas de imediato a todos os dados.
- Flexível de opções, incluindo lógica predefinida para estruturas de dados específico de análise.
 
Desvantagens deste método incluem o seguinte:

- Requer consultas mais complexas. Isso pode ser atenuado utilizando [funções para simular uma tabela](#use-function-to-simulate-a-table).
- Têm de replicar análise lógica em várias consultas. Pode partilhar uma lógica por meio das funções.
- Pode criar sobrecarga quando a execução de uma lógica complexa no registo muito grande conjuntos de (milhares de milhões de registos).

## <a name="parse-data-as-its-collected"></a>Analisar dados à medida que é recolhido
Ver [criar campos personalizados no Azure Monitor](../platform/custom-fields.md) para obter detalhes sobre a análise de dados à medida que é recolhido. Esta ação cria propriedades personalizadas na tabela que pode ser utilizada pelas consultas assim como qualquer outra propriedade.

## <a name="parse-data-in-query-using-patterns"></a>Analisar dados com padrões de consulta
Quando os dados que pretende analisar podem ser identificados por um padrão repetido em registos, pode utilizar diferentes operadores no [linguagem de consulta de Kusto](/azure/kusto/query/) para extrair o conjunto específico de dados para uma ou mais propriedades de novo.

### <a name="simple-text-patterns"></a>Padrões de texto simples

Utilize o [analisar](/azure/kusto/query/parseoperator) operador na sua consulta para criar um ou mais propriedades personalizadas que podem ser extraídas a partir de uma expressão de cadeia de caracteres. Especifique o padrão para ser identificados e os nomes das propriedades para criar. Isto é particularmente útil para dados com cadeias de caracteres de chave-valor com um formulário semelhante _chave = valor_.

Considere um registo personalizado com dados no seguinte formato.

```
Time=2018-03-10 01:34:36 Event Code=207 Status=Success Message=Client 05a26a97-272a-4bc9-8f64-269d154b0e39 connected
Time=2018-03-10 01:33:33 Event Code=208 Status=Warning Message=Client ec53d95c-1c88-41ae-8174-92104212de5d disconnected
Time=2018-03-10 01:35:44 Event Code=209 Status=Success Message=Transaction 10d65890-b003-48f8-9cfc-9c74b51189c8 succeeded
Time=2018-03-10 01:38:22 Event Code=302 Status=Error Message=Application could not connect to database
Time=2018-03-10 01:31:34 Event Code=303 Status=Error Message=Application lost connection to database
```

A seguinte consulta seria analisar estes dados em propriedades individuais. A linha com _project_ é adicionado ao devolver apenas as propriedades calculadas e não _RawData_, que é a única propriedade que contém toda a entrada de log personalizado.

```Kusto
MyCustomLog_CL
| parse RawData with * "Time=" EventTime " Event Code=" Code " Status=" Status " Message=" Message
| project EventTime, Code, Status, Message
```

Segue-se outro exemplo que divide o nome de utilizador de um UPN no _AzureActivity_ tabela.

```Kusto
AzureActivity
| parse  Caller with UPNUserPart "@" * 
| where UPNUserPart != "" //Remove non UPN callers (apps, SPNs, etc)
| distinct UPNUserPart, Caller
```


### <a name="regular-expressions"></a>Expressões regulares
Se seus dados podem ser identificados com uma expressão regular, pode usar [funções que usam expressões regulares](/azure/kusto/query/re2) para extrair os valores individuais. O exemplo seguinte utiliza [extrair](/azure/kusto/query/extractfunction) para dividir o _UPN_ campo _AzureActivity_ regista e, em seguida, retornar utilizadores distintos.

```Kusto
AzureActivity
| extend UPNUserPart = extract("([a-z.]*)@", 1, Caller) 
| distinct UPNUserPart, Caller
```

Para ativar a análise em grande escala, eficiente do Azure Monitor utiliza re2 versão das expressões regulares, que é semelhante, mas não idêntico a algumas das outras variantes de expressão regular. Consulte a [sintaxe de expressão re2](https://aka.ms/kql_re2syntax) para obter detalhes.


## <a name="parse-delimited-data-in-a-query"></a>Analisar dados delimitados numa consulta
Dados delimitados separa os campos com um caráter comuns, tal como uma vírgula num arquivo CSV. Utilize o [dividir](/azure/kusto/query/splitfunction) função para analisar dados delimitados com um delimitador especificado. Pode utilizá-lo com [expandir](/azure/kusto/query/extendoperator) operador para retornar todos os campos de dados ou para especificar campos individuais a serem incluídos na saída.

> [!NOTE]
> Uma vez que a divisão retorna um objeto dinâmico, os resultados poderão ter de ser explicitamente convertido para tipos de dados como cadeia de caracteres a ser utilizado no operadores e filtros.

Considere um registo personalizado com dados no seguinte formato CSV.

```
2018-03-10 01:34:36, 207,Success,Client 05a26a97-272a-4bc9-8f64-269d154b0e39 connected
2018-03-10 01:33:33, 208,Warning,Client ec53d95c-1c88-41ae-8174-92104212de5d disconnected
2018-03-10 01:35:44, 209,Success,Transaction 10d65890-b003-48f8-9cfc-9c74b51189c8 succeeded
2018-03-10 01:38:22, 302,Error,Application could not connect to database
2018-03-10 01:31:34, 303,Error,Application lost connection to database
```

A seguinte consulta seria analisar estes dados e resumir por duas propriedades calculadas. A primeira linha divide a _RawData_ propriedade numa matriz de cadeia de caracteres. Cada uma das linhas seguintes dá um nome ao propriedades individuais e adiciona-as para a saída com as funções para convertê-los para o tipo de dados apropriados.

```Kusto
MyCustomCSVLog_CL
| extend CSVFields  = split(RawData, ',')
| extend EventTime  = todatetime(CSVFields[0])
| extend Code       = toint(CSVFields[1]) 
| extend Status     = tostring(CSVFields[2]) 
| extend Message    = tostring(CSVFields[3]) 
| where getyear(EventTime) == 2018
| summarize count() by Status,Code
```

## <a name="parse-predefined-structures-in-a-query"></a>Analisar estruturas predefinidas numa consulta
Se os dados estão formatados numa estrutura conhecida, poderá conseguir utilizar uma das funções no [linguagem de consulta de Kusto](/azure/kusto/query/) para analisar estruturas predefinidas:

- [JSON](/azure/kusto/query/parsejsonfunction)
- [XML](/azure/kusto/query/parse-xmlfunction)
- [IPv4](/azure/kusto/query/parse-ipv4function)
- [URL](/azure/kusto/query/parseurlfunction)
- [Consulta de URL](/azure/kusto/query/parseurlqueryfunction)
- [Caminho do ficheiro](/azure/kusto/query/parsepathfunction)
- [Agente de utilizador](/azure/kusto/query/parse-useragentfunction)
- [Cadeia de versão](/azure/kusto/query/parse-versionfunction)

A seguinte consulta de exemplo analisa o _propriedades_ campo o _AzureActivity_ tabela, que está estruturada em JSON. Este guarda os resultados para uma propriedade dinâmica chamada _parsedProp_, que inclui o indivíduo valor no JSON com o nome. Estes valores são utilizados para filtrar e resumir os resultados da consulta.

```Kusto
AzureActivity
| extend parsedProp = parse_json(Properties) 
| where parsedProp.isComplianceCheck == "True" 
| summarize count() by ResourceGroup, tostring(parsedProp.tags.businessowner)
```

Estas funções de análise podem ser processador intensiva, pelo que deve ser utilizados apenas quando a consulta utiliza várias propriedades dos dados formatados. Caso contrário, o processamento de correspondência de padrão de simple será mais rápido.

O exemplo seguinte mostra a estrutura do tipo de TGT Preauth do controlador de domínio. O tipo existe apenas no campo EventData, que é uma cadeia de caracteres XML, mas não outros dados este campo é necessário. Neste caso, [analisar](/azure/kusto/query/parseoperator) é usado para encontrar a parte necessária de dados.

```Kusto
SecurityEvent
| where EventID == 4768
| parse EventData with * 'PreAuthType">' PreAuthType '</Data>' * 
| summarize count() by PreAuthType
```

## <a name="use-function-to-simulate-a-table"></a>Utilize a função para simular uma tabela
Pode ter várias consultas que realizam a mesma de análise de uma determinada tabela. Neste caso, [criar uma função](functions.md) que retorna dados analisados em vez de replicar a lógica de análise em cada consulta. Em seguida, pode utilizar o alias de função no lugar da tabela original em outras consultas.

Considere o exemplo de registo personalizado delimitada por vírgulas acima. Para usar dados analisados de várias consultas, criar uma função utilizando a seguinte consulta e guarde-o com o alias _MyCustomCSVLog_.

```Kusto
MyCustomCSVLog_CL
| extend CSVFields = split(RawData, ',')
| extend DateTime  = tostring(CSVFields[0])
| extend Code      = toint(CSVFields[1]) 
| extend Status    = tostring(CSVFields[2]) 
| extend Message   = tostring(CSVFields[3]) 
```

Agora, pode utilizar o alias _MyCustomCSVLog_ em vez do nome de tabela real em consultas semelhante ao seguinte.

```Kusto
MyCustomCSVLog
| summarize count() by Status,Code
```


## <a name="next-steps"></a>Passos Seguintes
* Saiba mais sobre [registar as consultas](log-query-overview.md) para analisar os dados recolhidos a partir de origens de dados e soluções.