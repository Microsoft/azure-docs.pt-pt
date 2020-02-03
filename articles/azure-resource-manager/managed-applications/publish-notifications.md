---
title: Aplicativos gerenciados com notificações
description: Configure aplicativos gerenciados com pontos de extremidade de webhook para receber notificações sobre criações, atualizações, exclusões e erros nas instâncias do aplicativo gerenciado.
ms.topic: conceptual
ms.author: ilahat
author: ilahat
ms.date: 11/01/2019
ms.openlocfilehash: ff058d7b51bd2e5efd80db69e5928d58fc5a7725
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76715682"
---
# <a name="azure-managed-applications-with-notifications"></a>Aplicativos gerenciados do Azure com notificações

As notificações de aplicativo gerenciado do Azure permitem que os editores automatizem ações com base nos eventos do ciclo de vida das instâncias do aplicativo gerenciado. Os editores podem especificar pontos de extremidade de webhook de notificação personalizados para receber notificações de eventos sobre instâncias do aplicativo gerenciado novas e existentes. Os editores podem configurar fluxos de trabalho personalizados no momento do provisionamento, das atualizações e da exclusão do aplicativo.

## <a name="getting-started"></a>Introdução
Para começar a receber aplicativos gerenciados, crie um ponto de extremidade HTTPS público e especifique-o ao publicar a definição de aplicativo do catálogo de serviços ou a oferta do Azure Marketplace.

Aqui estão as etapas recomendadas para começar rapidamente:
1. Rode um ponto final HTTPS público que inicie os pedidos de edições postais e devoluções `200 OK`.
2. Adicione o ponto de extremidade à definição de aplicativo do catálogo de serviços ou à oferta do Azure Marketplace, conforme explicado posteriormente neste artigo.
3. Crie uma instância de aplicativo gerenciado que faça referência à definição de aplicativo ou à oferta do Azure Marketplace.
4. Valide se as notificações estão sendo recebidas.
5. Ativar a autorização tal como explicado na secção de **autenticação Endpoint** deste artigo.
6. Siga as instruções na secção **de informação** de notificação deste artigo para analisar os pedidos de notificação e implementar a sua lógica de negócio com base na notificação.

## <a name="add-service-catalog-application-definition-notifications"></a>Adicionar notificações de definição de aplicativo do catálogo de serviços
#### <a name="azure-portal"></a>Portal do Azure
Para começar, consulte A Publicação de uma aplicação de catálogo de [serviços através do portal Azure](./publish-portal.md).

![Notificações de definição de aplicativo do catálogo de serviços no portal do Azure](./media/publish-notifications/service-catalog-notifications.png)

#### <a name="rest-api"></a>API REST

> [!NOTE]
> Atualmente, só pode fornecer um ponto final no `notificationEndpoints` nas propriedades de definição de aplicação.

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
## <a name="add-azure-marketplace-managed-application-notifications"></a>Adicionar notificações de aplicativo gerenciado do Azure Marketplace
Para mais informações, consulte Criar uma oferta de [aplicação Azure.](../../marketplace/cloud-partner-portal/azure-applications/cpp-create-offer.md)

![Notificações de aplicativo gerenciado do Azure Marketplace no portal do Azure](./media/publish-notifications/marketplace-notifications.png)
## <a name="event-triggers"></a>Acionadores de eventos
A tabela a seguir descreve todas as combinações possíveis de EventType e ProvisioningState e seus gatilhos:

