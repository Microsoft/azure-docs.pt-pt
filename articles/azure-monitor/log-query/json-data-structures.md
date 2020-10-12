---
title: Trabalhando com cordas em consultas de log do Azure Monitor Microsoft Docs
description: Este artigo fornece um tutorial para a utilização do Azure Monitor Log Analytics no portal Azure para consultar e analisar dados de registo no Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/16/2018
ms.openlocfilehash: 5021b815da1419052ceb4d6114fbb2bc676fe8d6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90088404"
---
# <a name="working-with-json-and-data-structures-in-azure-monitor-log-queries"></a>Trabalhar com json e estruturas de dados em consultas de log do Azure Monitor

> [!NOTE]
> Deve completar [Começar com a Azure Monitor Log Analytics](get-started-portal.md) e começar com as consultas de registo do [Azure Monitor](get-started-queries.md) antes de concluir esta lição.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

Objetos aninhados são objetos que contêm outros objetos em uma matriz ou um mapa de pares de valor-chave. Estes objetos são representados como cordas JSON. Este artigo descreve como o JSON é usado para recuperar dados e analisar objetos aninhados.

## <a name="working-with-json-strings"></a>Trabalhar com cordas JSON
Utilize `extractjson` para aceder a um elemento JSON específico num caminho conhecido. Esta função requer uma expressão de caminho que utilize as seguintes convenções.

- _$_ para se referir à pasta raiz
- Utilize o suporte ou a notação de pontos para se referir a índices e elementos como ilustrado nos seguintes exemplos.


Utilize suportes para índices e pontos para separar elementos:

```Kusto
let hosts_report='{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}';
print hosts_report
| extend status = extractjson("$.hosts[0].status", hosts_report)
```

Este é o mesmo resultado utilizando apenas a notação dos suportes:

```Kusto
let hosts_report='{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}';
print hosts_report 
| extend status = extractjson("$['hosts'][0]['status']", hosts_report)
```

Se houver apenas um elemento, pode utilizar apenas a notação de pontos:

```Kusto
let hosts_report=dynamic({"location":"North_DC", "status":"running", "rate":5});
print hosts_report 
| extend status = hosts_report.status
```


## <a name="working-with-objects"></a>Trabalhar com objetos

### <a name="parsejson"></a>pálpison
Para aceder a vários elementos na sua estrutura json, é mais fácil acessá-lo como um objeto dinâmico. Utilize `parsejson` para lançar dados de texto a um objeto dinâmico. Uma vez convertidos para um tipo dinâmico, podem ser utilizadas funções adicionais para analisar os dados.

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| extend status0=hosts_object.hosts[0].status, rate1=hosts_object.hosts[1].rate
```



### <a name="arraylength"></a>comprimento de arraylength
Utilizar `arraylength` para contar o número de elementos numa matriz:

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| extend hosts_num=arraylength(hosts_object.hosts)
```

### <a name="mvexpand"></a>mvexpand
Utilize `mvexpand` para quebrar as propriedades de um objeto em linhas separadas.

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| mvexpand hosts_object.hosts[0]
```

![A screenshot mostra hosts_0 com valores para localização, estado e taxa.](media/json-data-structures/mvexpand.png)

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
Esta saída descreve os nomes dos campos de objetos e os seus tipos de dados correspondentes. 

Objetos aninhados podem ter esquemas diferentes, tais como no seguinte exemplo:

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"status":"stopped", "rate":"3", "range":100}]}');
print hosts_object 
| summarize buildschema(hosts_object)
```


![Construir esquema](media/json-data-structures/buildschema.png)

## <a name="next-steps"></a>Passos seguintes
Consulte outras lições para a utilização de consultas de registo no Azure Monitor:

- [Operações de cadeia](string-operations.md)
- [Operações de data e hora](datetime-operations.md)
- [Funções de agregação](aggregations.md)
- [Agregações avançadas](advanced-aggregations.md)
- [Escrita de consulta avançada](advanced-query-writing.md)
- [Associações](joins.md)
- [Gráficos](charts.md)
