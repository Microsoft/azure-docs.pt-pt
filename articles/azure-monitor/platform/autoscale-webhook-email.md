---
title: Usar o dimensionamento automático para enviar notificações de alerta por email e webhook
description: Saiba como usar ações de dimensionamento automático para chamar URLs da Web ou enviar notificações por email no Azure Monitor.
ms.topic: conceptual
ms.date: 04/03/2017
ms.subservice: autoscale
ms.openlocfilehash: fd5aeadd72123b58801ce038b0cc99d17dcfd200
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75364208"
---
# <a name="use-autoscale-actions-to-send-email-and-webhook-alert-notifications-in-azure-monitor"></a>Usar ações de dimensionamento automático para enviar notificações de alerta por email e webhook no Azure Monitor
Este artigo mostra como configurar gatilhos para que você possa chamar URLs da Web específicas ou enviar emails com base em ações de dimensionamento automático no Azure.  

## <a name="webhooks"></a>Webhooks
WebHooks permitem que você encaminhe as notificações de alerta do Azure para outros sistemas para pós-processamento ou notificações personalizadas. Por exemplo, rotear o alerta para serviços que podem lidar com uma solicitação da Web de entrada para enviar SMS, registrar bugs, notificar uma equipe usando serviços de chat ou mensagens, etc. O URI do webhook deve ser um ponto de extremidade HTTP ou HTTPS válido.

## <a name="email"></a>E-mail
O email pode ser enviado para qualquer endereço de email válido. Os administradores e coadministradores da assinatura em que a regra está em execução também serão notificados.

## <a name="cloud-services-and-web-apps"></a>Serviços de nuvem e aplicativos Web
Você pode optar pelo portal do Azure para serviços de nuvem e farms de servidores (aplicativos Web).

* Escolha a métrica **Dimensionar por** .

![dimensionar por](./media/autoscale-webhook-email/insights-autoscale-notify.png)

## <a name="virtual-machine-scale-sets"></a>Conjuntos de Dimensionamento de Máquinas Virtuais
Para máquinas virtuais mais novas criadas com o Resource Manager (conjuntos de dimensionamento de máquinas virtuais), você pode configurar isso usando a API REST, os modelos do Resource Manager, o PowerShell e a CLI. Uma interface do portal ainda não está disponível.
Ao usar a API REST ou o modelo do Resource Manager, inclua o elemento notificações em seu [autoscalesettings](https://docs.microsoft.com/azure/templates/microsoft.insights/2015-04-01/autoscalesettings) com as opções a seguir.

```
"notifications": [
      {
        "operation": "Scale",
        "email": {
          "sendToSubscriptionAdministrator": false,
          "sendToSubscriptionCoAdministrators": false,
          "customEmails": [
              "user1@mycompany.com",
              "user2@mycompany.com"
              ]
        },
        "webhooks": [
          {
            "serviceUri": "https://foo.webhook.example.com?token=abcd1234",
            "properties": {
              "optional_key1": "optional_value1",
              "optional_key2": "optional_value2"
            }
          }
        ]
      }
    ]
```

| Campo | Obrigatório? | Descrição |
| --- | --- | --- |
| operation |sim |o valor deve ser "Scale" |
| sendToSubscriptionAdministrator |sim |o valor deve ser "true" ou "false" |
| sendToSubscriptionCoAdministrators |sim |o valor deve ser "true" ou "false" |
| customEmails |sim |o valor pode ser nulo [] ou uma matriz de cadeia de caracteres de emails |
| WebHooks |sim |o valor pode ser nulo ou um URI válido |
| serviceUri |sim |um URI de HTTPS válido |
| propriedades |sim |o valor deve estar vazio {} ou pode conter pares de chave-valor |

## <a name="authentication-in-webhooks"></a>Autenticação em WebHooks
O webhook pode autenticar usando a autenticação baseada em token, em que você salva o URI do webhook com uma ID de token como um parâmetro de consulta. Por exemplo, https:\//mysamplealert/Webcallback? tokenid = sometokenid & someparameter = someValue

## <a name="autoscale-notification-webhook-payload-schema"></a>Esquema de carga de webhook de notificação de dimensionamento automático
Quando a notificação de dimensionamento automático é gerada, os seguintes metadados são incluídos no conteúdo do webhook:

```
{
        "version": "1.0",
        "status": "Activated",
        "operation": "Scale In",
        "context": {
                "timestamp": "2016-03-11T07:31:04.5834118Z",
                "id": "/subscriptions/s1/resourceGroups/rg1/providers/microsoft.insights/autoscalesettings/myautoscaleSetting",
                "name": "myautoscaleSetting",
                "details": "Autoscale successfully started scale operation for resource 'MyCSRole' from capacity '3' to capacity '2'",
                "subscriptionId": "s1",
                "resourceGroupName": "rg1",
                "resourceName": "MyCSRole",
                "resourceType": "microsoft.classiccompute/domainnames/slots/roles",
                "resourceId": "/subscriptions/s1/resourceGroups/rg1/providers/microsoft.classicCompute/domainNames/myCloudService/slots/Production/roles/MyCSRole",
                "portalLink": "https://portal.azure.com/#resource/subscriptions/s1/resourceGroups/rg1/providers/microsoft.classicCompute/domainNames/myCloudService",
                "oldCapacity": "3",
                "newCapacity": "2"
        },
        "properties": {
                "key1": "value1",
                "key2": "value2"
        }
}
```


| Campo | Obrigatório? | Descrição |
| --- | --- | --- |
| status |sim |O status que indica que uma ação de dimensionamento automático foi gerada |
| operation |sim |Para um aumento de instâncias, será "Scale Out" e, para uma diminuição nas instâncias, será "reduzir horizontalmente" |
| noticioso |sim |O contexto da ação de dimensionamento automático |
| carimbo de data/hora |sim |Carimbo de data/hora quando a ação de autoescala foi disparada |
| ID |Sim |ID do Gerenciador de recursos da configuração de dimensionamento automático |
| nome |Sim |O nome da configuração de dimensionamento automático |
| details |Sim |Explicação da ação que o serviço de dimensionamento automático levou e a alteração na contagem de instâncias |
| subscriptionId |Sim |ID da assinatura do recurso de destino que está sendo dimensionado |
| resourceGroupName |Sim |Nome do grupo de recursos do recurso de destino que está sendo dimensionado |
| resourceName |Sim |Nome do recurso de destino que está sendo dimensionado |
| resourceType |Sim |Os três valores com suporte: "Microsoft. classiccompute/nome_do_domínio/Slots/Roles"-funções de serviço de nuvem, "Microsoft. Compute/virtualmachinescalesets" – conjuntos de dimensionamento de máquinas virtuais e "Microsoft. Web/serverfarms"-aplicativo Web |
| resourceId |Sim |ID do Gerenciador de recursos do recurso de destino que está sendo dimensionado |
| portalLink |Sim |portal do Azure vincular à página de resumo do recurso de destino |
| oldCapacity |Sim |A contagem de instâncias atual (antiga) quando a autoescala pega uma ação de escala |
| newCapacity |Sim |A nova contagem de instâncias para a qual o dimensionamento automático foi dimensionado no recurso |
| propriedades |Não |Opcional. Conjunto de < chave, valor > pares (por exemplo, Dictionary < String, String >). O campo de propriedades é opcional. Em uma interface de usuário personalizada ou um fluxo de trabalho baseado em aplicativo lógico, você pode inserir chaves e valores que podem ser passados usando a carga. Uma maneira alternativa de passar as propriedades personalizadas de volta para a chamada de webhook de saída é usar o próprio URI do webhook (como parâmetros de consulta) |

