---
title: Funções em consultas de registo do Monitor Azure / Microsoft Docs
description: Este artigo descreve como usar funções para chamar uma consulta de outra consulta de registo no Monitor Azure.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/15/2018
ms.openlocfilehash: 7d94e53abbe8f4d2953729aa2363c3906ce94f74
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77670224"
---
# <a name="using-functions-in-azure-monitor-log-queries"></a>Utilização de funções em consultas de registo do Monitor Azure

Para utilizar uma consulta de log com outra consulta, pode guardá-la em função. Isto permite-lhe simplificar consultas complexas, dividindo-as em partes e permite-lhe reutilizar o código comum com múltiplas consultas.

## <a name="create-a-function"></a>Criar uma função

Crie uma função com log analytics no portal Azure clicando em **Guardar** e, em seguida, fornecendo a informação na tabela seguinte.

| Definição | Descrição |
|:---|:---|
| Nome           | Mostrar o nome da consulta no explorador de **consulta**. |
| Guardar como        | Função |
| Pseudónimos de Função | Nome curto para utilizar a função em outras consultas. Pode não conter espaços e deve ser único. |
| Categoria       | Uma categoria para organizar consultas e funções guardadas no **explorador de Consulta.** |

> [!NOTE]
> Uma função no Monitor Azure não pode conter outra função.




## <a name="use-a-function"></a>Use uma função
Use uma função incluindo o seu pseudónimo em outra consulta. Pode ser usado como qualquer outra mesa.

## <a name="example"></a>Exemplo
A seguinte consulta de amostra retorna todas as atualizações de segurança em falta reportadas no último dia. Guarde esta consulta como uma função com o pseudónimo _security_updates_last_day_. 

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

## <a name="next-steps"></a>Passos seguintes
Consulte outras lições para escrever consultas de registo do Monitor Azure:

- [Operações de cadeia](string-operations.md)
- [Operações de data e hora](datetime-operations.md)
- [Funções de agregação](aggregations.md)
- [Agregações avançadas](advanced-aggregations.md)
- [Estruturas de dados e JSON](json-data-structures.md)
- [Associações](joins.md)
- [Gráficos](charts.md)
