---
title: Monitorizar as APIs publicadas na Gestão de API do Azure | Microsoft Docs
description: Siga os passos deste tutorial para aprender a monitorizar a sua API na Gestão de API do Azure.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 06/15/2018
ms.author: apimpm
ms.openlocfilehash: 6ecce4dc97272f03a3151708cd9c047212c36e03
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74707207"
---
# <a name="monitor-published-apis"></a>Monitorizar as APIs publicadas

Com o Azure Monitor, pode visualizar, consultar, encaminhar, arquivar e tomar medidas relativamente a métricas ou registos provenientes de recursos do Azure.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Ver registos de atividade
> * Ver registos de diagnóstico
> * Ver métricas da API 
> * Configurar uma regra de alerta quando a API recebe chamadas não autorizadas

O vídeo seguinte mostra como monitorizar a Gestão de API através do Azure Monitor. 

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Monitor-API-Management-with-Azure-Monitor/player]

## <a name="prerequisites"></a>Pré-requisitos

+ Conhecer a [terminologia da Gestão de API do Azure](api-management-terminology.md).
+ Conclua o guia de início rápido seguinte: [Criar uma instância da Gestão de API do Azure](get-started-create-service-instance.md).
+ Conclua também o tutorial seguinte: [Importar e publicar a sua primeira API](import-and-publish.md).

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="view-metrics-of-your-apis"></a>Ver métricas das APIs

A Gestão de API emite métricas a cada minuto, o que lhe permite ter visibilidade quase em tempo real sobre o estado geral e o estado de funcionamento das suas APIs. Segue-se um resumo de algumas das métricas disponíveis:

* Capacidade (pré-visualização): ajuda-o a tomar decisões sobre a atualização/mudança para uma versão anterior dos serviços APIM. A métrica é emitida por minuto e reflete a capacidade do gateway no momento da criação de relatórios. A métrica varia entre 0 e 100 e é calculada com base nos recursos do gateway, como a utilização da CPU e da memória.
* Total de Pedidos do Gateway: número de pedidos da API num determinado período. 
* Pedidos do Gateway Com Êxito: número de pedidos da API que receberam códigos de resposta HTTP com êxito, incluindo 304, 307 e tudo o que for inferior a 301 (por exemplo, 200).
* Pedidos do Gateway Falhados: número de pedidos da API que receberam códigos de resposta HTTP erróneos, incluindo 400 e tudo o que for superior a 500.
* Pedidos do Gateway Não Autorizados: número de pedidos da API que receberam códigos de resposta HTTP, incluindo 401, 403 e 429.
* Outros Pedidos do Gateway: número de pedidos da API que receberam códigos de resposta HTTP que não pertencem a nenhuma das categorias anteriores (por exemplo, 418).

![gráfico de métricas](./media/api-management-azure-monitor/apim-monitor-metrics.png)

Para aceder a métricas:

1. Selecione **Métricas** no menu junto à parte inferior da página.

    ![métricas](./media/api-management-azure-monitor/api-management-metrics-blade.png)

1. Na lista pendente, selecione as métricas que lhe interessam. Por exemplo, **solicitações**. 
1. O gráfico mostra o número total de chamadas à API.
1. O gráfico pode ser filtrado usando as dimensões da métrica de **solicitações** . Por exemplo, clique em **Adicionar filtro**, escolha **código de resposta de back-end**, insira 500 como o valor. Agora, o gráfico mostra o número de solicitações que falharam no back-end da API.   

## <a name="set-up-an-alert-rule-for-unauthorized-request"></a>Configurar uma regra de alerta para um pedido não autorizado

Pode configurar a receção de alertas com base em métricas e registos de atividades. O Azure Monitor permite-lhe configurar um alerta para fazer o seguinte quando é acionado:

* Enviar uma notificação por e-mail
* Chamar um webhook
* Invocar uma Aplicação Lógica do Azure

Para configurar alertas:

1. Selecione **alertas** na barra de menus próxima à parte inferior da página.

    ![alerts](./media/api-management-azure-monitor/alert-menu-item.png)

