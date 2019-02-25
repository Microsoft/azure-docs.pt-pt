---
title: É associado a nas consultas de registo do Azure Monitor | Documentos da Microsoft
description: Este artigo inclui uma lição sobre a utilização de junções em consultas de registo do Azure Monitor.
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
ms.openlocfilehash: c79becfda84e1a7d65399c7211bdc3b98a28696f
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/24/2019
ms.locfileid: "56750570"
---
# <a name="joins-in-azure-monitor-log-queries"></a>É associado a nas consultas de registo do Azure Monitor

> [!NOTE]
> Deve efetuar [introdução ao Log Analytics do Azure Monitor](get-started-portal.md) e [consultas de registo do Azure Monitor](get-started-queries.md) antes de concluir esta lição.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

As associações permitem-lhe analisar os dados de várias tabelas, da mesma consulta. Eles intercale as linhas de dois conjuntos de dados ao corresponder valores de colunas especificadas.


```Kusto
SecurityEvent 
| where EventID == 4624     // sign-in events
| project Computer, Account, TargetLogonId, LogonTime=TimeGenerated
| join kind= inner (
    SecurityEvent 
    | where EventID == 4634     // sign-out events
    | project TargetLogonId, LogoffTime=TimeGenerated
) on TargetLogonId
| extend Duration = LogoffTime-LogonTime
| project-away TargetLogonId1 
| top 10 by Duration desc
```

Neste exemplo, o primeiro conjunto de dados filtra para todos os eventos de início de sessão. Isso é associado um segundo conjunto de dados que filtra para todos os eventos de fim de sessão. As colunas previstas são _computador_, _conta_, _TargetLogonId_, e _TimeGenerated_. Os conjuntos de dados estão correlacionados por uma coluna compartilhada, _TargetLogonId_. O resultado é um único registo por correlação, que tem ambos os a hora de início de sessão e fim de sessão.

Se ambos os conjuntos de dados têm colunas com os mesmos nomes, as colunas do conjunto de dados do lado direito receberiam um número de índice, portanto, neste exemplo os resultados seriam mostrados _TargetLogonId_ pelos valores da tabela esquerda e  _TargetLogonId1_ com valores da tabela direita. Neste caso, a segunda _TargetLogonId1_ coluna foi removida com o `project-away` operador.

> [!NOTE]
> Para melhorar o desempenho, manter apenas as colunas relevantes as associado ao-de conjuntos de dados, usando o `project` operador.


Utilize a seguinte sintaxe para associar os dois conjuntos de dados e a chave associado ao tem um nome diferente entre as duas tabelas:
```
Table1
| join ( Table2 ) 
on $left.key1 == $right.key2
```

## <a name="lookup-tables"></a>Tabelas de pesquisa
Um uso comum de junções está a utilizar o mapeamento estático de valores usando `datatable` que podem ajudar a transformar os resultados na forma mais apresentável. Por exemplo, para melhorar a segurança dados de eventos com o nome do evento para cada evento ID.

```Kusto
let DimTable = datatable(EventID:int, eventName:string)
  [
    4625, "Account activity",
    4688, "Process action",
    4634, "Account activity",
    4658, "The handle to an object was closed",
    4656, "A handle to an object was requested",
    4690, "An attempt was made to duplicate a handle to an object",
    4663, "An attempt was made to access an object",
    5061, "Cryptographic operation",
    5058, "Key file operation"
  ];
SecurityEvent
| join kind = inner
 DimTable on EventID
| summarize count() by eventName
```

![Junte-se com um datatable](media/joins/dim-table.png)

## <a name="join-kinds"></a>Junte-se de tipos
Especifique o tipo de associação com o _tipo_ argumento. Cada tipo executa uma correspondência diferente entre os registos das tabelas de determinado conforme descrito na tabela seguinte.

| Tipo de associação | Descrição |
|:---|:---|
| innerunique | Este é o modo de associação padrão. Em primeiro lugar os valores da coluna correspondente na tabela da esquerda são localizados e valores duplicados são removidos.  Em seguida, o conjunto de valores exclusivos é comparado com a tabela direita. |
| interna | Apenas registos correspondentes em ambas as tabelas são incluídos nos resultados. |
| leftouter | Todos os registos na tabela esquerda e os registos correspondentes na tabela da direita são incluídos nos resultados. Propriedades de saída sem correspondência contenham valores nulos.  |
| leftanti | Registos do lado esquerdo, que não têm as correspondências da direita são incluídos nos resultados. A tabela de resultados tem apenas as colunas da tabela da esquerda. |
| leftsemi | Registos do lado esquerdo que tenham as correspondências da direita são incluídos nos resultados. A tabela de resultados tem apenas as colunas da tabela da esquerda. |


## <a name="best-practices"></a>Melhores práticas

Considere os seguintes pontos para um desempenho ideal:

- Utilize um filtro de horas em cada tabela para reduzir os registos que têm de ser avaliados para a junção.
- Uso `where` e `project` para reduzir o número de linhas e colunas nas tabelas de entrada antes da junção.
* Se uma tabela é sempre menor do que o outro, utilize-o como o lado esquerdo da associação.


## <a name="next-steps"></a>Passos Seguintes
Consulte outras lições para a utilização de consultas de registo do Azure Monitor:

- [Operações de cadeia de caracteres](string-operations.md)
- [Funções de agregação](aggregations.md)
- [Agregações avançadas](advanced-aggregations.md)
- [Estruturas de dados e JSON](json-data-structures.md)
- [Escrita de consultas avançado](advanced-query-writing.md)
- [Gráficos](charts.md)