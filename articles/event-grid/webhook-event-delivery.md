---
title: Entrega de eventos WebHook
description: Este artigo descreve a entrega de eventos WebHook e a validação do ponto final quando se utiliza webhooks.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: e9a52d0cb3e4e880d91e1b748d97ef3041298930
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "87461243"
---
# <a name="webhook-event-delivery"></a>Entrega do Evento Webhook
Webhooks são uma das muitas formas de receber eventos da Azure Event Grid. Quando um novo evento estiver pronto, o serviço de Rede de Eventos POSTs um pedido HTTP para o ponto final configurado com o evento no organismo de pedido.

Como muitos outros serviços que suportam webhooks, o Event Grid requer que você prove a propriedade do seu ponto final webhook antes que comece a entregar eventos a esse ponto final. Este requisito impede que um utilizador malicioso inunde o seu ponto final com eventos. Quando utiliza qualquer um dos três serviços Azure listados abaixo, a infraestrutura Azure lida automaticamente com esta validação:

- Aplicativos Azure Logic com [Conector de Grelha de Evento](/connectors/azureeventgrid/)
- Azure Automation via [webhook](../event-grid/ensure-tags-exists-on-new-virtual-machines.md)
- Funções Azure com [gatilho de grelha de evento](../azure-functions/functions-bindings-event-grid.md)

## <a name="endpoint-validation-with-event-grid-events"></a>Validação de ponto final com eventos de Grade de Eventos
Se estiver a utilizar qualquer outro tipo de ponto final, como uma função Azure baseada no gatilho HTTP, o seu código de ponto final tem de participar num aperto de mão de validação com a Grade de Eventos. O Event Grid suporta duas formas de validar a subscrição.

1. **Aperto de mão sincronizado**: No momento da criação de subscrição de eventos, o Event Grid envia um evento de validação de subscrição para o seu ponto final. O esquema deste evento é semelhante a qualquer outro evento da Grade de Eventos. A parte de dados deste evento inclui uma `validationCode` propriedade. A sua aplicação verifica que o pedido de validação é para uma subscrição de evento esperada e devolve o código de validação na resposta de forma sincronizada. Este mecanismo de aperto de mão é suportado em todas as versões Event Grid.

2. **Aperto de mão assíncronos**: Em certos casos, não é possível devolver o Código de Validação em resposta de forma sincronizada. Por exemplo, se utilizar um serviço de terceiros (como [`Zapier`](https://zapier.com) ou [IFTTT),](https://ifttt.com/)não poderá responder programáticamente com o código de validação.

   Começando pela versão 2018-05-01-pré-visualização, a Grade de Eventos suporta um aperto de mão de validação manual. Se estiver a criar uma subscrição de eventos com um SDK ou uma ferramenta que utilize a versão API 2018-05-01-pré-visualização ou posterior, a Event Grid envia uma `validationUrl` propriedade na parte de dados do evento de validação de subscrição. Para completar o aperto de mão, encontre o URL nos dados do evento e faça um pedido GET. Pode utilizar um cliente REST ou o seu navegador web.

   O URL fornecido é válido por **5 minutos.** Durante esse período, o estado de provisionamento da subscrição do evento é `AwaitingManualAction` . Se não completar a validação manual dentro de 5 minutos, o estado de provisionamento está definido para `Failed` . Terá de criar novamente a subscrição do evento antes de iniciar a validação manual.

   Este mecanismo de autenticação também requer que o ponto final webhook devolva um código de estado HTTP de 200 para que saiba que o POST para o evento de validação foi aceite antes de poder ser colocado no modo de validação manual. Por outras palavras, se o ponto final devolver 200 mas não devolver uma resposta de validação sincronizadamente, o modo é transitado para o modo de validação manual. Se houver um GET no URL de validação dentro de 5 minutos, o aperto de mão de validação é considerado um sucesso.

> [!NOTE]
> A utilização de certificados auto-assinados para validação não é suportada. Utilize um certificado assinado por uma autoridade de certificados comerciais (CA) em vez disso.

### <a name="validation-details"></a>Detalhes da validação

- No momento da criação/atualização da subscrição do evento, a Event Grid publica um evento de validação de subscrição para o ponto final alvo.
- O evento contém um valor de cabeçalho "aeg-event-type: SubscriptionValidation".
- O corpo do evento tem o mesmo esquema que outros eventos da Grade de Eventos.
- A propriedade do eventType do evento é `Microsoft.EventGrid.SubscriptionValidationEvent` .
- A propriedade de dados do evento inclui uma `validationCode` propriedade com uma cadeia gerada aleatoriamente. Por exemplo, "validaçãoDisco: acb13...".
- Os dados do evento também incluem uma `validationUrl` propriedade com UM URL para validar manualmente a subscrição.
- A matriz contém apenas o evento de validação. Outros eventos são enviados num pedido separado depois de fazer eco do código de validação.
- Os EventGrid DataPlane SDKs têm classes correspondentes aos dados do evento de validação de subscrição e resposta de validação de subscrição.

Um exemplo SubscriptionValidationEvent é mostrado no seguinte exemplo:

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

Para provar a propriedade do ponto final, ressoe o código de validação na propriedade validaçãoResponse, como mostra o seguinte exemplo:

```json
{
  "validationResponse": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"
}
```

Tem de devolver um código de estado de resposta HTTP 200 OK. HTTP 202 Aceite não é reconhecido como uma resposta válida de validação de subscrição de Event Grid. O pedido http deve estar concluído dentro de 30 segundos. Se a operação não terminar dentro de 30 segundos, a operação será cancelada e poderá ser retribuída após 5 segundos. Se todas as tentativas falharem, será tratado como erro de aperto de mão de validação.

Ou, pode validar manualmente a subscrição enviando um pedido GET para o URL de validação. A subscrição do evento permanece em estado pendente até ser validada. O Url de validação utiliza a porta 553. Se as regras de firewall bloquearem a porta 553, as regras poderão ter de ser atualizadas para um aperto de mão manual bem sucedido.

Para um exemplo de manuseamento do aperto de mão de validação de assinatura, consulte uma [amostra C#](https://github.com/Azure-Samples/event-grid-dotnet-publish-consume-events/blob/master/EventGridConsumer/EventGridConsumer/Function1.cs).

## <a name="endpoint-validation-with-cloudevents-v10"></a>Validação de ponto final com CloudEvents v1.0
Se já está familiarizado com a Grade de Eventos, poderá estar ciente do aperto de mão de validação de ponto final da Event Grid para prevenir abusos. O CloudEvents v1.0 implementa a sua própria [semântica de proteção contra abusos](webhook-event-delivery.md) utilizando o método HTTP OPTIONS. Pode ler mais [aqui.](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#4-abuse-protection) Ao utilizar o esquema cloudEvents para saída, a Grade de Eventos utiliza com a proteção de abuso cloudEvents v1.0 em vez do mecanismo de validação da Grelha de Eventos.

## <a name="next-steps"></a>Passos seguintes
Consulte o seguinte artigo para saber como resolver as validações de subscrição de eventos: 

[Validações de subscrição de eventos de resolução de problemas](troubleshoot-subscription-validation.md)
