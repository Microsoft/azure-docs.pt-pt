---
title: Azure Event Grid - Validação de subscrição de resolução de problemas
description: Este artigo mostra-lhe como pode resolver validações de subscrição.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 48844859013507ab684ef8879b7b85dd6b6fe8cd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86118992"
---
# <a name="troubleshoot-azure-event-grid-subscription-validations"></a>Validações de subscrição de Azure Event Grid de resolução de problemas
Este artigo fornece-lhe informações sobre validações de subscrição de eventos de resolução de problemas. 

> [!IMPORTANT]
> Para obter informações detalhadas sobre a validação do ponto final para webhooks, consulte [a entrega do evento Webhook](webhook-event-delivery.md).

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

## <a name="error-code-403"></a>Código de erro: 403
Se o seu webhook estiver a devolver o 403 (Proibido) na resposta, verifique se o seu webhook está por detrás de um Gateway de Aplicações Azure ou firewall de aplicação web. Se for, tem de desativar as seguintes regras de firewall e fazer um HTTP POST novamente:

  - 920300 (Solicito falta de um cabeçalho de aceitação, podemos corrigir isto)
  - 942430 (Deteção restrita de anomalias de caracteres SQL (args): # de caracteres especiais excedidos (12))
  - 920230 (Encoding multiple URL Detetado)
  - 942130 (SqL Injection Attack: SQL Tautology Detected.)
  - 931130 (Possível Inclusão de Ficheiros Remotos (RFI) Ataque = Off-Domain Referência/Ligação)

## <a name="next-steps"></a>Passos seguintes
Se precisar de mais ajuda, publique o seu problema no [fórum Stack Overflow](https://stackoverflow.com/questions/tagged/azure-eventgrid) ou abra um bilhete de [apoio.](https://azure.microsoft.com/support/options/) 
