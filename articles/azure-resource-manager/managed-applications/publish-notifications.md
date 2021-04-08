---
title: Aplicativos geridos com notificações
description: Configure aplicações geridas com pontos finais webhook para receber notificações sobre criações, atualizações, eliminações e erros nas instâncias de aplicação geridas.
ms.topic: conceptual
ms.author: ilahat
author: ilahat
ms.date: 11/01/2019
ms.openlocfilehash: 2a2e9d429d494c35c49a5b0a3e10b291fd8f24a6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100633942"
---
# <a name="azure-managed-applications-with-notifications"></a>Azure geriu aplicações com notificações

As notificações de aplicações geridas pela Azure permitem aos editores automatizar ações com base em eventos de ciclo de vida das instâncias de aplicação geridas. Os editores podem especificar pontos finais de notificação personalizada para receber notificações de eventos sobre novas e já existentes instâncias de aplicação geridas. Os editores podem configurar fluxos de trabalho personalizados no momento do fornecimento de aplicações, atualizações e eliminação.

## <a name="getting-started"></a>Introdução
Para começar a receber aplicações geridas, rode um ponto final HTTPS público e especifique-o quando publicar a definição de aplicação do catálogo de serviços ou a oferta do Azure Marketplace.

Aqui estão os passos recomendados para começar rapidamente:
1. Spin up um ponto final HTTPS público que regista os pedidos e devoluções do POST que `200 OK` chega.
2. Adicione o ponto final à definição de aplicação do catálogo de serviços ou à oferta do Azure Marketplace, como explicado mais tarde neste artigo.
3. Crie uma instância de aplicação gerida que faça referência à definição de aplicação ou oferta do Azure Marketplace.
4. Validar que as notificações estão a ser recebidas.
5. Habilitar a autorização tal como explicado na secção de **autenticação endpoint** deste artigo.
6. Siga as instruções na secção de esquema de **Notificação** deste artigo para analisar os pedidos de notificação e implementar a sua lógica de negócio com base na notificação.

## <a name="add-service-catalog-application-definition-notifications"></a>Adicionar notificações de definição de aplicação de catálogo de serviços
#### <a name="azure-portal"></a>Portal do Azure
Para começar, consulte [publicar uma aplicação de catálogo de serviços através do portal Azure.](./publish-portal.md)

![Notificações de definição de aplicação de catálogo de serviços no portal Azure](./media/publish-notifications/service-catalog-notifications.png)

#### <a name="rest-api"></a>API REST

> [!NOTE]
> Atualmente, pode fornecer apenas um ponto final nas `notificationEndpoints` propriedades de definição de aplicação.

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
Para mais informações, consulte [Criar uma oferta de aplicação Azure.](../../marketplace/create-new-azure-apps-offer.md)

![Azure Marketplace geriu notificações de aplicações no portal Azure](./media/publish-notifications/marketplace-notifications.png)
## <a name="event-triggers"></a>Acionadores de eventos
A tabela a seguir descreve todas as combinações possíveis de EventType e ProvisioningState e seus gatilhos:

EventType | Estado de Provisionamento | Gatilho para notificação
---|---|---
PUT | Aceite | O grupo de recursos geridos foi criado e projetado com sucesso após a aplicação PUT (antes da implantação dentro do grupo de recursos gerido ser iniciada).
PUT | Com êxito | O fornecimento integral do pedido gerido foi bem sucedido após um PUT.
PUT | Com falhas | Falha no fornecimento de instância de aplicação em qualquer ponto.
PATCH | Com êxito | Depois de um PATCH bem sucedido na aplicação gerida para atualizar tags, política de acesso JIT ou identidade gerida.
DELETE | Eliminar | Assim que o utilizador iniciar um DELETE de uma aplicação gerida.
DELETE | Eliminado | Após a supressão completa e bem sucedida da aplicação gerida.
DELETE | Com falhas | Após qualquer erro durante o processo de desprovisionamento que bloqueia a eliminação.
## <a name="notification-schema"></a>Esquema de notificação
Quando girar o seu ponto final webhook para lidar com notificações, terá de analisar a carga útil para obter propriedades importantes para, em seguida, agir após a notificação. O catálogo de serviços e as notificações de aplicações geridas pelo Azure Marketplace fornecem muitas das mesmas propriedades. Duas pequenas diferenças estão descritas na tabela que segue as amostras.

