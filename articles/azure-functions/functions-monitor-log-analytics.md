---
title: Monitorização de funções de Azure com registos do monitor Azure
description: Aprenda a utilizar registos de monitores Azure com Funções Azure para monitorizar execuções de funções.
author: craigshoemaker
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: b6ce9e77421df0563810fd7f8255720c1fd2d0c0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100591079"
---
# <a name="monitoring-azure-functions-with-azure-monitor-logs"></a>Monitorização de funções de Azure com registos do monitor Azure

O Azure Functions oferece uma integração com [registos monitores Azure](../azure-monitor/logs/data-platform-logs.md) para monitorizar funções. Este artigo mostra-lhe como configurar funções Azure para enviar registos gerados pelo sistema e gerados pelo utilizador para registos do Monitor Azure.

O Azure Monitor Logs dá-lhe a capacidade de consolidar registos de diferentes recursos no mesmo espaço de trabalho, onde pode ser analisado com [consultas](../azure-monitor/logs/log-query-overview.md) para recuperar, consolidar e analisar rapidamente dados recolhidos.  Pode criar e testar consultas utilizando o [Log Analytics](../azure-monitor/logs/log-query-overview.md) no portal Azure e, em seguida, analisar diretamente os dados utilizando estas ferramentas ou guardar consultas para utilização com visualizações ou [regras](../azure-monitor/visualizations.md) [de alerta](../azure-monitor/alerts/alerts-overview.md).

O Azure Monitor utiliza uma versão da linguagem de [consulta Kusto](/azure/kusto/query/) utilizada pelo Azure Data Explorer que é adequada para consultas simples de log, mas também inclui funcionalidades avançadas como agregações, juntas e análise inteligente. Pode aprender rapidamente a linguagem de consulta usando [várias lições.](../azure-monitor/logs/get-started-queries.md)

> [!NOTE]
> A integração com os Registos do Monitor Azure está atualmente em pré-visualização pública para aplicações de funções V2 e V3 em execução em planos de alojamento do Windows Consumption, Premium e Dedicado.

## <a name="setting-up"></a>Configuração

1. A partir da secção de **monitorização** da sua aplicação de função no [portal Azure,](https://portal.azure.com)selecione **definições de Diagnóstico** e, em seguida, selecione Adicionar a **definição de diagnóstico**.

   :::image type="content" source="media/functions-monitor-log-analytics/diagnostic-settings-add.png" alt-text="Selecione definições de diagnóstico":::

1. Na página de **definições de Diagnóstico,** em **detalhes de categoria** e **log**, escolha **FunctionAppLogs**.

   A tabela **FunctionAppLogs** contém os registos pretendidos.

1. Nos **detalhes do Destino**, escolha Enviar para Registar **Análises** e, em seguida, selecione o seu espaço de trabalho Log **Analytics**. 

1. Introduza o nome de **definições de Diagnóstico** e, em seguida, selecione **Guardar**.

   :::image type="content" source="media/functions-monitor-log-analytics/choose-table.png" alt-text="Adicione uma definição de diagnóstico":::

## <a name="user-generated-logs"></a>Registos gerados pelo utilizador

Para gerar registos personalizados, utilize a declaração de registo específico do seu idioma. Aqui estão os fragmentos de código de amostra:


# <a name="c"></a>[C#](#tab/csharp)

```csharp
log.LogInformation("My app logs here.");
```

# <a name="java"></a>[Java](#tab/java)

```java
context.getLogger().info("My app logs here.");
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
context.log('My app logs here.');
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
Write-Host "My app logs here."
```

# <a name="python"></a>[Python](#tab/python)

```python
logging.info('My app logs here.')
```

---

## <a name="querying-the-logs"></a>Consulta dos troncos

Para consultar os registos gerados:
 
1. A partir da sua aplicação de função, selecione **definições de Diagnóstico**. 

1. A partir da lista **de definições de Diagnóstico,** selecione o espaço de trabalho Do Log Analytics que configura para enviar os registos de funções para. 

1. A partir da página **do espaço de trabalho Log Analytics,** selecione **Logs**.

   A Azure Functions escreve todos os registos na tabela **FunctionAppLogs** em **LogManagement**. 

   :::image type="content" source="media/functions-monitor-log-analytics/querying.png" alt-text="Janela de consulta no espaço de trabalho Log Analytics":::

Aqui estão algumas consultas de amostra:

### <a name="all-logs"></a>Todos os troncos

```

FunctionAppLogs
| order by TimeGenerated desc

```

### <a name="specific-function-logs"></a>Troncos de função específicos

```

FunctionAppLogs
| where FunctionName == "<Function name>" 

```

### <a name="exceptions"></a>Exceções

```

FunctionAppLogs
| where ExceptionDetails != ""  
| order by TimeGenerated asc

```

## <a name="next-steps"></a>Passos seguintes

- Reveja a [visão geral das funções Azure](functions-overview.md).
- Saiba mais sobre [os Registos do Monitor Azure](../azure-monitor/logs/data-platform-logs.md).
- Saiba mais sobre a [língua de consulta.](../azure-monitor/logs/get-started-queries.md)
