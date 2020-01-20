---
title: Analisar logs e métricas no Azure Spring Cloud | Microsoft Docs
description: Saiba como analisar dados de diagnóstico no Azure Spring Cloud
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: brendm
ms.openlocfilehash: fc1f81c616dc6ee664bb5be924f2a1586646d16d
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/19/2020
ms.locfileid: "76279160"
---
# <a name="analyze-logs-and-metrics-with-diagnostics-settings"></a>Analisar logs e métricas com configurações de diagnóstico

Usando a funcionalidade de diagnóstico do Azure Spring Cloud, você pode analisar os logs e as métricas com qualquer um dos seguintes serviços:

* Use o Azure Log Analytics, onde os dados são gravados no armazenamento do Azure. Há um atraso ao exportar logs para Log Analytics.
* Salve os logs em uma conta de armazenamento para auditoria ou inspeção manual. Você pode especificar o tempo de retenção (em dias).
* Transmita logs para o Hub de eventos para ingestão por um serviço de terceiros ou uma solução de análise personalizada.

Escolha a categoria de log e a categoria de métrica que você deseja monitorar.

## <a name="logs"></a>Registos

|Registo | Descrição |
|----|----|
| **ApplicationConsole** | Log do console de todos os aplicativos do cliente. | 
| **SystemLogs** | Atualmente, somente os logs do [servidor de configuração do Spring Cloud](https://cloud.spring.io/spring-cloud-config/reference/html/#_spring_cloud_config_server) nesta categoria. |

## <a name="metrics"></a>Métricas

Para obter uma lista completa de métricas, consulte [métricas de nuvem Spring](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-concept-metrics#user-portal-metrics-options)

Para começar, habilite um desses serviços para receber os dados. Para saber mais sobre como configurar Log Analytics, consulte [introdução ao log Analytics no Azure monitor](../azure-monitor/log-query/get-started-portal.md). 

## <a name="configure-diagnostics-settings"></a>Definir configurações de diagnóstico

1. Na portal do Azure, acesse sua instância do Azure Spring Cloud.
1. Selecione a opção **configurações de diagnóstico** e, em seguida, selecione **Adicionar configuração de diagnóstico**.
1. Insira um nome para a configuração e escolha para onde deseja enviar os logs. Você pode selecionar qualquer combinação das três opções a seguir:
    * **Arquivar em uma conta de armazenamento**
    * **Transmitir para um hub de eventos**
    * **Enviar para Log Analytics**

1. Escolha qual categoria de log e categoria de métrica você deseja monitorar e, em seguida, especifique o tempo de retenção (em dias). O tempo de retenção se aplica somente à conta de armazenamento.
1. Selecione **Guardar**.

> [!NOTE]
> Pode haver um intervalo de até 15 minutos entre o momento em que os logs ou as métricas são emitidos e quando aparecem na sua conta de armazenamento, no Hub de eventos ou Log Analytics.

## <a name="view-the-logs-and-metrics"></a>Exibir os logs e as métricas
Há vários métodos para exibir os logs e as métricas, conforme descrito nos cabeçalhos a seguir.

### <a name="use-logs-blade"></a>Folha de logs de uso

1. Na portal do Azure, acesse sua instância do Azure Spring Cloud.
1. Para abrir o painel **pesquisa de logs** , selecione **logs**.
1. Na caixa de pesquisa de **log**
   * Para exibir os logs, insira uma consulta simples, como:

    ```sql
    AppPlatformLogsforSpring
    | limit 50
    ```
   * Para exibir as métricas, insira uma consulta simples, como:

    ```sql
    AzureMetrics
    | limit 50
    ```
1. Para exibir o resultado da pesquisa, selecione **executar**.

### <a name="use-log-analytics"></a>Utilizar o Log Analytics

1. No portal do Azure, no painel esquerdo, selecione **log Analytics**.
1. Selecione o espaço de trabalho Log Analytics que você escolheu ao adicionar as configurações de diagnóstico.
1. Para abrir o painel **pesquisa de logs** , selecione **logs**.
1. Na caixa de pesquisa de **log** ,
   * Para exibir os logs, insira uma consulta simples, como:

    ```sql
    AppPlatformLogsforSpring
    | limit 50
    ```
    * Para exibir as métricas, insira uma consulta simples, como:

    ```sql
    AzureMetrics
    | limit 50
    ```

1. Para exibir o resultado da pesquisa, selecione **executar**.
1. Você pode pesquisar os logs do aplicativo ou da instância específica definindo uma condição de filtro:

    ```sql
    AppPlatformLogsforSpring
    | where ServiceName == "YourServiceName" and AppName == "YourAppName" and InstanceName == "YourInstanceName"
    | limit 50
    ```
> [!NOTE]  
> `==` diferencia maiúsculas de minúsculas, mas `=~` não é.

Para saber mais sobre a linguagem de consulta usada no Log Analytics, consulte [Azure monitor consultas de log](../azure-monitor/log-query/query-language.md).

### <a name="use-your-storage-account"></a>Usar sua conta de armazenamento 

1. No portal do Azure, no painel esquerdo, selecione contas de **armazenamento**.

1. Selecione a conta de armazenamento que você escolheu ao adicionar as configurações de diagnóstico.
1. Para abrir o painel **contêiner de blob** , selecione **BLOBs**.
1. Para examinar os logs de aplicativo, procure um contêiner chamado **insights-logs-applicationconsole**.
1. Para revisar as métricas do aplicativo, procure um contêiner chamado **insights-métricas-PT1M**.

Para saber mais sobre como enviar informações de diagnóstico para uma conta de armazenamento, consulte [armazenar e exibir dados de diagnóstico no armazenamento do Azure](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostics-extension-to-storage).

### <a name="use-your-event-hub"></a>Usar seu hub de eventos

1. No portal do Azure, no painel esquerdo, selecione hubs de **eventos**.

1. Pesquise e selecione o Hub de eventos que você escolheu quando adicionou as configurações de diagnóstico.
1. Para abrir o painel **lista de Hub de eventos** , selecione hubs de **eventos**.
1. Para examinar os logs de aplicativo, procure um hub de eventos chamado **insights-logs-applicationconsole**.
1. Para revisar as métricas do aplicativo, procure um hub de eventos chamado **insights-métricas-PT1M**.

Para saber mais sobre como enviar informações de diagnóstico para um hub de eventos, confira [Streaming diagnóstico do Azure dados no Hot Path usando os hubs de eventos](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostics-extension-stream-event-hubs).

## <a name="analyze-the-logs"></a>Analisar os logs

O Log Analytics do Azure está em execução com um mecanismo de Kusto para que você possa consultar seus logs para análise. Para obter uma breve introdução à consulta de logs usando o Kusto, consulte o [tutorial de log Analytics](../azure-monitor/log-query/get-started-portal.md).

Os logs de aplicativo fornecem informações críticas e logs detalhados sobre a integridade, o desempenho e muito mais do aplicativo. Nas próximas seções estão algumas consultas simples para ajudá-lo a entender os Estados atuais e antigos do seu aplicativo.

### <a name="show-application-logs-from-azure-spring-cloud"></a>Mostrar logs de aplicativo do Azure Spring Cloud

Para examinar uma lista de logs de aplicativo do Azure Spring Cloud, classificados por tempo com os logs mais recentes mostrados primeiro, execute a seguinte consulta:

```sql
AppPlatformLogsforSpring
| project TimeGenerated , ServiceName , AppName , InstanceName , Log
| sort by TimeGenerated desc
```

### <a name="show-logs-entries-containing-errors-or-exceptions"></a>Mostrar entradas de logs contendo erros ou exceções

Para examinar as entradas de log não classificadas que mencionam um erro ou uma exceção, execute a seguinte consulta:

```sql
AppPlatformLogsforSpring
| project TimeGenerated , ServiceName , AppName , InstanceName , Log
| where Log contains "error" or Log contains "exception"
```

Use essa consulta para localizar erros ou modificar os termos da consulta para localizar códigos de erro ou exceções específicas. 

### <a name="show-the-number-of-errors-and-exceptions-reported-by-your-application-over-the-last-hour"></a>Mostrar o número de erros e exceções relatados pelo seu aplicativo na última hora

Para criar um gráfico de pizza que exibe o número de erros e exceções registrados pelo seu aplicativo na última hora, execute a seguinte consulta:

```sql
AppPlatformLogsforSpring
| where TimeGenerated > ago(1h)
| where Log contains "error" or Log contains "exception"
| summarize count_per_app = count() by AppName
| sort by count_per_app desc
| render piechart
```

### <a name="learn-more-about-querying-application-logs"></a>Saiba mais sobre como consultar logs de aplicativos

O Azure Monitor fornece amplo suporte para consultar logs de aplicativos usando Log Analytics. Para saber mais sobre esse serviço, confira [introdução às consultas de log em Azure monitor](../azure-monitor/log-query/get-started-queries.md). Para obter mais informações sobre como criar consultas para analisar os logs do aplicativo, consulte [visão geral das consultas de log no Azure monitor](../azure-monitor/log-query/log-query-overview.md).
