---
title: Analise os registos e métricas em Azure Spring Cloud Microsoft Docs
description: Saiba como analisar dados de diagnóstico em Azure Spring Cloud
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: 11f5fcd74b228fa2d57658f5c268e3bebc3c7e93
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96499531"
---
# <a name="analyze-logs-and-metrics-with-diagnostics-settings"></a>Analisar registos e métricas com definições de diagnóstico

**Este artigo aplica-se a:** ✔️ Java ✔️ C #

Utilizando a funcionalidade de diagnóstico da Azure Spring Cloud, pode analisar registos e métricas com qualquer um dos seguintes serviços:

* Utilize a Azure Log Analytics, onde os dados são escritos para o Azure Storage. Há um atraso na exportação de registos para o Log Analytics.
* Guarde os registos numa conta de armazenamento para auditoria ou inspeção manual. Pode especificar o tempo de retenção (em dias).
* Faça o streaming no seu centro de eventos para ingestão através de um serviço de terceiros ou de uma solução de análise personalizada.

Escolha a categoria de registo e a categoria métrica que pretende monitorizar.

> [!TIP]
> Só querem transmitir os vossos registos? Confira este [comando Azure CLI!](/cli/azure/ext/spring-cloud/spring-cloud/app?preserve-view=true&view=azure-cli-latest#ext-spring-cloud-az-spring-cloud-app-logs)

## <a name="logs"></a>Registos

|Registar | Descrição |
|----|----|
| **AplicaçãoConsola** | Registo de consola de todas as aplicações do cliente. |
| **SystemLogs** | Atualmente, apenas os registos [do Servidor Config da Nuvem de primavera](https://cloud.spring.io/spring-cloud-config/reference/html/#_spring_cloud_config_server) nesta categoria. |

## <a name="metrics"></a>Métricas

Para obter uma lista completa de métricas, consulte [spring cloud metrics](./spring-cloud-concept-metrics.md#user-metrics-options).

Para começar, permita que um destes serviços receba os dados. Para saber mais sobre a configuração do Log Analytics, consulte [Começar com o Log Analytics no Azure Monitor](../azure-monitor/log-query/log-analytics-tutorial.md).

## <a name="configure-diagnostics-settings"></a>Configurar configurações de diagnóstico

1. No portal Azure, vá ao seu exemplo de Azure Spring Cloud.
1. Selecione a opção **de definições de diagnóstico** e, em seguida, selecione Adicionar a **definição de diagnósticos**.
1. Insira um nome para a definição e, em seguida, escolha para onde pretende enviar os registos. Pode selecionar qualquer combinação das seguintes três opções:
    * **Arquivar para uma conta de armazenamento**
    * **Transmitir em fluxo para um hub de eventos**
    * **Enviar para o Log Analytics**

1. Escolha qual a categoria de registo e categoria métrica que pretende monitorizar e, em seguida, especifique o tempo de retenção (em dias). O tempo de retenção aplica-se apenas à conta de armazenamento.
1. Selecione **Guardar**.

> [!NOTE]
> 1. Pode haver uma lacuna de até 15 minutos entre quando os registos ou métricas são emitidos e quando aparecem na sua conta de armazenamento, no seu centro de eventos ou no Log Analytics.
> 1. Se a instância Azure Spring Cloud for eliminada ou movida, a operação não se cascata para os recursos de **definição de diagnóstico.** Os recursos **de definição de diagnóstico** devem ser eliminados manualmente antes da operação contra o seu progenitor, ou seja, a instância Azure Spring Cloud. Caso contrário, se uma nova instância Azure Spring Cloud for aprovisionada com o mesmo ID de recurso que o eliminado, ou se a instância Azure Spring Cloud for transferida para trás, os recursos de **definições** de diagnóstico anteriores continuam a alargá-lo.

## <a name="view-the-logs-and-metrics"></a>Ver os registos e métricas
Existem vários métodos para visualizar registos e métricas como descrito nas seguintes rubricas.

### <a name="use-the-logs-blade"></a>Use a lâmina de logs

1. No portal Azure, vá ao seu exemplo de Azure Spring Cloud.
1. Para abrir o painel **de pesquisa de registo,** selecione **Logs**.
1. Na caixa de pesquisa **de Tabelas**
   * Para visualizar registos, insira uma consulta simples como:

    ```sql
    AppPlatformLogsforSpring
    | limit 50
    ```
   * Para ver métricas, insira uma consulta simples como:

    ```sql
    AzureMetrics
    | limit 50
    ```
1. Para ver o resultado da pesquisa, selecione **Executar**.

### <a name="use-log-analytics"></a>Utilizar o Log Analytics

1. No portal Azure, no painel esquerdo, selecione **Log Analytics**.
1. Selecione o espaço de trabalho Log Analytics que escolheu quando adicionou as definições de diagnóstico.
1. Para abrir o painel **de pesquisa de registo,** selecione **Logs**.
1. Na caixa de pesquisa **de Tabelas,**
   * para visualizar registos, insira uma consulta simples, como:

    ```sql
    AppPlatformLogsforSpring
    | limit 50
    ```
    * para ver métricas, insira uma consulta simples, como:

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
> `==` é sensível a caso, mas `=~` não é.

Para saber mais sobre a linguagem de consulta que é usada no Log Analytics, consulte as consultas de registo do [Azure Monitor](/azure/data-explorer/kusto/query/).

### <a name="use-your-storage-account"></a>Use a sua conta de armazenamento

1. No portal Azure, encontre **as contas de Armazenamento** no painel de navegação à esquerda ou na caixa de pesquisa.
1. Selecione a conta de armazenamento que escolheu quando adicionou as definições de diagnóstico.
1. Para abrir o painel do **recipiente Blob,** selecione **Blobs**.
1. Para rever os registos de aplicações, procure um recipiente chamado **insights-logs-applicationconsole**.
1. Para rever as métricas da aplicação, procure um recipiente chamado **insights-metrics-pt1m**.

Para saber mais sobre o envio de informações de diagnóstico para uma conta de armazenamento, consulte [a Loja e veja os dados de diagnóstico no Azure Storage](../storage/common/storage-introduction.md).

### <a name="use-your-event-hub"></a>Use o seu centro de eventos

1. No portal Azure, encontre **Os Centros de Eventos** no painel de navegação ou caixa de pesquisa à esquerda.

1. Procure e selecione o centro de eventos que escolheu quando adicionou as definições de diagnóstico.
1. Para abrir o painel de lista de centros de **eventos,** selecione **Centros de Eventos**.
1. Para rever os registos de aplicações, procure um centro de eventos chamado **insights-logs-applicationconsole**.
1. Para rever as métricas da aplicação, procure um centro de eventos chamado **insights-metrics-pt1m**.

Para saber mais sobre o envio de informações de diagnóstico para um centro de [eventos, consulte os dados do Streaming Azure Diagnostics no caminho quente utilizando os Centros de Eventos.](../azure-monitor/platform/diagnostics-extension-stream-event-hubs.md)

## <a name="analyze-the-logs"></a>Analisar os registos

O Azure Log Analytics está a funcionar com um motor Kusto para que possa consultar os seus registos para análise. Para uma rápida introdução aos registos de consulta utilizando o Kusto, reveja o [tutorial do Log Analytics](../azure-monitor/log-query/log-analytics-tutorial.md).

Os registos de aplicações fornecem informações críticas e registos verbosos sobre a saúde, desempenho e muito mais da sua aplicação. Nas secções seguintes estão algumas perguntas simples para ajudá-lo a entender os estados atuais e passados da sua aplicação.

### <a name="show-application-logs-from-azure-spring-cloud"></a>Mostrar registos de aplicações da Azure Spring Cloud

Para rever uma lista de registos de aplicações da Azure Spring Cloud, ordenada por tempo com os registos mais recentes mostrados primeiro, execute a seguinte consulta:

```sql
AppPlatformLogsforSpring
| project TimeGenerated , ServiceName , AppName , InstanceName , Log
| sort by TimeGenerated desc
```

### <a name="show-logs-entries-containing-errors-or-exceptions"></a>Mostrar entradas de registos que contenham erros ou exceções

Para rever entradas de registo não pontuais que mencionem um erro ou exceção, execute a seguinte consulta:

```sql
AppPlatformLogsforSpring
| project TimeGenerated , ServiceName , AppName , InstanceName , Log
| where Log contains "error" or Log contains "exception"
```

Utilize esta consulta para encontrar erros ou modificar os termos de consulta para encontrar códigos de erro específicos ou exceções.

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

### <a name="learn-more-about-querying-application-logs"></a>Saiba mais sobre os registos de aplicações de consulta

O Azure Monitor fornece um suporte extensivo para consulta de registos de aplicações utilizando o Log Analytics. Para saber mais sobre este serviço, consulte [Começar com consultas de log no Azure Monitor.](../azure-monitor/log-query/get-started-queries.md) Para obter mais informações sobre consultas de construção para analisar os registos de aplicações, consulte [a visão geral das consultas de registo no Azure Monitor](../azure-monitor/log-query/log-query-overview.md).

## <a name="frequently-asked-questions-faq"></a>Perguntas mais frequentes (FAQ)

### <a name="how-to-convert-multi-line-java-stack-traces-into-a-single-line"></a>Como converter vestígios de pilhas de Java multi-linhas numa única linha?

Há uma solução para converter os seus traços de pilha de várias linhas numa única linha. Pode modificar a saída de registo java para reformar mensagens de traço de pilha, substituindo os caracteres newline por um símbolo. Se utilizar a biblioteca Java Logback, pode reformar as mensagens de rastreio de pilhas adicionando `%replace(%ex){'[\r\n]+', '\\n'}%nopex` o seguinte:

```xml
<configuration>
    <appender name="CONSOLE" class="ch.qos.logback.core.ConsoleAppender">
        <encoder>
            <pattern>
                level: %level, message: "%logger{36}: %msg", exceptions: "%replace(%ex){'[\r\n]+', '\\n'}%nopex"%n
            </pattern>
        </encoder>
    </appender>
    <root level="INFO">
        <appender-ref ref="CONSOLE"/>
    </root>
</configuration>
```
E, em seguida, pode substituir o token por caracteres newline novamente no Log Analytics como abaixo:

```sql
AppPlatformLogsforSpring
| extend Log = array_strcat(split(Log, '\\n'), '\n')
```
Poderá utilizar a mesma estratégia para outras bibliotecas de registos Java.

## <a name="next-steps"></a>Passos seguintes

* [Quickstart: Implemente a sua primeira aplicação Azure Spring Cloud](spring-cloud-quickstart.md)