---
title: Segurança e autenticação da Rede de Eventos Azure
description: Este artigo descreve diferentes formas de autenticar o acesso aos seus recursos da Rede de Eventos (WebHook, subscrições, tópicos personalizados)
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 03/06/2020
ms.author: babanisa
ms.openlocfilehash: 0b7c5b42ac6291c6687337ba8d6a9d35830b9bda
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281018"
---
# <a name="authenticating-access-to-event-grid-resources"></a>Autenticação de acesso aos recursos da Rede de Eventos

A Azure Event Grid tem três tipos de autenticação:

* Entrega de eventos WebHook
* Subscrições de eventos
* Publicação de tópicos personalizados

## <a name="webhook-event-delivery"></a>Entrega do Evento WebHook

Webhooks são uma das muitas formas de receber eventos da Azure Event Grid. Quando um novo evento está pronto, o serviço De Event Grid POSTs um pedido HTTP para o ponto final configurado com o evento no organismo de pedido.

Como muitos outros serviços que suportam webhooks, a Event Grid requer que prove a propriedade do seu ponto final webhook antes de começar a entregar eventos a esse ponto final. Este requisito impede que um utilizador malicioso inunde o seu ponto final com eventos. Quando utiliza qualquer um dos três serviços Azure listados abaixo, a infraestrutura Azure trata automaticamente desta validação:

