---
title: Registos de diagnóstico do Azure Stream à área de trabalho do Log Analytics no Azure Monitor
description: Aprenda a transmitir em fluxo registos de diagnóstico do Azure para uma área de trabalho do Log Analytics no Azure Monitor.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: johnkem
ms.subservice: logs
ms.openlocfilehash: b17978da3195b364f868d33ab7ad9faa1544e9ec
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60238028"
---
# <a name="stream-azure-diagnostic-logs-to-log-analytics-workspace-in-azure-monitor"></a>Registos de diagnóstico do Azure Stream à área de trabalho do Log Analytics no Azure Monitor

**[Registos de diagnóstico do Azure](diagnostic-logs-overview.md)**  podem ser transmitidos em tempo real para uma área de trabalho do Log Analytics no Azure Monitor com o portal, cmdlets do PowerShell ou da CLI do Azure.

## <a name="what-you-can-do-with-diagnostics-logs-in-a-log-analytics-workspace"></a>O que pode fazer com o diagnóstico de registos numa área de trabalho do Log Analytics

O Azure Monitor proporciona uma ferramenta de consulta e análise de registo flexível que permite que pode obter informações sobre os dados de registos não processados gerados a partir de recursos do Azure. Alguns recursos incluem:

* **Consulta de registo** -escrita consultas avançadas sobre os dados de registo, correlacionar os registos de várias origens e a gerarem gráficos que podem ser afixadas ao dashboard do Azure.
* **Alertas** -detetar quando um ou mais eventos correspondem a uma consulta específica e ser notificados com uma chamada de e-mail ou webhook com alertas do Azure Monitor.
* **O Advanced analytics** - aplicam-se de aprendizagem automática e padrão de algoritmos correspondentes para identificar possíveis problemas revelados pelos seus registos.

## <a name="enable-streaming-of-diagnostic-logs-to-log-analytics-workspace"></a>Ativar a transmissão em fluxo de registos de diagnóstico para a área de trabalho do Log Analytics

Pode ativar a transmissão em fluxo de registos de diagnóstico através de programação, através do portal, ou utilizando o [APIs de REST do Azure Monitor](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings). De qualquer forma, criar uma definição de diagnóstico em que especificar uma área de trabalho do Log Analytics e as categorias de registo e métricas que deseja enviar para área de trabalho. Um diagnóstico **categoria de registo** é um tipo de registo que pode fornecer um recurso.

A área de trabalho do Log Analytics não tem de estar na mesma subscrição que o recurso emite os registos, desde que o utilizador que configura a definição possui acesso RBAC adequado para ambas as subscrições.

> [!NOTE]
> Atualmente, o envio de métricas multidimensionais através das definições de diagnóstico não é suportado. As métricas com dimensões são exportadas como métricas dimensionais simples e agregadas em valores de dimensões.
>
> *Por exemplo*: A métrica "Mensagens recebidas" num Hub de eventos pode ser explorada e representada um por nível de fila. No entanto, se for exportada através das definições de diagnóstico, a métrica será representada como todas as mensagens recebidas em todas as filas do hub do Hub de Eventos.
>
>

## <a name="stream-diagnostic-logs-using-the-portal"></a>Registos de diagnóstico do Stream com o portal
1. No portal, navegue para o Azure Monitor e clique em **das definições de diagnóstico** no **definições** menu.


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

## <a name="how-do-i-query-the-data-from-a-log-analytics-workspace"></a>Como faço para consultar os dados a partir de uma área de trabalho do Log Analytics?

No painel de registos no portal do Azure Monitor, pode consultar os registos de diagnóstico como parte da solução de gestão de registos na tabela AzureDiagnostics. Também existem [várias soluções de monitorização para recursos do Azure](../../azure-monitor/insights/solutions.md) pode instalar para obter informações imediatas sobre os dados de registo estão a enviar para o Azure Monitor.

