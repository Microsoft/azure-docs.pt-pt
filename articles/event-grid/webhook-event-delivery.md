---
title: Entrega de eventos WebHook
description: Este artigo descreve a entrega de eventos WebHook e a validação de pontos finais ao utilizar webhooks.
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 03/06/2020
ms.author: babanisa
ms.openlocfilehash: 80efee18ff7cc927ea9029c11aadcf13ad75781a
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83747602"
---
# <a name="webhook-event-delivery"></a>Entrega do Evento Webhook
Webhooks são uma das muitas formas de receber eventos da Azure Event Grid. Quando um novo evento está pronto, o serviço De Event Grid POSTs um pedido HTTP para o ponto final configurado com o evento no organismo de pedido.

Como muitos outros serviços que suportam webhooks, a Event Grid requer que prove a propriedade do seu ponto final webhook antes de começar a entregar eventos a esse ponto final. Este requisito impede que um utilizador malicioso inunde o seu ponto final com eventos. Quando utiliza qualquer um dos três serviços Azure listados abaixo, a infraestrutura Azure trata automaticamente desta validação:

- Aplicativos da lógica azure com [conector](https://docs.microsoft.com/connectors/azureeventgrid/) de grelha de eventos
- Automação Azure via [webhook](../event-grid/ensure-tags-exists-on-new-virtual-machines.md)
- Funções Azure com [gatilho da grelha](../azure-functions/functions-bindings-event-grid.md) de evento

## <a name="endpoint-validation-with-event-grid-events"></a>Validação de ponto final com eventos da Grelha de Eventos
Se estiver a utilizar qualquer outro tipo de ponto final, como uma função Azure baseada em gatilho HTTP, o seu código de ponto final precisa de participar num aperto de mão de validação com a Grelha de Eventos. A Event Grid suporta duas formas de validar a subscrição.

1. **Aperto de mão sincronizado**: No momento da criação de subscrição de eventos, a Event Grid envia um evento de validação de subscrição para o seu ponto final. O esquema deste evento é semelhante a qualquer outro evento da Event Grid. A parte de dados deste evento inclui uma `validationCode` propriedade. A sua aplicação verifica que o pedido de validação é para uma subscrição de evento esperada, e devolve o código de validação na resposta sincronizadamente. Este mecanismo de aperto de mão é suportado em todas as versões da Rede de Eventos.

2. **Aperto de mão assíncrono**: Em certos casos, não pode devolver o Código de Validação em resposta sincronizadamente. Por exemplo, se utilizar um serviço de terceiros (como [`Zapier`](https://zapier.com) ou [IFTTT),](https://ifttt.com/)não pode responder programáticamente com o código de validação.

   Começando com a versão 2018-05-01-pré-visualização, a Event Grid suporta um aperto de mão de validação manual. Se estiver a criar uma subscrição de evento com um SDK ou uma ferramenta que utiliza a versão API 2018-05-01-pré-visualização ou posterior, o Event Grid envia uma `validationUrl` propriedade na parte de dados do evento de validação de subscrição. Para completar o aperto de mão, encontre esse URL nos dados do evento e faça-lhe um pedido get. Pode utilizar um cliente REST ou o seu navegador web.

   O URL fornecido é válido por **5 minutos.** Durante esse período, o estado de provisionamento da subscrição do evento é `AwaitingManualAction` . Se não completar a validação manual dentro de 5 minutos, o estado de provisionamento está definido para `Failed` . Terá de criar novamente a subscrição do evento antes de iniciar a validação manual.

   Este mecanismo de autenticação também requer que o ponto final do webhook devolva um código de estado HTTP de 200 para que saiba que o POST para o evento de validação foi aceite antes de poder ser colocado no modo de validação manual. Por outras palavras, se o ponto final devolver 200, mas não devolver uma resposta de validação sincronizadamente, o modo é transitado para o modo de validação manual. Se houver um GET no URL de validação dentro de 5 minutos, o aperto de mão de validação é considerado um sucesso.

> [!NOTE]
> A utilização de certificados auto-assinados para validação não é suportada. Utilize um certificado assinado de uma autoridade de certificadocomercial (CA) em vez disso.

### <a name="validation-details"></a>Detalhes da validação

- No momento da criação/atualização de subscrição de eventos, a Event Grid publica um evento de validação de subscrição para o ponto final alvo.
- O evento contém um valor cabeçalho "aeg-event-type: SubscriptionValidação".
- O corpo do evento tem o mesmo esquema que outros eventos da Grelha de Eventos.
- O eventoType propriedade do evento é `Microsoft.EventGrid.SubscriptionValidationEvent` .
- A propriedade de dados do evento inclui uma `validationCode` propriedade com uma cadeia gerada aleatoriamente. Por exemplo, "validaçãoCódigo: acb13...".
- Os dados do evento também incluem uma `validationUrl` propriedade com um URL para validação manual da subscrição.
- A matriz contém apenas o evento de validação. Outros eventos são enviados num pedido separado depois de fazer eco do código de validação.
- Os SDKs EventGrid DataPlane têm classes correspondentes aos dados do evento de validação de subscrição e à resposta de validação de subscrição.

Um exemplo De Validação De Validação De AssinaturaÉ mostrado no seguinte exemplo:

```json
[
  {
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
  }
]
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

## <a name="endpoint-validation-with-cloudevents-v10"></a>Validação do ponto final com CloudEvents v1.0
Se já está familiarizado com a Grelha de Eventos, pode estar ciente do aperto de mão de validação de ponto final da Event Grid para prevenir abusos. CloudEvents v1.0 implementa a sua própria [semântica](webhook-event-delivery.md) de proteção contra abusos utilizando o método HTTP OPTIONS. Pode ler mais sobre o assunto [aqui](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#4-abuse-protection). Ao utilizar o esquema CloudEvents para saída, a Rede de Eventos utiliza com a proteção de abuso CloudEvents v1.0 em vez do mecanismo de eventode validação da Rede de Eventos.

## <a name="next-steps"></a>Passos seguintes
Consulte o seguinte artigo para aprender a resolver as validações de subscrição de eventos: 

[Validações de subscrição de eventos de resolução de problemas](troubleshoot-subscription-validation.md)
