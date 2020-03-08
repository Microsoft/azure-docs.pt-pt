---
title: Segurança do Event Grid e autenticação do Azure
description: Este artigo descreve diferentes formas de autenticar o acesso aos seus recursos da Rede de Eventos (WebHook, subscrições, tópicos personalizados)
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 03/06/2020
ms.author: babanisa
ms.openlocfilehash: 0b7c5b42ac6291c6687337ba8d6a9d35830b9bda
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2020
ms.locfileid: "78924782"
---
# <a name="authenticating-access-to-event-grid-resources"></a>Autenticação de acesso aos recursos da Rede de Eventos

O Azure Event Grid tem três tipos de autenticação:

* Entrega de eventos de WebHook
* Subscrições de eventos
* Publicação de um tópico personalizado

## <a name="webhook-event-delivery"></a>Entrega de eventos de WebHook

Os Webhooks são uma das diversas formas de receber eventos do Azure Event Grid. Quando um novo evento estiver pronto, o serviço do Event Grid publica uma solicitação HTTP para o ponto final configurado com o evento no corpo do pedido.

Como muitos outros serviços que suportam webhooks, o Event Grid requer a provar a propriedade do ponto final do Webhook, antes de iniciar a entrega de eventos para esse ponto final. Esse requisito impede que um utilizador mal intencionado sobrecarregar o ponto final com eventos. Quando utiliza qualquer um dos três serviços do Azure listados abaixo, a infraestrutura do Azure processa automaticamente esta validação:

