---
title: Registo de diagnósticos
titleSuffix: Azure Cognitive Services
description: Este guia fornece instruções passo a passo para permitir o registo de diagnóstico para um Serviço Cognitivo Azure. Estes registos fornecem dados ricos e frequentes sobre o funcionamento de um recurso que são utilizados para identificar e depurar.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 06/14/2019
ms.author: erhopf
ms.openlocfilehash: 4a78e233a41bf3b6682f52bac912528d6bcab76c
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107816343"
---
# <a name="enable-diagnostic-logging-for-azure-cognitive-services"></a>Ativar o registo de diagnóstico para serviços cognitivos Azure

Este guia fornece instruções passo a passo para permitir o registo de diagnóstico para um Serviço Cognitivo Azure. Estes registos fornecem dados ricos e frequentes sobre o funcionamento de um recurso que são utilizados para identificar e depurar. Antes de continuar, tem de ter uma conta Azure com uma subscrição de pelo menos um Serviço Cognitivo, como [Bing Web Search,](./bing-web-search/overview.md) [Speech Services,](./speech-service/overview.md)ou [LUIS](./luis/what-is-luis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para ativar a sessão de diagnóstico, precisará de um lugar para armazenar os seus dados de registo. Este tutorial utiliza a Azure Storage e Log Analytics.

* [Armazenamento Azure](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage) - Retém registos de diagnóstico para auditoria de política, análise estática ou backup. A conta de armazenamento não tem de estar na mesma subscrição que os registos emissores de recursos, desde que o utilizador que configura a definição tenha acesso RBAC adequado a ambas as subscrições.
* [Log Analytics](../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace) - Uma ferramenta flexível de pesquisa de registos e análise que permite a análise de troncos crus gerados por um recurso Azure.

> [!NOTE]
> Estão disponíveis opções de configuração adicionais. Para saber mais, consulte [Recolher e consumir dados de registo dos seus recursos Azure.](../azure-monitor/essentials/platform-logs-overview.md)

## <a name="enable-diagnostic-log-collection"></a>Ativar a recolha de registos de diagnóstico  

Comecemos por permitir a verificação de registos de diagnóstico utilizando o portal Azure.

> [!NOTE]
> Para ativar esta funcionalidade utilizando o PowerShell ou o Azure CLI, utilize as instruções fornecidas na [Recolha e consuma os dados de registo dos seus recursos Azure](../azure-monitor/essentials/platform-logs-overview.md).

1. Navegue para o portal do Azure. Em seguida, localize e selecione um recurso de Serviços Cognitivos. Por exemplo, a sua subscrição para bing Web Search.   
2. Em seguida, a partir do menu de navegação à esquerda, **localize as** definições de Monitorização e selecione **As definições de Diagnóstico**. Este ecrã contém todas as definições de diagnóstico previamente criadas para este recurso.
3. Se houver um recurso previamente criado que gostaria de utilizar, pode selecioná-lo agora. Caso contrário, **selecione + Adicione a definição de diagnóstico**.
4. Insira um nome para a definição. Em seguida, selecione **Archive para uma conta de armazenamento** e envie para registar a **Analytics**.
5. Quando solicitado para configurar, selecione a conta de armazenamento e o espaço de trabalho OMS que gostaria de usar para armazenar os seus registos de diagnóstico. **Nota:** Se não tiver uma conta de armazenamento ou espaço de trabalho OMS, siga as indicações para criar uma.
6. **Selecione Auditoria,** **SolicitaçãoResponse** e **AllMetrics**. Em seguida, desa estale o período de retenção para os seus dados de registo de diagnóstico. Se uma política de retenção for definida como zero, os eventos para essa categoria de registo são armazenados indefinidamente.
7. Clique em **Guardar**.

Pode levar até duas horas antes de os dados de registo estar disponíveis para consulta e análise. Por isso, não te preocupes se não vires nada imediatamente.

## <a name="view-and-export-diagnostic-data-from-azure-storage"></a>Ver e exportar dados de diagnóstico da Azure Storage

O Azure Storage é uma solução robusta de armazenamento de objetos que é otimizada para armazenar grandes quantidades de dados não estruturados. Nesta secção, você aprenderá a consultar a sua conta de armazenamento para transações totais ao longo de um prazo de 30 dias e exportar os dados para se destacar.

1. A partir do portal Azure, localize o recurso Azure Storage que criou na última secção.
2. A partir do menu de navegação à esquerda, localize **a Monitorização** e selecione **Métricas**.
3. Utilize as reduções disponíveis para configurar a sua consulta. Para este exemplo, vamos definir o intervalo de tempo para **os últimos 30 dias** e a métrica para **a Transação.**
4. Quando a consulta estiver completa, verá uma visualização da transação nos últimos 30 dias. Para exportar estes dados, utilize o botão **Exportação para Excel** localizado no topo da página.

Saiba mais sobre o que pode fazer com dados de diagnóstico no [Azure Storage](../storage/blobs/storage-blobs-introduction.md).

## <a name="view-logs-in-log-analytics"></a>Ver registos no Log Analytics

Siga estas instruções para explorar os dados de análise de registo para o seu recurso.

1. A partir do portal Azure, localize e selecione **Log Analytics** a partir do menu de navegação à esquerda.
2. Localize e selecione o recurso que criou ao ativar os diagnósticos.
3. Em **Geral,** localizar e selecionar **Registos**. A partir desta página, pode executar consultas contra os seus registos.

### <a name="sample-queries"></a>Consultas de exemplo

Aqui estão algumas consultas básicas de Kusto que você pode usar para explorar os seus dados de registo.

Executar esta consulta para todos os registos de diagnóstico dos Serviços Cognitivos Azure durante um período de tempo especificado:

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
```

Faça esta consulta para ver os 10 registos mais recentes:

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| take 10
```

Executar esta consulta para agrupar operações por **Recurso:**

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES" |
summarize count() by Resource
```
Execute esta consulta para encontrar o tempo médio que leva para realizar uma operação:

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| summarize avg(DurationMs)
by OperationName
```

Executar esta consulta para ver o volume de operações ao longo do tempo dividido pela Operação Natal com contagens para cada 10 s.

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| summarize count()
by bin(TimeGenerated, 10s), OperationName
| render areachart kind=unstacked
```

## <a name="next-steps"></a>Passos seguintes

* Para compreender como permitir o registo, bem como as métricas e categorias de registo que são suportadas pelos vários serviços Azure, leia tanto a [visão geral das métricas](../azure-monitor/data-platform.md) nos artigos microsoft Azure e [Overview of Azure Diagnostic Logs.](../azure-monitor/essentials/platform-logs-overview.md)
* Leia estes artigos para saber sobre os centros de eventos:
  * [O que são os Hubs de Eventos do Azure?](../event-hubs/event-hubs-about.md)
  * [Introdução ao Event Hubs](../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)
* Leia [As métricas de descarregamento e os registos de diagnóstico do Azure Storage](../storage/blobs/storage-quickstart-blobs-dotnet.md#download-blobs).
* Leia [As pesquisas de registo em registos do Azure Monitor](../azure-monitor/logs/log-query-overview.md).