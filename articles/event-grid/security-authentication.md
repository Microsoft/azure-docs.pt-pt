---
title: Segurança do Event Grid e autenticação do Azure
description: Descreve o Azure Event Grid e respetivos conceitos.
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: babanisa
ms.openlocfilehash: d66db88dd1e962acc63c960270bb5685218aa391
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58316076"
---
# <a name="event-grid-security-and-authentication"></a>Autenticação e segurança do Event Grid 

O Azure Event Grid tem três tipos de autenticação:

* Entrega de eventos de WebHook
* Subscrições de eventos
* Publicação de um tópico personalizado

## <a name="webhook-event-delivery"></a>Entrega de eventos de WebHook

Os Webhooks são uma das diversas formas de receber eventos do Azure Event Grid. Quando um novo evento estiver pronto, o serviço do Event Grid publica uma solicitação HTTP para o ponto final configurado com o evento no corpo do pedido.

Como muitos outros serviços que suportam webhooks, o Event Grid requer a provar a propriedade do ponto final do Webhook, antes de iniciar a entrega de eventos para esse ponto final. Esse requisito impede que um utilizador mal intencionado sobrecarregar o ponto final com eventos. Quando utiliza qualquer um dos três serviços do Azure listados abaixo, a infraestrutura do Azure processa automaticamente esta validação:

