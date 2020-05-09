---
title: Azure Event Grid - Validação de subscrição de resolução de problemas
description: Este artigo mostra-lhe como pode resolver as validações de subscrição.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: spelluru
ms.openlocfilehash: a052d4c268fadc60f754630156fe0bc0d33acf3b
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/01/2020
ms.locfileid: "82630190"
---
# <a name="troubleshoot-azure-event-grid-errors"></a>Problemas de suturação de erros da Grelha de Eventos Azure
Este guia de resolução de problemas fornece-lhe informações sobre validações de subscrição de eventos de resolução de problemas. 


## <a name="troubleshoot-event-subscription-validation"></a>Validação de subscrição de eventos de resolução de problemas

Durante a criação de subscrição de eventos, se estiver a ver uma mensagem de erro como, `The attempt to validate the provided endpoint https://your-endpoint-here failed. For more details, visit https://aka.ms/esvalidation`indica que há uma falha no aperto de mão de validação. Para resolver este erro, verifique os seguintes aspetos:

- Faça uma PUBLICAÇÃO HTTP no seu url webhook com uma [amostra SubscriptionValidaçãoOrganismo](webhook-event-delivery.md#validation-details) solicitar corpo usando o Carteiro ou caracol ou ferramenta semelhante.
- Se o seu webhook estiver a implementar um mecanismo de aperto de mão de validação sincronizada, verifique se o Código de Validação é devolvido como parte da resposta.
- Se o seu webhook estiver a implementar um mecanismo de aperto de mão de validação assíncrono, verifique se é o HTTP POST que está a devolver 200 OK.
- Se o seu webhook estiver a devolver 403 (Proibido) na resposta, verifique se o seu webhook está por detrás de um Portal de Aplicações Azure ou firewall de aplicação web. Se for, então a sua necessidade de desativar estas regras de firewall e fazer um POST HTTP novamente:

  920300 (Pedido faltando um cabeçalho de aceitação, podemos corrigir isto)

  942430 (Deteção restrita de anomalias de caracteres SQL (args): # de caracteres especiais ultrapassados (12))

  920230 (Múltipla codificação de URL Detetada)

  942130 (Ataque de Injeção SQL: Tautology SQL Detetado.)

  931130 (Possível ataque de inclusão de ficheiros remotos (RFI) = Referência/Link off-domínio)

> [!IMPORTANT]
> Para obter informações detalhadas sobre a validação do ponto final para webhooks, consulte a entrega do [evento Webhook](webhook-event-delivery.md).

## <a name="validate-event-grid-event-subscription-using-postman"></a>Validar a subscrição de eventos de grelha de eventousando o Carteiro
Aqui está um exemplo de usar o Carteiro para validar uma subscrição webhook de um evento da Grelha de Eventos: 

![Validação de subscrição de eventos de eventos usando o Carteiro](./media/troubleshoot-subscription-validation/event-subscription-validation-postman.png)

Aqui está uma amostra De Validação De ValidaçãoEvento JSON:

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

Para saber mais sobre a validação de eventos de eventos para webhooks, consulte a [validação endpoint com eventos](webhook-event-delivery.md#endpoint-validation-with-event-grid-events)de grelha de eventos .

## <a name="validate-cloud-event-subscription-using-postman"></a>Validar a subscrição de eventos em nuvem usando o Carteiro
Aqui está um exemplo de usar o Carteiro para validar uma subscrição webhook de um evento em nuvem: 

![Validação de subscrição de evento sinuoso usando o Carteiro](./media/troubleshoot-subscription-validation/cloud-event-subscription-validation-postman.png)

Utilize o método **HTTP OPTIONS** para validação com eventos na nuvem. Para saber mais sobre a validação de eventos em nuvem para webhooks, consulte a [validação endpoint com eventos na nuvem](webhook-event-delivery.md#endpoint-validation-with-event-grid-events).

## <a name="event-grid-event-subscription-validation-using-curl"></a>Validação de subscrição de eventos de eventos usando Curl 
Aqui está o comando curl da amostra para validar uma subscrição webhook de um evento da Grelha de Eventos: 

```bash
curl -X POST -d '[{"id": "2d1781af-3a4c-4d7c-bd0c-e34b19da4e66","topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx","subject": "","data": {"validationCode": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"},"eventType": "Microsoft.EventGrid.SubscriptionValidationEvent","eventTime": "2018-01-25T22:12:19.4556811Z", "metadataVersion": "1","dataVersion": "1"}]' -H 'Content-Type: application/json' https://{your-webhook-url.com}
```

## <a name="next-steps"></a>Passos seguintes
Se precisar de mais ajuda, publique o seu problema no [fórum Stack Overflow](https://stackoverflow.com/questions/tagged/azure-eventgrid) ou abra um bilhete de [apoio](https://azure.microsoft.com/support/options/). 
