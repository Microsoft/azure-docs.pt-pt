---
title: Utilizar o dimensionamento automático para enviar o e-mail e webhook notificações de alerta
description: 'Veja como utilizar as ações de dimensionamento automático para chamar URLs da web ou enviar notificações por correio eletrónico no Azure Monitor. '
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/03/2017
ms.author: ancav
ms.subservice: autoscale
ms.openlocfilehash: 25ef2541dfa0b4cbd6e11d64381da645acfe653a
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58259300"
---
# <a name="use-autoscale-actions-to-send-email-and-webhook-alert-notifications-in-azure-monitor"></a>Utilizar ações de dimensionamento automático para enviar e-mail e webhook notificações de alertas no Azure Monitor
Este artigo mostra-lhe como configurar acionadores, para que pode chamar URLs da web específica ou enviar e-mails com base em ações de dimensionamento automático no Azure.  

## <a name="webhooks"></a>Webhooks
Os Webhooks permitem-lhe encaminhar as notificações de alertas do Azure para outros sistemas para notificações de pós-processamento ou personalizados. Por exemplo, encaminhamento de alerta para serviços que podem processar uma solicitação de web de entrada para enviar que SMS, erros de registo, notificar a equipa através de bate-papo ou mensagens de serviços, etc. O URI de webhook tem de ser um ponto final HTTP ou HTTPS válido.

## <a name="email"></a>Email
E-mail pode ser enviado para qualquer endereço de e-mail válido. Os administradores e coadministradores da subscrição em que está a executar a regra serão notificados.

## <a name="cloud-services-and-web-apps"></a>Serviços cloud e aplicações Web
Pode participar do portal do Azure para serviços Cloud e Farms de servidores (aplicações Web).

* Escolha o **dimensione o** métrica.

![Dimensionar por](./media/autoscale-webhook-email/insights-autoscale-notify.png)

## <a name="virtual-machine-scale-sets"></a>Conjuntos de Dimensionamento de Máquinas Virtuais
Para mais recente máquinas de virtuais criadas com o Resource Manager (conjuntos de dimensionamento de Máquina Virtual), pode configurar esta opção com a REST API, modelos do Resource Manager, PowerShell e CLI. Uma interface de portal ainda não está disponível.
Ao utilizar o modelo de REST API ou do Resource Manager, inclua o elemento de notificações com as seguintes opções.

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
| operação |sim |o valor tem de ser "Dimensionamento" |
| sendToSubscriptionAdministrator |sim |valor tem de ser "verdadeiro" ou "false" |
| sendToSubscriptionCoAdministrators |sim |valor tem de ser "verdadeiro" ou "false" |
| customEmails |sim |valor pode ser [null] ou matriz de cadeia de caracteres de mensagens de e-mail |
| webhooks |sim |o valor pode ser nulo ou válido Uri |
| serviceUri |sim |um Uri de https válido |
| propriedades |sim |o valor tem de estar vazio {} ou podem conter pares chave-valor |

## <a name="authentication-in-webhooks"></a>Autenticação em webhooks
O webhook pode autenticar através da autenticação baseada em tokens, onde guardar o webhook URI com um ID de token como um parâmetro de consulta. Por exemplo, https: \/ /mysamplealert/webcallback? tokenid = sometokenid & someparameter = somevalue

## <a name="autoscale-notification-webhook-payload-schema"></a>Esquema de payload de webhook de notificação de dimensionamento automático
Quando a notificação de dimensionamento automático é gerada, os seguintes metadados está incluído no payload do webook:

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
| status |sim |O estado que indica que uma ação de dimensionamento automático foi gerada |
| operação |sim |Para um aumento do número de instâncias, é "Aumentar horizontalmente" e para uma diminuição nas instâncias, será "Escala em" |
| context |sim |O contexto de ação de dimensionamento automático |
| carimbo de data/hora |sim |Carimbo de hora quando a ação de dimensionamento automático foi acionada |
| ID |Sim |Gestor de recursos do ID de definição de dimensionamento automático |
| nome |Sim |O nome da definição de dimensionamento automático |
| detalhes |Sim |Explicação da ação que demorou o serviço de dimensionamento automático e a alteração na contagem de instâncias |
| subscriptionId |Sim |ID de subscrição do recurso de destino que está a ser ajustado |
| resourceGroupName |Sim |Nome do grupo de recursos do recurso de destino que está a ser ajustado |
| resourceName |Sim |Nome do recurso de destino que está a ser ajustado |
| resourceType |Sim |Os três valores de suportados: "microsoft.classiccompute/domainnames/slots/roles" - funções do serviço em nuvem, "Compute/virtualmachinescalesets" - conjuntos de dimensionamento de máquinas virtuais e "Microsoft.Web/serverfarms" - aplicação Web |
| resourceId |Sim |ID de Gestor de recursos do recurso de destino que está a ser ajustado |
| portalLink |Sim |Hiperligação do portal do Azure para a página de resumida do recurso de destino |
| oldCapacity |Sim |O atual número de instâncias (antiga) quando o dimensionamento automático executou uma ação de dimensionamento |
| newCapacity |Sim |A nova contagem de instância que o recurso de ajustados de dimensionamento automático |
| Propriedades |Não |Opcional. Conjunto de < chave, valor > pares (por exemplo, Dictionary < String, String >). O campo de propriedades é opcional. Numa interface do usuário personalizada ou fluxo de trabalho de aplicação com base de lógica, pode introduzir as chaves e valores que podem ser transmitidos a utilizar o payload. Outra forma de passar as propriedades personalizadas para a chamada de webhook saída consiste em utilizar o webhook URI em si (como parâmetros de consulta) |

