---
title: Analise os registos e métricas na Nuvem de primavera de Azure  Microsoft Docs
description: Saiba como analisar dados de diagnóstico na Nuvem de primavera do Azure
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: brendm
ms.openlocfilehash: 90ff59bb76bceaacc7b0528ed0461b76832dc7fd
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77662132"
---
# <a name="analyze-logs-and-metrics-with-diagnostics-settings"></a>Analisar registos e métricas com definições de diagnóstico

Utilizando a funcionalidade de diagnóstico da Nuvem de primavera Azure, pode analisar registos e métricas com qualquer um dos seguintes serviços:

* Utilize o Azure Log Analytics, onde os dados são escritos para o Armazenamento Azure. Há um atraso na exportação de registos para log Analytics.
* Guarde os registos para uma conta de armazenamento para auditoria ou inspeção manual. Pode especificar o tempo de retenção (em dias).
* Faça logs no seu centro de eventos para ingestão por um serviço de terceiros ou uma solução de análise personalizada.

Escolha a categoria de registo e a categoria métrica que pretende monitorizar.

## <a name="logs"></a>Registos

|Registar | Descrição |
|----|----|
| **ApplicationConsole** | Registo de consolas de todas as aplicações do cliente. | 
| **SystemLogs** | Atualmente, apenas [spring cloud Config Server](https://cloud.spring.io/spring-cloud-config/reference/html/#_spring_cloud_config_server) regista nesta categoria. |

## <a name="metrics"></a>Métricas

Para obter uma lista completa de métricas, consulte [métricas](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-concept-metrics#user-portal-metrics-options) de nuvem de primavera

Para começar, permita que um destes serviços receba os dados. Para aprender sobre configurar o Log Analytics, consulte [Iniciar com Log Analytics no Monitor Azure](../azure-monitor/log-query/get-started-portal.md). 

## <a name="configure-diagnostics-settings"></a>Configurar definições de diagnóstico

1. No portal Azure, vá ao seu exemplo azure Spring Cloud.
1. Selecione a opção de **definição** de Diagnóstico e, em seguida, selecione **a definição de Adicionar Diagnósticos**.
1. Introduza um nome para a definição e, em seguida, escolha para onde pretende enviar os registos. Pode selecionar qualquer combinação das seguintes três opções:
    * **Arquivar para uma conta de armazenamento**
    * **Stream para um centro de eventos**
    * **Enviar para Log Analytics**

1. Escolha qual a categoria de registo e a categoria métrica que pretende monitorizar e, em seguida, especifique o tempo de retenção (em dias). O tempo de retenção aplica-se apenas à conta de armazenamento.
1. Selecione **Guardar**.

> [!NOTE]
> Pode haver uma lacuna de até 15 minutos entre quando os registos ou métricas são emitidos e quando aparecem na sua conta de armazenamento, no seu centro de eventos ou no Log Analytics.

## <a name="view-the-logs-and-metrics"></a>Ver os registos e métricas
Existem vários métodos para visualizar troncos e métricas conforme descrito nas seguintes rubricas.

### <a name="use-logs-blade"></a>Usar lâmina de registos

1. No portal Azure, vá ao seu exemplo azure Spring Cloud.
1. Para abrir o painel de pesquisa de **registo,** selecione **Registos**.
1. Na caixa de pesquisa **de Log**
   * Para visualizar registos, introduza uma consulta simples como:

    ```sql
    AppPlatformLogsforSpring
    | limit 50
    ```
   * Para visualizar métricas, insira uma consulta simples como:

    ```sql
    AzureMetrics
    | limit 50
    ```
1. Para ver o resultado da pesquisa, selecione **Executar**.

### <a name="use-log-analytics"></a>Utilizar o Log Analytics

1. No portal Azure, no painel esquerdo, selecione **Log Analytics**.
1. Selecione o espaço de trabalho do Log Analytics que escolheu quando adicionou as definições de diagnóstico.
1. Para abrir o painel de pesquisa de **registo,** selecione **Registos**.
1. Na caixa de pesquisa **de Log,**
   * Para visualizar registos, introduza uma consulta simples como:

    ```sql
    AppPlatformLogsforSpring
    | limit 50
    ```
    * Para visualizar métricas, insira uma consulta simples como:

    ```sql
    AzureMetrics
    | limit 50
    ```

1. Para ver o resultado da pesquisa, selecione **Executar**.
1. Pode pesquisar os registos da aplicação ou instância específica, definindo uma condição de filtro:

    ```sql
    AppPlatformLogsforSpring
    | where ServiceName == "YourServiceName" and AppName == "YourAppName" and InstanceName == "YourInstanceName"
    | limit 50
    ```
> [!NOTE]  
> `==` é sensível ao caso, mas `=~` não.

Para saber mais sobre a linguagem de consulta que é usada no Log Analytics, consulte consultas de [registo do Monitor Azure](../azure-monitor/log-query/query-language.md).

### <a name="use-your-storage-account"></a>Use a sua conta de armazenamento 

1. No portal Azure, no painel esquerdo, selecione **contas de armazenamento.**

1. Selecione a conta de armazenamento que escolheu quando adicionou as definições de diagnóstico.
1. Para abrir o painel **blob container,** selecione **Blobs**.
1. Para rever os registos de aplicações, procure um recipiente chamado **insights-logs-applicationconsole**.
1. Para rever as métricas da aplicação, procure um recipiente chamado **insights-metrics-pt1m**.

Para saber mais sobre o envio de informações de diagnóstico para uma conta de armazenamento, consulte a Loja e veja os dados de [diagnóstico no Armazenamento Azure](../storage/common/storage-introduction.md).

### <a name="use-your-event-hub"></a>Use o seu centro de eventos

1. No portal Azure, no painel esquerdo, selecione Centros de **Eventos**.

1. Procure e selecione o centro de eventos que escolheu quando adicionou as definições de diagnóstico.
1. Para abrir o painel da Lista de Hub de **Eventos,** selecione Centros de **Eventos**.
1. Para rever os registos de aplicações, procure um hub de evento chamado **insights-logs-applicationconsole**.
1. Para rever as métricas da aplicação, procure um centro de eventos chamado **insights-metrics-pt1m**.

Para saber mais sobre o envio de informações de diagnóstico para um centro de eventos, consulte os dados do [Streaming Azure Diagnostics no caminho quente, utilizando os Hubs](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostics-extension-stream-event-hubs)de Eventos .

## <a name="analyze-the-logs"></a>Analisar os registos

O Azure Log Analytics está a funcionar com um motor Kusto para que possa consultar os seus registos para análise. Para uma rápida introdução aos registos de consulta utilizando kusto, reveja o [tutorial log analytics](../azure-monitor/log-query/get-started-portal.md).

Os registos de aplicação fornecem informações críticas e registos verbosos sobre a saúde, desempenho e muito mais da sua aplicação. Nas secções seguintes estão algumas consultas simples para ajudá-lo a entender os estados atuais e passados da sua aplicação.

### <a name="show-application-logs-from-azure-spring-cloud"></a>Mostrar registos de aplicações da Nuvem de primavera azure

Para rever uma lista de registos de aplicações da Azure Spring Cloud, ordenadas por tempo com os registos mais recentes mostrados primeiro, faça a seguinte consulta:

```sql
AppPlatformLogsforSpring
| project TimeGenerated , ServiceName , AppName , InstanceName , Log
| sort by TimeGenerated desc
```

### <a name="show-logs-entries-containing-errors-or-exceptions"></a>Mostrar entradas de registos que contenham erros ou exceções

Para rever entradas de registo não sorteadas que mencionem um erro ou exceção, faça a seguinte consulta:

```sql
AppPlatformLogsforSpring
| project TimeGenerated , ServiceName , AppName , InstanceName , Log
| where Log contains "error" or Log contains "exception"
```

Utilize esta consulta para encontrar erros ou modifique os termos de consulta para encontrar códigos de erro ou exceções específicos. 

### <a name="show-the-number-of-errors-and-exceptions-reported-by-your-application-over-the-last-hour"></a>Mostre o número de erros e exceções reportados pela sua aplicação ao longo da última hora

Para criar um gráfico de tartes que apresente o número de erros e exceções registados pela sua aplicação na última hora, execute a seguinte consulta:

```sql
AppPlatformLogsforSpring
| where TimeGenerated > ago(1h)
| where Log contains "error" or Log contains "exception"
| summarize count_per_app = count() by AppName
| sort by count_per_app desc
| render piechart
```

### <a name="learn-more-about-querying-application-logs"></a>Saiba mais sobre consulta de registos de aplicações

O Azure Monitor fornece um apoio extensivo para consulta de registos de aplicações utilizando o Log Analytics. Para saber mais sobre este serviço, consulte [Começar com consultas de log no Monitor Azure](../azure-monitor/log-query/get-started-queries.md). Para mais informações sobre consultas de construção para analisar os registos de aplicações, consulte a [visão geral das consultas de registo no Monitor Azure](../azure-monitor/log-query/log-query-overview.md).
