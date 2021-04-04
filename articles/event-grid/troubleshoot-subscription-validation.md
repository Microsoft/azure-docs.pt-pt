---
title: Azure Event Grid - Validação de subscrição de resolução de problemas
description: Este artigo mostra-lhe como pode resolver validações de subscrição.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 857760182675d5673a3b09495c2faaf7372a4164
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "94592945"
---
# <a name="troubleshoot-azure-event-grid-subscription-validations"></a>Validações de subscrição de Azure Event Grid de resolução de problemas
Durante a criação de subscrição de eventos, se estiver a ver uma mensagem de erro `The attempt to validate the provided endpoint https://your-endpoint-here failed. For more details, visit https://aka.ms/esvalidation` como, indica que há uma falha no aperto de mão de validação. Para resolver este erro, verifique os seguintes aspetos:

- Faça um HTTP POST no seu url webhook com uma [amostra SubscriçãoSevent](webhook-event-delivery.md#validation-details) request body usando Carteiro ou curl ou ferramenta semelhante.
- Se o seu webhook estiver a implementar um mecanismo de aperto de mão de validação sincronizado, verifique se o Código de Validação é devolvido como parte da resposta.
- Se o seu webhook estiver a implementar um mecanismo de aperto de mão de validação assíncrona, verifique se é o HTTP POST que está a devolver 200 OK.
- Se o seu webhook estiver a voltar `403 (Forbidden)` na resposta, verifique se o seu webhook está por detrás de um Gateway de Aplicação Azure ou firewall de aplicação web. Se for, então precisa de desativar estas regras de firewall e fazer um HTTP POST novamente:
    - 920300 (Pedido de falta de um cabeçalho de aceitação)
    - 942430 (Deteção restrita de anomalias de caracteres SQL (args): # de caracteres especiais excedidos (12))
    - 920230 (Codificação de URL múltipla detetada)
    - 942130 (ataque de injeção SQL: tautologia SQL detetada.)
    - 931130 (Possível inclusão de ficheiros remotos (RFI) ataque = referência/ligação off-domain)

> [!IMPORTANT]
> Para obter informações detalhadas sobre a validação do ponto final para webhooks, consulte [a entrega do evento Webhook](webhook-event-delivery.md).

As secções seguintes mostram-lhe como validar uma subscrição de eventos usando o Carteiro e o Curl.  

## <a name="validate-event-grid-event-subscription-using-postman"></a>Validar a subscrição do evento da Grade de Eventos usando o Carteiro
Aqui está um exemplo de usar o Carteiro para validar uma subscrição webhook de um evento de Grade de Eventos: 

![Validação de subscrição de eventos de grelha de eventos usando Carteiro](./media/troubleshoot-subscription-validation/event-subscription-validation-postman.png)

Aqui está uma amostra **SubscriçãoValidationEvent** JSON:

```json
[
  {
    "id": "2d1781af-3a4c-4d7c-bd0c-e34b19da4e66",
    "topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "subject": "",
    "data": {
      "validationCode": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6",
    },
    "eventType": "Microsoft.EventGrid.SubscriptionValidationEvent",
    "eventTime": "2018-01-25T22:12:19.4556811Z",
    "metadataVersion": "1",
    "dataVersion": "1"
  }
]
```

Aqui está a resposta bem sucedida da amostra:

```json
{
  "validationResponse": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"
}
```

Para saber mais sobre a validação do evento Event Grid para webhooks, consulte [a validação de Endpoint com eventos de grelha de eventos.](webhook-event-delivery.md#endpoint-validation-with-event-grid-events)


## <a name="validate-event-grid-event-subscription-using-curl"></a>Validar a subscrição do evento da Grelha de Eventos usando o Curl 
Aqui está a amostra do comando Curl para validar uma subscrição webhook de um evento de Grade de Eventos: 

```bash
curl -X POST -d '[{"id": "2d1781af-3a4c-4d7c-bd0c-e34b19da4e66","topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx","subject": "","data": {"validationCode": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"},"eventType": "Microsoft.EventGrid.SubscriptionValidationEvent","eventTime": "2018-01-25T22:12:19.4556811Z", "metadataVersion": "1","dataVersion": "1"}]' -H 'Content-Type: application/json' https://{your-webhook-url.com}
```

## <a name="validate-cloud-event-subscription-using-postman"></a>Validar a subscrição de eventos em nuvem usando o Carteiro
Aqui está um exemplo de usar o Carteiro para validar uma subscrição webhook de um evento em nuvem: 

![Validação de subscrição de evento em nuvem usando Carteiro](./media/troubleshoot-subscription-validation/cloud-event-subscription-validation-postman.png)

Utilize o método **HTTP OPTIONS** para validação com eventos na nuvem. Para saber mais sobre a validação de eventos em nuvem para webhooks, consulte [a validação de Endpoint com eventos na nuvem.](webhook-event-delivery.md#endpoint-validation-with-event-grid-events)

## <a name="troubleshoot-event-subscription-validation"></a>Validação de subscrição de eventos de resolução de problemas

## <a name="next-steps"></a>Passos seguintes
Se precisar de mais ajuda, publique o seu problema no [fórum Stack Overflow](https://stackoverflow.com/questions/tagged/azure-eventgrid) ou abra um bilhete de [apoio.](https://azure.microsoft.com/support/options/) 
