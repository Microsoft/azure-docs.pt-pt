---
title: Trabalhar com cordas em consultas de registo do Monitor Azure [ Microsoft Docs
description: Este artigo fornece um tutorial para a utilização do Azure Monitor Log Analytics no portal Azure para consultar e analisar dados de registo no Monitor Azure.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/16/2018
ms.openlocfilehash: f792820b7b0dff20e647031410ba87ac26c2495a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80672962"
---
# <a name="working-with-json-and-data-structures-in-azure-monitor-log-queries"></a>Trabalhar com a JSON e as estruturas de dados em consultas de registo do Monitor Azure

> [!NOTE]
> Deve completar [O Get started com o Azure Monitor Log Analytics](get-started-portal.md) e começar com consultas de registo do [Azure Monitor](get-started-queries.md) antes de concluir esta aula.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

Objetos aninhados são objetos que contêm outros objetos numa matriz ou num mapa de pares de valor-chave. Estes objetos são representados como cordas JSON. Este artigo descreve como a JSON é usada para recuperar dados e analisar objetos aninhados.

## <a name="working-with-json-strings"></a>Trabalhar com cordas JSON
Utilize `extractjson` para aceder a um elemento JSON específico num caminho conhecido. Esta função requer uma expressão de caminho que utiliza as seguintes convenções.

- _$_ para se referir à pasta raiz
- Utilize o suporte ou notação do ponto para se referir a índices e elementos ilustrados nos seguintes exemplos.


Utilize os suportes para índices e pontos para separar elementos:

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

Se houver apenas um elemento, só pode utilizar a notação do ponto:

```Kusto
let hosts_report=dynamic({"location":"North_DC", "status":"running", "rate":5});
print hosts_report 
| extend status = hosts_report.status
```


## <a name="working-with-objects"></a>Trabalhar com objetos

### <a name="parsejson"></a>parsejson
Para aceder a vários elementos da sua estrutura json, é mais fácil acessá-lo como um objeto dinâmico. Utilize `parsejson` para lançar dados de texto num objeto dinâmico. Uma vez convertidos para um tipo dinâmico, funções adicionais podem ser usadas para analisar os dados.

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| extend status0=hosts_object.hosts[0].status, rate1=hosts_object.hosts[1].rate
```



### <a name="arraylength"></a>arraylength
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

![mvexpand](media/json-data-structures/mvexpand.png)

### <a name="buildschema"></a>buildschema
Use `buildschema` para obter o esquema que admite todos os valores de um objeto:

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| summarize buildschema(hosts_object)
```

A saída é um esquema em formato JSON:
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

Os objetos aninhados podem ter diferentes esquemmas, tais como no seguinte exemplo:

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"status":"stopped", "rate":"3", "range":100}]}');
print hosts_object 
| summarize buildschema(hosts_object)
```


![Construir esquemas](media/json-data-structures/buildschema.png)

## <a name="next-steps"></a>Passos seguintes
Consulte outras lições para utilizar consultas de registo no Monitor Azure:

- [Operações de cadeia](string-operations.md)
- [Operações de data e hora](datetime-operations.md)
- [Funções de agregação](aggregations.md)
- [Agregações avançadas](advanced-aggregations.md)
- [Escrita de consulta avançada](advanced-query-writing.md)
- [Associações](joins.md)
- [Gráficos](charts.md)