### <a name="known-limitation-column-limit-in-azurediagnostics"></a>Conhecido limitação: limite de coluna em AzureDiagnostics
Uma vez que muitos recursos enviar tipos de dados são enviados para a mesma tabela (_AzureDiagnostics_), o esquema desta tabela é o conjunto superutilizadores dos esquemas de todos os tipos de dados diferentes a ser recolhidos. Por exemplo, se tiver criado as definições de diagnóstico para a coleção dos seguintes tipos de dados, tudo a ser enviados para a mesma área de trabalho:
- Registos de 1 de recursos (ter um esquema consiste em colunas A, B e C) de auditoria  
- Registos de erros de 2 de recursos (ter um esquema consiste em colunas D, E e F)  
- Registos de fluxo de dados de 3 de recursos (ter um esquema formada por colunas G, H e eu)  
 
A tabela de AzureDiagnostics será semelhante ao seguinte, com alguns dados de exemplo:  
 
| ResourceProvider | Category | A | B | C | D | E | F | G | H | I |
| -- | -- | -- | -- | -- | -- | -- | -- | -- | -- | -- |
| Microsoft.Resource1 | AuditLogs | x1 | y1 | z1 |
| Microsoft.Resource2 | ErrorLogs | | | | q1 | W1 | e1 |
| Microsoft.Resource3 | DataFlowLogs | | | | | | | j1 | k1 | l1|
| Microsoft.Resource2 | ErrorLogs | | | | q2 | W2 | e2 |
| Microsoft.Resource3 | DataFlowLogs | | | | | | | j3 | k3 | l3|
| Microsoft.Resource1 | AuditLogs | x5 | y5 | z5 |
| ... |
 
Existe um limite explícito de qualquer tabela registo do Azure não ter mais de 500 colunas. Uma vez atingido, quaisquer linhas que contêm dados com qualquer coluna fora da primeira 500 serão ignoradas durante a ingestão. A tabela de AzureDiagnostics é em particular suscetível a ser afetado este limite. Normalmente, isto acontece porque uma grande variedade de origens de dados são enviados para a mesma área de trabalho, ou várias origens de dados muito detalhada a ser enviadas para a mesma área de trabalho. 
 
#### <a name="azure-data-factory"></a>Azure Data Factory  
O Azure Data Factory, devido a um conjunto muito detalhado de registos, é um recurso que é conhecido por ser particularmente afetadas por este limite. Em particular:  
- *Parâmetros de utilizador definidos em relação a qualquer atividade no seu pipeline*: haverá uma nova coluna criada para cada parâmetro exclusivamente-com o nome de utilizador em relação a qualquer atividade. 
- *Atividade entradas e saídas*: estas variam de atividade para atividade e gerar uma grande quantidade de devido a respetiva natureza verbosa. 
 
Como com as mais amplas propostas de solução abaixo, é recomendado para isolar os registos do ADF para sua própria área de trabalho para minimizar a possibilidade destes registos afetar outros tipos de registos recolhidos em suas áreas de trabalho. Esperamos que tenha mantido registos do Azure Data Factory em breve.
 
#### <a name="workarounds"></a>Soluções alternativas
Curta duração, até que o limite de 500 coluna é redefinido, é recomendável separar os tipos de dados verboso em áreas de trabalho separadas para reduzir a possibilidade de atingir o limite.
 
Longo prazo, o diagnóstico do Azure irá ser deixando de lado um esquema unificado, disperso nas tabelas individuais por cada tipo de dados; combinado com o suporte para tipos dinâmicos, isto melhorará bastante a usabilidade de dados que entram no registos do Azure através do mecanismo de diagnóstico do Azure. Já verá isto para, selecione os tipos de recursos do Azure, por exemplo [do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-analyze-activity-logs-log-analytics) ou [Intune](https://docs.microsoft.com/intune/review-logs-using-azure-monitor) registos. Procure notícias sobre novos tipos de recursos no Azure que suporta estes registos organizados no [Azure atualiza as](https://azure.microsoft.com/updates/) blogue!


## <a name="next-steps"></a>Passos Seguintes

* [Leia mais sobre os Registos de Diagnóstico do Azure](diagnostic-logs-overview.md)

