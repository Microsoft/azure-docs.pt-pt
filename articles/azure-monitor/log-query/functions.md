---
title: Funções em consultas de log de Azure Monitor | Microsoft Docs
description: Este artigo descreve como usar funções para chamar uma consulta de outra consulta de log no Azure Monitor.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/15/2018
ms.openlocfilehash: 8d8473b88327d3d17346a0351d0a9fc510152cd8
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72894182"
---
# <a name="using-functions-in-azure-monitor-log-queries"></a>Usando funções em consultas de log de Azure Monitor

Para usar uma consulta de log com outra consulta, você pode salvá-la como uma função. Isso permite que você simplifique consultas complexas dividindo-as em partes e permite reutilizar código comum com várias consultas.

## <a name="create-a-function"></a>Criar uma função

Crie uma função com Log Analytics na portal do Azure clicando em **salvar** e, em seguida, fornecendo as informações na tabela a seguir.

| Definição | Descrição |
|:---|:---|
| Nome           | Nome de exibição da consulta no **Gerenciador de consultas**. |
| Guardar como        | Função |
| Alias da função | Nome curto para usar a função em outras consultas. Não pode conter espaços e deve ser exclusivo. |
| Categoria       | Uma categoria para organizar consultas e funções salvas no **Gerenciador de consultas**. |

> [!NOTE]
> Uma função no Azure Monitor não pode conter outra função.




## <a name="use-a-function"></a>Usar uma função
Use uma função incluindo seu alias em outra consulta. Ele pode ser usado como qualquer outra tabela.

## <a name="example"></a>Exemplo
A consulta de exemplo a seguir retorna todas as atualizações de segurança ausentes relatadas no último dia. Salve essa consulta como uma função com o alias _security_updates_last_day_. 

```Kusto
Update
| where TimeGenerated > ago(1d) 
| where Classification == "Security Updates" 
| where UpdateState == "Needed"
```

Crie outra consulta e referencie a função _security_updates_last_day_ para procurar atualizações de segurança necessárias relacionadas ao SQL.

```Kusto
security_updates_last_day | where Title contains "SQL"
```

## <a name="next-steps"></a>Passos seguintes
Consulte outras lições para escrever Azure Monitor consultas de log:

- [Operações de cadeia de caracteres](string-operations.md)
- [Operações de data e hora](datetime-operations.md)
- [Funções de agregação](aggregations.md)
- [Agregações avançadas](advanced-aggregations.md)
- [JSON e estruturas de dados](json-data-structures.md)
- [Junções](joins.md)
- [Spersão](charts.md)
