---
title: Entrega e retenção - Azure Event Grid IoT Edge | Microsoft Docs
description: Entrega e repetição em Grade de Eventos em IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: aa0b3a05fb26f6be951b697145d7b22e03b7792d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "86171623"
---
# <a name="delivery-and-retry"></a>Entrega e tentativa

A Grade de Eventos proporciona uma entrega duradoura. Tenta entregar cada mensagem pelo menos uma vez para cada subscrição correspondente imediatamente. Se o ponto final de um assinante não reconhecer a receção de um evento ou se houver uma falha, a entrega de retração da Event Grid com base numa agenda fixa de **retry** e **política de retenção**.  Por predefinição, o módulo 'Grade de Evento' entrega um evento de cada vez ao assinante. A carga útil é, no entanto, uma matriz com um único evento. Pode fazer com que o módulo entregue mais do que um evento de cada vez, ativando a função de loteamento de saída. Para obter mais informações sobre esta funcionalidade, consulte [o lote de saída](delivery-output-batching.md).  

> [!IMPORTANT]
>Não existe apoio à persistência para os dados do evento. Isto significa que a recolocação ou o reinício do módulo 'Grade de Eventos' fará com que perca quaisquer eventos que ainda não estejam entregues.

## <a name="retry-schedule"></a>Horário de recandidução

A Grade de Eventos aguarda até 60 segundos para uma resposta depois de entregar uma mensagem. Se o ponto final do assinante não conseguir a resposta, a mensagem será encosa numa das nossas filas de recuo para posteriores retréis.

Há duas filas pré-configuradas que determinam o horário em que uma nova tentativa será tentada. A saber:

| Agenda | Description |
| ---------| ------------ |
| 1 minuto | As mensagens que acabam aqui são tentadas a cada minuto.
| 10 minutos | As mensagens que acabam aqui são tentadas a cada 10 minutos.

### <a name="how-it-works"></a>Como funciona

1. A mensagem chega ao módulo 'Grade de Eventos'. Tenta-se entregá-lo imediatamente.
1. Se a entrega falhar, a mensagem é encosada em fila de 1 minuto e novamente julgada após um minuto.
1. Se a entrega continuar a falhar, a mensagem é encosada em fila de 10 minutos e novamente julgada a cada 10 minutos.
1. As entregas são tentadas até que os limites de política bem sucedidos ou redaçam sejam atingidos.

## <a name="retry-policy-limits"></a>Redaçar os limites da política

Existem duas configurações que determinam a política de retíria. A saber:

* Número máximo de tentativas
* Tempo de vida do evento (TTL)

Um evento será eliminado se um dos limites da política de recandidologia for atingido. O calendário de re-decisão em si foi descrito na secção Retry Schedule. A configuração destes limites pode ser feita para todos os subscritores ou por subscrição. A secção seguinte descreve cada um deles e é mais pormenorizado.

## <a name="configuring-defaults-for-all-subscribers"></a>Configurar incumprimentos para todos os subscritores

Existem duas propriedades: `brokers__defaultMaxDeliveryAttempts` e que podem ser `broker__defaultEventTimeToLiveInSeconds` configuradas como parte da implementação da Grade de Eventos, que controla os incumprimentos de políticas de retenção para todos os subscritores.

| Nome da Propriedade | Description |
| ---------------- | ------------ |
| `broker__defaultMaxDeliveryAttempts` | Número máximo de tentativas para entregar um evento. Valor predefinido: 30.
| `broker__defaultEventTimeToLiveInSeconds` | Evento TTL em segundos após o qual um evento será retirado se não for entregue. Valor predefinido: **7200** segundos

## <a name="configuring-defaults-per-subscriber"></a>Configurar incumprimentos por subscritor

Também pode especificar os limites de política de retíria por subscrição.
Consulte a nossa [documentação da API](api.md) para obter informações sobre como fazer configurar os predefinições por subscritor. As predefinições do nível de subscrição substituem as configurações do nível do módulo.

## <a name="examples"></a>Exemplos

O exemplo a seguir define a política de relembração no módulo de Grelha de Eventos com maxNumberOfAttempts = 3 e Event TTL de 30 minutos

```json
{
  "Env": [
    "broker__defaultMaxDeliveryAttempts=3",
    "broker__defaultEventTimeToLiveInSeconds=1800"
  ],
  "HostConfig": {
    "PortBindings": {
      "4438/tcp": [
        {
          "HostPort": "4438"
        }
      ]
    }
  }
}
```

O exemplo a seguir configura uma subscrição de gancho web com maxNumberOfAttempts = 3 e Event TTL de 30 minutos

```json
{
 "properties": {
  "destination": {
   "endpointType": "WebHook",
   "properties": {
    "endpointUrl": "<your_webhook_url>",
    "eventDeliverySchema": "eventgridschema"
   }
  },
  "retryPolicy": {
   "eventExpiryInMinutes": 30,
   "maxDeliveryAttempts": 3
  }
 }
}
```
