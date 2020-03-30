---
title: Junta-se a consultas de registo do Monitor Azure [ Microsoft Docs
description: Este artigo inclui uma lição sobre a utilização de juntas em consultas de registo do Monitor Azure.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/16/2018
ms.openlocfilehash: 2dace6968fbbe69f806c27fb7a46e60c63f78b4f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77670207"
---
# <a name="joins-in-azure-monitor-log-queries"></a>Junta-se a consultas de registo do Monitor Azure

> [!NOTE]
> Deve completar O Get started com o [Azure Monitor Log Analytics](get-started-portal.md) e o [Azure Monitor consultas](get-started-queries.md) de registo antes de concluir esta aula.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

Adesão permite-lhe analisar dados de várias tabelas, na mesma consulta. Fundem as linhas de dois conjuntos de dados combinando valores de colunas especificadas.


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

Neste exemplo, os primeiros filtros de conjunto de dados para todos os eventos de início de sessão. Isto é acompanhado por um segundo conjunto de dados que filtra todos os eventos de inscrição. As colunas projetadas são _Computador,_ _Conta,_ _TargetLogonId_e _TimeGenerated_. Os conjuntos de dados são correlacionados por uma coluna partilhada, _TargetLogonId_. A saída é um único recorde por correlação, que tem tanto o tempo de inscrição como o tempo de inscrição.

Se ambos os conjuntos de dados tiverem colunas com os mesmos nomes, as colunas do conjunto de dados do lado direito receberiam um número de índice, pelo que, neste exemplo, os resultados mostrariam _targetLogonId_ com valores da tabela do lado esquerdo e _TargetLogonId1_ com valores da tabela do lado direito. Neste caso, a segunda coluna _TargetLogonId1_ `project-away` foi removida utilizando o operador.

> [!NOTE]
> Para melhorar o desempenho, mantenha apenas as colunas relevantes `project` dos conjuntos de dados unidos, utilizando o operador.


Utilize a seguinte sintaxe para se juntar a dois conjuntos de dados e a chave unida tem um nome diferente entre as duas tabelas:
```
Table1
| join ( Table2 ) 
on $left.key1 == $right.key2
```

## <a name="lookup-tables"></a>Mesas de lookup
Um uso comum de juntas é `datatable` usar o mapeamento estático de valores usando que pode ajudar a transformar os resultados em forma mais apresentável. Por exemplo, para enriquecer os dados do evento de segurança com o nome do evento para cada ID do evento.

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

![Junte-se a uma datatable](media/joins/dim-table.png)

## <a name="join-kinds"></a>Junte-se a tipos
Especifique o tipo de juntar-se ao argumento _do tipo._ Cada tipo executa uma correspondência diferente entre os registos das tabelas dadas, conforme descrito na tabela seguinte.

| Tipo de associação | Descrição |
|:---|:---|
| innerunique | Este é o modo de união padrão. Primeiro são encontrados os valores da coluna em correspondência na mesa esquerda e os valores duplicados são removidos.  Em seguida, o conjunto de valores únicos é igualado contra a mesa certa. |
| interior | Apenas os registos correspondentes em ambas as tabelas estão incluídos nos resultados. |
| leftouter | Todos os registos na mesa esquerda e registos correspondentes na mesa direita estão incluídos nos resultados. Propriedades de saída incomparáveis contêm nulos.  |
| esquerdaanti | Os registos do lado esquerdo que não têm correspondências da direita estão incluídos nos resultados. A tabela de resultados tem apenas colunas da mesa esquerda. |
| leftsemi | Os registos do lado esquerdo que têm correspondências da direita estão incluídos nos resultados. A tabela de resultados tem apenas colunas da mesa esquerda. |


## <a name="best-practices"></a>Melhores práticas

Considere os seguintes pontos para um desempenho ótimo:

- Utilize um filtro de tempo em cada mesa para reduzir os registos que devem ser avaliados para a adesão.
- Utilize `where` `project` e reduza o número de linhas e colunas nas tabelas de entrada antes da adesão.
- Se uma mesa for sempre mais pequena que a outra, use-a como o lado esquerdo da união.


## <a name="next-steps"></a>Passos seguintes
Consulte outras lições para utilizar consultas de registo do Monitor Azure:

- [Operações de cadeia](string-operations.md)
- [Funções de agregação](aggregations.md)
- [Agregações avançadas](advanced-aggregations.md)
- [Estruturas de dados e JSON](json-data-structures.md)
- [Escrita de consulta avançada](advanced-query-writing.md)
- [Gráficos](charts.md)
