---
title: Segurança e autenticação da Rede de Eventos Azure
description: Descreve o Azure Event Grid e respetivos conceitos.
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: babanisa
ms.openlocfilehash: 5f8b0a779e6cb70537d126c251e1e065892934a9
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/01/2020
ms.locfileid: "82629512"
---
# <a name="authorizing-access-to-event-grid-resources"></a>Autorizar o acesso aos recursos da Rede de Eventos
A Azure Event Grid permite controlar o nível de acesso dado aos diferentes utilizadores para fazer várias operações de gestão, tais como subscrições de eventos de listas, criar novas e gerar chaves. A Event Grid utiliza o controlo de acesso baseado em papéis da Azure (RBAC).


## <a name="operation-types"></a>Tipos de operação

A Rede de Eventos apoia as seguintes ações:

* Microsoft.EventGrid/*/read
* Microsoft.EventGrid/*/write
* Microsoft.EventGrid/*/delete
* Microsoft.EventGrid/eventSubscriptions/getFullUrl/action
* Microsoft.EventGrid/topics/listKeys/action
* Microsoft.EventGrid/topics/regeneraçãoChave/ação

As últimas três operações devolvem informações potencialmente secretas, que são filtradas das operações normais de leitura. Recomenda-se que restrinja o acesso a estas operações. 

## <a name="built-in-roles"></a>Funções incorporadas

O Event Grid fornece duas funções incorporadas para a gestão de subscrições de eventos. São importantes na implementação de domínios de [eventos](event-domains.md) porque dão aos utilizadores as permissões necessárias para subscrever em tópicos no domínio do seu evento. Estas funções estão focadas nas subscrições de eventos e não concedem acesso a ações como a criação de tópicos.

Pode [atribuir estas funções a um utilizador ou grupo](../role-based-access-control/quickstart-assign-role-user-portal.md).

**EventGrid EventSubscription Contributor**: gerencie as operações de subscrição da Rede de Eventos

```json
[
  {
    "Description": "Lets you manage EventGrid event subscription operations.",
    "IsBuiltIn": true,
    "Id": "428e0ff05e574d9ca2212c70d0e0a443",
    "Name": "EventGrid EventSubscription Contributor",
    "IsServiceRole": false,
    "Permissions": [
      {
        "Actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.EventGrid/eventSubscriptions/*",
          "Microsoft.EventGrid/topicTypes/eventSubscriptions/read",
          "Microsoft.EventGrid/locations/eventSubscriptions/read",
          "Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Resources/deployments/*",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Support/*"
        ],
        "NotActions": [],
        "DataActions": [],
        "NotDataActions": [],
        "Condition": null
      }
    ],
    "Scopes": [
      "/"
    ]
  }
]
```

Leitor de **Subscrição de EventosGrid**: leia as subscrições da Grelha de Eventos

```json
[
  {
    "Description": "Lets you read EventGrid event subscriptions.",
    "IsBuiltIn": true,
    "Id": "2414bbcf64974faf8c65045460748405",
    "Name": "EventGrid EventSubscription Reader",
    "IsServiceRole": false,
    "Permissions": [
      {
        "Actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.EventGrid/eventSubscriptions/read",
          "Microsoft.EventGrid/topicTypes/eventSubscriptions/read",
          "Microsoft.EventGrid/locations/eventSubscriptions/read",
          "Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read"
        ],
        "NotActions": [],
        "DataActions": [],
        "NotDataActions": []
       }
    ],
    "Scopes": [
      "/"
    ]
  }
]
```

## <a name="custom-roles"></a>Funções personalizadas

Se precisar especificar permissões diferentes das funções incorporadas, pode criar papéis personalizados.

Seguem-se as definições de funções da Rede de Eventos de Amostra que permitem aos utilizadores tomar diferentes ações. Estas funções personalizadas são diferentes das funções incorporadas porque concedem um acesso mais amplo do que apenas subscrições de eventos.

**EventGridReadOnlyRole.json**: Só permita operações apenas de leitura.

```json
{
  "Name": "Event grid read only role",
  "Id": "7C0B6B59-A278-4B62-BA19-411B70753856",
  "IsCustom": true,
  "Description": "Event grid read only role",
  "Actions": [
    "Microsoft.EventGrid/*/read"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/<Subscription Id>"
  ]
}
```

**EventGridNoDeleteListKeysRole.json**: Permitir ações de posta restritas, mas não permitir ações de exclusão.

```json
{
  "Name": "Event grid No Delete Listkeys role",
  "Id": "B9170838-5F9D-4103-A1DE-60496F7C9174",
  "IsCustom": true,
  "Description": "Event grid No Delete Listkeys role",
  "Actions": [
    "Microsoft.EventGrid/*/write",
    "Microsoft.EventGrid/eventSubscriptions/getFullUrl/action"
    "Microsoft.EventGrid/topics/listkeys/action",
    "Microsoft.EventGrid/topics/regenerateKey/action"
  ],
  "NotActions": [
    "Microsoft.EventGrid/*/delete"
  ],
  "AssignableScopes": [
    "/subscriptions/<Subscription id>"
  ]
}
```

**EventGridContributorRole.json**: Permite todas as ações da grelha de eventos.

```json
{
  "Name": "Event grid contributor role",
  "Id": "4BA6FB33-2955-491B-A74F-53C9126C9514",
  "IsCustom": true,
  "Description": "Event grid contributor role",
  "Actions": [
    "Microsoft.EventGrid/*/write",
    "Microsoft.EventGrid/*/delete",
    "Microsoft.EventGrid/topics/listkeys/action",
    "Microsoft.EventGrid/topics/regenerateKey/action",
    "Microsoft.EventGrid/eventSubscriptions/getFullUrl/action"
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/<Subscription id>"
  ]
}
```

Pode criar funções personalizadas com [PowerShell,](../role-based-access-control/custom-roles-powershell.md) [Azure CLI](../role-based-access-control/custom-roles-cli.md)e [REST](../role-based-access-control/custom-roles-rest.md).



### <a name="encryption-at-rest"></a>Encriptação inativa

Todos os eventos ou dados escritos em disco pelo serviço Event Grid são encriptados por uma chave gerida pela Microsoft, garantindo que está encriptado em repouso. Adicionalmente, o período máximo de tempo que os eventos ou dados retidos é de 24 horas de adesão à política de [retry da Rede](delivery-and-retry.md)de Eventos . A Grelha de Eventos eliminará automaticamente todos os eventos ou dados após 24 horas, ou o evento tem tempo de vida, o que for menor.

## <a name="permissions-for-event-subscriptions"></a>Permissões para subscrições de eventos
Se estiver a usar um manipulador de eventos que não seja um WebHook (como um hub de eventos ou armazenamento de fila), precisa de ter acesso a esse recurso. Esta verificação de permissões impede um utilizador não autorizado de enviar eventos para o seu recurso.

Tem de ter a **microsoft.EventGrid/EventSubscriptions/Write** no recurso que é a fonte do evento. Precisa desta permissão porque está a escrever uma nova subscrição no âmbito do recurso. O recurso necessário difere com base no facto de estar a subscrever um tópico do sistema ou um tópico personalizado. Ambos os tipos são descritos nesta secção.

### <a name="system-topics-azure-service-publishers"></a>Tópicos do sistema (editores de serviços Azure)
Para tópicos do sistema, precisa de permissão para escrever uma nova subscrição de evento sintetizador no âmbito do recurso que publica o evento. O formato do recurso é:`/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{resource-provider}/{resource-type}/{resource-name}`

Por exemplo, para subscrever um evento numa conta de armazenamento chamada **myacct,** precisa da Microsoft.EventGrid/EventSubscriptions/Write permission em:`/subscriptions/####/resourceGroups/testrg/providers/Microsoft.Storage/storageAccounts/myacct`

### <a name="custom-topics"></a>Tópicos personalizados
Para tópicos personalizados, você precisa de permissão para escrever uma nova subscrição de evento no âmbito do tópico da grelha de eventos. O formato do recurso é:`/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.EventGrid/topics/{topic-name}`

Por exemplo, para subscrever um tópico personalizado chamado **mytopic,** você precisa da Microsoft.EventGrid/EventSubscriptions/Write permission em:`/subscriptions/####/resourceGroups/testrg/providers/Microsoft.EventGrid/topics/mytopic`



## <a name="next-steps"></a>Passos seguintes

* Para uma introdução à Grelha de Eventos, consulte sobre a grelha de [eventos](overview.md)
