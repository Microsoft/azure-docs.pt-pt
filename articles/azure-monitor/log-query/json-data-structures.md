---
title: Trabalhar com cadeias de caracteres em consultas de registo do Azure Monitor | Documentos da Microsoft
description: Este artigo fornece um tutorial para utilizar o Monitor do Azure Log Analytics no portal do Azure para consultar e analisar dados de registo no Azure Monitor.
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
ms.date: 08/16/2018
ms.author: bwren
ms.openlocfilehash: 718b12c8a66d66a75796f88ef31b5f0f62abbbc4
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/24/2019
ms.locfileid: "56750961"
---
# <a name="working-with-json-and-data-structures-in-azure-monitor-log-queries"></a>Trabalhar com dados e de JSON estruturas em consultas de registo do Azure Monitor

> [!NOTE]
> Deve efetuar [introdução ao Log Analytics do Azure Monitor](get-started-portal.md) e [introdução às consultas de registo do Azure Monitor](get-started-queries.md) antes de concluir esta lição.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

Os objetos aninhados são objetos que contêm outros objetos numa matriz ou um mapa dos pares chave-valor. Esses objetos são representados como cadeias de caracteres do JSON. Este artigo descreve como o JSON é usado para recuperar dados e analisar os objetos aninhados.

## <a name="working-with-json-strings"></a>Trabalhar com cadeias de caracteres JSON
Utilize `extractjson` para aceder a um elemento JSON específico num caminho conhecido. Essa função requer uma expressão de caminho que utiliza as seguintes convenções.

- _$_ para fazer referência para a pasta raiz
- Utilize a notação de colchete ou ponto para fazer referência aos índices e elementos conforme ilustrado nos exemplos a seguir.


Utilize parênteses Retos para índices e pontos para separar elementos:

```Kusto
let hosts_report='{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}';
print hosts_report
| extend status = extractjson("$.hosts[0].status", hosts_report)
```

Este é o mesmo resultado usando apenas a notação de parênteses Retos:

```Kusto
let hosts_report='{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}';
print hosts_report 
| extend status = extractjson("$['hosts'][0]['status']", hosts_report)
```

Se houver apenas um elemento, pode usar a notação de ponto:

```Kusto
let hosts_report='{"location":"North_DC", "status":"running", "rate":5}';
print hosts_report 
| extend status = hosts_report.status
```


## <a name="working-with-objects"></a>Trabalhando com objetos

### <a name="parsejson"></a>parsejson
Para acessar vários elementos na sua estrutura de json, é mais fácil para acessá-lo como um objeto dinâmico. Utilize `parsejson` converter os dados de texto para um objeto dinâmico. Após a conversão de tipo dinâmico, funções adicionais podem ser utilizadas para analisar os dados.

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| extend status0=hosts_object.hosts[0].status, rate1=hosts_object.hosts[1].rate
```



### <a name="arraylength"></a>arraylength
Utilize `arraylength` para contar o número de elementos numa matriz:

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| extend hosts_num=arraylength(hosts_object.hosts)
```

### <a name="mvexpand"></a>mvexpand
Utilize `mvexpand` dividir as propriedades de um objeto em linhas separadas.

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| mvexpand hosts_object.hosts[0]
```

![mvexpand](media/json-data-structures/mvexpand.png)

### <a name="buildschema"></a>buildschema
Utilize `buildschema` para obter o esquema que admits todos os valores de um objeto:

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| summarize buildschema(hosts_object)
```

O resultado é um esquema no formato JSON:
```json
{
    "hosts":
    {
        "indexer":
        {
            "location": "string",
            "rate": "int",
            "status": "string"
        }
    }
}
```
Esta saída descreve os nomes dos campos do objeto e os tipos de dados correspondente. 

Os objetos aninhados podem ter esquemas diferentes, tal como no exemplo a seguir:

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"status":"stopped", "rate":"3", "range":100}]}');
print hosts_object 
| summarize buildschema(hosts_object)
```


![Criar esquema](media/json-data-structures/buildschema.png)

## <a name="next-steps"></a>Passos Seguintes
Consulte outras lições para utilizar consultas de registo no Azure Monitor:

- [Operações de cadeia de caracteres](string-operations.md)
- [Operações de data e hora](datetime-operations.md)
- [Funções de agregação](aggregations.md)
- [Agregações avançadas](advanced-aggregations.md)
- [Escrita de consultas avançado](advanced-query-writing.md)
- [Associações](joins.md)
- [Gráficos](charts.md)