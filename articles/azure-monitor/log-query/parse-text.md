---
title: Dados de texto parse em registos do Monitor Do Azure Microsoft Docs
description: Descreve diferentes opções para analisar dados de registo no Registo do Monitor Do Azure quando os dados são ingeridos e quando são recuperados numa consulta, comparando as vantagens relativas para cada um.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 12/04/2018
ms.openlocfilehash: d7a37d51c411488231205fd036f9a287f5206ce5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77672451"
---
# <a name="parse-text-data-in-azure-monitor-logs"></a>Dados de texto parse em registos do Monitor Azure
Alguns dados de registo recolhidos pelo Azure Monitor incluirão várias informações numa única propriedade. Analisar estes dados em várias propriedades facilita a utilização em consultas. Um exemplo comum é um [registo personalizado](../../log-analytics/log-analytics-data-sources-custom-logs.md) que recolhe uma entrada de registo inteira com múltiplos valores numa única propriedade. Ao criar propriedades separadas para os diferentes valores, pode pesquisar e agregar cada um.

Este artigo descreve diferentes opções para analisar dados de registo no Monitor Azure quando os dados são ingeridos e quando são recuperados numa consulta, comparando as vantagens relativas para cada um.


## <a name="parsing-methods"></a>Métodos de análise
Pode analisar os dados no tempo de ingestão quando os dados são recolhidos ou no momento da consulta ao analisar os dados com uma consulta. Cada estratégia tem vantagens únicas, como descrito abaixo.

### <a name="parse-data-at-collection-time"></a>Dados parse no momento da recolha
Quando analisa os dados no momento da recolha, configura campos [personalizados](../../log-analytics/log-analytics-custom-fields.md) que criam novas propriedades na tabela. As consultas não têm de incluir qualquer lógica de análise e simplesmente usar estas propriedades como qualquer outro campo na tabela.

As vantagens deste método incluem:

- Mais fácil consultar os dados recolhidos, uma vez que não precisa de incluir comandos de parse na consulta.
- Melhor desempenho de consulta já que a consulta não precisa de fazer análises.
 
As desvantagens para este método incluem:

- Deve ser definido com antecedência. Não posso incluir dados que já foram recolhidos.
- Se alterar a lógica de análise, só se aplicará a novos dados.
- Menos opções de análise do que disponíveis em consultas.
- Aumenta o tempo de latência para a recolha de dados.
- Erros podem ser difíceis de lidar.


### <a name="parse-data-at-query-time"></a>Dados parse no momento da consulta
Quando analisa os dados na hora da consulta, inclui lógica na sua consulta para analisar dados em vários campos. A mesa em si não é modificada.

As vantagens deste método incluem:

- Aplica-se a quaisquer dados, incluindo dados que já foram recolhidos.
- As mudanças de lógica podem ser aplicadas imediatamente a todos os dados.
- Opções flexíveis de análise, incluindo lógica pré-definida para estruturas de dados específicas.
 
As desvantagens para este método incluem:

