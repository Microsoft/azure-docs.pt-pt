---
title: Aplicativos geridos com notificações
description: Configure aplicações geridas com pontos finais webhook para receber notificações sobre criações, atualizações, eliminações e erros nas instâncias de aplicação geridas.
ms.topic: conceptual
ms.author: ilahat
author: ilahat
ms.date: 11/01/2019
ms.openlocfilehash: ff058d7b51bd2e5efd80db69e5928d58fc5a7725
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76715682"
---
# <a name="azure-managed-applications-with-notifications"></a>O Azure geriu aplicações com notificações

As notificações de aplicação geridas pela Azure permitem aos editores automatizar ações baseadas em eventos de ciclo de vida das instâncias de aplicação geridas. Os editores podem especificar pontos finais de webhook de notificação personalizada para receber notificações de eventos sobre novas e existentes instâncias de aplicação geridas. Os editores podem configurar fluxos de trabalho personalizados no momento do fornecimento de aplicações, atualizações e eliminação.

## <a name="getting-started"></a>Introdução
Para começar a receber aplicações geridas, rode um ponto final HTTPS público e especifique-o quando publicar a definição de aplicação do catálogo de serviços ou a oferta do Azure Marketplace.

Aqui estão os passos recomendados para começar rapidamente:
1. Rode um ponto final HTTPS público que inicie os `200 OK`pedidos e devoluções do POST que chegam .
2. Adicione o ponto final à definição de aplicação do catálogo de serviços ou à oferta do Azure Marketplace, conforme explicado mais tarde neste artigo.
3. Crie uma instância de aplicação gerida que refira a definição de candidatura ou oferta do Azure Marketplace.
4. Valide que as notificações estão a ser recebidas.
5. Ativar a autorização tal como explicado na secção de **autenticação Endpoint** deste artigo.
6. Siga as instruções na secção **de informação** de notificação deste artigo para analisar os pedidos de notificação e implementar a sua lógica de negócio com base na notificação.

## <a name="add-service-catalog-application-definition-notifications"></a>Adicionar notificações de definição de definição de aplicação de catálogo de serviços
#### <a name="azure-portal"></a>Portal do Azure
Para começar, consulte A Publicação de uma aplicação de catálogo de [serviços através do portal Azure](./publish-portal.md).

![Notificações de definição de aplicação de catálogo de serviços no portal Azure](./media/publish-notifications/service-catalog-notifications.png)

#### <a name="rest-api"></a>API REST

> [!NOTE]
> Atualmente, só pode fornecer um `notificationEndpoints` ponto final nas propriedades de definição de aplicação.

``` JSON
    {
      "properties": {
        "isEnabled": true,
        "lockLevel": "ReadOnly",
        "displayName": "Sample Application Definition",
        "description": "Notification-enabled application definition.",
        "notificationPolicy": {
            "notificationEndpoints": [
                {
                    "uri": "https://isv.azurewebsites.net:1214?sig=unique_token"
                }
            ]
        },
        "authorizations": [
          {
            "principalId": "d6b7fbd3-4d99-43fe-8a7a-f13aef11dc18",
            "roleDefinitionId": "8e3af657-a8ff-443c-a75c-2fe8c4bcb635"
          },
        ...

```
## <a name="add-azure-marketplace-managed-application-notifications"></a>Adicionar notificações de aplicações geridas pelo Azure Marketplace
Para mais informações, consulte Criar uma oferta de [aplicação Azure.](../../marketplace/cloud-partner-portal/azure-applications/cpp-create-offer.md)

![Azure Marketplace geriu notificações de aplicações no portal Azure](./media/publish-notifications/marketplace-notifications.png)
## <a name="event-triggers"></a>Acionadores de eventos
A tabela seguinte descreve todas as combinações possíveis de EventType e ProvisioningState e seus gatilhos:

Tipo de evento | ProvisioningState | Gatilho para notificação
---|---|---
PUT | Aceite | O grupo de recursos geridos foi criado e projetado com sucesso após a aplicação PUT (antes da implantação dentro do grupo de recursos geridos ser iniciada).
PUT | Bem-sucedido | O fornecimento integral da aplicação gerida foi bem sucedido após um PUT.
PUT | Falhou | Falha no fornecimento de instâncias de aplicação em qualquer ponto.
PATCH | Bem-sucedido | Após um PATCH bem sucedido na instância de aplicação gerida para atualizar tags, política de acesso JIT ou identidade gerida.
DELETE | Apagando | Assim que o utilizador iniciar um DELETE de uma instância de aplicação gerida.
DELETE | Eliminado | Após a supressão completa e bem sucedida da aplicação gerida.
DELETE | Falhou | Após qualquer erro durante o processo de desprovisionamento que bloqueia a eliminação.
## <a name="notification-schema"></a>Esquema de notificação
Quando girar o seu ponto final do webhook para lidar com as notificações, terá de analisar a carga útil para obter propriedades importantes para, em seguida, agir após a notificação. O catálogo de serviços e as notificações de aplicações geridas pelo Azure Marketplace fornecem muitas das mesmas propriedades. Duas pequenas diferenças são descritas na tabela que segue as amostras.

