---
title: Segurança e autenticação da Grelha de Eventos Azure
description: Descreve o Azure Event Grid e respetivos conceitos.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 09317b310a5934d27b82d265ec7f9b276135f882
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86119094"
---
# <a name="authorizing-access-to-event-grid-resources"></a>Autorizar o acesso aos recursos da Grade de Eventos
O Azure Event Grid permite-lhe controlar o nível de acesso dado a diferentes utilizadores para fazer várias operações de gestão, tais como subscrições de eventos de lista, criar novas e gerar chaves. A Grade de Eventos utiliza o controlo de acesso baseado em funções da Azure (RBAC).


## <a name="operation-types"></a>Tipos de operação

A Grade de Eventos suporta as seguintes ações:

* Microsoft.EventGrid/*/read
* Microsoft.EventGrid/*/write
* Microsoft.EventGrid/*/delete
* Microsoft.EventGrid/eventSubscriptions/getFullUrl/action
* Microsoft.EventGrid/topics/listKeys/action
* Microsoft.EventGrid/topics/regenerateKey/action

As últimas três operações devolvem informações potencialmente secretas, que são filtradas fora das operações normais de leitura. Recomenda-se que restrinja o acesso a estas operações. 

## <a name="built-in-roles"></a>Funções incorporadas

O Event Grid oferece duas funções incorporadas para gerir subscrições de eventos. São importantes na implementação [de domínios de eventos](event-domains.md) porque dão aos utilizadores as permissões necessárias para subscreverem tópicos no domínio do evento. Estas funções estão focadas em subscrições de eventos e não dão acesso a ações como a criação de tópicos.

Pode [atribuir estas funções a um utilizador ou grupo.](../role-based-access-control/quickstart-assign-role-user-portal.md)

**Colaborador de eventosSSubscription**: gerir operações de subscrição de Event Grid

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

**EventGrid EventSubscription Reader**: leia subscrições da Grade de Eventos

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

Se precisar de especificar permissões diferentes das funções incorporadas, pode criar funções personalizadas.

Seguem-se definições de funções de sample Event Grid que permitem aos utilizadores tomar diferentes ações. Estas funções personalizadas são diferentes das funções incorporadas porque concedem um acesso mais amplo do que apenas subscrições de eventos.

**EventGridReadOnlyRole.js:** Apenas permitir operações de leitura.

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

**EventGridNoDeleteListKeysRole.jsem:** Permitir ações postadas restritas, mas não permitir a exclusão de ações.

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

**EventGridContributorRole.jsem**: Permite todas as ações da grelha de eventos.

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

Todos os eventos ou dados escritos em disco pelo serviço Desempenhado pelo Serviço de Grelha de Eventos são encriptados por uma chave gerida pela Microsoft, garantindo que está encriptada em repouso. Adicionalmente, o período máximo de tempo que os eventos ou dados retidos são de 24 horas de adesão à [política de relíndi da Grelha de Eventos.](delivery-and-retry.md) A Grelha de Eventos eliminará automaticamente todos os eventos ou dados após 24 horas, ou o tempo de vida do evento, o que for menor.

## <a name="permissions-for-event-subscriptions"></a>Permissões para subscrições de eventos
Se estiver a usar um manipulador de eventos que não seja um WebHook (como um centro de eventos ou armazenamento de fila), precisa de escrever acesso a esse recurso. Esta verificação de permissões impede um utilizador não autorizado de enviar eventos para o seu recurso.

Tem de ter a permissão **Microsoft.EventGrid/EventSubscriptions/Write** no recurso que é a fonte do evento. Precisa desta permissão porque está a escrever uma nova subscrição no âmbito do recurso. O recurso necessário difere com base no facto de estar a subscrever um tópico do sistema ou um tópico personalizado. Ambos os tipos são descritos nesta secção.

### <a name="system-topics-azure-service-publishers"></a>Tópicos do sistema (editores de serviços Azure)
Para tópicos do sistema, precisa de permissão para escrever uma nova subscrição de eventos no âmbito da publicação de recursos do evento. O formato do recurso é: `/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{resource-provider}/{resource-type}/{resource-name}`

Por exemplo, para subscrever um evento numa conta de armazenamento chamada **myacct,** precisa da permissão microsoft.EventGrid/EventSubscriptions/Write on: `/subscriptions/####/resourceGroups/testrg/providers/Microsoft.Storage/storageAccounts/myacct`

### <a name="custom-topics"></a>Tópicos personalizados
Para tópicos personalizados, você precisa de permissão para escrever uma nova subscrição de evento no âmbito do tópico da grelha de eventos. O formato do recurso é: `/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.EventGrid/topics/{topic-name}`

Por exemplo, para subscrever um tópico personalizado chamado **mytopic,** precisa da permissão microsoft.EventGrid/EventSubscriptions/Write em: `/subscriptions/####/resourceGroups/testrg/providers/Microsoft.EventGrid/topics/mytopic`



## <a name="next-steps"></a>Passos seguintes

* Para uma introdução à Grade de Eventos, consulte [Sobre a Grelha de Eventos](overview.md)
