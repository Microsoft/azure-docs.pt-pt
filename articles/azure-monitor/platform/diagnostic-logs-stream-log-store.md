---
title: Stream registos de diagnóstico do Azure para o Log Analytics
description: Aprenda a transmitir em fluxo registos de diagnóstico do Azure para uma área de trabalho do Log Analytics.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/04/2018
ms.author: johnkem
ms.subservice: logs
ms.openlocfilehash: 3d187851fda9054bbfbae245ef34440b66ad017e
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2019
ms.locfileid: "57309320"
---
# <a name="stream-azure-diagnostic-logs-to-log-analytics"></a>Stream registos de diagnóstico do Azure para o Log Analytics

**[Registos de diagnóstico do Azure](diagnostic-logs-overview.md)**  podem ser transmitidos em fluxo em tempo real para o Azure Log Analytics com o portal, cmdlets do PowerShell ou da CLI do Azure.

## <a name="what-you-can-do-with-diagnostics-logs-in-log-analytics"></a>O que pode fazer com o diagnóstico de registos no Log Analytics

O Azure Log Analytics é uma ferramenta de análise e pesquisa de registo flexível que permite que pode obter informações sobre os dados de registos não processados gerados a partir de recursos do Azure. Alguns recursos incluem:

* **Pesquisa de registos** -escrita avançadas consultas sobre os seus dados de registo, correlacionar os registos de várias fontes e até mesmo geram gráficos que podem ser afixados ao dashboard do Azure.
* **Alertas** -detetar quando um ou mais eventos correspondem a uma consulta específica e ser notificados com uma chamada de e-mail ou webhook.
* **Soluções** -utilizar pré-criados vistas e dashboards que proporcionam a informações imediatas sobre os seus dados de registo.
* **O Advanced analytics** - aplicam-se de aprendizagem automática e padrão de algoritmos correspondentes para identificar possíveis problemas revelados pelos seus registos.

## <a name="enable-streaming-of-diagnostic-logs-to-log-analytics"></a>Ativar a transmissão em fluxo de registos de diagnóstico para o Log Analytics

Pode ativar a transmissão em fluxo de registos de diagnóstico através de programação, através do portal, ou utilizando o [APIs de REST do Azure Monitor](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings). De qualquer forma, criar uma definição de diagnóstico em que especificar uma área de trabalho do Log Analytics e as categorias de registo e métricas que deseja enviar para área de trabalho. Um diagnóstico **categoria de registo** é um tipo de registo que pode fornecer um recurso.

A área de trabalho do Log Analytics não tem de estar na mesma subscrição que o recurso emite os registos, desde que o utilizador que configura a definição possui acesso RBAC adequado para ambas as subscrições.

> [!NOTE]
> Atualmente, o envio de métricas multidimensionais através das definições de diagnóstico não é suportado. As métricas com dimensões são exportadas como métricas dimensionais simples e agregadas em valores de dimensões.
>
> *Por exemplo*: A métrica "Mensagens recebidas" num Hub de eventos pode ser explorada e representada um por nível de fila. No entanto, se for exportada através das definições de diagnóstico, a métrica será representada como todas as mensagens recebidas em todas as filas do hub do Hub de Eventos.
>
>

## <a name="stream-diagnostic-logs-using-the-portal"></a>Registos de diagnóstico do Stream com o portal
1. No portal, navegue para o Azure Monitor e clique em **definições de diagnóstico**

    ![Secção de monitorização do Azure Monitor](media/diagnostic-logs-stream-log-store/diagnostic-settings-blade.png)

2. Opcionalmente, filtrar a lista por grupo de recursos ou tipo de recurso, em seguida, clique no recurso para o qual pretende configurar uma definição de diagnóstico.

3. Se não existem definições existem no recurso que selecionou, lhe for pedido para criar uma definição. Clique em "Ativar diagnósticos."

   ![Adicionar definição de diagnóstico - sem definições existentes](media/diagnostic-logs-stream-log-store/diagnostic-settings-none.png)

   Se existirem definições existentes no recurso, verá uma lista das definições já configurada neste recurso. Clique em "Adicionar definição de diagnóstico".

   ![Adicionar definição de diagnóstico - existente definições](media/diagnostic-logs-stream-log-store/diagnostic-settings-multiple.png)

3. Dê a sua definição de um nome e marque a caixa **enviar para o Log Analytics**, em seguida, selecione uma área de trabalho do Log Analytics.

   ![Adicionar definição de diagnóstico - existente definições](media/diagnostic-logs-stream-log-store/diagnostic-settings-configure.png)

4. Clique em **Guardar**.

Após alguns instantes, a nova definição é apresentada na sua lista de definições para este recurso, e os registos de diagnóstico são transmitidas em fluxo para essa área de trabalho assim que novos dados de evento são gerados. Tenha em atenção que pode haver até quinze minutos entre quando um evento é emitido e quando for apresentada no Log Analytics.

### <a name="via-powershell-cmdlets"></a>Cmdlets do PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Para ativar a transmissão em fluxo através do [Cmdlets do Azure PowerShell](../../azure-monitor/platform/powershell-quickstart-samples.md), pode utilizar o `Set-AzDiagnosticSetting` cmdlet com estes parâmetros:

```powershell
Set-AzDiagnosticSetting -ResourceId [your resource ID] -WorkspaceID [resource ID of the Log Analytics workspace] -Categories [list of log categories] -Enabled $true
```

Tenha em atenção que a propriedade workspaceID pega a ID de recurso Azure completo da área de trabalho, não a área de trabalho/chave de ID apresentado no portal do Log Analytics.

### <a name="via-azure-cli"></a>Através da CLI do Azure

Para ativar a transmissão em fluxo através do [CLI do Azure](../../azure-monitor/platform/cli-samples.md), pode utilizar o [az monitor diagnostic-settings criar](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create) comando.

```azurecli
az monitor diagnostic-settings create --name <diagnostic name> \
    --workspace <log analytics name or object ID> \
    --resource <target resource object ID> \
    --resource-group <log analytics workspace resource group> \
    --logs '[
    {
        "category": <category name>,
        "enabled": true
    }
    ]'
```

Pode adicionar categorias adicionais no registo de diagnóstico através da adição de dicionários para a matriz JSON passada como o `--logs` parâmetro.

O `--resource-group` argumento só é necessária se `--workspace` não é um ID de objeto.

## <a name="how-do-i-query-the-data-in-log-analytics"></a>Como faço para consultar os dados no Log Analytics?

No painel pesquisa de registos no portal ou na experiência de Advanced Analytics como parte do Log Analytics, pode consultar os registos de diagnóstico como parte da solução de gestão de registos na tabela AzureDiagnostics. Também existem [várias soluções para recursos do Azure](../../azure-monitor/insights/solutions.md) pode instalar para obter informações imediatas sobre os dados de registo estão a enviar para o Log Analytics.

## <a name="next-steps"></a>Passos Seguintes

* [Leia mais sobre os Registos de Diagnóstico do Azure](diagnostic-logs-overview.md)