#### <a name="service-catalog-application-notification-schema"></a>Esquema de notificação de pedido de catálogo de serviços
Aqui está uma notificação de catálogo de serviço de amostra após o fornecimento bem sucedido de uma instância de aplicação gerida:
``` HTTP
POST https://{your_endpoint_URI}/resource?{optional_parameter}={optional_parameter_value} HTTP/1.1

{
    "eventType": "PUT",
    "applicationId": "subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applications/<applicationName>",
    "eventTime": "2019-08-14T19:20:08.1707163Z",
    "provisioningState": "Succeeded",
    "applicationDefinitionId": "subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applicationDefinitions/<appDefName>"    
}

```

Se o fornecimento falhar, uma notificação com os dados de erro será enviada para o ponto final especificado.

``` HTTP
POST https://{your_endpoint_URI}/resource?{optional_parameter}={optional_parameter_value} HTTP/1.1

{
    "eventType": "PUT",
    "applicationId": "subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applications/<applicationName>",
    "eventTime": "2019-08-14T19:20:08.1707163Z",
    "provisioningState": "Failed",
    "applicationDefinitionId": "subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applicationDefinitions/<appDefName>",
    "error": {
        "code": "ErrorCode",
        "message": "error message",
        "details": [
            {
                "code": "DetailedErrorCode",
                "message": "error message"
            }
        ]
    }
}

```

#### <a name="azure-marketplace-application-notification-schema"></a>Esquema de notificação de pedido do Azure Marketplace

Aqui está uma notificação de catálogo de serviço de amostra após o fornecimento bem sucedido de uma instância de aplicação gerida:
``` HTTP
POST https://{your_endpoint_URI}/resource?{optional_parameter}={optional_parameter_value} HTTP/1.1

{
    "eventType": "PUT",
    "applicationId": "subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applications/<applicationName>",
    "eventTime": "2019-08-14T19:20:08.1707163Z",
    "provisioningState": "Succeeded",
    "billingDetails": {
        "resourceUsageId":"<resourceUsageId>"
    },
    "plan": {
        "publisher": "publisherId",
        "product": "offer",
        "name": "skuName",
        "version": "1.0.1"
    }
}

```

Se o fornecimento falhar, uma notificação com os dados de erro será enviada para o ponto final especificado.

``` HTTP
POST https://{your_endpoint_URI}/resource?{optional_parameter}={optional_parameter_value} HTTP/1.1

{
    "eventType": "PUT",
    "applicationId": "subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applications/<applicationName>",
    "eventTime": "2019-08-14T19:20:08.1707163Z",
    "provisioningState": "Failed",
    "billingDetails": {
        "resourceUsageId":"<resourceUsageId>"
    },
    "plan": {
        "publisher": "publisherId",
        "product": "offer",
        "name": "skuName",
        "version": "1.0.1"
    },
    "error": {
        "code": "ErrorCode",
        "message": "error message",
        "details": [
            {
                "code": "DetailedErrorCode",
                "message": "error message"
            }
        ]
    }
}

```

Parâmetro | Descrição
---|---
eventType | O tipo de evento que desencadeou a notificação. (Por exemplo, PUT, PATCH, DELETE.)
applicationId | O identificador de recursos totalmente qualificado do pedido gerido para o qual a notificação foi desencadeada.
eventTime | O carimbo temporal do evento que desencadeou a notificação. (Data e hora no formato UTC ISO 8601.)
provisionamento Estado | O estado de provisionamento da instância de candidatura gerida. (Por exemplo, Bem sucedido, falhado, eliminação, eliminado.)
erro | *Especificado apenas quando o Estado de provisionamento for falhado*. Contém o código de erro, mensagem e detalhes do problema que causou a falha.
aplicaçãoDefiniId | *Especificado apenas para aplicações geridas*por catálogo de serviços. Representa o identificador de recursos totalmente qualificado da definição de aplicação para a qual foi aprovisionada a instância de candidatura gerida.
plano | *Especificadas apenas para aplicações geridas pelo Azure Marketplace.* Representa a editora, oferta, SKU e versão da instância de aplicação gerida.
faturaçãoDetalhes | *Especificadas apenas para aplicações geridas pelo Azure Marketplace.* Os detalhes de faturação da instância de candidatura gerida. Contém o recursoUsageId que pode usar para consultar o Azure Marketplace para obter detalhes de utilização.

## <a name="endpoint-authentication"></a>Autenticação endpoint
Para fixar o ponto final do webhook e garantir a autenticidade da notificação:
1. Forneça um parâmetro de consulta em cima do webhook\:URI, como este: https //your-endpoint.com?sig=Guid. A cada notificação, verifique se `sig` o parâmetro `Guid`de consulta tem o valor esperado .
2. Emita um GET na instância de aplicação gerida utilizando o applicationId. Valide que o Estado de provisionamento corresponde ao Estado de fornecimento da notificação para garantir a coerência.

## <a name="notification-retries"></a>Retrys de notificação

O serviço de notificação `200 OK` de aplicação gerida espera uma resposta do ponto final do webhook à notificação. O serviço de notificação irá voltar a tentar se o ponto final do webhook devolver um código de erro HTTP superior ou igual a 500, se devolver um código de erro de 429, ou se o ponto final estiver temporariamente inacessível. Se o ponto final do webhook não ficar disponível dentro de 10 horas, a mensagem de notificação será retirada e as repetições pararão.