* Aplicativos da lógica azure com [conector](https://docs.microsoft.com/connectors/azureeventgrid/) de grelha de eventos
* Automação Azure via [webhook](../event-grid/ensure-tags-exists-on-new-virtual-machines.md)
* Funções Azure com [gatilho da grelha](../azure-functions/functions-bindings-event-grid.md) de evento

Se estiver a utilizar qualquer outro tipo de ponto de extremidade, como um acionador HTTP com base em função do Azure, o código de ponto final tem de participar de um handshake de validação com o Event Grid. Event Grid suporta duas formas de validar a subscrição.

1. **ValidaçãoAAAAA Aperto de mão Do Código (programático)** : Se controlar o código de origem para o seu ponto final, este método é recomendado. No momento da criação de subscrição de eventos, o Event Grid envia um evento de validação de subscrição para o ponto final. O esquema deste evento é semelhante a qualquer outro evento do Event Grid. A parte de dados deste evento inclui uma propriedade `validationCode`. A aplicação verifica que o pedido de validação é uma subscrição de evento esperado e ecoa o código de validação para o Event Grid. Esse mecanismo de handshake é suportado em todas as versões do Event Grid.

2. **Aperto de mão validaçãoURL (manual)** : Em certos casos, não é possível aceder ao código fonte do ponto final para implementar o aperto de mão do ValidaçãoCódigo. Por exemplo, se utilizar um serviço de terceiros (como [zapier](https://zapier.com) ou [IFTTT),](https://ifttt.com/)não pode responder programáticamente com o código de validação.

   A partir da versão de 2018-05-01-pré-visualização, o Event Grid suporta um handshake de validação manual. Se estiver a criar uma subscrição de evento com um SDK ou uma ferramenta que utiliza a versão API 2018-05-01-pré-visualização ou posterior, o Event Grid envia uma propriedade `validationUrl` na parte de dados do evento de validação de subscrição. Para concluir o handshake, encontrar essa URL nos dados de eventos e manualmente enviar um pedido GET para o mesmo. Pode utilizar um cliente REST ou de seu navegador da web.

   O URL fornecido é válido por 5 minutos. Durante esse período, o estado de provisionamento da subscrição do evento é `AwaitingManualAction`. Se não completar a validação manual dentro de 5 minutos, o estado de provisionamento está definido para `Failed`. Terá de criar a subscrição de evento novamente antes de iniciar a validação manual.

    Este mecanismo de autenticação também requer que o ponto final do webhook devolva um código de estado HTTP de 200 para que saiba que o POST para o evento de validação foi aceite antes de poder ser colocado no modo de validação manual. Por outras palavras, se o ponto final devolver 200, mas não devolver uma resposta de validação programáticamente, o modo é transitado para o modo de validação manual. Se houver um GET no URL de validação dentro de 5 minutos, o aperto de mão de validação é considerado um sucesso.

> [!NOTE]
> A utilização de certificados auto-assinados para validação não é suportada. Utilize um certificado assinado de uma autoridade de certificados (CA) em vez disso.

### <a name="validation-details"></a>Detalhes da validação

* No momento da criação/atualização de subscrição de evento, o Event Grid publica um evento de validação de subscrição para o ponto de extremidade de destino. 
* O evento contém um valor de cabeçalho "tipo de evento aeg: SubscriptionValidation".
* O corpo do evento tem o mesmo esquema que outros eventos do Event Grid.
* A propriedade eventType do evento é `Microsoft.EventGrid.SubscriptionValidationEvent`.
* A propriedade de dados do evento inclui uma propriedade `validationCode` com uma cadeia gerada aleatoriamente. Por exemplo, "validationCode: acb13...".
* Os dados do evento também incluem uma propriedade `validationUrl` com um URL para validação manual da subscrição.
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
    "validationUrl": "https://rp-eastus2.eventgrid.azure.net:553/eventsubscriptions/estest/validate?id=512d38b6-c7b8-40c8-89fe-f46f9e9622b6&t=2018-04-26T20:30:54.4538837Z&apiVersion=2018-05-01-preview&token=1A1A1A1A"
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

Deve devolver um código de estado de resposta HTTP 200 OK. HTTP 202 Aceite não é reconhecido como uma resposta válida de validação de subscrição de Event Grid. O pedido http deve ser concluído dentro de 30 segundos. Se a operação não terminar dentro de 30 segundos, a operação será cancelada e poderá ser novamente tentada após 5 segundos. Se todas as tentativas falharem, será tratado como erro de aperto de mão de validação.

Em alternativa, pode validar manualmente a subscrição ao enviar um pedido GET para o URL de validação. A subscrição de evento permanece no estado pendente até que sejam validados. O Url de validação utiliza a porta 553. Se as regras da firewall bloquearem a porta 553, as regras podem ter de ser atualizadas para um aperto de mão manual bem sucedido.

Para um exemplo de manipulação do [ C# ](https://github.com/Azure-Samples/event-grid-dotnet-publish-consume-events/blob/master/EventGridConsumer/EventGridConsumer/Function1.cs)aperto de mão de validação de subscrição, consulte uma amostra .

### <a name="checklist"></a>Lista de Verificação

Durante a criação de subscrição de eventos, se estiver a ver uma mensagem de erro como "A tentativa de validar o ponto final fornecido https:\//your-endpoint-here falhou. Para mais detalhes, visite https:\//aka.ms/esvalidation", indica ndo que há uma falha no aperto de mão de validação. Para resolver este erro, verifique se os seguintes aspetos:

* Controla o código de aplicação em execução no ponto final do alvo? Por exemplo, se estiver escrevendo um acionador HTTP com base em função do Azure, tem acesso ao código do aplicativo para fazer alterações ao mesmo?
* Se tiver acesso ao código do aplicativo, implemente o mecanismo de handshake ValidationCode com base, conforme mostrado no exemplo acima.

* Se não tiver acesso ao código do aplicativo (por exemplo, se estiver a utilizar um serviço de terceiros que suporte webhooks), pode usar o mecanismo de manual handshake. Certifique-se de que está a utilizar a versão de API de 2018-05-01-pré-visualização ou posterior (instalar extensão da CLI do Azure do Event Grid) para receber o validationUrl no evento de validação. Para completar o aperto de mão de validação manual, obtenha o valor da propriedade `validationUrl` e visite esse URL no seu navegador web. Se a validação for concluída com êxito, deverá ver uma mensagem no seu navegador da web que a validação é efetuada com êxito. Verá que provisioningState de subscrição de evento é "concluído com êxito". 

### <a name="event-delivery-security"></a>Segurança de entrega de eventos

#### <a name="azure-ad"></a>Azure AD

Pode garantir o seu ponto final do webhook utilizando o Diretório Ativo Azure para autenticar e autorizar a Rede de Eventos a publicar eventos nos seus pontos finais. Você precisará criar uma Aplicação de Diretório Ativo Azure, criar um papel e princípio de serviço na sua aplicação autorizando a Grelha de Eventos, e configurar a subscrição do evento para usar a Aplicação AD Azure. [Saiba como configurar a AAD com](secure-webhook-delivery.md)a Grelha de Eventos .

#### <a name="query-parameters"></a>Parâmetros de consulta
Pode proteger o seu ponto final do webhook ao adicionar parâmetros de consulta para o URL do webhook durante a criação de uma subscrição de evento. Defina um destes parâmetros de consulta como um segredo, como um [sinal de acesso.](https://en.wikipedia.org/wiki/Access_token) O webhook pode utilizar o segredo para reconhecer que o evento é proveniente de Event Grid com permissões válidas. Grelha de eventos irá incluir esses parâmetros de consulta em cada entrega de eventos para o webhook.

Ao editar a Subscrição do Evento, os parâmetros de consulta não são apresentados ou devolvidos a menos que o parâmetro [---incluído-final-final-url-url](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-show) seja usado no Azure [CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest).

Por fim, é importante observar que o Azure Event Grid suporta apenas pontos finais do webhook HTTPS.

## <a name="event-subscription"></a>Subscrição de evento

Para subscrever um evento, tem de provar que tem acesso para a origem de evento e o manipulador. A provar que é proprietário de um WebHook foi coberta na secção anterior. Se estiver usando um manipulador de eventos que não seja um WebHook (por exemplo, um armazenamento de hub ou de fila de eventos), precisa de acesso de escrita a esse recurso. Esta verificação de permissões impede que um utilizador não autorizado o envio de eventos para o seu recurso.

Tem de ter a **microsoft.EventGrid/EventSubscriptions/Write** no recurso que é a fonte do evento. Tem esta permissão porque estiver escrevendo uma nova subscrição no âmbito do recurso. O recurso necessário difere com base em se estiver subscrever um tópico de sistema ou um tópico personalizado. Ambos os tipos são descritos nesta secção.

### <a name="system-topics-azure-service-publishers"></a>Tópicos de sistema (editores de serviço do Azure)

Para os tópicos de sistema, necessita de permissão para escrever uma nova subscrição de evento no âmbito do recurso publicar o evento. O formato do recurso é: `/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{resource-provider}/{resource-type}/{resource-name}`

Por exemplo, para subscrever um evento numa conta de armazenamento chamada **myacct,** precisa da Microsoft.EventGrid/EventSubscriptions/Write permission em: `/subscriptions/####/resourceGroups/testrg/providers/Microsoft.Storage/storageAccounts/myacct`

### <a name="custom-topics"></a>Tópicos personalizados

Para obter tópicos personalizados, necessita de permissão para escrever uma nova subscrição de evento no âmbito do tópico do event grid. O formato do recurso é: `/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.EventGrid/topics/{topic-name}`

Por exemplo, para subscrever um tópico personalizado chamado **mytopic,** você precisa da Microsoft.EventGrid/EventSubscriptions/Write permission on: `/subscriptions/####/resourceGroups/testrg/providers/Microsoft.EventGrid/topics/mytopic`

## <a name="custom-topic-publishing"></a>Publicação de um tópico personalizado

Tópicos personalizados utilizam a assinatura de acesso partilhado (SAS) ou autenticação de chave. Recomendamos a SAS, mas a autenticação de chave fornece a programação simple e é compatível com muitos publicadores existentes do webhook. 

Incluir o valor de autenticação no cabeçalho de HTTP. Para sas, utilize **aeg-sas-token** para o valor do cabeçalho. Para autenticação chave, utilize a **tecla aeg-sas** para o valor do cabeçalho.

### <a name="key-authentication"></a>Autenticação de chave

Autenticação de chave é a forma mais simples de autenticação. Utilize o formato: `aeg-sas-key: <your key>`

Por exemplo, tem de transmitir uma chave com:

```
aeg-sas-key: VXbGWce53249Mt8wuotr0GPmyJ/nDT4hgdEj9DpBeRr38arnnm5OFg==
```

### <a name="sas-tokens"></a>Tokens SAS

Tokens SAS para o Event Grid incluem o recurso, um prazo de expiração e uma assinatura. O formato do token SAS é: `r={resource}&e={expiration}&s={signature}`.

O recurso é o caminho para o tópico do event grid para o qual está a enviar eventos. Por exemplo, um caminho de recursos válido é: `https://<yourtopic>.<region>.eventgrid.azure.net/eventGrid/api/events`

Gerar a assinatura a partir de uma chave.

Por exemplo, um valor válido **aeg-sas-token** é:

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

### <a name="encryption-at-rest"></a>Encriptação inativa

Todos os eventos ou dados escritos em disco pelo serviço Event Grid são encriptados por uma chave gerida pela Microsoft, garantindo que está encriptado em repouso. Adicionalmente, o período máximo de tempo que os eventos ou dados retidos é de 24 horas de adesão à política de [retry da Rede](delivery-and-retry.md)de Eventos . A Grelha de Eventos eliminará automaticamente todos os eventos ou dados após 24 horas, ou o evento tem tempo de vida, o que for menor.

## <a name="next-steps"></a>Passos seguintes

* Para uma introdução à Grelha de Eventos, consulte sobre a grelha de [eventos](overview.md)