* Aplicações lógicas do Azure com [conector de grelha de eventos](https://docs.microsoft.com/connectors/azureeventgrid/)
* A automatização do Azure através de [webhook](../event-grid/ensure-tags-exists-on-new-virtual-machines.md)
* Das funções do Azure com [acionador do Event Grid](../azure-functions/functions-bindings-event-grid.md)

Se estiver a utilizar qualquer outro tipo de ponto de extremidade, como um acionador HTTP com base em função do Azure, o código de ponto final tem de participar de um handshake de validação com o Event Grid. Event Grid suporta duas formas de validar a subscrição.

1. **Handshake ValidationCode (programático)**: Se controlar o código-fonte para o ponto final, este método é recomendado. No momento da criação de subscrição de eventos, o Event Grid envia um evento de validação de subscrição para o ponto final. O esquema deste evento é semelhante a qualquer outro evento do Event Grid. A parte de dados deste evento inclui um `validationCode` propriedade. A aplicação verifica que o pedido de validação é uma subscrição de evento esperado e ecoa o código de validação para o Event Grid. Esse mecanismo de handshake é suportado em todas as versões do Event Grid.

2. **O handshake de ValidationURL (manual)**: Em certos casos, não é possível acessar o código-fonte do ponto de extremidade para implementar o handshake ValidationCode. Por exemplo, se usar um serviço de terceiros (como [Zapier](https://zapier.com) ou [IFTTT](https://ifttt.com/)), não pode responder por meio de programação com o código de validação.

   A partir da versão de 2018-05-01-pré-visualização, o Event Grid suporta um handshake de validação manual. Se estiver a criar uma subscrição de evento com um SDK ou ferramenta que utiliza a versão de 2018-05-01-a pré-visualização da API ou posterior, o Event Grid envia um `validationUrl` propriedade na parte de dados do evento de validação de subscrição. Para concluir o handshake, encontrar essa URL nos dados de eventos e manualmente enviar um pedido GET para o mesmo. Pode utilizar um cliente REST ou de seu navegador da web.

   O URL fornecido é válido durante 10 minutos. Durante esse tempo, o estado de aprovisionamento a subscrição de evento é `AwaitingManualAction`. Se não concluir a validação manual no prazo de 10 minutos, o estado de aprovisionamento é definido como `Failed`. Terá de criar a subscrição de evento novamente antes de iniciar a validação manual.

### <a name="validation-details"></a>Detalhes da validação

* No momento da criação/atualização de subscrição de evento, o Event Grid publica um evento de validação de subscrição para o ponto de extremidade de destino. 
* O evento contém um valor de cabeçalho "tipo de evento aeg: SubscriptionValidation".
* O corpo do evento tem o mesmo esquema que outros eventos do Event Grid.
* A propriedade eventType do evento é `Microsoft.EventGrid.SubscriptionValidationEvent`.
* A propriedade de dados do evento inclui um `validationCode` propriedade com uma cadeia de caracteres gerada aleatoriamente. Por exemplo, "validationCode: acb13...".
* Os dados de evento também incluem um `validationUrl` propriedade com um URL para a subscrição a validação manual.
* A matriz contém apenas o evento de validação. Outros eventos são enviados numa solicitação separada depois de recuperar o código de validação.
* Os SDKs do plano de dados de EventGrid possuem classes correspondentes para os dados de eventos de validação de subscrição e a resposta de validação de subscrição.

Um exemplo SubscriptionValidationEvent é mostrado no exemplo a seguir:

```json
[{
  "id": "2d1781af-3a4c-4d7c-bd0c-e34b19da4e66",
  "topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "subject": "",
  "data": {
    "validationCode": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6",
    "validationUrl": "https://rp-eastus2.eventgrid.azure.net:553/eventsubscriptions/estest/validate?id=B2E34264-7D71-453A-B5FB-B62D0FDC85EE&t=2018-04-26T20:30:54.4538837Z&apiVersion=2018-05-01-preview&token=1BNqCxBBSSE9OnNSfZM4%2b5H9zDegKMY6uJ%2fO2DFRkwQ%3d"
  },
  "eventType": "Microsoft.EventGrid.SubscriptionValidationEvent",
  "eventTime": "2018-01-25T22:12:19.4556811Z",
  "metadataVersion": "1",
  "dataVersion": "1"
}]
```

Para provar a propriedade de ponto de extremidade, retornar o código de validação na propriedade validationResponse, conforme mostrado no exemplo a seguir:

```json
{
  "validationResponse": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"
}
```

Tem de devolver um código de estado de resposta HTTP 200 OK. HTTP 202 aceite não é reconhecida como uma resposta de validação de subscrição do Event Grid válida.

Em alternativa, pode validar manualmente a subscrição ao enviar um pedido GET para o URL de validação. A subscrição de evento permanece no estado pendente até que sejam validados.

Para obter um exemplo de manipular o handshake de validação de assinatura, consulte um [ C# exemplo](https://github.com/Azure-Samples/event-grid-dotnet-publish-consume-events/blob/master/EventGridConsumer/EventGridConsumer/Function1.cs).

### <a name="checklist"></a>Lista de verificação

Durante a criação de subscrição de evento, se estiver a ver uma mensagem de erro, como "a tentativa de validar o https de ponto final fornecido:\//your-endpoint-here falhou. Para obter mais detalhes, visite https:\//aka.ms/esvalidation ", ele indica que existe uma falha no handshake de validação. Para resolver este erro, verifique se os seguintes aspetos:

* Tem controle de código da aplicação no ponto de extremidade de destino? Por exemplo, se estiver escrevendo um acionador HTTP com base em função do Azure, tem acesso ao código do aplicativo para fazer alterações ao mesmo?
* Se tiver acesso ao código do aplicativo, implemente o mecanismo de handshake ValidationCode com base, conforme mostrado no exemplo acima.

* Se não tiver acesso ao código do aplicativo (por exemplo, se estiver a utilizar um serviço de terceiros que suporte webhooks), pode usar o mecanismo de manual handshake. Certifique-se de que está a utilizar a versão de API de 2018-05-01-pré-visualização ou posterior (instalar extensão da CLI do Azure do Event Grid) para receber o validationUrl no evento de validação. Para concluir o handshake de validação manual, obter o valor da `validationUrl` propriedade e visitar esse URL no seu browser. Se a validação for concluída com êxito, deverá ver uma mensagem no seu navegador da web que a validação é efetuada com êxito. Verá que provisioningState de subscrição de evento é "concluído com êxito". 

### <a name="event-delivery-security"></a>Segurança de entrega de eventos

Pode proteger o seu ponto final do webhook ao adicionar parâmetros de consulta para o URL do webhook durante a criação de uma subscrição de evento. Definir um destes parâmetros de consulta para ser um segredo como um [token de acesso](https://en.wikipedia.org/wiki/Access_token). O webhook pode utilizar o segredo para reconhecer que o evento é proveniente de Event Grid com permissões válidas. Grelha de eventos irá incluir esses parâmetros de consulta em cada entrega de eventos para o webhook.

Ao editar a subscrição de evento, os parâmetros de consulta não são apresentados ou retornados, a menos que o [– incluir-full--url de ponto final](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-show) parâmetro é utilizado no Azure [CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest).

Por fim, é importante observar que o Azure Event Grid suporta apenas pontos finais do webhook HTTPS.

## <a name="event-subscription"></a>Subscrição de evento

Para subscrever um evento, tem de provar que tem acesso para a origem de evento e o manipulador. A provar que é proprietário de um WebHook foi coberta na secção anterior. Se estiver usando um manipulador de eventos que não seja um WebHook (por exemplo, um armazenamento de hub ou de fila de eventos), precisa de acesso de escrita a esse recurso. Esta verificação de permissões impede que um utilizador não autorizado o envio de eventos para o seu recurso.

Tem de ter o **Microsoft.EventGrid/EventSubscriptions/Write** permissão no recurso que é a origem de evento. Tem esta permissão porque estiver escrevendo uma nova subscrição no âmbito do recurso. O recurso necessário difere com base em se estiver subscrever um tópico de sistema ou um tópico personalizado. Ambos os tipos são descritos nesta secção.

### <a name="system-topics-azure-service-publishers"></a>Tópicos de sistema (editores de serviço do Azure)

Para os tópicos de sistema, necessita de permissão para escrever uma nova subscrição de evento no âmbito do recurso publicar o evento. O formato do recurso é: `/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{resource-provider}/{resource-type}/{resource-name}`

Por exemplo subscrever um evento numa conta de armazenamento com o nome **myacct**, tem de ter a permissão de Microsoft.EventGrid/EventSubscriptions/Write em: `/subscriptions/####/resourceGroups/testrg/providers/Microsoft.Storage/storageAccounts/myacct`

### <a name="custom-topics"></a>Tópicos personalizados

Para obter tópicos personalizados, necessita de permissão para escrever uma nova subscrição de evento no âmbito do tópico do event grid. O formato do recurso é: `/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.EventGrid/topics/{topic-name}`

Por exemplo subscrever um tópico personalizado com o nome **mytopic**, tem de ter a permissão de Microsoft.EventGrid/EventSubscriptions/Write em: `/subscriptions/####/resourceGroups/testrg/providers/Microsoft.EventGrid/topics/mytopic`

## <a name="custom-topic-publishing"></a>Publicação de um tópico personalizado

Tópicos personalizados utilizam a assinatura de acesso partilhado (SAS) ou autenticação de chave. Recomendamos a SAS, mas a autenticação de chave fornece a programação simple e é compatível com muitos publicadores existentes do webhook. 

Incluir o valor de autenticação no cabeçalho de HTTP. SAs, utilize **token de sas de aeg** para o valor de cabeçalho. Para autenticação de chave, utilize **chave de sas de aeg** para o valor de cabeçalho.

### <a name="key-authentication"></a>Autenticação de chave

Autenticação de chave é a forma mais simples de autenticação. Utilize o formato: `aeg-sas-key: <your key>`

Por exemplo, tem de transmitir uma chave com:

```
aeg-sas-key: VXbGWce53249Mt8wuotr0GPmyJ/nDT4hgdEj9DpBeRr38arnnm5OFg==
```

### <a name="sas-tokens"></a>Tokens SAS

Tokens SAS para o Event Grid incluem o recurso, um prazo de expiração e uma assinatura. O formato do SAS token é: `r={resource}&e={expiration}&s={signature}`.

O recurso é o caminho para o tópico do event grid para o qual está a enviar eventos. Por exemplo, é um caminho de recurso válido: `https://<yourtopic>.<region>.eventgrid.azure.net/eventGrid/api/events`

Gerar a assinatura a partir de uma chave.

Por exemplo, um válido **token de sas de aeg** valor é:

```http
aeg-sas-token: r=https%3a%2f%2fmytopic.eventgrid.azure.net%2feventGrid%2fapi%2fevent&e=6%2f15%2f2017+6%3a20%3a15+PM&s=a4oNHpRZygINC%2fBPjdDLOrc6THPy3tDcGHw1zP4OajQ%3d
```

O exemplo seguinte cria um token SAS para utilização com o Event Grid:

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

## <a name="management-access-control"></a>Controlo de acesso de gestão

O Azure Event Grid permite-lhe controlar o nível de acesso que é atribuído aos usuários diferentes para fazer várias operações de gestão, tais como as subscrições de eventos da lista, criar novos e gerar as chaves. Grelha de eventos utiliza o controlo de acesso baseado em funções do Azure (RBAC).

### <a name="operation-types"></a>Tipos de operação

Event Grid suporta as seguintes ações:

* Microsoft.EventGrid/*/read
* Microsoft.EventGrid/*/write
* Microsoft.EventGrid/*/delete
* Microsoft.EventGrid/eventSubscriptions/getFullUrl/action
* Microsoft.EventGrid/topics/listKeys/action
* Microsoft.EventGrid/topics/regenerateKey/action

As últimas três operações devolvem informações potencialmente secretas, que obtém filtradas dos operações de leitura normais. Recomenda-se que restringir o acesso a essas operações. 

### <a name="built-in-roles"></a>Funções incorporadas

Event Grid fornece duas funções incorporadas para gerir subscrições de eventos. Eles são importantes ao implementar [domínios de evento](event-domains.md) porque eles dar aos utilizadores as permissões necessárias para subscrever tópicos no seu domínio de evento. Estas funções estão focalizadas em subscrições de eventos e não a conceder acesso para ações como a criação de tópicos.

Pode [atribuir essas funções a um utilizador ou grupo](../role-based-access-control/quickstart-assign-role-user-portal.md).

**Contribuinte de EventSubscription EventGrid (pré-visualização)**: gerir as operações de subscrição do Event Grid

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

**Leitor de EventSubscription EventGrid (pré-visualização)**: ler subscrições do Event Grid

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

Se tiver de especificar permissões que são diferentes de funções incorporadas, pode criar funções personalizadas.

Seguem-se as definições de funções de grelha de eventos de exemplo que permitem aos utilizadores efetuar ações diferentes. Estas funções personalizadas são diferentes das funções incorporadas, porque eles concedem um acesso mais abrangente do que apenas as subscrições de eventos.

**EventGridReadOnlyRole.json**: Permitir apenas operações só de leitura.

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

**EventGridNoDeleteListKeysRole.json**: Permitir as ações post restrito, mas não permitir ações de eliminação.

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

**EventGridContributorRole.json**: Permite que todas as ações de grelha de eventos.

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

Pode criar funções personalizadas com [PowerShell](../role-based-access-control/custom-roles-powershell.md), [CLI do Azure](../role-based-access-control/custom-roles-cli.md), e [REST](../role-based-access-control/custom-roles-rest.md).

## <a name="next-steps"></a>Passos Seguintes

* Para obter uma introdução ao Event Grid, consulte [sobre o Event Grid](overview.md)
