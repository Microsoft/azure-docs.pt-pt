---
title: Analisar dados de texto em logs de Azure Monitor | Microsoft Docs
description: Descreve opções diferentes para analisar dados de log em registros de Azure Monitor quando os dados são ingeridos e quando eles são recuperados em uma consulta, comparando as vantagens relativas para cada um.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 12/04/2018
ms.openlocfilehash: 5a3b6852563955bfac940073bdda7d0afa02e77f
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72900252"
---
# <a name="parse-text-data-in-azure-monitor-logs"></a>Analisar dados de texto em logs de Azure Monitor
Alguns dados de log coletados pelo Azure Monitor incluirão várias partes de informações em uma única propriedade. A análise desses dados em várias propriedades facilita o uso em consultas. Um exemplo comum é um [log personalizado](../../log-analytics/log-analytics-data-sources-custom-logs.md) que coleta uma entrada de log inteira com vários valores em uma única propriedade. Criando propriedades separadas para os valores diferentes, você pode pesquisar e agregar em cada uma.

Este artigo descreve diferentes opções para analisar dados de log em Azure Monitor quando os dados são ingeridos e quando eles são recuperados em uma consulta, comparando as vantagens relativas para cada um.


## <a name="parsing-methods"></a>Métodos de análise
Você pode analisar dados no momento da ingestão quando os dados são coletados ou no momento da consulta ao analisar os dados com uma consulta. Cada estratégia tem vantagens exclusivas, conforme descrito abaixo.

### <a name="parse-data-at-collection-time"></a>Analisar dados no momento da coleta
Ao analisar dados no momento da coleta, você configura [campos personalizados](../../log-analytics/log-analytics-custom-fields.md) que criam novas propriedades na tabela. As consultas não precisam incluir nenhuma lógica de análise e simplesmente usar essas propriedades como qualquer outro campo na tabela.

As vantagens para esse método incluem o seguinte:

- É mais fácil consultar os dados coletados, pois você não precisa incluir comandos de análise na consulta.
- Melhor desempenho de consulta, pois a consulta não precisa executar a análise.
 
As desvantagens desse método incluem o seguinte:

- Deve ser definido com antecedência. Não é possível incluir dados que já foram coletados.
- Se você alterar a lógica de análise, ela será aplicada somente a novos dados.
- Menos opções de análise do que as disponíveis em consultas.
- Aumenta o tempo de latência para coletar dados.
- Os erros podem ser difíceis de lidar.


### <a name="parse-data-at-query-time"></a>Analisar dados no momento da consulta
Ao analisar dados no momento da consulta, você inclui a lógica em sua consulta para analisar dados em vários campos. A própria tabela propriamente dita não é modificada.

As vantagens para esse método incluem o seguinte:

- Aplica-se a quaisquer dados, incluindo dados que já foram coletados.
- As alterações na lógica podem ser aplicadas imediatamente a todos os dados.
- Opções de análise flexíveis, incluindo lógica predefinida para estruturas de dados específicas.
 
As desvantagens desse método incluem o seguinte:

