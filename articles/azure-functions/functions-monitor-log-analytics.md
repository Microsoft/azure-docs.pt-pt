---
title: Azure Functions de monitoramento com logs de Azure Monitor
description: Saiba como usar os logs de Azure Monitor com Azure Functions para monitorar as execuções de função.
author: ahmedelnably
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: aelnably
ms.openlocfilehash: 9aac6662304395b1bce5dfc21770d296f6a4f2ab
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74226854"
---
# <a name="monitoring-azure-functions-with-azure-monitor-logs"></a>Azure Functions de monitoramento com logs de Azure Monitor

O Azure Functions oferece uma integração com [Azure monitor logs](../azure-monitor/platform/data-platform-logs.md) para monitorar funções. Este artigo mostra como configurar Azure Functions para enviar logs gerados pelo sistema e pelo usuário a logs do Azure Monitor.

Os logs de Azure Monitor fornecem a capacidade de consolidar logs de diferentes recursos no mesmo espaço de trabalho, onde podem ser analisados com [consultas](../azure-monitor/log-query/log-query-overview.md) para recuperar, consolidar e analisar rapidamente os dados coletados.  Você pode criar e testar consultas usando [log Analytics](../azure-monitor/log-query/portals.md) na portal do Azure e, em seguida, analisar diretamente os dados usando essas ferramentas ou salvar consultas para uso com [visualizações](../azure-monitor/visualizations.md) ou [regras de alerta](../azure-monitor/platform/alerts-overview.md).

Azure Monitor usa uma versão da [linguagem de consulta Kusto](/azure/kusto/query/) usada pelo data Explorer do Azure que é adequada para consultas de log simples, mas também inclui funcionalidades avançadas, como agregações, junções e análise inteligente. Você pode aprender rapidamente a linguagem de consulta usando [várias lições](../azure-monitor/log-query/get-started-queries.md).

> [!NOTE]
> A integração com os logs de Azure Monitor está atualmente em visualização pública para aplicativos de funções em execução no consumo do Windows, Premium e planos de hospedagem dedicados.

## <a name="setting-up"></a>Configurando

Na seção monitoramento, selecione **configurações de diagnóstico** e clique em **Adicionar**.

![Adicionar uma configuração de diagnóstico](media/functions-monitor-log-analytics/diagnostic-settings-add.png)

Na página configuração, escolha **Enviar para log Analytics**e, em **log** , escolha **FunctionAppLogs**, essa tabela contém os logs desejados.

![Adicionar uma configuração de diagnóstico](media/functions-monitor-log-analytics/choose-table.png)

## <a name="user-generated-logs"></a>Logs gerados pelo usuário

Para gerar logs personalizados, você pode usar a instrução de log específica, dependendo do seu idioma, aqui estão os trechos de código de exemplo:

**JavaScript**

```javascript
    context.log('My app logs here.');
```

**Python**

```python
    logging.info('My app logs here.')
```

**.NET**

```csharp
    log.LogInformation("My app logs here.");
```

**Java**

```java
    context.getLogger().info("My app logs here.");
```

**PowerShell**

```powershell
    Write-Host "My app logs here."
```

## <a name="querying-the-logs"></a>Consultando os logs

Para consultar os logs gerados, vá para o espaço de trabalho do log Analytics e clique em **logs**.

![Janela de consulta na LA espaço de trabalho](media/functions-monitor-log-analytics/querying.png)

Azure Functions grava todos os logs na tabela **FunctionAppLogs** , aqui estão algumas consultas de exemplo.

### <a name="all-logs"></a>Todos os logs

```

FunctionAppLogs
| order by TimeGenerated desc

```

### <a name="a-specific-function-logs"></a>Logs de função específicos

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

## <a name="next-steps"></a>Passos Seguintes

- Examinar a [visão geral de Azure Functions](functions-overview.md)
- Saiba mais sobre [os logs de Azure monitor](../azure-monitor/platform/data-platform-logs.md)
- Saiba mais sobre a [linguagem de consulta](../azure-monitor/log-query/get-started-queries.md).