Tipo de evento | ProvisioningState | Gatilho para notificação
---|---|---
PUT | Aceite | O grupo de recursos gerenciados foi criado e projetado com êxito após o aplicativo ser colocado (antes de a implantação dentro do grupo de recursos gerenciado ser inicializada).
PUT | Succeeded | O provisionamento completo do aplicativo gerenciado foi bem-sucedido após um PUT.
PUT | Falhou | Falha ao colocar o provisionamento da instância do aplicativo em qualquer ponto.
PATCH | Succeeded | Após um PATCH bem-sucedido na instância do aplicativo gerenciado para atualizar marcas, política de acesso JIT ou identidade gerenciada.
DELETE | Apagando | Assim que o usuário inicia uma exclusão de uma instância de aplicativo gerenciado.
DELETE | Eliminado | Após a exclusão completa e bem-sucedida do aplicativo gerenciado.
DELETE | Falhou | Após qualquer erro durante o processo de desprovisionamento que bloqueia a exclusão.
## <a name="notification-schema"></a>Esquema de notificação
Ao criar o ponto de extremidade do webhook para lidar com notificações, você precisará analisar a carga para obter propriedades importantes e, em seguida, agir sobre a notificação. As notificações do catálogo de serviços e do aplicativo gerenciado do Azure Marketplace fornecem muitas das mesmas propriedades. Duas pequenas diferenças são descritas na tabela que segue os exemplos.

#### <a name="service-catalog-application-notification-schema"></a>Esquema de notificação de aplicativo do catálogo de serviços
Aqui está um exemplo de notificação do catálogo de serviços após o provisionamento bem-sucedido de uma instância de aplicativo gerenciado:
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

#### <a name="azure-marketplace-application-notification-schema"></a>Esquema de notificação de aplicativo do Azure Marketplace

Aqui está um exemplo de notificação do catálogo de serviços após o provisionamento bem-sucedido de uma instância de aplicativo gerenciado:
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

Se o provisionamento falhar, uma notificação com os detalhes do erro será enviada para o ponto de extremidade especificado.

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
eventType | O tipo de evento que disparou a notificação. (Por exemplo, PUT, PATCH, DELETE.)
applicationId | O identificador de recurso totalmente qualificado do aplicativo gerenciado para o qual a notificação foi disparada.
eventTime | O carimbo de data/hora do evento que disparou a notificação. (Data e hora no formato UTC ISO 8601.)
ProvisioningState | O estado de provisionamento da instância do aplicativo gerenciado. (Por exemplo, com êxito, com falha, excluindo, excluiu.)
error | *Especificado apenas quando o Estado de provisionamento for falhado*. Contém o código de erro, a mensagem e os detalhes do problema que causou a falha.
applicationDefinitionId | *Especificado apenas para aplicações geridas*por catálogo de serviços. Representa o identificador de recurso totalmente qualificado da definição de aplicativo para a qual a instância do aplicativo gerenciado foi provisionada.
plano | *Especificadas apenas para aplicações geridas pelo Azure Marketplace.* Representa o editor, a oferta, a SKU e a versão da instância do aplicativo gerenciado.
billingDetails | *Especificadas apenas para aplicações geridas pelo Azure Marketplace.* Os detalhes de cobrança da instância do aplicativo gerenciado. Contém o resourceUsageId que você pode usar para consultar o Azure Marketplace para obter detalhes de uso.

## <a name="endpoint-authentication"></a>Autenticação de ponto de extremidade
Para proteger o ponto de extremidade do webhook e garantir a autenticidade da notificação:
1. Forneça um parâmetro de consulta em cima do webhook URI, como este: https\://your-endpoint.com?sig=Guid. A cada notificação, verifique se o parâmetro de consulta `sig` tem o valor esperado `Guid`.
2. Emita um GET na instância do aplicativo gerenciado usando applicationId. Valide se o provisioningState corresponde ao provisioningState da notificação para garantir a consistência.

## <a name="notification-retries"></a>Tentativas de notificação

O serviço de notificação de aplicação gerida espera uma resposta `200 OK` do ponto final do webhook à notificação. O serviço de notificação será repetido se o ponto de extremidade do webhook retornar um código de erro HTTP maior ou igual a 500, se ele retornar um código de erro 429 ou se o ponto de extremidade estiver temporariamente inacessível. Se o ponto de extremidade do webhook não ficar disponível em 10 horas, a mensagem de notificação será descartada e as novas tentativas serão interrompidas.
