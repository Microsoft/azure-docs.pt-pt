---
title: Utilize a escala automática para enviar notificações de alerta de e-mail e webhook
description: Saiba como usar ações de escala automática para ligar para urLs web ou enviar notificações de e-mail no Monitor Azure.
ms.topic: conceptual
ms.date: 04/03/2017
ms.subservice: autoscale
ms.openlocfilehash: c82b170bb3801bdc701ed84230db57f5691523ea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77120700"
---
# <a name="use-autoscale-actions-to-send-email-and-webhook-alert-notifications-in-azure-monitor"></a>Utilize ações de escala automática para enviar notificações de alerta de e-mail e webhook no Monitor Azure
Este artigo mostra-lhe como configura os gatilhos para que possa ligar para URLs web específicos ou enviar e-mails com base em ações de escala automática em Azure.  

## <a name="webhooks"></a>Webhooks
Os Webhooks permitem-lhe encaminhar as notificações de alerta Azure para outros sistemas para notificações pós-processamento ou personalizadas. Por exemplo, encaminhar o alerta para serviços que possam lidar com um pedido web de envio de SMS, registar bugs, notificar uma equipa usando serviços de chat ou mensagens, etc. O webhook URI deve ser um ponto final HTTP ou HTTPS válido.

## <a name="email"></a>Email
O email de e-mail pode ser enviado para qualquer endereço de e-mail válido. Os administradores e coadministradores da subscrição em que a regra está em execução também serão notificados.

## <a name="cloud-services-and-app-services"></a>Serviços de Cloud e Serviços de Aplicações
Pode optar pelo portal Azure para Serviços na Nuvem e Quintas de Servidores (Serviços de Aplicações).

* Escolha a **balança por** métrica.

![escala por](./media/autoscale-webhook-email/insights-autoscale-notify.png)

## <a name="virtual-machine-scale-sets"></a>Conjuntos de Dimensionamento de Máquinas Virtuais
Para máquinas virtuais mais recentes criadas com O Gestor de Recursos (conjuntos de escala de máquina virtual), pode configurá-lo usando modelos REST API, Resource Manager, PowerShell e CLI. Uma interface do portal ainda não está disponível.
Ao utilizar o modelo REST API ou Gestor de Recursos, inclua o elemento notificações nas suas [definições automáticas](https://docs.microsoft.com/azure/templates/microsoft.insights/2015-04-01/autoscalesettings) com as seguintes opções.

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
| operação |sim |valor deve ser "Escala" |
| enviarAdministrador de subscrição |sim |valor deve ser "verdadeiro" ou "falso" |
| enviarAdministradores Coadministradores de EnviodeSubscrições |sim |valor deve ser "verdadeiro" ou "falso" |
| emails personalizados |sim |valor pode ser nulo [] ou cadeia de e-mails |
| webhooks |sim |valor pode ser nulo ou válido Uri |
| serviceUri |sim |um https válido Uri |
| propriedades |sim |valor deve {} ser vazio ou pode conter pares de valor-chave |

## <a name="authentication-in-webhooks"></a>Autenticação em webhooks
O webhook pode autenticar usando autenticação baseada em token, onde você guarda o webhook URI com um ID simbólico como parâmetro de consulta. Por exemplo,\/https: /mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue

## <a name="autoscale-notification-webhook-payload-schema"></a>Esquema de carga útil de webhook de notificação automática
Quando a notificação de escala automática é gerada, os seguintes metadados são incluídos na carga útil do webhook:

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
| status |sim |O estado que indica que uma ação de escala automática foi gerada |
| operação |sim |Para um aumento de casos, será "Scale out" e para uma diminuição de casos, será "Scale In" |
| contexto |sim |O contexto de ação de escala automática |
| carimbo de data/hora |sim |Carimbo de tempo quando a ação de escala automática foi desencadeada |
| ID |Sim |ID do Gestor de Recursos da definição de escala automática |
| nome |Sim |O nome da definição de escala automática |
| detalhes |Sim |Explicação da ação que o serviço de escala automática tomou e a mudança na contagem de exemplos |
| subscriptionId |Sim |Id de subscrição do recurso-alvo que está a ser escalado |
| resourceGroupName |Sim |Nome do Grupo de Recursos do recurso-alvo que está a ser escalado |
| resourceName |Sim |Nome do recurso-alvo que está a ser escalado |
| resourceType |Sim |Os três valores suportados: "microsoft.classiccompute/domainnames/slots/roles" - Funções cloud Service, "microsoft.compute/virtualmachinescalesets" - Conjuntos de Escala de Máquinavirtual e "Microsoft.Web/serverfarms" - Web App |
| resourceId |Sim |Id do gestor de recursos do recurso-alvo que está a ser escalado |
| portalLink |Sim |Link do portal Azure para a página sumária do recurso-alvo |
| oldCapacity |Sim |A contagem atual (antiga) de instância quando a Escala Automática tomou uma ação de escala |
| novaCapacidade |Sim |A nova contagem de casos que autoescala escala o recurso para |
| propriedades |Não |Opcional. Conjunto de pares <Key, Value> (por exemplo, Dicionário <String, String>). O campo de propriedades é opcional. Numa interface personalizada do utilizador ou no fluxo de trabalho baseado em aplicações lógicas, pode introduzir chaves e valores que podem ser passados através da carga útil. Uma forma alternativa de passar propriedades personalizadas de volta à chamada de webhook de saída é usar o próprio WEBhook URI (como parâmetros de consulta) |

