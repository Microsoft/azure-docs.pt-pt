---
title: Segurança e autenticação da grade de eventos do Azure
description: Descreve o Azure Event Grid e respetivos conceitos.
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: babanisa
ms.openlocfilehash: f22d8c57b0127e646321a20587d0cd89f5c9ea45
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72325411"
---
# <a name="event-grid-security-and-authentication"></a>Segurança e autenticação da grade de eventos 

A grade de eventos do Azure tem três tipos de autenticação:

* Entrega de evento de webhook
* Subscrições de eventos
* Publicação de tópico personalizado

## <a name="webhook-event-delivery"></a>Entrega de evento de webhook

WebHooks são uma das várias maneiras de receber eventos da grade de eventos do Azure. Quando um novo evento estiver pronto, o serviço de grade de eventos postará uma solicitação HTTP para o ponto de extremidade configurado com o evento no corpo da solicitação.

Como muitos outros serviços que dão suporte a WebHooks, a grade de eventos exige que você comprove a propriedade do ponto de extremidade do webhook antes de começar a entregar eventos para esse ponto de extremidade. Esse requisito impede que um usuário mal-intencionado inundasse seu ponto de extremidade com eventos. Quando você usa qualquer um dos três serviços do Azure listados abaixo, a infraestrutura do Azure manipula automaticamente essa validação:

* Aplicativo lógico do Azure com [conector de grade de eventos](https://docs.microsoft.com/connectors/azureeventgrid/)
* Automação do Azure via [webhook](../event-grid/ensure-tags-exists-on-new-virtual-machines.md)
* Azure Functions com [gatilho de grade de eventos](../azure-functions/functions-bindings-event-grid.md)

Se você estiver usando qualquer outro tipo de ponto de extremidade, como um gatilho HTTP baseado no Azure Function, seu código de ponto de extremidade precisará participar de um handshake de validação com a grade de eventos. A grade de eventos dá suporte a duas maneiras de validar a assinatura.

1. **ValidationCode handshake (programático)** : se você controlar o código-fonte para seu ponto de extremidade, esse método é recomendado. No momento da criação da assinatura do evento, a grade de eventos envia um evento de validação de assinatura para seu ponto de extremidade. O esquema desse evento é semelhante a qualquer outro evento de grade de eventos. A parte de dados desse evento inclui uma propriedade `validationCode`. Seu aplicativo verifica se a solicitação de validação é para uma assinatura de evento esperada e ecoa o código de validação para a grade de eventos. Esse mecanismo de handshake tem suporte em todas as versões de grade de eventos.

2. **Handshake ValidationURL (manual)** : em determinados casos, você não pode acessar o código-fonte do ponto de extremidade para implementar o handshake ValidationCode. Por exemplo, se você usar um serviço de terceiros (como [Zapier](https://zapier.com) ou [IFTTT](https://ifttt.com/)), não poderá responder de forma programática com o código de validação.

   A partir da versão 2018-05-01-Preview, a grade de eventos dá suporte a um handshake de validação manual. Se você estiver criando uma assinatura de evento com um SDK ou ferramenta que usa a versão de API 2018-05-01-Preview ou posterior, a grade de eventos enviará uma propriedade `validationUrl` na parte de dados do evento de validação de assinatura. Para concluir o handshake, localize essa URL nos dados do evento e envie manualmente uma solicitação GET para ele. Você pode usar um cliente REST ou seu navegador da Web.

   A URL fornecida é válida por 5 minutos. Durante esse tempo, o estado de provisionamento da assinatura de evento é `AwaitingManualAction`. Se você não concluir a validação manual em 5 minutos, o estado de provisionamento será definido como `Failed`. Você precisará criar a assinatura de evento novamente antes de iniciar a validação manual.

    Esse mecanismo de autenticação também exige que o ponto de extremidade do webhook retorne um código de status HTTP 200 para que ele saiba que a POSTAgem do evento de validação foi aceita antes que possa ser colocada no modo de validação manual. Em outras palavras, se o ponto de extremidade retornar 200, mas não retornar uma resposta de validação programaticamente, o modo será transferido para o modo de validação manual. Se houver um GET na URL de validação em 5 minutos, o handshake de validação será considerado com êxito.

> [!NOTE]
> Não há suporte para o uso de certificados autoassinados para validação. Em vez disso, use um certificado assinado de uma autoridade de certificação (CA).

### <a name="validation-details"></a>Detalhes da validação

* No momento da criação/atualização da assinatura do evento, a grade de eventos posta um evento de validação de assinatura no ponto de extremidade de destino. 
* O evento contém um valor de cabeçalho "AEG-Event-Type: SubscriptionValidation".
* O corpo do evento tem o mesmo esquema que outros eventos da grade de eventos.
* A propriedade eventType do evento é `Microsoft.EventGrid.SubscriptionValidationEvent`.
* A propriedade data do evento inclui uma propriedade `validationCode` com uma cadeia de caracteres gerada aleatoriamente. Por exemplo, "validationCode: acb13...".
* Os dados do evento também incluem uma propriedade `validationUrl` com uma URL para validar manualmente a assinatura.
* A matriz contém apenas o evento de validação. Outros eventos são enviados em uma solicitação separada depois que você retorna o código de validação.
* Os SDKs do EventGrid dataplane têm classes correspondentes aos dados de eventos de validação de assinatura e à resposta de validação de assinatura.

Um exemplo de SubscriptionValidationEvent é mostrado no exemplo a seguir:

```json
[{
  "id": "2d1781af-3a4c-4d7c-bd0c-e34b19da4e66",
  "topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "subject": "",
  "data": {
    "validationCode": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6",
    "validationUrl": "https://rp-eastus2.eventgrid.azure.net:553/eventsubscriptions/estest/validate?id=512d38b6-c7b8-40c8-89fe-f46f9e9622b6&t=2018-04-26T20:30:54.4538837Z&apiVersion=2018-05-01-preview&token=1A1A1A1A"
  },
  "eventType": "Microsoft.EventGrid.SubscriptionValidationEvent",
  "eventTime": "2018-01-25T22:12:19.4556811Z",
  "metadataVersion": "1",
  "dataVersion": "1"
}]
```

Para provar a propriedade do ponto de extremidade, retorne o código de validação na propriedade validationResponse, conforme mostrado no exemplo a seguir:

```json
{
  "validationResponse": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"
}
```

Você deve retornar um código de status de resposta HTTP 200 OK. O HTTP 202 aceito não é reconhecido como uma resposta de validação de assinatura de grade de eventos válida.

Ou, você pode validar manualmente a assinatura enviando uma solicitação GET para a URL de validação. A assinatura de evento permanece em um estado pendente até ser validada.

Para obter um exemplo de tratamento do handshake de validação de assinatura, consulte um [ C# exemplo](https://github.com/Azure-Samples/event-grid-dotnet-publish-consume-events/blob/master/EventGridConsumer/EventGridConsumer/Function1.cs).

### <a name="checklist"></a>Lista de Verificação

Durante a criação da assinatura do evento, se você estiver vendo uma mensagem de erro como "a tentativa de validar o ponto de extremidade fornecido https: \//seu ponto de extremidade-aqui falhou. Para obter mais detalhes, visite https: \//aka. ms/esvalidation ", mas isso indica que há uma falha no handshake de validação. Para resolver esse erro, verifique os seguintes aspectos:

* Você tem controle do código do aplicativo no ponto de extremidade de destino? Por exemplo, se você estiver escrevendo um gatilho HTTP com base em uma função do Azure, você terá acesso ao código do aplicativo para fazer alterações nele?
* Se você tiver acesso ao código do aplicativo, implemente o mecanismo de handshake baseado em ValidationCode, conforme mostrado no exemplo acima.

* Se você não tiver acesso ao código do aplicativo (por exemplo, se estiver usando um serviço de terceiros que ofereça suporte a WebHooks), poderá usar o mecanismo de handshake manual. Verifique se você está usando a versão da API 2018-05-01-Preview ou posterior (instale a CLI do Azure extensão da grade de eventos) para receber o validationUrl no evento de validação. Para concluir o handshake de validação manual, obtenha o valor da propriedade `validationUrl` e visite essa URL no navegador da Web. Se a validação for bem-sucedida, você verá uma mensagem no navegador da Web de que a validação foi bem-sucedida. Você verá que o provisioningState da assinatura do evento é "êxito". 

### <a name="event-delivery-security"></a>Segurança de entrega de eventos

Você pode proteger o ponto de extremidade do webhook adicionando parâmetros de consulta à URL do webhook ao criar uma assinatura de evento. Defina um desses parâmetros de consulta como um segredo, como um [token de acesso](https://en.wikipedia.org/wiki/Access_token). O webhook pode usar o segredo para reconhecer que o evento é proveniente da grade de eventos com permissões válidas. A grade de eventos incluirá esses parâmetros de consulta em cada entrega de evento para o webhook.

Ao editar a assinatura de evento, os parâmetros de consulta não são exibidos ou retornados, a menos que o parâmetro [--include-Full-Endpoint-URL](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-show) seja usado na [CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest)do Azure.

Por fim, é importante observar que a grade de eventos do Azure dá suporte apenas a pontos de extremidade de webhook HTTPS.

## <a name="event-subscription"></a>Assinatura de evento

Para assinar um evento, você deve provar que tem acesso à origem e ao manipulador do evento. Provar que você possui um webhook foi abordado na seção anterior. Se você estiver usando um manipulador de eventos que não seja um webhook (como um hub de eventos ou armazenamento de fila), precisará de acesso de gravação a esse recurso. Essa verificação de permissões impede que um usuário não autorizado envie eventos para seu recurso.

Você deve ter a permissão **Microsoft. EventGrid/EventSubscriptions/Write** no recurso que é a origem do evento. Você precisa dessa permissão porque está escrevendo uma nova assinatura no escopo do recurso. O recurso necessário difere com base no fato de você estar assinando um tópico do sistema ou um tópico personalizado. Ambos os tipos são descritos nesta seção.

### <a name="system-topics-azure-service-publishers"></a>Tópicos do sistema (Publicadores de serviço do Azure)

Para tópicos do sistema, você precisa de permissão para gravar uma nova assinatura de evento no escopo do recurso que está publicando o evento. O formato do recurso é: `/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{resource-provider}/{resource-type}/{resource-name}`

Por exemplo, para assinar um evento em uma conta de armazenamento chamada **MyAcct**, você precisa da permissão Microsoft. EventGrid/EventSubscriptions/Write em: `/subscriptions/####/resourceGroups/testrg/providers/Microsoft.Storage/storageAccounts/myacct`

### <a name="custom-topics"></a>Tópicos personalizados

Para tópicos personalizados, você precisa de permissão para gravar uma nova assinatura de evento no escopo do tópico da grade de eventos. O formato do recurso é: `/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.EventGrid/topics/{topic-name}`

Por exemplo, para assinar um tópico personalizado chamado **mytopic**, você precisa da permissão Microsoft. EventGrid/EventSubscriptions/Write em: `/subscriptions/####/resourceGroups/testrg/providers/Microsoft.EventGrid/topics/mytopic`

## <a name="custom-topic-publishing"></a>Publicação de tópico personalizado

Os tópicos personalizados usam a assinatura de acesso compartilhado (SAS) ou a autenticação de chave. Recomendamos SAS, mas a autenticação de chave fornece programação simples e é compatível com muitos Publicadores de webhook existentes. 

Você inclui o valor de autenticação no cabeçalho HTTP. Para SAS, use **AEG-SAS-token** para o valor do cabeçalho. Para a autenticação de chave, use **AEG-SAS-Key** para o valor de cabeçalho.

### <a name="key-authentication"></a>Autenticação de chave

A autenticação de chave é a forma mais simples de autenticação. Use o formato: `aeg-sas-key: <your key>`

Por exemplo, você passa uma chave com:

```
aeg-sas-key: VXbGWce53249Mt8wuotr0GPmyJ/nDT4hgdEj9DpBeRr38arnnm5OFg==
```

### <a name="sas-tokens"></a>Tokens SAS

Os tokens SAS para a grade de eventos incluem o recurso, um tempo de expiração e uma assinatura. O formato do token SAS é: `r={resource}&e={expiration}&s={signature}`.

O recurso é o caminho para o tópico da grade de eventos para o qual você está enviando eventos. Por exemplo, um caminho de recurso válido é: `https://<yourtopic>.<region>.eventgrid.azure.net/eventGrid/api/events`

Você gera a assinatura de uma chave.

Por exemplo, um valor **AEG-SAS-token** válido é:

```http
aeg-sas-token: r=https%3a%2f%2fmytopic.eventgrid.azure.net%2feventGrid%2fapi%2fevent&e=6%2f15%2f2017+6%3a20%3a15+PM&s=a4oNHpRZygINC%2fBPjdDLOrc6THPy3tDcGHw1zP4OajQ%3d
```

O exemplo a seguir cria um token SAS para uso com a grade de eventos:

```cs
static string BuildSharedAccessSignature(string resource, DateTime expirationUtc, string key)
{
    const char Resource = 'r';
    const char Expiration = 'e';
    const char Signature = 's';

    string encodedResource = HttpUtility.UrlEncode(resource);
    var culture = CultureInfo.CreateSpecificCulture("en-US");
    var encodedExpirationUtc = HttpUtility.UrlEncode(expirationUtc.ToString(culture));

    string unsignedSas = $"{Resource}={encodedResource}&{Expiration}={encodedExpirationUtc}";
    using (var hmac = new HMACSHA256(Convert.FromBase64String(key)))
    {
        string signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(unsignedSas)));
        string encodedSignature = HttpUtility.UrlEncode(signature);
        string signedSas = $"{unsignedSas}&{Signature}={encodedSignature}";

        return signedSas;
    }
}
```

## <a name="management-access-control"></a>Controle de acesso de gerenciamento

A grade de eventos do Azure permite que você controle o nível de acesso fornecido a diferentes usuários para realizar várias operações de gerenciamento, como listar assinaturas de evento, criar novos e gerar chaves. A grade de eventos usa o RBAC (controle de acesso baseado em função) do Azure.

### <a name="operation-types"></a>Tipos de operação

A grade de eventos oferece suporte às seguintes ações:

* Microsoft. EventGrid/*/Read
* Microsoft. EventGrid/*/Write
* Microsoft. EventGrid/*/Delete
* Microsoft. EventGrid/eventSubscriptions/getFullUrl/Action
* Microsoft. EventGrid/topics/listKeys/Action
* Microsoft. EventGrid/topics/regenerateKey/Action

As últimas três operações retornam informações potencialmente secretas, que são filtradas de operações de leitura normais. É recomendável restringir o acesso a essas operações. 

### <a name="built-in-roles"></a>Funções incorporadas

A grade de eventos fornece duas funções internas para o gerenciamento de assinaturas de evento. Eles são importantes ao implementar [domínios de evento](event-domains.md) porque fornecem aos usuários as permissões de que eles precisam para assinar tópicos em seu domínio de evento. Essas funções se concentram em assinaturas de eventos e não concedem acesso a ações como a criação de tópicos.

Você pode [atribuir essas funções a um usuário ou grupo](../role-based-access-control/quickstart-assign-role-user-portal.md).

**Colaborador do EventGrid EventSubscription (visualização)** : gerenciar operações de assinatura da grade de eventos

```json
[
  {
    "Description": "Lets you manage EventGrid event subscription operations.",
    "IsBuiltIn": true,
    "Id": "428e0ff05e574d9ca2212c70d0e0a443",
    "Name": "EventGrid EventSubscription Contributor (Preview)",
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

**Leitor EventGrid EventSubscription (versão prévia)** : ler assinaturas de grade de eventos

```json
[
  {
    "Description": "Lets you read EventGrid event subscriptions.",
    "IsBuiltIn": true,
    "Id": "2414bbcf64974faf8c65045460748405",
    "Name": "EventGrid EventSubscription Reader (Preview)",
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

### <a name="custom-roles"></a>Funções personalizadas

Se precisar especificar permissões que sejam diferentes das funções internas, você poderá criar funções personalizadas.

Veja a seguir as definições de função da grade de eventos de exemplo que permitem que os usuários executem ações diferentes. Essas funções personalizadas são diferentes das funções internas porque concedem acesso mais amplo do que apenas assinaturas de evento.

**EventGridReadOnlyRole. JSON**: permitir somente operações somente leitura.

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

**EventGridNoDeleteListKeysRole. JSON**: permitir ações de postagem restritas, mas não permitir ações de exclusão.

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

**EventGridContributorRole. JSON**: permite todas as ações da grade de eventos.

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

Você pode criar funções personalizadas com o [PowerShell](../role-based-access-control/custom-roles-powershell.md), [CLI do Azure](../role-based-access-control/custom-roles-cli.md)e [REST](../role-based-access-control/custom-roles-rest.md).

## <a name="next-steps"></a>Passos seguintes

* Para obter uma introdução à grade de eventos, consulte [sobre a grade de eventos](overview.md)
