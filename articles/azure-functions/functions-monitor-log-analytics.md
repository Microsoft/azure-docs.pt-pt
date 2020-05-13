---
title: Monitorização de funções azure com registos de monitores Azure
description: Aprenda a utilizar registos de monitores Azure com funções Azure para monitorizar as execuções de funções.
author: craigshoemaker
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: cshoe
ms.openlocfilehash: 4b21912de95ccba1d97d187922bfada4d9dc2c56
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83121653"
---
# <a name="monitoring-azure-functions-with-azure-monitor-logs"></a>Monitorização de funções azure com registos de monitores Azure

A Azure Functions oferece uma integração com registos de [monitores Azure](../azure-monitor/platform/data-platform-logs.md) para monitorizar funções. Este artigo mostra-lhe como configurar as Funções Azure para enviar registos gerados pelo sistema e gerados pelo utilizador para registos do Monitor Azure.

O Azure Monitor Logs dá-lhe a capacidade de consolidar registos de diferentes recursos no mesmo espaço de trabalho, onde pode ser analisado com [consultas](../azure-monitor/log-query/log-query-overview.md) para recuperar, consolidar e analisar rapidamente os dados recolhidos.  Pode criar e testar consultas utilizando [log analytics](../azure-monitor/log-query/portals.md) no portal Azure e, em seguida, analisar diretamente os dados usando estas ferramentas ou guardar consultas para uso com [visualizações](../azure-monitor/visualizations.md) ou regras de [alerta](../azure-monitor/platform/alerts-overview.md).

O Azure Monitor utiliza uma versão da [linguagem de consulta Kusto](/azure/kusto/query/) utilizada pelo Azure Data Explorer que é adequada para consultas de registo simples, mas também inclui funcionalidades avançadas como agregações, adesões e análises inteligentes. Você pode aprender rapidamente a linguagem de consulta usando [várias lições](../azure-monitor/log-query/get-started-queries.md).

> [!NOTE]
> A integração com o Azure Monitor Logs está atualmente em pré-visualização pública para aplicações de funções em execução em planos de hospedagem Windows Consumption, Premium e Dedicados.

## <a name="setting-up"></a>Configuração

1. A partir da secção **de Monitorização** da sua aplicação de funções no [portal Azure,](https://portal.azure.com)selecione **definições**de diagnóstico e, em seguida, **selecione Adicionar definição**de diagnóstico .

   :::image type="content" source="media/functions-monitor-log-analytics/diagnostic-settings-add.png" alt-text="Selecione definições de diagnóstico":::

1. Na página de definições de **Diagnóstico,** em **detalhes de categoria** e **registo,** escolha O Funcionamento dos **AppLogs**.

   A tabela **FunctionAppLogs** contém os registos desejados.

1. Em **detalhes do Destino,** escolha **Enviar para Registar Analytics**.e, em seguida, selecione o seu espaço de trabalho **Log Analytics**. 

1. Introduza um nome de **definições de diagnóstico**e, em seguida, selecione **Guardar**.

   :::image type="content" source="media/functions-monitor-log-analytics/choose-table.png" alt-text="Adicione uma definição de diagnóstico":::

## <a name="user-generated-logs"></a>Registos gerados pelo utilizador

Para gerar registos personalizados, utilize a declaração de registo específica da sua língua. Aqui estão os fragmentos de código de amostra:


# <a name="c"></a>[C #](#tab/csharp)

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

## <a name="querying-the-logs"></a>Consultando os troncos

Para consultar os registos gerados:
 
1. A partir da sua aplicação de funções, selecione **definições de diagnóstico**. 

1. A partir da lista de definições de **Diagnóstico,** selecione o espaço de trabalho do Log Analytics para o que configurapara enviar os registos de função. 

1. A partir da página do **espaço de trabalho Log Analytics,** selecione **Registos**.

   As Funções Azure escrevem todos os registos na tabela **FunctionAppLogs** em **LogManagement**. 

   :::image type="content" source="media/functions-monitor-log-analytics/querying.png" alt-text="Janela de consulta no espaço de trabalho log Analytics":::

Aqui estão algumas consultas de amostra:

### <a name="all-logs"></a>Todos os registos

```

FunctionAppLogs
| order by TimeGenerated desc

```

### <a name="specific-function-logs"></a>Registos de funções específicos

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

- Reveja a visão geral das [Funções Azure.](functions-overview.md)
- Saiba mais sobre [os registos do Monitor Azure](../azure-monitor/platform/data-platform-logs.md).
- Saiba mais sobre a [linguagem de consulta.](../azure-monitor/log-query/get-started-queries.md)