2. Clique em uma **nova regra de alerta** para este alerta.
3. Clique em **Adicionar condição**.
4. Selecione **métricas** na lista suspensa tipo de sinal.
5. Selecione **solicitação de gateway não autorizado** como o sinal a ser monitorado.

    ![alerts](./media/api-management-azure-monitor/signal-type.png)

6. Na exibição de **lógica de sinal de configuração** , especifique um limite após o qual o alerta deve ser disparado e clique em **concluído**.

    ![alerts](./media/api-management-azure-monitor/threshold.png)

7. Selecione um grupo de ações existente ou crie um novo. No exemplo a seguir, um email será enviado para os administradores. 

    ![alerts](./media/api-management-azure-monitor/action-details.png)

8. Forneça um nome, uma descrição da regra de alerta e escolha o nível de severidade. 
9. Pressione **criar regra de alerta**.
10. Agora, tente chamar a API de conferência sem uma chave de API. O alerta será disparado e o email será enviado aos administradores. 

## <a name="activity-logs"></a>Registos de Atividade

Os registos de atividades fornecem informações aprofundadas sobre as operações executadas nos serviços de Gestão de API. Ao utilizar registos de atividades, pode determinar o "quê, quem e quando" de quaisquer operações de escrita (PUT, POST, DELETE) efetuadas nos seus serviços de Gestão de API.

> [!NOTE]
> Os registos de atividades não incluem operações de leitura (GET) nem operações executadas no Portal do Azure ou com as APIs de Gestão originais.

Pode aceder aos registos de atividades no serviço de Gestão de API ou aceder aos registos de todos os recursos do Azure no Azure Monitor. 

![registos de atividades](./media/api-management-azure-monitor/apim-monitor-activity-logs.png)

Para ver registos de atividades:

1. Selecione a instância de serviço APIM.
2. Clique em **Registo de atividades**.

    ![registo de atividades](./media/api-management-azure-monitor/api-management-activity-logs-blade.png)

3. Selecione o âmbito de filtragem pretendido e clique em **Aplicar**.

## <a name="diagnostic-logs"></a>Registos de diagnóstico

Os registos de diagnóstico fornecem informações avançadas sobre operações e erros que são importantes para auditoria, bem como para fins de resolução de problemas. Os registos de diagnóstico diferem dos registos de atividades. Os registos de atividades fornecem informações aprofundadas sobre as operações executadas nos recursos do Azure. Os registos de diagnóstico fornecem informações aprofundadas sobre as operações executadas pelo recurso.

Para configurar os registos de diagnóstico:

1. Selecione a instância de serviço APIM.
2. Clique em **Definições de diagnóstico**.

    ![registos de diagnóstico](./media/api-management-azure-monitor/api-management-diagnostic-logs-blade.png)

3. Clique em **Ativar diagnósticos**. Você pode arquivar logs de diagnóstico junto com as métricas para uma conta de armazenamento, transmiti-los para um hub de eventos ou enviá-los para Azure Monitor logs. 

Atualmente, a Gestão de API disponibiliza registos de diagnósticos (batches criados de hora a hora) sobre pedidos à API individuais tendo cada entrada o esquema seguinte:

```json
{  
    "isRequestSuccess" : "",
    "time": "",
    "operationName": "",
    "category": "",
    "durationMs": ,
    "callerIpAddress": "",
    "correlationId": "",
    "location": "",
    "httpStatusCodeCategory": "",
    "resourceId": "",
    "properties": {   
        "method": "", 
        "url": "", 
        "clientProtocol": "", 
        "responseCode": , 
        "backendMethod": "", 
        "backendUrl": "", 
        "backendResponseCode": ,
        "backendProtocol": "",  
        "requestSize": , 
        "responseSize": , 
        "cache": "", 
        "cacheTime": "", 
        "backendTime": , 
        "clientTime": , 
        "apiId": "",
        "operationId": "", 
        "productId": "", 
        "userId": "", 
        "apimSubscriptionId": "", 
        "backendId": "",
        "lastError": { 
            "elapsed" : "", 
            "source" : "", 
            "scope" : "", 
            "section" : "" ,
            "reason" : "", 
            "message" : ""
        } 
    }      
}  
```

