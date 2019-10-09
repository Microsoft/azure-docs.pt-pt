---
title: Analisar logs e métricas no Azure Spring Cloud | Microsoft Docs
description: Saiba como analisar dados de diagnóstico no Azure Spring Cloud
services: spring-cloud
author: jpconnock
manager: gwallace
editor: ''
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/06/2019
ms.author: jeconnoc
ms.openlocfilehash: e6b90fb09c536f68bee7fd5d57507fe3920bcf1e
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038889"
---
# <a name="analyze-logs-and-metrics-with-diagnostic-settings"></a>Analisar logs e métricas com configurações de diagnóstico

A funcionalidade de diagnóstico do Azure Spring Cloud permite que você analise os logs e as métricas usando um dos seguintes serviços:

* Analise-os com o Azure Log Analytics, em que os dados são gravados imediatamente no Azure Log Analytics sem a necessidade de primeiro gravar os dados no armazenamento.
* Salve-os em uma conta de armazenamento para auditoria ou inspeção manual. Você pode especificar o tempo de retenção (em dias).
* Transmita-os para os hubs de eventos para ingestão por um serviço de terceiros ou uma solução de análise personalizada.

Para começar, você precisará habilitar um desses serviços para receber os dados.  Para saber mais sobre como configurar Log Analytics, leia [este tutorial](../azure-monitor/log-query/get-started-portal.md).  

## <a name="configure-diagnostic-settings"></a>Definir configurações de diagnóstico

1. Acesse sua instância do Azure Spring Cloud na portal do Azure.
1. Selecione a opção de menu **configurações de diagnóstico** .
1. Selecione o botão **Adicionar configuração de diagnóstico** .
1. Insira um nome para a configuração e escolha para onde deseja enviar os logs. Você pode selecionar qualquer combinação das três opções a seguir:
    * Arquivar numa conta de armazenamento
    * Transmitir em fluxo para um hub de eventos
    * Enviar para o Log Analytics

1. Escolha qual categoria de log e categoria de métrica você deseja monitorar e especifique o tempo de retenção (em dias). O tempo de retenção se aplica somente à conta de armazenamento.
1. Selecione **salvar** para aplicar a configuração.

> [!NOTE]
> Pode haver até 15 minutos entre o momento em que os logs ou as métricas são emitidos e quando aparecem na conta de armazenamento/Hub de eventos/Log Analytics.

## <a name="viewing-logs"></a>Ver registos

### <a name="using-log-analytics"></a>Utilizar o Log Analytics

1. Na portal do Azure, selecione Log Analytics no menu de navegação à esquerda.
1. Selecione o espaço de trabalho Log Analytics que você escolheu ao adicionar configurações de diagnóstico.
1. Selecione `Logs` para abrir a folha pesquisa de logs.
1. Insira uma consulta simples na caixa de pesquisa de log.  Por exemplo:

    ```sql
    AppPlatformLogsforSpring
    | limit 50
    ```

1. Selecione `Run` para ver o resultado da pesquisa.
1. Você pode pesquisar os logs do aplicativo ou da instância específica definindo uma condição de filtro:

    ```sql
    AppPlatformLogsforSpring
    | where ServiceName == "YourServiceName" and AppName == "YourAppName" and InstanceName == "YourInstanceName"
    | limit 50
    ```

Saiba mais sobre a linguagem de consulta usada em Log Analytics neste [artigo](../azure-monitor/log-query/query-language.md)

### <a name="using-logs-and-metrics-in-storage-account"></a>Usando logs e métricas na conta de armazenamento

1. No portal do Azure, selecione contas de armazenamento no menu de navegação à esquerda.
1. Selecione a conta de armazenamento que você escolheu ao adicionar configurações de diagnóstico.
1. Selecione a entrada `Blobs` para abrir a folha contêiner de BLOBs.
1. Localize um contêiner chamado `insights-logs-applicationconsole` para examinar os logs do aplicativo.
1. Localize um contêiner chamado `insights-metrics-pt1m` para examinar as métricas do aplicativo.

[Saiba mais sobre como enviar informações de diagnóstico para uma conta de armazenamento.](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostics-extension-to-storage)

### <a name="using-event-hubs"></a>Usando os hubs de eventos

1. No portal do Azure, selecione hubs de eventos no menu de navegação à esquerda.
1. Localize e selecione os hubs de eventos que você escolheu ao adicionar configurações de diagnóstico.
1. Selecione `Event Hubs` para abrir a folha lista de Hub de eventos.
1. Localize um hub de eventos chamado `insights-logs-applicationconsole` para examinar os logs do aplicativo.
1. Localize um hub de eventos chamado `insights-metrics-pt1m` para examinar as métricas do aplicativo.

[Saiba mais sobre como enviar informações de diagnóstico para um hub de eventos.](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostics-extension-stream-event-hubs)

## <a name="analyzing-logs"></a>Analisando logs

O Azure Log Analytics fornece Kusto para que você possa consultar os logs para análise.  Examine o [tutorial de log Analytics](../azure-monitor/log-query/get-started-portal.md) para obter uma breve introdução à consulta de logs usando o Kusto.

Os logs de aplicativo fornecem informações críticas sobre a integridade, o desempenho e muito mais do aplicativo.  Abaixo estão algumas consultas simples para ajudá-lo a entender os Estados atuais e antigos do seu aplicativo.

### <a name="show-application-logs-from-azure-spring-cloud"></a>Mostrar logs de aplicativo do Azure Spring Cloud

Para examinar uma lista de logs de aplicativo do Azure Spring Cloud, classificados por tempo com os logs mais recentes mostrados primeiro:

```sql
AppPlatformLogsforSpring
| project TimeGenerated , ServiceName , AppName , InstanceName , Log
| sort by TimeGenerated desc
```

### <a name="show-logs-entries-containing-errors-or-exceptions"></a>Mostrar entradas de logs contendo erros ou exceções

Essa consulta permite que você examine as entradas de log que mencionam um erro ou exceção.  Os resultados não são classificados.

```sql
AppPlatformLogsforSpring
| project TimeGenerated , ServiceName , AppName , InstanceName , Log
| where Log contains "error" or Log contains "exception"
```

Use essa consulta para localizar erros ou modificar os termos da consulta para localizar códigos de erro ou exceções específicas.  

### <a name="show-the-number-of-errors-and-exceptions-reported-by-your-application-over-the-last-hour"></a>Mostrar o número de erros e exceções relatados pelo seu aplicativo na última hora

Essa consulta cria um gráfico de pizza exibindo o número de erros e exceções registradas pelo seu aplicativo na última hora:

```sql
AppPlatformLogsforSpring
| where TimeGenerated > ago(1h)
| where Log contains "error" or Log contains "exception"
| summarize count_per_app = count() by AppName
| sort by count_per_app desc
| render piechart
```

### <a name="learn-more-about-querying-application-logs"></a>Saiba mais sobre como consultar logs de aplicativos

Azure Monitor fornece amplo suporte para consultar logs de aplicativos usando Log Analytics.  Para saber mais sobre esse serviço, examine o tutorial sobre [consultas de log](../azure-monitor/log-query/get-started-queries.md) usando Azure monitor. A [visão geral das consultas de log no Azure monitor](../azure-monitor/log-query/log-query-overview.md) fornece mais informações sobre a criação de consultas para analisar os logs do aplicativo.
