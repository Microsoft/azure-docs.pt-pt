---
title: Aplicativos gerenciados do Azure com notificações
description: Configure o aplicativo gerenciado com pontos de extremidade de webhook para receber notificações sobre criações, atualizações, exclusões e erros nas instâncias do aplicativo gerenciado.
services: managed-applications
ms.service: managed-applications
ms.topic: conceptual
ms.reviewer: ''
ms.author: ilahat
author: ilahat
ms.date: 11/01/2019
ms.openlocfilehash: a00e5be4493b8c8116e2925e88a3ce4bf8cfb722
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74085320"
---
# <a name="azure-managed-applications-with-notifications"></a>Aplicativos gerenciados do Azure com notificações

As notificações de aplicativo gerenciado do Azure permitem que os editores automatizem ações com base nos eventos do ciclo de vida das instâncias do aplicativo gerenciado. Os editores podem especificar pontos de extremidade de webhook de notificação personalizados para receber notificações de eventos sobre instâncias do aplicativo gerenciado novas e existentes. Ele permite que o Publicador configure fluxos de trabalho personalizados no momento do provisionamento, das atualizações e da exclusão do aplicativo.

## <a name="getting-started"></a>Introdução
Para começar a receber aplicativos gerenciados, crie um ponto de extremidade HTTPS público e especifique-o ao publicar a definição de aplicativo do catálogo de serviços ou a oferta do Marketplace.

Aqui estão a série recomendada de etapas para colocar em funcionamento rapidamente:
1. Crie um ponto de extremidade HTTPS público que registra as solicitações POST de entrada e retorna `200 OK`.
2. Adicione o ponto de extremidade à definição de aplicativo do catálogo de serviços ou à oferta do Marketplace, conforme explicado abaixo.
3. Crie uma instância de aplicativo gerenciado que faça referência à definição de aplicativo ou à oferta do Marketplace.
4. Valide se as notificações estão sendo recebidas com êxito.
5. Habilite a autorização, conforme explicado na seção **autenticação de ponto de extremidade** abaixo.
6. Siga a documentação do **esquema de notificação** abaixo para analisar as solicitações de notificação e implementar a lógica de negócios com base na notificação.

## <a name="adding-service-catalog-application-definition-notifications"></a>Adicionando notificações de definição de aplicativo do catálogo de serviços
#### <a name="azure-portal"></a>Portal do Azure
Leia [publicar um aplicativo de catálogo de serviços por meio do portal do Azure](./publish-portal.md) para começar.

![Notificações de definição de aplicativo do catálogo de serviços no portal](./media/publish-notifications/service-catalog-notifications.png)
#### <a name="rest-api"></a>API REST

> [!NOTE]
> Atualmente, apenas um ponto de extremidade tem suporte como parte do **notificationEndpoints** nas propriedades de definição do aplicativo

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
## <a name="adding-marketplace-managed-application-notifications"></a>Adicionando notificações de aplicativo gerenciado do Marketplace
Para obter mais informações, consulte [criar uma oferta de aplicativo do Azure](../marketplace/cloud-partner-portal/azure-applications/cpp-create-offer.md).

![Notificações de definição de aplicativo do catálogo de serviços no portal](./media/publish-notifications/marketplace-notifications.png)
## <a name="event-triggers"></a>Acionadores de eventos
A tabela a seguir descreve todas as combinações possíveis de EventType + ProvisioningState e seus gatilhos:

EventType | ProvisioningState | Gatilho para notificação
---|---|---
PUT | Aceitar | O grupo de recursos gerenciados foi criado e projetado com êxito após o aplicativo ser colocado. (Antes de a implantação dentro do RG gerenciado ser inicializada.)
PUT | Bem-sucedido | O provisionamento completo do aplicativo gerenciado foi bem-sucedido após um PUT.
PUT | Com Falhas | Falha ao colocar o provisionamento da instância do aplicativo em qualquer ponto.
DISTRIBUÍDO | Bem-sucedido | Após o PATCH bem-sucedido na instância do aplicativo gerenciado para atualizar as marcas, a política de acesso JIT ou a identidade gerenciada.
DELETE | Excluído | Assim que o usuário inicia uma exclusão de uma instância de aplicativo gerenciado.
DELETE | Eliminado | Após a exclusão completa e bem-sucedida do aplicativo gerenciado.
DELETE | Com Falhas | Após qualquer erro durante o processo de desprovisionamento que bloqueia a exclusão.
## <a name="notification-schema"></a>Esquema de notificação
Ao criar o ponto de extremidade do webhook para lidar com notificações, você precisará analisar a carga para obter propriedades importantes e, em seguida, agir sobre a notificação. As notificações do catálogo de serviços e do aplicativo gerenciado do Marketplace fornecem muitas das mesmas propriedades com a pequena diferença descrita abaixo.