| Propriedade  | Tipo | Descrição |
| ------------- | ------------- | ------------- |
| isRequestSuccess | boolean | “Verdadeiro” se o pedido HTTP for concluído com o código de estado de resposta no intervalo 2xx ou 3xx |
| hora | date-time | Carimbo de data/hora de quando o gateway começa a processar a solicitação |
| operationName | string | Valor constante “Microsoft.ApiManagement/GatewayLogs” |
| categoria | string | Valor constante “GatewayLogs” |
| durationMs | número inteiro | Número de milissegundos do gateway de momento recebido a solicitação até que a resposta do momento seja enviada por completo. Inclui clienTime, CacheTime e back-EndTime. |
| callerIpAddress | string | Endereço IP do chamador de Gateway de imediato (pode ser um intermediário) |
| correlationId | string | Identificador de pedido http exclusivo atribuído pela Gestão de API |
| localização | string | Nome da região do Azure em que o Gateway que processou o pedido estava localizado |
| httpStatusCodeCategory | string | Categoria do código de estado da resposta http: bem-sucedida (301 ou menos ou 304 ou 307), não autorizada (401, 403, 429), Errónea (400, entre 500 e 600), Outro |
| resourceId | string | ID do recurso de gerenciamento de API/SUBSCRIPTIONS/\<assinatura >/RESOURCEGROUPS/\<grupo de recursos >/PROVIDERS/MICROSOFT. Nome do APIMANAGEMENT/serviço/\< |
| propriedades | objeto | Propriedades do pedido atual |
| método | string | Método HTTP do pedido a receber |
| url | string | URL do pedido a receber |
| clientProtocol | string | Versão do protocolo HTTP do pedido a receber |
| responseCode | número inteiro | Código de estado da resposta  HTTP enviada para um cliente |
| backendMethod | string | Método HTTP do pedido enviado para um back-end |
| backendUrl | string | URLdo pedido enviado para um back-end |
| backendResponseCode | número inteiro | Código da resposta HTTP recebido a partir de um back-end |
| backendProtocol | string | Versão do protocolo HTTP do pedido enviado para um back-end | 
| requestSize | número inteiro | Número de bytes recebidos de um cliente durante o processamento do pedido | 
| responseSize | número inteiro | Número de bytes enviados para um cliente durante o processamento do pedido | 
| cache | string | Estado do envolvimento da cache da Gestão de API no processamento do pedido (ou seja, hit, miss, none) | 
| cacheTime | número inteiro | Número de milissegundos despendidos na E/S de cache da Gestão de API global (ligar, enviar e receber bytes) | 
| backendTime | número inteiro | Número de milissegundos despendidos na E/S de back-end global (ligar, enviar e receber bytes) | 
| clientTime | número inteiro | Número de milissegundos despendidos na E/S de cliente global (ligar, enviar e receber bytes) | 
| apiId | string | Identificador da entidade de API do pedido atual | 
| operationId | string | Identificador da entidade de operação do pedido atual | 
| productId | string | Identificador da entidade de produto do pedido atual | 
| userId | string | Identificador da entidade de utilizador do pedido atual | 
| apimSubscriptionId | string | Identificador da entidade de subscrição do pedido atual | 
| backendId | string | Identificador da entidade de back-end do pedido atual | 
| LastError | objeto | Último erro de processamento de pedido | 
| elapsed | número inteiro | Número de milissegundos decorridos entre o momento em que o Gateway recebeu a solicitação e o momento em que o erro ocorreu | 
| source | string | Nome da política ou processador interno provocou o erro | 
| scope | string | Âmbito do documento da política que contém a política que provocou o erro | 
| section | string | Secção do documento da política que contém a política que provocou o erro | 
| reason | string | Motivo do erro | 
| message | string | Mensagem de erro | 

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Ver registos de atividade
> * Ver registos de diagnóstico
> * Ver métricas da API
> * Configurar uma regra de alerta quando a API recebe chamadas não autorizadas

Avance para o tutorial seguinte:

> [!div class="nextstepaction"]
> [Rastrear chamadas](api-management-howto-api-inspector.md)
