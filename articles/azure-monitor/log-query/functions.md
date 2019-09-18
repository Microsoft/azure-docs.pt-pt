---
title: Funções em consultas de log de Azure Monitor | Microsoft Docs
description: Este artigo descreve como usar funções para chamar uma consulta de outra consulta de log no Azure Monitor.
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
ms.date: 11/15/2018
ms.author: bwren
ms.openlocfilehash: 75beb7b66863efd2fb3679f034a3663dca4a6d2f
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71076707"
---
# <a name="using-functions-in-azure-monitor-log-queries"></a>Usando funções em consultas de log de Azure Monitor

Para usar uma consulta de log com outra consulta, você pode salvá-la como uma função. Isso permite que você simplifique consultas complexas dividindo-as em partes e permite reutilizar código comum com várias consultas.

## <a name="create-a-function"></a>Criar uma função

Crie uma função com Log Analytics na portal do Azure clicando em **salvar** e, em seguida, fornecendo as informações na tabela a seguir.

| Definição | Descrição |
|:---|:---|
| Name           | Nome de exibição da consulta no **Gerenciador de consultas**. |
| Guardar como        | Função |
| Alias de Função | Nome curto para usar a função em outras consultas. Não pode conter espaços e deve ser exclusivo. |
| Category       | Uma categoria para organizar consultas e funções salvas no **Gerenciador de consultas**. |

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
