---
title: Tutorial - Monitor publicado APIs em Azure API Management / Microsoft Docs
description: Siga os passos deste tutorial para aprender a usar métricas, alertas, registos de atividades e registos de recursos para monitorizar as suas APIs na Azure API Management.
services: api-management
author: vladvino
ms.service: api-management
ms.custom: mvc
ms.topic: tutorial
ms.date: 10/14/2020
ms.author: apimpm
ms.openlocfilehash: 031b3bfd30e0272589c5c7ee2c546b9459767b33
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96183197"
---
# <a name="tutorial-monitor-published-apis"></a>Tutorial: Monitor publicado APIs

Com o Azure Monitor, pode visualizar, consultar, encaminhar, arquivar e tomar ações sobre as métricas ou registos provenientes do seu serviço de Gestão API Azure.

Neste tutorial, vai aprender a:

> [!div class="checklist"]
> * Ver métricas da API 
> * Estabeleça uma regra de alerta 
> * Ver registos de atividades
> * Ativar e visualizar registos de recursos

## <a name="prerequisites"></a>Pré-requisitos

+ Conhecer a [terminologia da Gestão de API do Azure](api-management-terminology.md).
+ Conclua o guia de início rápido seguinte: [Criar uma instância da Gestão de API do Azure](get-started-create-service-instance.md).
+ Conclua também o tutorial seguinte: [Importar e publicar a sua primeira API](import-and-publish.md).

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="view-metrics-of-your-apis"></a>Ver métricas das APIs