- Requer consultas mais complexas. Isso pode ser reduzido com o uso [de funções para simular uma tabela](#use-function-to-simulate-a-table).
- É necessário replicar a lógica de análise em várias consultas. Pode compartilhar alguma lógica por meio de funções.
- Pode criar sobrecarga ao executar lógica complexa em conjuntos de registros muito grandes (bilhões de registros).

## <a name="parse-data-as-its-collected"></a>Analisar dados conforme eles são coletados
Consulte [criar campos personalizados no Azure monitor](../platform/custom-fields.md) para obter detalhes sobre a análise de dados conforme eles são coletados. Isso cria propriedades personalizadas na tabela que podem ser usadas por consultas, assim como qualquer outra propriedade.

## <a name="parse-data-in-query-using-patterns"></a>Analisar dados na consulta usando padrões
Quando os dados que você deseja analisar podem ser identificados por um padrão repetido entre os registros, você pode usar operadores diferentes na [linguagem de consulta Kusto](/azure/kusto/query/) para extrair a parte específica dos dados em uma ou mais propriedades novas.

### <a name="simple-text-patterns"></a>Padrões de texto simples

Use o operador [Parse](/azure/kusto/query/parseoperator) em sua consulta para criar uma ou mais propriedades personalizadas que podem ser extraídas de uma expressão de cadeia de caracteres. Você especifica o padrão a ser identificado e os nomes das propriedades a serem criadas. Isso é particularmente útil para dados com cadeias de caracteres de valor-chave com um formulário semelhante a _key = value_.

Considere um log personalizado com dados no formato a seguir.

```
Time=2018-03-10 01:34:36 Event Code=207 Status=Success Message=Client 05a26a97-272a-4bc9-8f64-269d154b0e39 connected
Time=2018-03-10 01:33:33 Event Code=208 Status=Warning Message=Client ec53d95c-1c88-41ae-8174-92104212de5d disconnected
Time=2018-03-10 01:35:44 Event Code=209 Status=Success Message=Transaction 10d65890-b003-48f8-9cfc-9c74b51189c8 succeeded
Time=2018-03-10 01:38:22 Event Code=302 Status=Error Message=Application could not connect to database
Time=2018-03-10 01:31:34 Event Code=303 Status=Error Message=Application lost connection to database
```

A consulta a seguir analisaria esses dados em propriedades individuais. A linha com _projeto_ é adicionada para retornar apenas as propriedades calculadas e não _RAWDATA_, que é a única propriedade que contém a entrada inteira do log personalizado.

```Kusto
MyCustomLog_CL
| parse RawData with * "Time=" EventTime " Event Code=" Code " Status=" Status " Message=" Message
| project EventTime, Code, Status, Message
```

Veja a seguir outro exemplo que divide o nome de usuário de um UPN na tabela _AzureActivity_ .

```Kusto
AzureActivity
| parse  Caller with UPNUserPart "@" * 
| where UPNUserPart != "" //Remove non UPN callers (apps, SPNs, etc)
| distinct UPNUserPart, Caller
```


### <a name="regular-expressions"></a>Expressões regulares
Se os dados puderem ser identificados com uma expressão regular, você poderá usar [funções que usam expressões regulares](/azure/kusto/query/re2) para extrair valores individuais. O exemplo a seguir usa [Extract](/azure/kusto/query/extractfunction) para dividir o campo _UPN_ de registros _AzureActivity_ e, em seguida, retornar usuários distintos.

```Kusto
AzureActivity
| extend UPNUserPart = extract("([a-z.]*)@", 1, Caller) 
| distinct UPNUserPart, Caller
```

Para habilitar a análise eficiente em grande escala, o Azure Monitor usa a versão RE2 de expressões regulares, que é semelhante, mas não idêntica a algumas das outras variantes de expressão regular. Consulte a [sintaxe da expressão RE2](https://aka.ms/kql_re2syntax) para obter detalhes.


## <a name="parse-delimited-data-in-a-query"></a>Analisar dados delimitados em uma consulta
Os dados delimitados separam os campos com um caractere comum, como uma vírgula em um arquivo CSV. Use a função [Split](/azure/kusto/query/splitfunction) para analisar dados delimitados usando um delimitador que você especificar. Você pode usar isso com o operador [Extend](/azure/kusto/query/extendoperator) para retornar todos os campos nos dados ou para especificar campos individuais a serem incluídos na saída.

> [!NOTE]
> Como Split retorna um objeto dinâmico, os resultados podem precisar ser convertidos explicitamente em tipos de dados como a cadeia de caracteres a ser usada em operadores e filtros.

Considere um log personalizado com dados no formato CSV a seguir.

```
2018-03-10 01:34:36, 207,Success,Client 05a26a97-272a-4bc9-8f64-269d154b0e39 connected
2018-03-10 01:33:33, 208,Warning,Client ec53d95c-1c88-41ae-8174-92104212de5d disconnected
2018-03-10 01:35:44, 209,Success,Transaction 10d65890-b003-48f8-9cfc-9c74b51189c8 succeeded
2018-03-10 01:38:22, 302,Error,Application could not connect to database
2018-03-10 01:31:34, 303,Error,Application lost connection to database
```

A consulta a seguir analisaria esses dados e resumiria duas das propriedades calculadas. A primeira linha divide a propriedade _RAWDATA_ em uma matriz de cadeia de caracteres. Cada uma das linhas seguintes fornece um nome para as propriedades individuais e as adiciona à saída usando funções para convertê-las no tipo de dados apropriado.

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

## <a name="parse-predefined-structures-in-a-query"></a>Analisar estruturas predefinidas em uma consulta
Se os dados estiverem formatados em uma estrutura conhecida, você poderá usar uma das funções na [linguagem de consulta Kusto](/azure/kusto/query/) para analisar estruturas predefinidas:

- [JSON](/azure/kusto/query/parsejsonfunction)
- [XML](/azure/kusto/query/parse-xmlfunction)
- [IPv6](/azure/kusto/query/parse-ipv4function)
- [URL](/azure/kusto/query/parseurlfunction)
- [Consulta de URL](/azure/kusto/query/parseurlqueryfunction)
- [Caminho do ficheiro](/azure/kusto/query/parsepathfunction)
- [Agente do usuário](/azure/kusto/query/parse-useragentfunction)
- [Cadeia de caracteres de versão](/azure/kusto/query/parse-versionfunction)

A consulta de exemplo a seguir analisa o campo de _Propriedades_ da tabela _AzureActivity_ , que é estruturada em JSON. Ele salva os resultados em uma propriedade dinâmica chamada _parsedProp_, que inclui o valor nomeado individual no JSON. Esses valores são usados para filtrar e resumir os resultados da consulta.

```Kusto
AzureActivity
| extend parsedProp = parse_json(Properties) 
| where parsedProp.isComplianceCheck == "True" 
| summarize count() by ResourceGroup, tostring(parsedProp.tags.businessowner)
```

Essas funções de análise podem ser intensivas no processador, portanto, elas devem ser usadas somente quando a consulta usa várias propriedades dos dados formatados. Caso contrário, o processamento de correspondência de padrão simples será mais rápido.

O exemplo a seguir mostra a divisão do tipo de PreAuth do TGT do controlador de domínio. O tipo existe somente no campo EventData, que é uma cadeia de caracteres XML, mas nenhum outro dado desse campo é necessário. Nesse caso, [Parse](/azure/kusto/query/parseoperator) é usado para escolher a parte necessária dos dados.

```Kusto
SecurityEvent
| where EventID == 4768
| parse EventData with * 'PreAuthType">' PreAuthType '</Data>' * 
| summarize count() by PreAuthType
```

## <a name="use-function-to-simulate-a-table"></a>Usar a função para simular uma tabela
Você pode ter várias consultas que executam a mesma análise de uma tabela específica. Nesse caso, [crie uma função](functions.md) que retorne os dados analisados em vez de replicar a lógica de análise em cada consulta. Em seguida, você pode usar o alias da função no lugar da tabela original em outras consultas.

Considere o exemplo de log personalizado delimitado por vírgula acima. Para usar os dados analisados em várias consultas, crie uma função usando a consulta a seguir e salve-a com o alias _MyCustomCSVLog_.

```Kusto
MyCustomCSVLog_CL
| extend CSVFields = split(RawData, ',')
| extend DateTime  = tostring(CSVFields[0])
| extend Code      = toint(CSVFields[1]) 
| extend Status    = tostring(CSVFields[2]) 
| extend Message   = tostring(CSVFields[3]) 
```

Agora você pode usar o alias _MyCustomCSVLog_ no lugar do nome da tabela real em consultas como a seguir.

```Kusto
MyCustomCSVLog
| summarize count() by Status,Code
```


## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre [consultas de log](log-query-overview.md) para analisar os dados coletados de fontes de dados e soluções.