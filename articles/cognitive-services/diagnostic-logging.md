---
title: Registo de diagnósticos
titleSuffix: Azure Cognitive Services
description: Este guia fornece instruções passo a passo para permitir o registo de diagnóstico de um Serviço Cognitivo Azure. Estes registos fornecem dados ricos e frequentes sobre o funcionamento de um recurso que são usados para identificar e depurar.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.topic: article
ms.date: 06/14/2019
ms.author: erhopf
ms.openlocfilehash: 539a35f170b2ee0c94762a30ed9376ca4a416210
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "71827906"
---
# <a name="enable-diagnostic-logging-for-azure-cognitive-services"></a>Ativar a exploração de diagnóstico para serviços cognitivos Azure

Este guia fornece instruções passo a passo para permitir o registo de diagnóstico de um Serviço Cognitivo Azure. Estes registos fornecem dados ricos e frequentes sobre o funcionamento de um recurso que são usados para identificar e depurar. Antes de continuar, deve ter uma conta Azure com uma subscrição de pelo menos um Serviço Cognitivo, como [Bing Web Search,](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/overview) [Speech Services](https://docs.microsoft.com/azure/cognitive-services/speech-service/overview)ou [LUIS](https://docs.microsoft.com/azure/cognitive-services/luis/what-is-luis).

## <a name="prerequisites"></a>Pré-requisitos

Para permitir o registo de diagnóstico, necessitará de um lugar para armazenar os seus dados de registo. Este tutorial utiliza o Azure Storage e o Log Analytics.

* [Armazenamento azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-archive-diagnostic-logs) - Retém registos de diagnóstico para auditoria de políticas, análise estática ou backup. A conta de armazenamento não tem de estar na mesma subscrição que os registos emissores de recursos, desde que o utilizador que configura a definição tenha acesso rBAC adequado a ambas as subscrições.
* [Log Analytics](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-stream-diagnostic-logs-log-analytics) - Uma ferramenta flexível de pesquisa de registos e análise que permite a análise de troncos brutos gerados por um recurso Azure.

> [!NOTE]
> Estão disponíveis opções adicionais de configuração. Para saber mais, consulte [Recolher e consumir dados de registo dos seus recursos Azure.](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview)

## <a name="enable-diagnostic-log-collection"></a>Ativar a recolha de registos de diagnóstico  

Vamos começar por permitir o registo de diagnóstico utilizando o portal Azure.

> [!NOTE]
> Para ativar esta funcionalidade utilizando o PowerShell ou o Azure CLI, utilize as instruções fornecidas no [Collect e consuma os dados de registo dos seus recursos Azure](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview).

1. Navegue para o portal do Azure. Em seguida, localize e selecione um recurso dos Serviços Cognitivos. Por exemplo, a sua subscrição de Bing Web Search.   
2. Em seguida, a partir do menu de navegação à esquerda, localize **a Monitorização** e selecione **definições**de diagnóstico . Este ecrã contém todas as definições de diagnóstico previamente criadas para este recurso.
3. Se houver um recurso previamente criado que gostaria de usar, pode selecioná-lo agora. Caso contrário, **selecione + Adicione a definição de diagnóstico**.
4. Introduza um nome para a definição. Em seguida, selecione **Archive para uma conta** de armazenamento e enviar para registar **Analytics**.
5. Quando solicitado para configurar, selecione a conta de armazenamento e o espaço de trabalho OMS que gostaria de usar para armazenar os seus registos de diagnóstico. **Nota:** Se não tiver uma conta de armazenamento ou espaço de trabalho OMS, siga as instruções para criar uma.
6. Selecione **Auditoria,** **Resposta de Pedido**e **AllMetrics**. Em seguida, detete o período de retenção para os seus dados de registo de diagnóstico. Se uma política de retenção estiver definida para zero, os eventos para essa categoria de registo são armazenados indefinidamente.
7. Clique em **Guardar**.

Pode levar até duas horas antes de registar dados disponíveis para consulta e análise. Por isso, não te preocupes se não vires nada imediatamente.

## <a name="view-and-export-diagnostic-data-from-azure-storage"></a>Ver e exportar dados de diagnóstico do Armazenamento Azure

O Azure Storage é uma solução robusta de armazenamento de objetos que é otimizada para armazenar grandes quantidades de dados não estruturados. Nesta secção, você vai aprender a consultar a sua conta de armazenamento para transações totais ao longo de um prazo de 30 dias e exportar os dados para se destacar.

1. A partir do portal Azure, localize o recurso de Armazenamento Azure que criou na última secção.
2. A partir do menu de navegação à esquerda, localize **a Monitorização** e selecione **Métricas**.
3. Utilize as desistências disponíveis para configurar a sua consulta. Para este exemplo, vamos definir o intervalo de tempo para **30 dias e** a métrica para **Transação**.
4. Quando a consulta estiver concluída, verá uma visualização da transação nos últimos 30 dias. Para exportar estes dados, utilize o botão **Export to Excel** localizado no topo da página.

Saiba mais sobre o que pode fazer com dados de diagnóstico no [Armazenamento Azure.](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction)

## <a name="view-logs-in-log-analytics"></a>Ver registos no Log Analytics

Siga estas instruções para explorar dados de análise de registo para o seu recurso.

1. A partir do portal Azure, localize e selecione **Log Analytics** do menu de navegação à esquerda.
2. Localize e selecione o recurso que criou ao permitir diagnósticos.
3. Em **Geral,** localize e selecione **Registos**. A partir desta página, pode fazer consultas contra os seus registos.

### <a name="sample-queries"></a>Consultas de exemplo

Aqui estão algumas consultas básicas da Kusto que pode usar para explorar os seus dados de registo.

Faça esta consulta para todos os registos de diagnóstico dos Serviços Cognitivos Azure durante um determinado período de tempo:

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

Execute esta consulta para operações de grupo por **Recurso:**

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

Execute esta consulta para ver o volume de operações ao longo do tempo dividido pela OperaçãoNome com contagens acadas por cada 10.

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| summarize count()
by bin(TimeGenerated, 10s), OperationName
| render areachart kind=unstacked
```

## <a name="next-steps"></a>Passos seguintes

* Para entender como permitir a exploração madeireira, bem como as métricas e categorias de registo que são suportadas pelos vários serviços Azure, leia tanto a [visão geral das métricas](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics) no Microsoft Azure como a visão geral dos artigos de [Registos de Diagnóstico do Azure.](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview)
* Leia estes artigos para saber sobre os centros de eventos:
  * [O que são os Hubs de Eventos do Azure?](https://docs.microsoft.com/azure/event-hubs/event-hubs-what-is-event-hubs)
  * [Introdução ao Event Hubs](https://docs.microsoft.com/azure/event-hubs/event-hubs-csharp-ephcs-getstarted)
* Leia [as métricas de descarregamento e os registos de diagnóstico do Armazenamento Azure](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-dotnet#download-blobs).
* Leia Compreender as pesquisas de [registo nos registos do Monitor Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-log-search-new).
