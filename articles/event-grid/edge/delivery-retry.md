---
title: Entrega e retry - Azure Event Grid IoT Edge [ Microsoft Docs
description: Entrega e retenção em Event Grid na IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/29/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 7df283b12a0d04d2b785c13a2f12b03115581e79
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76841717"
---
# <a name="delivery-and-retry"></a>Entrega e tentativa

A Rede de Eventos proporciona uma entrega duradoura. Tenta entregar cada mensagem pelo menos uma vez para cada subscrição correspondente imediatamente. Se o ponto final de um assinante não reconhecer a receção de um evento ou se houver uma falha, a Rede de Eventos retenta a entrega com base num calendário fixo de **retry** e uma política de **retry**.  Por padrão, o módulo DeRede de Eventos oferece um evento de cada vez ao assinante. A carga útil é, no entanto, uma matriz com um único evento. Pode fazer com que o módulo entregue mais de um evento de cada vez, permitindo a funcionalidade de loteamento de saída. Para mais detalhes sobre esta funcionalidade, consulte o [lote de saída](delivery-output-batching.md).  

> [!IMPORTANT]
>Não há suporte de persistência para os dados do evento. Isto significa que a recolocação ou o reinício do módulo Da Grelha de Eventos fará com que perca quaisquer eventos que ainda não estejam entregues.

## <a name="retry-schedule"></a>Horário de retry

A Grelha de Eventos aguarda até 60 segundos por uma resposta após a entrega de uma mensagem. Se o ponto final do assinante não responder, então a mensagem será envolta numa das nossas filas de back off para posteriores tentativas.

Há duas filas de back back back pré-configuradas que determinam o horário em que será tentado um novo tempo. São:

| Agenda | Descrição |
| ---------| ------------ |
| 1 minuto | As mensagens que acabam aqui são tentadas a cada minuto.
| 10 minutos | As mensagens que acabam aqui são tentadas a cada 10 minutos.

### <a name="how-it-works"></a>Como funciona

1. A mensagem chega ao módulo Da Grelha de Eventos. Tenta-se entregá-lo imediatamente.
1. Se a entrega falhar, a mensagem é gravada em fila de 1 minuto e novamente tentada após um minuto.
1. Se a entrega continuar a falhar, a mensagem é gravada em fila de 10 minutos e novamente tentada a cada 10 minutos.
1. As entregas são tentadas até que os limites de política de sucesso ou de novo sejam atingidos.

## <a name="retry-policy-limits"></a>Limites de política de retry

Há duas configurações que determinam a política de retry. São:

* Número máximo de tentativas
* Hora do evento ao vivo (TTL)

Um evento será abandonado se um dos limites da política de repescagem for atingido. O calendário de retry em si foi descrito na secção Retry Schedule. A configuração destes limites pode ser feita quer para todos os subscritores quer por base de subscrição. A secção seguinte descreve cada uma delas é mais detalhada.

## <a name="configuring-defaults-for-all-subscribers"></a>Configurar incumprimentos para todos os subscritores

Existem duas `brokers__defaultMaxDeliveryAttempts` propriedades: e `broker__defaultEventTimeToLiveInSeconds` que podem ser configuradas como parte da implementação da Rede de Eventos, que controla os incumprimentos da política de retry para todos os subscritores.

| Nome da Propriedade | Descrição |
| ---------------- | ------------ |
| `broker__defaultMaxDeliveryAttempts` | Número máximo de tentativas para entregar um evento. Valor predefinido: 30.
| `broker__defaultEventTimeToLiveInSeconds` | Evento TTL em segundos após o qual um evento será abandonado se não for entregue. Valor predefinido: **7200** segundos

## <a name="configuring-defaults-per-subscriber"></a>Configurar os incumprimentos por assinante

Também pode especificar limites de política de retry por subscrição.
Consulte a nossa [documentação da API](api.md) para obter informações sobre como configurar os incumprimentos por assinante. Os predefinições do nível de subscrição sobrepõem-se às configurações do nível do módulo.

## <a name="examples"></a>Exemplos

O exemplo seguinte estabelece a política de retry no módulo De Retry Grid com maxNumberOfAttempts = 3 e Evento TTL de 30 minutos

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

O exemplo seguinte configura uma subscrição de web hook com maxNumberOfAttempts = 3 e Event TTL de 30 minutos

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