* Aplicativos da lógica azure com [conector](https://docs.microsoft.com/connectors/azureeventgrid/) de grelha de eventos
* Automação Azure via [webhook](../event-grid/ensure-tags-exists-on-new-virtual-machines.md)
* Funções Azure com [gatilho da grelha](../azure-functions/functions-bindings-event-grid.md) de evento

Se estiver a utilizar qualquer outro tipo de ponto final, como uma função Azure baseada em gatilho HTTP, o seu código de ponto final precisa de participar num aperto de mão de validação com a Grelha de Eventos. A Event Grid suporta duas formas de validar a subscrição.

1. **ValidaçãoAAAAA Aperto de mão Do Código (programático)**: Se controlar o código de origem para o seu ponto final, este método é recomendado. No momento da criação de subscrição de eventos, a Event Grid envia um evento de validação de subscrição para o seu ponto final. O esquema deste evento é semelhante a qualquer outro evento da Event Grid. A parte de dados `validationCode` deste evento inclui uma propriedade. A sua aplicação verifica que o pedido de validação é para uma subscrição de evento esperada, e faz eco do código de validação para A Grelha de Eventos. Este mecanismo de aperto de mão é suportado em todas as versões da Rede de Eventos.

2. **Aperto de mão validaçãoURL (manual)**: Em certos casos, não é possível aceder ao código fonte do ponto final para implementar o aperto de mão do ValidaçãoCódigo. Por exemplo, se utilizar um serviço de terceiros (como [zapier](https://zapier.com) ou [IFTTT),](https://ifttt.com/)não pode responder programáticamente com o código de validação.

   Começando com a versão 2018-05-01-pré-visualização, a Event Grid suporta um aperto de mão de validação manual. Se estiver a criar uma subscrição de evento com um SDK ou uma ferramenta que utiliza a versão `validationUrl` API 2018-05-01-pré-visualização ou posterior, o Event Grid envia uma propriedade na parte de dados do evento de validação de subscrição. Para completar o aperto de mão, encontre esse URL nos dados do evento e envie-lhe manualmente um pedido GET. Pode utilizar um cliente REST ou o seu navegador web.

   O URL fornecido é válido por 5 minutos. Durante esse período, o estado de `AwaitingManualAction`provisionamento da subscrição do evento é . Se não completar a validação manual dentro de 5 minutos, o estado de provisionamento está definido para `Failed`. Terá de criar novamente a subscrição do evento antes de iniciar a validação manual.

    Este mecanismo de autenticação também requer que o ponto final do webhook devolva um código de estado HTTP de 200 para que saiba que o POST para o evento de validação foi aceite antes de poder ser colocado no modo de validação manual. Por outras palavras, se o ponto final devolver 200, mas não devolver uma resposta de validação programáticamente, o modo é transitado para o modo de validação manual. Se houver um GET no URL de validação dentro de 5 minutos, o aperto de mão de validação é considerado um sucesso.

> [!NOTE]
> A utilização de certificados auto-assinados para validação não é suportada. Utilize um certificado assinado de uma autoridade de certificados (CA) em vez disso.

### <a name="validation-details"></a>Detalhes da validação

* No momento da criação/atualização de subscrição de eventos, a Event Grid publica um evento de validação de subscrição para o ponto final alvo. 
* O evento contém um valor cabeçalho "aeg-event-type: SubscriptionValidação".
* O corpo do evento tem o mesmo esquema que outros eventos da Grelha de Eventos.
* O eventoType propriedade do `Microsoft.EventGrid.SubscriptionValidationEvent`evento é .
* A propriedade de dados `validationCode` do evento inclui uma propriedade com uma cadeia gerada aleatoriamente. Por exemplo, "validaçãoCódigo: acb13...".
* Os dados do `validationUrl` evento também incluem uma propriedade com um URL para validação manual da subscrição.
* A matriz contém apenas o evento de validação. Outros eventos são enviados num pedido separado depois de fazer eco do código de validação.
* Os SDKs EventGrid DataPlane têm classes correspondentes aos dados do evento de validação de subscrição e à resposta de validação de subscrição.

Um exemplo De Validação De Validação De AssinaturaÉ mostrado no seguinte exemplo:

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

Para provar a propriedade do ponto final, faça eco do código de validação na propriedade validaçãoResponse, como mostra o seguinte exemplo:

```json
{
  "validationResponse": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"
}
```

Deve devolver um código de estado de resposta HTTP 200 OK. HTTP 202 Aceite não é reconhecido como uma resposta válida de validação de subscrição de Event Grid. O pedido http deve ser concluído dentro de 30 segundos. Se a operação não terminar dentro de 30 segundos, a operação será cancelada e poderá ser novamente tentada após 5 segundos. Se todas as tentativas falharem, será tratado como erro de aperto de mão de validação.

Ou, pode validar manualmente a subscrição enviando um pedido GET para o URL de validação. A subscrição do evento permanece em estado pendente até ser validada. O Url de validação utiliza a porta 553. Se as regras da firewall bloquearem a porta 553, as regras podem ter de ser atualizadas para um aperto de mão manual bem sucedido.

Para um exemplo de manipulação do aperto de mão de validação de subscrição, consulte uma [amostra C#](https://github.com/Azure-Samples/event-grid-dotnet-publish-consume-events/blob/master/EventGridConsumer/EventGridConsumer/Function1.cs).

### <a name="checklist"></a>Lista de Verificação

Durante a criação de subscrição de eventos, se estiver a ver\/uma mensagem de erro como "A tentativa de validar o ponto final fornecido https: /your-endpoint-here falhou. Para mais detalhes,\/visite https: /aka.ms/esvalidation", indica que há uma falha no aperto de mão de validação. Para resolver este erro, verifique os seguintes aspetos:

* Controla o código de aplicação em execução no ponto final do alvo? Por exemplo, se estiver a escrever uma Função Azure baseada em gatilho seleção HTTP, tem acesso ao código de aplicação para fazer alterações no mesmo?
* Se tiver acesso ao código de aplicação, implemente o mecanismo de aperto de mão baseado em ValidaçãoCode, tal como indicado na amostra acima.

* Se não tiver acesso ao código de aplicação (por exemplo, se estiver a utilizar um serviço de terceiros que suporta webhooks), pode utilizar o mecanismo de aperto de mão manual. Certifique-se de que está a utilizar a versão API de pré-visualização 2018-05-01 ou posteriormente (instalar a extensão ClI do Event Grid Azure) para receber o ValidaçãoUrl no evento de validação. Para completar o aperto de mão `validationUrl` de validação manual, obtenha o valor da propriedade e visite esse URL no seu navegador web. Se a validação for bem sucedida, deve ver uma mensagem no seu navegador web que a validação é bem sucedida. Verá que o fornecimento de informação da subscrição do evento é "Bem sucedido". 

### <a name="event-delivery-security"></a>Segurança de entrega de eventos

#### <a name="azure-ad"></a>Azure AD

Pode garantir o seu ponto final do webhook utilizando o Diretório Ativo Azure para autenticar e autorizar a Rede de Eventos a publicar eventos nos seus pontos finais. Você precisará criar uma Aplicação de Diretório Ativo Azure, criar um papel e princípio de serviço na sua aplicação autorizando a Grelha de Eventos, e configurar a subscrição do evento para usar a Aplicação AD Azure. [Saiba como configurar a AAD com](secure-webhook-delivery.md)a Grelha de Eventos .

#### <a name="query-parameters"></a>Parâmetros de consulta
Pode fixar o ponto final do webhook adicionando parâmetros de consulta ao URL do webhook ao criar uma Subscrição de Eventos. Defina um destes parâmetros de consulta como um segredo, como um [sinal de acesso.](https://en.wikipedia.org/wiki/Access_token) O webhook pode usar o segredo para reconhecer que o evento vem da Grelha de Eventos com permissões válidas. A Grelha de Eventos incluirá estes parâmetros de consulta em cada entrega de eventos ao webhook.

Ao editar a Subscrição do Evento, os parâmetros de consulta não são apresentados ou devolvidos a menos que o parâmetro [---incluído-final-final-url-url](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-show) seja usado no Azure [CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest).

Finalmente, é importante notar que a Azure Event Grid apenas suporta pontos finais https webhook.

## <a name="event-subscription"></a>Assinatura de evento

Para subscrever um evento, tem de provar que tem acesso à fonte e manipulador do evento. Provar que possui um WebHook foi coberto na secção anterior. Se estiver a usar um manipulador de eventos que não seja um WebHook (como um hub de eventos ou armazenamento de fila), precisa de ter acesso a esse recurso. Esta verificação de permissões impede um utilizador não autorizado de enviar eventos para o seu recurso.

Tem de ter a **microsoft.EventGrid/EventSubscriptions/Write** no recurso que é a fonte do evento. Precisa desta permissão porque está a escrever uma nova subscrição no âmbito do recurso. O recurso necessário difere com base no facto de estar a subscrever um tópico do sistema ou um tópico personalizado. Ambos os tipos são descritos nesta secção.

### <a name="system-topics-azure-service-publishers"></a>Tópicos do sistema (editores de serviços Azure)

Para tópicos do sistema, precisa de permissão para escrever uma nova subscrição de evento sintetizador no âmbito do recurso que publica o evento. O formato do recurso é:`/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{resource-provider}/{resource-type}/{resource-name}`

Por exemplo, para subscrever um evento numa conta de armazenamento chamada **myacct,** precisa da Microsoft.EventGrid/EventSubscriptions/Write permission em:`/subscriptions/####/resourceGroups/testrg/providers/Microsoft.Storage/storageAccounts/myacct`

### <a name="custom-topics"></a>Tópicos personalizados

Para tópicos personalizados, você precisa de permissão para escrever uma nova subscrição de evento no âmbito do tópico da grelha de eventos. O formato do recurso é:`/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.EventGrid/topics/{topic-name}`

Por exemplo, para subscrever um tópico personalizado chamado **mytopic,** você precisa da Microsoft.EventGrid/EventSubscriptions/Write permission em:`/subscriptions/####/resourceGroups/testrg/providers/Microsoft.EventGrid/topics/mytopic`

## <a name="custom-topic-publishing"></a>Publicação de tópicos personalizados

Os tópicos personalizados utilizam a Assinatura de Acesso Partilhado (SAS) ou a autenticação chave. Recomendamos o SAS, mas a autenticação chave fornece uma programação simples, e é compatível com muitos editores de webhook existentes. 

Inclui o valor de autenticação no cabeçalho HTTP. Para sas, utilize **aeg-sas-token** para o valor do cabeçalho. Para autenticação chave, utilize a **tecla aeg-sas** para o valor do cabeçalho.

### <a name="key-authentication"></a>Autenticação chave

A autenticação-chave é a forma mais simples de autenticação. Utilize o formato:`aeg-sas-key: <your key>`

Por exemplo, passa-se uma chave com:

```
aeg-sas-key: VXbGWce53249Mt8wuotr0GPmyJ/nDT4hgdEj9DpBeRr38arnnm5OFg==
```

### <a name="sas-tokens"></a>Tokens SAS

As fichas SAS para A Grelha de Eventos incluem o recurso, um tempo de validade e uma assinatura. O formato do token `r={resource}&e={expiration}&s={signature}`SAS é: .

O recurso é o caminho para o tópico da grelha de eventos para o qual está a enviar eventos. Por exemplo, um caminho de recursos válido é:`https://<yourtopic>.<region>.eventgrid.azure.net/eventGrid/api/events`

Gera-se a assinatura a partir de uma chave.

Por exemplo, um valor válido **aeg-sas-token** é:

```http
aeg-sas-token: r=https%3a%2f%2fmytopic.eventgrid.azure.net%2feventGrid%2fapi%2fevent&e=6%2f15%2f2017+6%3a20%3a15+PM&s=a4oNHpRZygINC%2fBPjdDLOrc6THPy3tDcGHw1zP4OajQ%3d
```

O exemplo seguinte cria um símbolo SAS para utilização com a Grelha de Eventos:

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
