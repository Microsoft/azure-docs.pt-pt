---
title: Utilize autoescala para enviar notificações de alerta de email e webhook
description: Saiba como usar ações de autoescalação para ligar para URLs web ou enviar notificações de e-mail no Azure Monitor.
ms.topic: conceptual
ms.date: 04/03/2017
ms.subservice: autoscale
ms.openlocfilehash: 3b1f13fd1ce8bedcbe58385d4cee321f1d1405df
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86505524"
---
# <a name="use-autoscale-actions-to-send-email-and-webhook-alert-notifications-in-azure-monitor"></a>Utilize ações de autoescalação para enviar notificações de alerta de email e webhook no Azure Monitor
Este artigo mostra-lhe como configurar os gatilhos para que possa ligar para URLs web específicos ou enviar e-mails com base em ações de autoescala em Azure.  

## <a name="webhooks"></a>Webhooks
Os webhooks permitem-lhe encaminhar as notificações de alerta Azure para outros sistemas para notificações pós-processamento ou personalizadas. Por exemplo, encaminhar o alerta para serviços que possam lidar com um pedido web de envio de SMS, registar erros, notificar uma equipa usando serviços de chat ou mensagens, etc. O webhook URI deve ser um ponto final http ou HTTPS válido.

## <a name="email"></a>E-mail
O e-mail pode ser enviado para qualquer endereço de e-mail válido. Os administradores e coadministradores da subscrição em que a regra está a decorrer também serão notificados.

## <a name="cloud-services-and-app-services"></a>Serviços cloud e serviços de aplicativos
Pode optar pelo portal Azure para serviços em nuvem e quintas de servidores (Serviços de Aplicação).

* Escolha a **balança por** métrica.

![escala por](./media/autoscale-webhook-email/insights-autoscale-notify.png)

## <a name="virtual-machine-scale-sets"></a>Conjuntos de Dimensionamento de Máquinas Virtuais
Para máquinas virtuais mais recentes criadas com Gestor de Recursos (conjuntos de escala de máquina virtual), pode configurar isto usando API REST, modelos de Gestor de Recursos, PowerShell e CLI. Ainda não existe uma interface de portal.
Ao utilizar o modelo REST API ou Resource Manager, inclua o elemento de notificações nas suas [definições automáticas](/azure/templates/microsoft.insights/2015-04-01/autoscalesettings) com as seguintes opções.

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
| operation |sim |valor deve ser "Escala" |
| sendToSubscriptionAdministrator |sim |valor deve ser "verdadeiro" ou "falso" |
| sendToSubscriptionCoAdministrators |sim |valor deve ser "verdadeiro" ou "falso" |
| personalEmails |sim |valor pode ser nulo [] ou cadeia de e-mails |
| webhooks |sim |valor pode ser nula ou válido Uri |
| serviceUri |sim |um https uri válido |
| propriedades |sim |valor deve estar vazio {} ou pode conter pares de valor-chave |

## <a name="authentication-in-webhooks"></a>Autenticação em webhooks
O webhook pode autenticar usando a autenticação baseada em fichas, onde você guarda o webhook URI com um ID simbólico como parâmetro de consulta. Por exemplo, https: \/ /mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue

## <a name="autoscale-notification-webhook-payload-schema"></a>Esquema de carregamento de carga webhook de notificação automática
Quando a notificação de escala automática é gerada, os seguintes metadados são incluídos na carga útil webhook:

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
| status |sim |O estado que indica que uma ação de autoescala foi gerada |
| operation |sim |Para um aumento de casos, será "Scale out" e, para uma diminuição de casos, será "Scale In" |
| contexto |sim |O contexto de ação de autoescala |
| carimbo de data/hora |sim |Carimbo de tempo quando a ação de autoescala foi desencadeada |
| ID |Sim |ID do gestor de recursos da definição de autoescala |
| name |Sim |O nome da definição de autoescala |
| detalhes |Sim |Explicação da ação que o serviço de autoescala tomou e a alteração na contagem de exemplos |
| subscriptionId |Sim |ID de subscrição do recurso-alvo que está a ser dimensionado |
| resourceGroupName |Sim |Nome do Grupo de Recursos do recurso-alvo que está a ser dimensionado |
| resourceName |Sim |Nome do recurso-alvo que está a ser dimensionado |
| resourceType |Sim |Os três valores suportados: "microsoft.classiccompute/domainnames/slots/roles" - Funções cloud service, "microsoft.compute/virtualmachinescalesets" - Conjuntos de escala de máquina virtual e "Microsoft.Web/serverfarms" - Web App |
| resourceId |Sim |ID do gestor de recursos do recurso-alvo que está a ser dimensionado |
| portalLink |Sim |Ligação do portal Azure à página sumária do recurso-alvo |
| oldCapacity |Sim |A contagem de instâncias (antiga) atual quando a Autoscale tomou uma ação de escala |
| nova capacidade |Sim |A nova contagem de exemplos que Autoscale dimensionou o recurso para |
| propriedades |Não |Opcional. Conjunto de <Tecla, Pares de valor> (por exemplo, Dicionário <String, String>). O campo de propriedades é opcional. Numa interface personalizada de utilizador ou fluxo de trabalho baseado em aplicações lógicas, pode introduzir teclas e valores que podem ser passados usando a carga útil. Uma forma alternativa de passar propriedades personalizadas de volta para a chamada webhook de saída é usar o webhook URI em si (como parâmetros de consulta) |