#### <a name="service-catalog-application-notification-schema"></a>Esquema de notificação de aplicação de catálogo de serviços
Aqui está uma notificação do catálogo de serviços de amostra após o fornecimento bem sucedido de uma instância de aplicação gerida:
``` HTTP
POST https://{your_endpoint_URI}/resource?{optional_parameter}={optional_parameter_value} HTTP/1.1

{
    "eventType": "PUT",
    "applicationId": "/subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applications/<applicationName>",
    "eventTime": "2019-08-14T19:20:08.1707163Z",
    "provisioningState": "Succeeded",
    "applicationDefinitionId": "/subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applicationDefinitions/<appDefName>"    
}

```

Se o provisionamento falhar, será enviada uma notificação com os dados de erro para o ponto final especificado.

``` HTTP
POST https://{your_endpoint_URI}/resource?{optional_parameter}={optional_parameter_value} HTTP/1.1

{
    "eventType": "PUT",
    "applicationId": "subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applications/<applicationName>",
    "eventTime": "2019-08-14T19:20:08.1707163Z",
    "provisioningState": "Failed",
    "applicationDefinitionId": "/subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applicationDefinitions/<appDefName>",
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

#### <a name="azure-marketplace-application-notification-schema"></a>Esquema de notificação de aplicação do Azure Marketplace

Aqui está uma notificação do catálogo de serviços de amostra após o fornecimento bem sucedido de uma instância de aplicação gerida:
``` HTTP
POST https://{your_endpoint_URI}/resource?{optional_parameter}={optional_parameter_value} HTTP/1.1

{
    "eventType": "PUT",
    "applicationId": "/subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applications/<applicationName>",
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

Se o provisionamento falhar, será enviada uma notificação com os dados de erro para o ponto final especificado.

``` HTTP
POST https://{your_endpoint_URI}/resource?{optional_parameter}={optional_parameter_value} HTTP/1.1

{
    "eventType": "PUT",
    "applicationId": "/subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applications/<applicationName>",
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
eventTime | O tempo de data do evento que desencadeou a notificação. (Data e hora no formato UTC ISO 8601.)
ProvisioningState | O estado de provisionamento da instância de aplicação gerida. (Por exemplo, Conseguiu, Falhou, Eliminou, Eliminou.)
erro | *Especificado apenas quando o Estado de provisionamento está falhado*. Contém o código de erro, a mensagem e os detalhes do problema que causou a falha.
aplicaçãoDefinitionId | *Especificado apenas para aplicações geridas por catálogos de serviços*. Representa o identificador de recursos totalmente qualificado da definição de pedido para a qual foi a instância de aplicação gerida.
plano | *Especificado apenas para aplicações geridas pelo Azure Marketplace*. Representa a editora, a oferta, a SKU e a versão da aplicação gerida.
billingDetails | *Especificado apenas para aplicações geridas pelo Azure Marketplace.* Os detalhes da faturação da aplicação gerida. Contém o recursoUsageId que pode usar para consultar o Azure Marketplace para obter detalhes de utilização.

## <a name="endpoint-authentication"></a>Autenticação de ponto final
Para garantir o ponto final webhook e garantir a autenticidade da notificação:
1. Forneça um parâmetro de consulta em cima do webhook URI, assim: https \: //your-endpoint.com?sig=Guid. A cada notificação, verifique se o parâmetro de consulta `sig` tem o valor esperado `Guid` .
2. Emita um GET sobre a instância de aplicação gerida utilizando o applicationId. Validar que o Estado de provisionamento corresponde ao Estado de provisionamento da notificação para garantir a coerência.

## <a name="notification-retries"></a>Recauchutagem de notificação

O serviço de Notificação de Aplicações Geridos espera uma `200 OK` resposta do ponto final webhook para a notificação. O serviço de notificação tentará novamente se o ponto final do webhook devolver um código de erro HTTP superior ou igual a 500, se devolver um código de erro de 429, ou se o ponto final for temporariamente inacessível. Se o ponto final do webhook não estiver disponível dentro de 10 horas, a mensagem de notificação será retirada e as retráxis pararão.
