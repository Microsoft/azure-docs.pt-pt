---
title: Monitorização de funções azure com registos de monitores Azure
description: Aprenda a utilizar registos de monitores Azure com funções Azure para monitorizar as execuções de funções.
author: craigshoemaker
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: cshoe
ms.openlocfilehash: 13c72a1cf8a0dd4a1124e51b9ceee04ae04bf261
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77649879"
---
# <a name="monitoring-azure-functions-with-azure-monitor-logs"></a>Monitorização de funções azure com registos de monitores Azure

A Azure Functions oferece uma integração com registos de [monitores Azure](../azure-monitor/platform/data-platform-logs.md) para monitorizar funções. Este artigo mostra-lhe como configurar as Funções Azure para enviar registos gerados pelo sistema e gerados pelo utilizador para registos do Monitor Azure.

O Azure Monitor Logs dá-lhe a capacidade de consolidar registos de diferentes recursos no mesmo espaço de trabalho, onde pode ser analisado com [consultas](../azure-monitor/log-query/log-query-overview.md) para recuperar, consolidar e analisar rapidamente os dados recolhidos.  Pode criar e testar consultas utilizando [log analytics](../azure-monitor/log-query/portals.md) no portal Azure e, em seguida, analisar diretamente os dados usando estas ferramentas ou guardar consultas para uso com [visualizações](../azure-monitor/visualizations.md) ou regras de [alerta](../azure-monitor/platform/alerts-overview.md).

O Azure Monitor utiliza uma versão da [linguagem de consulta Kusto](/azure/kusto/query/) utilizada pelo Azure Data Explorer que é adequada para consultas de registo simples, mas também inclui funcionalidades avançadas como agregações, adesões e análises inteligentes. Você pode aprender rapidamente a linguagem de consulta usando [várias lições](../azure-monitor/log-query/get-started-queries.md).

> [!NOTE]
> A integração com o Azure Monitor Logs está atualmente em pré-visualização pública para aplicações de funções em execução em planos de hospedagem Windows Consumption, Premium e Dedicados.

## <a name="setting-up"></a>Configuração

Na secção **de Monitorização,** selecione **definições** de diagnóstico e, em seguida, clique em **adicionar definição de diagnóstico**.

![Adicione uma definição de diagnóstico](media/functions-monitor-log-analytics/diagnostic-settings-add.png)

Na página de **definições** de Diagnóstico, escolha **Enviar para Registar Analytics**e, em seguida, selecione o seu espaço de trabalho Log Analytics. Em **registo** escolha **FunctionAppLogs**, esta tabela contém os registos desejados.

![Adicione uma definição de diagnóstico](media/functions-monitor-log-analytics/choose-table.png)

## <a name="user-generated-logs"></a>Registos gerados pelo utilizador

Para gerar registos personalizados, pode utilizar a declaração de registo específica dependendo da sua língua, aqui estão os snippets de código de amostra:


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

# <a name="python"></a>[python](#tab/python)

```python
logging.info('My app logs here.')
```

---

## <a name="querying-the-logs"></a>Consultando os troncos

Para consultar os registos gerados, aceda ao espaço de trabalho do Log Analytics que configuraste para enviar os registos de função e clicar em **Registos**.

![Janela de consulta no espaço de trabalho de LA](media/functions-monitor-log-analytics/querying.png)

As Funções Azure escrevem todos os registos na tabela **FunctionAppLogs,** aqui estão algumas consultas de amostra.

### <a name="all-logs"></a>Todos os registos

```

FunctionAppLogs
| order by TimeGenerated desc

```

### <a name="a-specific-function-logs"></a>Um registo de função específico

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

- Rever a visão geral das [Funções Azure](functions-overview.md)
- Saiba mais sobre [os registos do Monitor Azure](../azure-monitor/platform/data-platform-logs.md)
- Saiba mais sobre a [linguagem de consulta.](../azure-monitor/log-query/get-started-queries.md)
