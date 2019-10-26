---
title: Junções em consultas de log de Azure Monitor | Microsoft Docs
description: Este artigo inclui uma lição sobre como usar junções em consultas Azure Monitor log.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/16/2018
ms.openlocfilehash: 526c359367271c69ccd461e4421c3223b00fbc36
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72900292"
---
# <a name="joins-in-azure-monitor-log-queries"></a>Junções em consultas de log de Azure Monitor

> [!NOTE]
> Você deve concluir a [introdução às consultas Azure Monitor log Analytics](get-started-portal.md) e [Azure monitor log](get-started-queries.md) antes de concluir esta lição.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

As junções permitem que você analise dados de várias tabelas, na mesma consulta. Eles mesclam as linhas de dois conjuntos de dados por valores correspondentes de colunas especificadas.


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

Neste exemplo, o primeiro conjunto de os filtros para todos os eventos de entrada. Isso é Unido a um segundo conjunto de um que filtra todos os eventos de saída. As colunas projetadas são _computador_, _conta_, _TargetLogonId_e _TimeGenerated_. Os conjuntos de valores são correlacionados por uma coluna compartilhada, _TargetLogonId_. A saída é um registro único por correlação, que tem o tempo de entrada e saída.

Se ambos os conjuntos de dados tiverem colunas com os mesmos nomes, as colunas do conjunto de dados do lado direito receberão um número de índice, portanto, neste exemplo, os resultados mostrarão _TargetLogonId_ com valores da tabela do lado esquerdo e _TargetLogonId1_ com valores da tabela do lado direito. Nesse caso, a segunda coluna _TargetLogonId1_ foi removida usando o operador `project-away`.

> [!NOTE]
> Para melhorar o desempenho, mantenha apenas as colunas relevantes dos conjuntos de dados associados, usando o operador de `project`.


Use a sintaxe a seguir para unir dois conjuntos de valores e a chave unida tem um nome diferente entre as duas tabelas:
```
Table1
| join ( Table2 ) 
on $left.key1 == $right.key2
```

## <a name="lookup-tables"></a>Tabelas de pesquisa
Um uso comum de junções é o uso de mapeamento estático de valores usando `datatable` que pode ajudar a transformar os resultados em uma maneira mais apresentável. Por exemplo, para enriquecer os dados de evento de segurança com o nome do evento para cada ID de evento.

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

![Unir com uma DataTable](media/joins/dim-table.png)

## <a name="join-kinds"></a>Tipos de junção
Especifique o tipo de junção com o argumento _Kind_ . Cada tipo executa uma correspondência diferente entre os registros das tabelas determinadas, conforme descrito na tabela a seguir.

| Tipo de junção | Descrição |
|:---|:---|
| innerunique | Esse é o modo de junção padrão. Primeiro, os valores da coluna correspondente na tabela esquerda são encontrados e os valores duplicados são removidos.  Em seguida, o conjunto de valores exclusivos é correspondido na tabela direita. |
| Inner | Somente os registros correspondentes em ambas as tabelas são incluídos nos resultados. |
| leftouter | Todos os registros na tabela esquerda e os registros correspondentes na tabela à direita são incluídos nos resultados. As propriedades de saída sem correspondência contêm nulos.  |
| leftanti | Os registros do lado esquerdo que não têm correspondências da direita são incluídos nos resultados. A tabela de resultados tem apenas colunas da tabela esquerda. |
| leftsemi | Registros do lado esquerdo que têm correspondências da direita são incluídos nos resultados. A tabela de resultados tem apenas colunas da tabela esquerda. |


## <a name="best-practices"></a>Melhores práticas

Considere os seguintes pontos para um desempenho ideal:

- Use um filtro de tempo em cada tabela para reduzir os registros que devem ser avaliados para a junção.
- Use `where` e `project` para reduzir os números de linhas e colunas nas tabelas de entrada antes da junção.
- Se uma tabela for sempre menor do que a outra, use-a como o lado esquerdo da junção.


## <a name="next-steps"></a>Passos seguintes
Consulte outras lições para usar Azure Monitor consultas de log:

- [Operações de cadeia de caracteres](string-operations.md)
- [Funções de agregação](aggregations.md)
- [Agregações avançadas](advanced-aggregations.md)
- [JSON e estruturas de dados](json-data-structures.md)
- [Gravação de consulta avançada](advanced-query-writing.md)
- [Spersão](charts.md)