A API Management emite [métricas](../azure-monitor/platform/data-platform-metrics.md) a cada minuto, dando-lhe quase visibilidade em tempo real para o estado e saúde das suas APIs. Seguem-se as duas métricas mais utilizadas. Para obter uma lista de todas as métricas disponíveis, consulte [as métricas suportadas.](../azure-monitor/platform/metrics-supported.md#microsoftapimanagementservice)

* **Capacidade** - ajuda-o a tomar decisões sobre a atualização/degradação dos seus serviços APIM. A métrica é emitida por minuto e reflete a capacidade do gateway no momento da criação de relatórios. A métrica varia entre 0 e 100 e é calculada com base nos recursos do gateway, como a utilização da CPU e da memória.
* **Pedidos** - ajuda a analisar o tráfego de API através dos seus serviços de Gestão de API. A métrica é emitida por minuto e relata o número de pedidos de gateway com dimensões, incluindo códigos de resposta, localização, nome de hospedeiro e erros. 

> [!IMPORTANT]
> As seguintes métricas foram depreciadas a partir de maio de 2019 e serão aposentadas em agosto de 2023: Total gateway Requests, Pedidos de Gateway Bem-Sucedidos, Pedidos de Gateway Não Autorizados, Pedidos de Gateway Falhados, Outros Pedidos de Gateway. Por favor, migrar para a métrica Solicitações que fornece funcionalidade equivalente.

:::image type="content" source="media/api-management-howto-use-azure-monitor/apim-monitor-metrics.png" alt-text="Screenshot de Métricas em Visão Geral de Gestão da API":::

Para aceder a métricas:

1. No [portal Azure,](https://portal.azure.com)navegue para o seu caso de Gestão API. Na página **'Visão Geral',** reveja as métricas-chave para as suas APIs.
1. Para investigar as métricas em detalhe, selecione **Métricas** do menu perto da parte inferior da página.

    :::image type="content" source="media/api-management-howto-use-azure-monitor/api-management-metrics-blade.png" alt-text="Screenshot do item métricas no menu de monitorização":::

1. Na lista pendente, selecione as métricas que lhe interessam. Por exemplo, **Pedidos**. 
1. O gráfico mostra o número total de chamadas à API.
1. O gráfico pode ser filtrado utilizando as dimensões da métrica **Solicitações.** Por exemplo, **selecione Adicionar filtro**, selecione **Backend Response Code Category**, introduza 500 como valor. Agora, o gráfico mostra o número de pedidos que foram falhados no backend da API.   

## <a name="set-up-an-alert-rule"></a>Estabeleça uma regra de alerta 

Pode receber [alertas](../azure-monitor/platform/alerts-metric-overview.md) com base em métricas e registos de atividade. O Azure Monitor [permite-lhe configurar um alerta](../azure-monitor/platform/alerts-metric.md) para fazer o seguinte quando ativa:

* Enviar uma notificação por e-mail
* Chamar um webhook
* Invocar uma Aplicação Lógica do Azure

Para configurar uma regra de alerta de exemplo com base numa métrica de pedido:

1. No [portal Azure,](https://portal.azure.com)navegue para o seu caso de Gestão API.
1. Selecione **Alertas** da barra de menus perto da parte inferior da página.

    :::image type="content" source="media/api-management-howto-use-azure-monitor/alert-menu-item.png" alt-text="Screenshot da opção Alertas no menu de monitorização":::

1. Selecione **+ Nova regra de alerta**.
1. Na janela **de regras de alerta Criar,** **selecione a condição**.
1. Na janela lógica de **sinal de configuração:**
    1. No **tipo sinal,** selecione **Métricas**.
    1. No **nome Signal**, selecione **Pedidos**.
    1. Em **Divisão por dimensões,** em **nome Dimension,** selecione **Gateway Response Code Category**.
    1. Em **Valores de Dimensão**, selecione **4xx,** para erros do cliente, tais como pedidos não autorizados ou inválidos.
    1. Na **lógica de alerta**, especifique um limiar após o qual o alerta deve ser acionado e selecione **Feito**.

    :::image type="content" source="media/api-management-howto-use-azure-monitor/threshold.png" alt-text="Screenshot das janelas Configure Signal Logic":::

1. Selecione um grupo de ação existente ou crie um novo. No exemplo seguinte, é criado um novo grupo de ação. Será enviado um e-mail de notificação para admin@contoso.com . 

    :::image type="content" source="media/api-management-howto-use-azure-monitor/action-details.png" alt-text="Screenshot das notificações para novo grupo de ação":::

1. Introduza um nome e descrição da regra de alerta e selecione o nível de gravidade. 
1. Selecione **Criar regra de alerta**.
1. Agora, teste a regra de alerta chamando a Conferência API sem uma chave API. Por exemplo:

    ```bash
    curl GET https://apim-hello-world.azure-api.net/conference/speakers HTTP/1.1 
    ```

    Um alerta será desencadeado com base no período de avaliação, e o e-mail será enviado para admin@contoso.com . 

    Também aparecem alertas na página **alertas** para a instância de Gestão da API.

    :::image type="content" source="media/api-management-howto-use-azure-monitor/portal-alerts.png" alt-text="Screenshot de alertas no portal":::

## <a name="activity-logs"></a>Registos de atividade

Os registos de atividades fornecem informações aprofundadas sobre as operações executadas nos serviços de Gestão de API. Ao utilizar registos de atividades, pode determinar o "quê, quem e quando" de quaisquer operações de escrita (PUT, POST, DELETE) efetuadas nos seus serviços de Gestão de API.

> [!NOTE]
> Os registos de atividades não incluem operações de leitura (GET) nem operações executadas no Portal do Azure ou com as APIs de Gestão originais.

Pode aceder aos registos de atividades no serviço de Gestão de API ou aceder aos registos de todos os recursos do Azure no Azure Monitor. 

:::image type="content" source="media/api-management-howto-use-azure-monitor/api-management-activity-logs.png" alt-text="Screenshot do login de atividade no portal":::

Para ver o registo de atividade:

1. No [portal Azure,](https://portal.azure.com)navegue para o seu caso de Gestão API.

1. Selecione **registo de atividades**.

    :::image type="content" source="media/api-management-howto-use-azure-monitor/api-management-activity-logs-blade.png" alt-text="Screenshot do item de registo de atividade no menu de monitorização":::
1. Selecione o âmbito de filtragem pretendido e, em seguida, **aplique**.

## <a name="resource-logs"></a>Registos do recurso

Os registos de recursos fornecem informações ricas sobre operações e erros que são importantes para a auditoria, bem como para efeitos de resolução de problemas. Os registos de recursos diferem dos registos de atividade. O registo de atividades fornece informações sobre as operações que foram realizadas nos seus recursos Azure. Os registos de recursos fornecem informações sobre as operações que o seu recurso realizou.

Para configurar registos de recursos:

1. No [portal Azure,](https://portal.azure.com)navegue para o seu caso de Gestão API.
2. Selecione **definições de diagnóstico**.

    :::image type="content" source="media/api-management-howto-use-azure-monitor/api-management-diagnostic-logs-blade.png" alt-text="Screenshot do item de definições de diagnóstico no menu de monitorização":::

1. **Selecione + Adicione a definição de diagnóstico**.
1. Selecione os registos ou métricas que pretende recolher.

   Pode arquivar registos de recursos juntamente com métricas para uma conta de armazenamento, transmiti-los para um Centro de Eventos ou enviá-los para um espaço de trabalho Log Analytics. 

Para obter mais informações, consulte [Criar configurações de diagnóstico para enviar registos e métricas da plataforma para diferentes destinos.](../azure-monitor/platform/diagnostic-settings.md)

## <a name="view-diagnostic-data-in-azure-monitor"></a>Ver dados de diagnóstico no Azure Monitor

Se ativar a recolha de GatewayLogs ou métricas num espaço de trabalho do Log Analytics, pode demorar alguns minutos para que os dados apareçam no Azure Monitor. Para ver os dados:

1. No [portal Azure,](https://portal.azure.com)navegue para o seu caso de Gestão API.
1. Selecione **Registos** do menu perto da parte inferior da página.

    :::image type="content" source="media/api-management-howto-use-azure-monitor/logs-menu-item.png" alt-text="Screenshot do item de Logs no menu de monitorização":::

Executar consultas para ver os dados. Várias consultas de amostra são [fornecidas,](../azure-monitor/log-query/example-queries.md) ou executar as suas próprias. Por exemplo, a seguinte consulta recupera as 24 horas mais recentes de dados da tabela GatewayLogs:

```kusto
ApiManagementGatewayLogs
| where TimeGenerated > ago(1d) 
```

Para obter mais informações sobre a utilização de registos de recursos para a Gestão da API, consulte:

* [Começa com o Azure Monitor Log Analytics,](../azure-monitor/log-query/log-analytics-tutorial.md)ou experimente o [ambiente de demonstração do Log Analytics](https://portal.loganalytics.io/demo).

* [Visão geral das consultas de registo no Azure Monitor](../azure-monitor/log-query/log-query-overview.md).

O seguinte JSON indica uma entrada de amostra em GatewayLogs para um pedido de API bem sucedido. Para mais detalhes, consulte a [referência de esquema](gateway-log-schema-reference.md). 

```json
{
    "Level": 4,
    "isRequestSuccess": true,
    "time": "2020-10-14T17:xx:xx.xx",
    "operationName": "Microsoft.ApiManagement/GatewayLogs",
    "category": "GatewayLogs",
    "durationMs": 152,
    "callerIpAddress": "xx.xx.xxx.xx",
    "correlationId": "3f06647e-xxxx-xxxx-xxxx-530eb9f15261",
    "location": "East US",
    "properties": {
        "method": "GET",
        "url": "https://apim-hello-world.azure-api.net/conference/speakers",
        "backendResponseCode": 200,
        "responseCode": 200,
        "responseSize": 41583,
        "cache": "none",
        "backendTime": 87,
        "requestSize": 526,
        "apiId": "demo-conference-api",
        "operationId": "GetSpeakers",
        "apimSubscriptionId": "master",
        "clientTime": 65,
        "clientProtocol": "HTTP/1.1",
        "backendProtocol": "HTTP/1.1",
        "apiRevision": "1",
        "clientTlsVersion": "1.2",
        "backendMethod": "GET",
        "backendUrl": "https://conferenceapi.azurewebsites.net/speakers"
    },
    "resourceId": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group>/PROVIDERS/MICROSOFT.APIMANAGEMENT/SERVICE/APIM-HELLO-WORLD"
}
```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Ver métricas da API
> * Estabeleça uma regra de alerta 
> * Ver registos de atividades
> * Ativar e visualizar registos de recursos


Avance para o tutorial seguinte:

> [!div class="nextstepaction"]
> [Rastrear chamadas](api-management-howto-api-inspector.md)