- Requer consultas mais complexas. Isto pode ser atenuado utilizando [funções para simular uma tabela](#use-function-to-simulate-a-table).
- Deve replicar a lógica de análise em múltiplas consultas. Pode partilhar alguma lógica através de funções.
- Pode criar sobrecarga seleções quando executa uma lógica complexa contra recordes muito grandes (biliões de recordes).

## <a name="parse-data-as-its-collected"></a>Dados parse como são recolhidos
Consulte a [Create custom fields in Azure Monitor](../platform/custom-fields.md) para obter detalhes sobre dados de análise à medida que são recolhidos. Isto cria propriedades personalizadas na tabela que podem ser usadas por consultas como qualquer outra propriedade.

## <a name="parse-data-in-query-using-patterns"></a>Dados parse em consulta usando padrões
Quando os dados que pretende analisar podem ser identificados por um padrão repetido em registos, pode utilizar diferentes operadores na [linguagem de consulta kusto](/azure/kusto/query/) para extrair a peça específica de dados em uma ou mais novas propriedades.

### <a name="simple-text-patterns"></a>Padrões de texto simples

Utilize o operador [de parse](/azure/kusto/query/parseoperator) na sua consulta para criar uma ou mais propriedades personalizadas que podem ser extraídas de uma expressão de corda. Especifica o padrão a identificar e os nomes das propriedades para criar. Isto é particularmente útil para dados com cordas de valor-chave com um formulário semelhante ao _valor chave=valor_.

Considere um registo personalizado com dados no seguinte formato.

```
Time=2018-03-10 01:34:36 Event Code=207 Status=Success Message=Client 05a26a97-272a-4bc9-8f64-269d154b0e39 connected
Time=2018-03-10 01:33:33 Event Code=208 Status=Warning Message=Client ec53d95c-1c88-41ae-8174-92104212de5d disconnected
Time=2018-03-10 01:35:44 Event Code=209 Status=Success Message=Transaction 10d65890-b003-48f8-9cfc-9c74b51189c8 succeeded
Time=2018-03-10 01:38:22 Event Code=302 Status=Error Message=Application could not connect to database
Time=2018-03-10 01:31:34 Event Code=303 Status=Error Message=Application lost connection to database
```

A seguinte consulta analisaria estes dados em propriedades individuais. A linha com o _projeto_ é adicionada apenas para devolver as propriedades calculadas e não _RawData_, que é a propriedade única que detém a totalidade da entrada do registo personalizado.

```Kusto
MyCustomLog_CL
| parse RawData with * "Time=" EventTime " Event Code=" Code " Status=" Status " Message=" Message
| project EventTime, Code, Status, Message
```

Segue-se outro exemplo que abre o nome de utilizador de uma UPN na tabela _AzureActivity._

```Kusto
AzureActivity
| parse  Caller with UPNUserPart "@" * 
| where UPNUserPart != "" //Remove non UPN callers (apps, SPNs, etc)
| distinct UPNUserPart, Caller
```


### <a name="regular-expressions"></a>Expressões regulares
Se os seus dados puderem ser identificados com uma expressão regular, pode utilizar [funções que utilizam expressões regulares](/azure/kusto/query/re2) para extrair valores individuais. O exemplo seguinte utiliza [extrato](/azure/kusto/query/extractfunction) para quebrar o campo _UPN_ a partir dos registos _Do AzureActivity_ e, em seguida, devolver utilizadores distintos.

```Kusto
AzureActivity
| extend UPNUserPart = extract("([a-z.]*)@", 1, Caller) 
| distinct UPNUserPart, Caller
```

Para permitir uma análise eficiente em larga escala, o Azure Monitor utiliza a versão re2 de Expressões Regulares, que é semelhante, mas não idêntica a algumas das outras variantes de expressão regular. Consulte a [sintaxe](https://aka.ms/kql_re2syntax) de expressão re2 para mais detalhes.


## <a name="parse-delimited-data-in-a-query"></a>Dados delimitados parse em uma consulta
Dados delimitados separam campos com um caráter comum, como uma vírema num ficheiro CSV. Utilize a função [de divisão](/azure/kusto/query/splitfunction) para analisar dados delimitados utilizando um delimitador que especifique. Pode usá-lo com [o operador de extensão](/azure/kusto/query/extendoperator) para devolver todos os campos dos dados ou especificar os campos individuais a incluir na saída.

> [!NOTE]
> Uma vez que a divisão devolve um objeto dinâmico, os resultados podem ter de ser explicitamente lançados para tipos de dados, como cordas a utilizar em operadores e filtros.

Considere um registo personalizado com dados no seguinte formato CSV.

```
2018-03-10 01:34:36, 207,Success,Client 05a26a97-272a-4bc9-8f64-269d154b0e39 connected
2018-03-10 01:33:33, 208,Warning,Client ec53d95c-1c88-41ae-8174-92104212de5d disconnected
2018-03-10 01:35:44, 209,Success,Transaction 10d65890-b003-48f8-9cfc-9c74b51189c8 succeeded
2018-03-10 01:38:22, 302,Error,Application could not connect to database
2018-03-10 01:31:34, 303,Error,Application lost connection to database
```

A seguinte consulta analisaria estes dados e resumiria por duas das propriedades calculadas. A primeira linha divide a propriedade _RawData_ numa matriz de cordas. Cada uma das linhas seguintes dá um nome às propriedades individuais e adiciona-as à saída usando funções para convertê-las ao tipo de dados apropriado.

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

## <a name="parse-predefined-structures-in-a-query"></a>Parse estruturas predefinidas em uma consulta
Se os seus dados forrem formatados numa estrutura conhecida, poderá utilizar uma das funções na linguagem de [consulta kusto](/azure/kusto/query/) para analisar estruturas predefinidas:

- [JSON](/azure/kusto/query/parsejsonfunction)
- [XML](/azure/kusto/query/parse-xmlfunction)
- [IPv4](/azure/kusto/query/parse-ipv4function)
- [URL](/azure/kusto/query/parseurlfunction)
- [Consulta de URL](/azure/kusto/query/parseurlqueryfunction)
- [Caminho do ficheiro](/azure/kusto/query/parsepathfunction)
- [Agente de utilizador](/azure/kusto/query/parse-useragentfunction)
- [Cadeia de versão](/azure/kusto/query/parse-versionfunction)

A consulta de exemplo seguinte analisa o campo _Propriedades_ da tabela _AzureActivity,_ que está estruturada em JSON. Guarda os resultados para uma propriedade dinâmica chamada _parsedProp,_ que inclui o valor individual nomeado no JSON. Estes valores são utilizados para filtrar e resumir os resultados da consulta.

```Kusto
AzureActivity
| extend parsedProp = parse_json(Properties) 
| where parsedProp.isComplianceCheck == "True" 
| summarize count() by ResourceGroup, tostring(parsedProp.tags.businessowner)
```

Estas funções de análise podem ser intensivas em processadores, pelo que só devem ser utilizadas quando a sua consulta utiliza várias propriedades a partir dos dados formatados. Caso contrário, o processamento simples de correspondência de padrões será mais rápido.

O exemplo seguinte mostra a avaria do tipo TGT Preauth do controlador de domínio. O tipo existe apenas no campo EventData, que é uma cadeia XML, mas não são necessários outros dados deste campo. Neste caso, a [parse](/azure/kusto/query/parseoperator) é usada para recolher o pedaço de dados necessário.

```Kusto
SecurityEvent
| where EventID == 4768
| parse EventData with * 'PreAuthType">' PreAuthType '</Data>' * 
| summarize count() by PreAuthType
```

## <a name="use-function-to-simulate-a-table"></a>Use a função para simular uma tabela
Você pode ter múltiplas consultas que executam a mesma análise de uma determinada mesa. Neste caso, [crie uma função](functions.md) que retorne os dados analisados em vez de replicar a lógica de análise em cada consulta. Em seguida, pode utilizar o pseudónimo de função no lugar da mesa original noutras consultas.

Considere o exemplo de registo personalizado delimitado pela vírposta acima. Para utilizar os dados analisados em múltiplas consultas, crie uma função utilizando as seguintes consultas e guarde-as com o pseudónimo _MyCustomCSVLog_.

```Kusto
MyCustomCSVLog_CL
| extend CSVFields = split(RawData, ',')
| extend DateTime  = tostring(CSVFields[0])
| extend Code      = toint(CSVFields[1]) 
| extend Status    = tostring(CSVFields[2]) 
| extend Message   = tostring(CSVFields[3]) 
```

Agora pode utilizar o pseudónimo _MyCustomCSVLog_ no lugar do nome real da tabela em consultas como a seguinte.

```Kusto
MyCustomCSVLog
| summarize count() by Status,Code
```


## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre consultas de [registo](log-query-overview.md) para analisar os dados recolhidos a partir de fontes e soluções de dados.
