---
title: Funções em Azure Monitor consultas de registos Microsoft Docs
description: Este artigo descreve como usar funções para chamar uma consulta de outra consulta de log no Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/18/2018
ms.openlocfilehash: 5fb9e48a6d6a0b95b61478a7877e9b46dd8963e9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "83649391"
---
# <a name="using-functions-in-azure-monitor-log-queries"></a>Utilização de funções em consultas de registo do Monitor de Azure

Para utilizar uma consulta de log com outra consulta, pode guardá-la em função. Isto permite-lhe simplificar consultas complexas, dividindo-as em peças e permitindo-lhe reutilizar o código comum com múltiplas consultas.

## <a name="create-a-function"></a>Criar uma função

Crie uma função com o Log Analytics no portal Azure clicando em **Guardar** e, em seguida, fornecendo as informações na tabela seguinte.

| Definição | Descrição |
|:---|:---|
| Name           | Nome do visor para a consulta no **explorador de consultas**. |
| Guardar como        | Função |
| Alias de função | Nome curto para utilizar a função em outras consultas. Pode não conter espaços e deve ser único. |
| Categoria       | Uma categoria para organizar consultas e funções guardadas no **explorador de consultas.** |




## <a name="use-a-function"></a>Use uma função
Utilize uma função incluindo o seu pseudónimo noutra consulta. Pode ser usado como qualquer outra mesa.

## <a name="example"></a>Exemplo
A seguinte consulta de amostra devolve todas as atualizações de segurança em falta reportadas no último dia. Guarde esta consulta em função do pseudónimo _security_updates_last_day_. 

```Kusto
Update
| where TimeGenerated > ago(1d) 
| where Classification == "Security Updates" 
| where UpdateState == "Needed"
```

Crie outra consulta e refira a função _security_updates_last_day_ para procurar atualizações de segurança necessárias relacionadas com o SQL.

```Kusto
security_updates_last_day | where Title contains "SQL"
```

## <a name="next-steps"></a>Próximos passos
Consulte outras lições para escrever consultas de registo do Azure Monitor:

- [Operações de cadeia](string-operations.md)
- [Operações de data e hora](datetime-operations.md)
- [Funções de agregação](aggregations.md)
- [Agregações avançadas](advanced-aggregations.md)
- [Estruturas de dados e JSON](json-data-structures.md)
- [Associações](joins.md)
- [Gráficos](charts.md)
