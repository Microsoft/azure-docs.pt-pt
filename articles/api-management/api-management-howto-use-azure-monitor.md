---
title: Monitorizar as APIs publicadas na Gestão de API do Azure | Microsoft Docs
description: Siga os passos deste tutorial para aprender a monitorizar a sua API na Gestão de API do Azure.
services: api-management
author: vladvino
manager: cfowler
ms.service: api-management
ms.workload: mobile
ms.custom: mvc
ms.topic: tutorial
ms.date: 06/15/2018
ms.author: apimpm
ms.openlocfilehash: 7f6c7a651e133122dab86d6ed81572f239718b43
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86243244"
---
# <a name="monitor-published-apis"></a>Monitorizar as APIs publicadas

Com o Azure Monitor, pode visualizar, consultar, encaminhar, arquivar e tomar medidas relativamente a métricas ou registos provenientes de recursos do Azure.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Ver registos de atividades
> * Ver registos de recursos
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

A Gestão de API emite métricas a cada minuto, o que lhe permite ter visibilidade quase em tempo real sobre o estado geral e o estado de funcionamento das suas APIs. Abaixo estão as duas métricas mais usadas. Para obter uma lista de todas as métricas disponíveis, consulte [as métricas suportadas.](../azure-monitor/platform/metrics-supported.md#microsoftapimanagementservice)

* Capacidade: ajuda-o a tomar decisões sobre a atualização/degradação dos seus serviços APIM. A métrica é emitida por minuto e reflete a capacidade do gateway no momento da criação de relatórios. A métrica varia entre 0 e 100 e é calculada com base nos recursos do gateway, como a utilização da CPU e da memória.
* Pedidos: ajuda-o a analisar o tráfego da API através dos seus serviços APIM. A métrica é emitida por minuto e relata o número de pedidos de gateway com dimensões, incluindo códigos de resposta, localização, nome de hospedeiro e erros. 

> [!IMPORTANT]
> As seguintes métricas foram depreciadas a partir de maio de 2019 e serão aposentadas em agosto de 2023: Total gateway Requests, Pedidos de Gateway Bem-Sucedidos, Pedidos de Gateway Não Autorizados, Pedidos de Gateway Falhados, Outros Pedidos de Gateway. Por favor, migrar para a métrica Solicitações que fornece funcionalidade equivalente.

![gráfico de métricas](./media/api-management-azure-monitor/apim-monitor-metrics.png)

Para aceder a métricas:

1. Selecione **Métricas** no menu junto à parte inferior da página.

    ![metrics](./media/api-management-azure-monitor/api-management-metrics-blade.png)

2. Na lista pendente, selecione as métricas que lhe interessam. Por exemplo, **Pedidos**. 
3. O gráfico mostra o número total de chamadas à API.
4. O gráfico pode ser filtrado utilizando as dimensões da métrica **Solicitações.** Por exemplo, clique no **filtro Adicionar,** escolha **o Código de Resposta de Backend,** introduza 500 como valor. Agora, o gráfico mostra o número de pedidos que foram falhados no backend da API.   

## <a name="set-up-an-alert-rule-for-unauthorized-request"></a>Configurar uma regra de alerta para um pedido não autorizado

Pode configurar a receção de alertas com base em métricas e registos de atividades. O Azure Monitor permite-lhe configurar um alerta para fazer o seguinte quando é acionado:

* Enviar uma notificação por e-mail
* Chamar um webhook
* Invocar uma Aplicação Lógica do Azure

Para configurar alertas:

1. Selecione **Alertas** da barra de menus perto da parte inferior da página.

    ![alerts](./media/api-management-azure-monitor/alert-menu-item.png)

2. Clique numa **nova regra de alerta** para este alerta.
3. Clique na **condição de adicionar**.
4. Selecione **métricas** no tipo sinal cair para baixo.
5. Selecione **Pedido de Gateway Não Autorizado** como o sinal para monitorizar.

    ![alerts](./media/api-management-azure-monitor/signal-type.png)

6. Na **visão lógica do sinal de configuração,** especifique um limiar após o qual o alerta deve ser acionado e clique em **'Fazer'.**

    ![alerts](./media/api-management-azure-monitor/threshold.png)

7. Selecione um Grupo de Ação existente ou crie um novo. No exemplo abaixo, será enviado um e-mail para os administradores. 

    ![alerts](./media/api-management-azure-monitor/action-details.png)

8. Forneça um nome, descrição da regra de alerta e escolha o nível de gravidade. 
9. Pressione **Criar regra de alerta**.
10. Agora, tente ligar para a Conferência API sem uma chave API. O alerta será desencadeado e o e-mail será enviado para os administradores. 

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

## <a name="resource-logs"></a>Registos de Recursos

Os registos de recursos fornecem informações ricas sobre operações e erros que são importantes para a auditoria, bem como para efeitos de resolução de problemas. Os registos de recursos diferem dos registos de atividade. Os registos de atividade fornecem informações sobre as operações que foram realizadas nos seus recursos Azure. Os registos de recursos fornecem informações sobre as operações que o seu recurso realizou.

Para configurar registos de recursos:

1. Selecione a instância de serviço APIM.
2. Clique em **Definições de diagnóstico**.

    ![registos de recursos](./media/api-management-azure-monitor/api-management-diagnostic-logs-blade.png)

3. Clique em **Ativar diagnósticos**. Pode arquivar registos de recursos juntamente com métricas para uma conta de armazenamento, transmiti-los para um Centro de Eventos ou enviá-los para registos do Azure Monitor. 

A API Management fornece atualmente registos de recursos (lotados por hora) sobre cada pedido de API individual, tendo cada entrada o seguinte esquema:

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
| hora | date-time | Timetamp de quando o gateway começa a processar o pedido |
| operationName | string | Valor constante “Microsoft.ApiManagement/GatewayLogs” |
| categoria | string | Valor constante “GatewayLogs” |
| durationMs | número inteiro | Número de milissegundos a partir do momento em que gateway recebeu pedido até ao momento em que a resposta foi enviada na íntegra. Inclui clienTime, cacheTime e backendTime. |
| callerIpAddress | string | Endereço IP do chamador de Gateway de imediato (pode ser um intermediário) |
| correlationId | string | Identificador de pedido http exclusivo atribuído pela Gestão de API |
| localização | string | Nome da região do Azure em que o Gateway que processou o pedido estava localizado |
| httpStatusCodeCategory | string | Categoria do código de estado da resposta http: bem-sucedida (301 ou menos ou 304 ou 307), não autorizada (401, 403, 429), Errónea (400, entre 500 e 600), Outro |
| resourceId | string | ID do recurso de Gestão da API /SUBSCRIÇÕES/ \<subscription> /RESOURCEGROUPS/ \<resource-group> /PROVIDERS/MICROSOFT. APIMANAGEMENT/SERVICE/\<name> |
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
| elapsed | número inteiro | Número de milissegundos decorrido entre quando o portal recebeu o pedido e o momento em que ocorreu o erro | 
| source | string | Nome da política ou processador interno provocou o erro | 
| scope | string | Âmbito do documento da política que contém a política que provocou o erro | 
| section | string | Secção do documento da política que contém a política que provocou o erro | 
| reason | string | Motivo do erro | 
| message | string | Mensagem de erro | 

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Ver registos de atividades
> * Ver registos de recursos
> * Ver métricas da API
> * Configurar uma regra de alerta quando a API recebe chamadas não autorizadas

Avance para o tutorial seguinte:

> [!div class="nextstepaction"]
> [Rastrear chamadas](api-management-howto-api-inspector.md)