#### <a name="service-catalog-application-notification-schema"></a>Esquema de notificação de aplicativo do catálogo de serviços
Aqui está um exemplo de notificação do catálogo de serviços após um provisionamento bem-sucedido de uma instância de aplicativo gerenciado.
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

Se o provisionamento falhar, uma notificação com os detalhes do erro será enviada para o ponto de extremidade especificado.

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

#### <a name="marketplace-application-notification-schema"></a>Esquema de notificação de aplicativo do Marketplace

Aqui está um exemplo de notificação do catálogo de serviços após um provisionamento bem-sucedido de uma instância de aplicativo gerenciado.
``` HTTP
POST https://{your_endpoint_URI}/resource?{optional_parameter}={optional_parameter_value} HTTP/1.1

{
    "eventType": "PUT",
    "applicationId": "subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applications/<applicationName>",
    "eventTime": "2019-08-14T19:20:08.1707163Z",
    "provisioningState": "Succeeded",
    "plan": {
        "publisher": "publisherId",
        "product": "offer",
        "name": "skuName",
        "version": "1.0.1"
    }
}

```

Se o provisionamento falhar, uma notificação com os detalhes do erro será enviada para o ponto de extremidade especificado.

``` HTTP
POST https://{your_endpoint_URI}/resource?{optional_parameter}={optional_parameter_value} HTTP/1.1

{
    "eventType": "PUT",
    "applicationId": "subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applications/<applicationName>",
    "eventTime": "2019-08-14T19:20:08.1707163Z",
    "provisioningState": "Failed",
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
eventType | O tipo de evento que disparou a notificação. (por exemplo, "PUT", "PATCH", "excluir")
applicationId | O identificador de recurso totalmente qualificado do aplicativo gerenciado para o qual a notificação foi disparada. 
eventTime | O carimbo de data/hora do evento que disparou a notificação. (Data e hora no formato UTC ISO 8601.)
ProvisioningState | O estado de provisionamento da instância do aplicativo gerenciado. (por exemplo, "êxito", "falha", "excluindo", "excluído")
erro | *Especificado somente quando provisioningState é com falha*. Contém o código de erro, a mensagem e os detalhes do problema que causou a falha.
applicationDefinitionId | *Especificado somente para aplicativos gerenciados do catálogo de serviços*. Representa o identificador de recurso totalmente qualificado da definição de aplicativo para a qual a instância do aplicativo gerenciado foi provisionada.
intenção | *Especificado somente para aplicativos gerenciados do Marketplace*. Representa o editor, a oferta, a SKU e a versão da instância do aplicativo gerenciado.

## <a name="endpoint-authentication"></a>Autenticação de ponto de extremidade
Para proteger o ponto de extremidade do webhook e garantir a autenticidade da notificação:
- Forneça um parâmetro de consulta na parte superior do URI do webhook, como https://your-endpoint.com?sig=Guid. Com cada notificação, faça uma verificação rápida de que o parâmetro de consulta `sig` tem o valor esperado `Guid`.
- Emita um GET na instância do aplicativo gerenciado com applicationId. Valide se o provisioningState corresponde ao provisioningState da notificação para garantir a consistência.

## <a name="notification-retries"></a>Tentativas de notificação

O serviço de notificação de aplicativo gerenciado espera uma resposta `200 OK` do ponto de extremidade do webhook para a notificação. O serviço de notificação será repetido se o ponto de extremidade do webhook retornar um código de erro HTTP > = 500, 429 ou se o ponto de extremidade estiver temporariamente inacessível. Se o ponto de extremidade do webhook não ficar disponível em 10 horas, a mensagem de notificação será descartada e as novas tentativas serão interrompidas.

