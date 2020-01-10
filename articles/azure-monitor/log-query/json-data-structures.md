---
title: Trabalhando com cadeias de caracteres em consultas de log de Azure Monitor | Microsoft Docs
description: Este artigo fornece um tutorial para usar Azure Monitor Log Analytics no portal do Azure para consultar e analisar dados de log no Azure Monitor.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/16/2018
ms.openlocfilehash: 940c82e9ef7016639a3ab334040c408f83996e2b
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75365313"
---
# <a name="working-with-json-and-data-structures-in-azure-monitor-log-queries"></a>Trabalhando com estruturas de dados e JSON em Azure Monitor consultas de log

> [!NOTE]
> Você deve concluir a introdução [ao Azure Monitor log Analytics](get-started-portal.md) e [a introdução às consultas de Azure monitor log](get-started-queries.md) antes de concluir esta lição.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

Objetos aninhados são objetos que contêm outros objetos em uma matriz ou um mapa de pares chave-valor. Esses objetos são representados como cadeias de caracteres JSON. Este artigo descreve como o JSON é usado para recuperar dados e analisar objetos aninhados.

## <a name="working-with-json-strings"></a>Trabalhando com cadeias de caracteres JSON
Use `extractjson` para acessar um elemento JSON específico em um caminho conhecido. Essa função requer uma expressão de caminho que usa as convenções a seguir.

- _$_ fazer referência à pasta raiz
- Use a notação de colchete ou ponto para fazer referência a índices e elementos, conforme ilustrado nos exemplos a seguir.


Use colchetes para índices e pontos para separar elementos:

```Kusto
let hosts_report='{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}';
print hosts_report
| extend status = extractjson("$.hosts[0].status", hosts_report)
```

Esse é o mesmo resultado usando apenas a notação de colchetes:

```Kusto
let hosts_report='{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}';
print hosts_report 
| extend status = extractjson("$['hosts'][0]['status']", hosts_report)
```

Se houver apenas um elemento, você poderá usar apenas a notação de ponto:

```Kusto
let hosts_report='{"location":"North_DC", "status":"running", "rate":5}';
print hosts_report 
| extend status = hosts_report.status
```


## <a name="working-with-objects"></a>Trabalhar com objetos

### <a name="parsejson"></a>parsejson
Para acessar vários elementos em sua estrutura JSON, é mais fácil acessá-lo como um objeto dinâmico. Use `parsejson` para converter dados de texto em um objeto dinâmico. Depois de convertido em um tipo dinâmico, funções adicionais podem ser usadas para analisar os dados.

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| extend status0=hosts_object.hosts[0].status, rate1=hosts_object.hosts[1].rate
```



### <a name="arraylength"></a>arraylength
Use `arraylength` para contar o número de elementos em uma matriz:

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| extend hosts_num=arraylength(hosts_object.hosts)
```

### <a name="mvexpand"></a>mvexpand
Use `mvexpand` para quebrar as propriedades de um objeto em linhas separadas.

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| mvexpand hosts_object.hosts[0]
```

![mvexpand](media/json-data-structures/mvexpand.png)

### <a name="buildschema"></a>buildschema
Use `buildschema` para obter o esquema que admite todos os valores de um objeto:

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| summarize buildschema(hosts_object)
```

A saída é um esquema no formato JSON:
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
Essa saída descreve os nomes dos campos de objeto e seus tipos de dados correspondentes. 

Objetos aninhados podem ter esquemas diferentes, como no exemplo a seguir:

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"status":"stopped", "rate":"3", "range":100}]}');
print hosts_object 
| summarize buildschema(hosts_object)
```


![Esquema de compilação](media/json-data-structures/buildschema.png)

## <a name="next-steps"></a>Passos seguintes
Consulte outras lições para usar consultas de log no Azure Monitor:

- [Operações de cadeia de caracteres](string-operations.md)
- [Operações de data e hora](datetime-operations.md)
- [Funções de agregação](aggregations.md)
- [Agregações avançadas](advanced-aggregations.md)
- [Gravação de consulta avançada](advanced-query-writing.md)
- [Junções](joins.md)
- [Spersão](charts.md)
