---
title: Registo de diagnósticos
titleSuffix: Azure Cognitive Services
description: Este guia fornece instruções passo a passo para ativar o registo de diagnóstico para um serviço cognitivos do Azure. Estes registos fornecem dados avançados e frequentes sobre o funcionamento de um recurso que são utilizados para identificação de problemas e depuração.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.topic: article
ms.date: 06/14/2019
ms.author: erhopf
ms.openlocfilehash: e1a6a44d7ff9d5786388fc47245ef5c79cb9be82
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/17/2019
ms.locfileid: "67155733"
---
# <a name="enable-diagnostic-logging-for-azure-cognitive-services"></a>Ativar o registo de diagnóstico para os serviços cognitivos do Azure

Este guia fornece instruções passo a passo para ativar o registo de diagnóstico para um serviço cognitivos do Azure. Estes registos fornecem dados avançados e frequentes sobre o funcionamento de um recurso que são utilizados para identificação de problemas e depuração. Antes de continuar, tem de ter uma conta do Azure com uma subscrição, pelo menos, um serviço cognitivos, como [pesquisa Web Bing](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/overview), [serviços de voz](https://docs.microsoft.com/azure/cognitive-services/speech-service/overview), ou [LUIS](https://docs.microsoft.com/azure/cognitive-services/luis/what-is-luis).

## <a name="prerequisites"></a>Pré-requisitos

Para ativar o registo de diagnósticos, será necessário em algum lugar para armazenar os dados de registo. Este tutorial utiliza o armazenamento do Azure e o Log Analytics.

* [O armazenamento do Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-archive-diagnostic-logs) -mantém registos de diagnóstico para auditoria de política, análise estática ou cópia de segurança. A conta de armazenamento não tem de estar na mesma subscrição que o recurso emite os registos, desde que o utilizador que configura a definição possui acesso RBAC adequado para ambas as subscrições.
* [Log Analytics](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-stream-diagnostic-logs-log-analytics) -uma ferramenta de análise e pesquisa de registo flexível que permite a análise de registos não processados gerados pelo recurso do Azure.

> [!NOTE]
> Opções de configuração adicionais estão disponíveis. Para obter mais informações, consulte [recolher e consumir dados de registo dos seus recursos do Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs).

## <a name="enable-diagnostic-log-collection"></a>Ativar a recolha de registo de diagnóstico  

Vamos começar por ativar o diagnóstico de registo no portal do Azure.

> [!NOTE]
> Para ativar esta funcionalidade com o PowerShell ou a CLI do Azure, utilize as instruções fornecidas [recolher e consumir dados de registo dos seus recursos do Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs#how-to-enable-collection-of-diagnostic-logs).

1. Navegue para o portal do Azure. Em seguida, localize e selecione um recurso dos serviços cognitivos. Por exemplo, a subscrição de pesquisa Web Bing.   
2. Em seguida, no menu de navegação do lado esquerdo, localize **monitorização** e selecione **das definições de diagnóstico**. Este ecrã contém todas as definições de diagnóstico criadas anteriormente para este recurso.
3. Se existir um recurso criado anteriormente que gostaria de utilizar, pode selecioná-lo agora. Caso contrário, selecione **+ Adicionar definição de diagnóstico**.
4. Introduza um nome para a definição. Em seguida, selecione **arquivo para uma conta de armazenamento** e **enviar para o log Analytics**.
5. Quando lhe for pedido para configurar, selecione a conta de armazenamento e a área de trabalho do OMS que pretende utilizar para armazenar os registos de diagnóstico. **Nota**: Se não tiver uma conta de armazenamento ou a área de trabalho do OMS, siga as instruções para criar um.
6. Selecione **auditoria**, **operace RequestResponse**, e **AllMetrics**. Em seguida, defina o período de retenção para os seus dados de registo de diagnóstico. Se uma política de retenção é definida como zero, os eventos para essa categoria de registo são armazenados indefinidamente.
7. Clique em **Guardar**.

Pode demorar até duas horas antes dos dados de registo estão disponíveis para consultar e analisar. Portanto, não se preocupe se não vir qualquer coisa imediatamente.

## <a name="view-and-export-diagnostic-data-from-azure-storage"></a>Ver e exportar dados de diagnóstico do armazenamento do Azure

Armazenamento do Azure é uma solução de armazenamento de objeto robusto que está otimizada para armazenar grandes quantidades de dados não estruturados. Nesta secção, irá aprender a consultar a sua conta de armazenamento para o total de transações durante um período de tempo de 30 dias e exportar dados para o excel.

1. No portal do Azure, localize o recurso de armazenamento do Azure que criou na última secção.
2. No menu de navegação do lado esquerdo, localize **monitorização** e selecione **métricas**.
3. Utilize as listas pendentes disponíveis para configurar a sua consulta. Neste exemplo, vamos definir o intervalo de tempo **últimos 30 dias** e a métrica para **transação**.
4. Quando a consulta estiver concluída, verá uma visualização de transação durante os últimos 30 dias. Para exportar estes dados, utilize o **exportar para Excel** botão na parte superior da página.

Saiba mais sobre o que pode fazer com dados de diagnóstico no [armazenamento do Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction).

## <a name="view-logs-in-log-analytics"></a>Ver registos no Log Analytics

Siga estas instruções para explorar dados do log analytics para o seu recurso.

1. A partir do portal do Azure, localize e selecione **do Log Analytics** no menu de navegação do lado esquerdo.
2. Localize e selecione o recurso que criou quando ativar os diagnósticos.
3. Sob **gerais**, localize e selecione **registos**. Nesta página, pode executar consultas nos seus registos.

### <a name="sample-queries"></a>Amostras de consultas

Aqui estão algumas consultas de Kusto básicas que pode usar para explorar os seus dados de registo.

Execute esta consulta para registos de diagnóstico de todos os serviços cognitivos do Azure durante um período de tempo especificado:

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
```

Execute esta consulta para ver os registos mais recentes 10:

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| take 10
```

Executar esta consulta para as operações de grupo através da **recursos**:

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES" |
summarize count() by Resource
```
Execute esta consulta para encontrar o tempo médio necessário para realizar uma operação:

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| summarize avg(DurationMs)
by OperationName
```

Execute esta consulta para ver o volume de operações ao longo do tempo dividir por OperationName com contagens de posicionado para cada 10s.

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| summarize count()
by bin(TimeGenerated, 10s), OperationName
| render areachart kind=unstacked
```

## <a name="next-steps"></a>Passos Seguintes

* Para compreender como ativar o registo e também as categorias de métricas e registos que são suportadas por vários serviços do Azure, leia ambos os [descrição geral das métricas](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics) no Microsoft Azure e [descrição geral do Azure os registos de diagnóstico ](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview) artigos.
* Leia os seguintes artigos para saber mais sobre os hubs de eventos:
  * [O que é o Event Hubs do Azure?](https://docs.microsoft.com/azure/event-hubs/event-hubs-what-is-event-hubs)
  * [Introdução ao Event Hubs](https://docs.microsoft.com/azure/event-hubs/event-hubs-csharp-ephcs-getstarted)
* Leia [transferir registos de diagnóstico e métricas do armazenamento do Azure](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-dotnet#download-blobs).
* Leia [pesquisas de registos de compreender nos registos do Azure Monitor](https://docs.microsoft.com/azure/log-analytics/log-analytics-log-search-new).
