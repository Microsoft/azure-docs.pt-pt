---
title: Compreender a mensagens de cloud para dispositivo IoT Hub do Azure | Documentos da Microsoft
description: Este guia do programador descreve como utilizar o cloud-para-dispositivo mensagens com o seu hub IoT. Ele inclui informações sobre o ciclo de vida de mensagem e opções de configuração.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/15/2018
ms.openlocfilehash: 0fc1b65a4ba1c8a3d76b48206d6a4703035e05bc
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67055310"
---
# <a name="send-cloud-to-device-messages-from-an-iot-hub"></a>Enviar mensagens da cloud para dispositivo a partir de um hub IoT

Para enviar notificações unidirecionais para uma aplicação de dispositivo da sua solução de back-end, envie mensagens da cloud para dispositivo do seu hub IoT para o seu dispositivo. Para uma discussão sobre outras opções de cloud-para-dispositivo suportado pelo IoT Hub do Azure, consulte [orientações de comunicações da Cloud-para-dispositivo](iot-hub-devguide-c2d-guidance.md).

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Enviar mensagens da cloud para o dispositivo através de um ponto de extremidade de serviço com acesso à */mensagens/devicebound*. Um dispositivo, em seguida, recebe as mensagens por meio de um ponto de extremidade específicos do dispositivo, */devices/ {deviceId} / mensagens/devicebound*.

Define o seu hub IoT para cada mensagem de cloud para o dispositivo num único dispositivo de destino, o **para** propriedade */devices/ {deviceId} / mensagens/devicebound*.

Cada fila de dispositivo contém, no máximo, 50 mensagens da cloud para o dispositivo. Para tentar enviar mais mensagens para os mesmos resultados de dispositivo num erro.

## <a name="the-cloud-to-device-message-life-cycle"></a>O ciclo de vida de mensagem da cloud para dispositivo

Para garantir a entrega de mensagens pelo-menos-uma vez, o hub IoT mantém as mensagens da cloud para o dispositivo em filas de por dispositivo. Para o hub IoT remover as mensagens da fila, os dispositivos explicitamente devem reconhecer *conclusão*. Essa abordagem garante resiliência contra falhas de dispositivo e de conectividade.

O gráfico de estado do ciclo de vida é apresentado no diagrama seguinte:

![Ciclo de vida de mensagem da cloud para dispositivo](./media/iot-hub-devguide-messages-c2d/lifecycle.png)

Quando o serviço hub IoT envia uma mensagem a um dispositivo, o serviço define o estado de mensagem como *colocados em fila*. Quando um dispositivo deseja *receber* uma mensagem, o hub IoT *bloqueios* a mensagem ao definir o estado como *invisível*. Este estado permite que outros threads no dispositivo para iniciar outras mensagens a receber. Quando um thread de dispositivo concluir o processamento de uma mensagem, notifica o hub IoT por *concluir* a mensagem. O hub IoT, em seguida, define o estado como *concluído*.

Um dispositivo também pode:

* *Rejeitar* a mensagem, que faz com que o hub IoT para configurá-lo para o *inutilizado lettered* estado. Dispositivos que ligam através do protocolo de mensagem colocação Telemetry Transport (MQTT) não podem rejeitar mensagens cloud para o dispositivo.

* *Abandonar* a mensagem, que faz com que o hub IoT colocar a mensagem de volta na fila, com o estado definido como *colocados em fila*. Dispositivos que ligam através do protocolo MQTT não é possível abandonar mensagens cloud para o dispositivo.

Um thread pode não conseguir processar uma mensagem sem notificar o hub IoT. Neste caso, as mensagens são automaticamente transição da *invisível* estado para o *colocados em fila* Estado após um *visibilidade* tempo limite (ou *bloqueio* limite de tempo). O valor predefinido desse intervalo é de um minuto.

O **máximo de contagem de entrega** propriedade no IoT hub determina o número máximo de vezes que uma mensagem pode fazer a transição entre os *colocados em fila* e *invisível* Estados. Após esse número de transições, o hub IoT define o estado da mensagem a *inutilizado lettered*. Da mesma forma, o hub IoT define o estado de uma mensagem *inutilizado lettered* após a hora de expiração. Para obter mais informações, consulte [TTL](#message-expiration-time-to-live).

O [como enviar mensagens da cloud para o dispositivo com o IoT Hub](iot-hub-csharp-csharp-c2d.md) artigo mostra-lhe como enviar mensagens da cloud para dispositivo a partir da cloud e recebê-las num dispositivo.

Um dispositivo normalmente conclui uma mensagem de cloud-para-dispositivo quando a perda da mensagem não afeta a lógica do aplicativo. Um exemplo disso pode ser quando o dispositivo se manteve o conteúdo da mensagem localmente ou executou com êxito uma operação. A mensagem também poderia levar a informações temporárias, cuja perda não afeta a funcionalidade do aplicativo. Às vezes, para tarefas de longa execução, pode:

* Conclua a mensagem de cloud-para-dispositivo depois do dispositivo se manteve a descrição da tarefa no armazenamento local.

* Notificar a solução de back-end com uma ou mais mensagens do dispositivo para a cloud em vários estágios de progresso da tarefa.

## <a name="message-expiration-time-to-live"></a>Expiração da mensagem (tempo até ao live)

Todas as mensagens da cloud para o dispositivo tem um prazo de expiração. Este tempo é definido por um dos seguintes:

* O **ExpiryTimeUtc** propriedade no serviço
* O hub IoT, ao utilizar a predefinição *TTL* que é especificado como uma propriedade do hub IoT

Ver [opções de configuração de Cloud-para-dispositivo](#cloud-to-device-configuration-options).

Uma maneira comum para tirar partido de expiração de mensagens e para evitar o envio de mensagens para dispositivos desligados consiste em definir curto *TTL* valores. Essa abordagem alcança o mesmo resultado que manter o estado de ligação do dispositivo, mas é mais eficiente. Quando solicitar confirmações de mensagem, o hub IoT notifica-o de que os dispositivos são:

* É possível receber mensagens.
* Não estão online ou ter falhado.

## <a name="message-feedback"></a>Comentários de mensagem

Quando envia uma mensagem de cloud para o dispositivo, o serviço pode solicitar o fornecimento de comentários por mensagem sobre o estado final dessa mensagem. Fazer isso definindo a **iothub-ack** propriedade da aplicação na mensagem de cloud para o dispositivo que está sendo enviada para um dos quatro valores seguintes:

| Valor da propriedade ACK | Comportamento |
| ------------ | -------- |
| Nenhum     | O hub IoT não gera uma mensagem de comentários (comportamento predefinido). |
| positiva | Se a mensagem de cloud-para-dispositivo atingir a *concluído* de estado, o hub IoT gera uma mensagem de comentários. |
| negativo | Se a mensagem de cloud-para-dispositivo atingir a *inutilizado lettered* de estado, o hub IoT gera uma mensagem de comentários. |
| completo     | O hub IoT gera uma mensagem de comentários em ambos os casos. |

Se o **Ack** valor é *completo*e não receberá uma mensagem de comentários, significa que a mensagem de comentários a expirou. O serviço não é possível saber o que aconteceu com a mensagem original. Na prática, um serviço deve certificar-se de que consegue processar os comentários antes de expirar. A hora de expiração máximo é dois dias, o que deixa o tempo para obter o serviço de executar novamente se ocorrer uma falha.

Conforme explicado [pontos de extremidade](iot-hub-devguide-endpoints.md), o hub IoT fornece comentários por meio de um ponto de extremidade de serviço com acesso à */messages/servicebound/feedback*, como mensagens. A semântica para o recebimento de comentários é iguais aos mensagens cloud para o dispositivo. Sempre que possível, os comentários da mensagem é em batch numa única mensagem, com o seguinte formato:

| Propriedade     | Descrição |
| ------------ | ----------- |
| EnqueuedTime | Um carimbo que indica quando a mensagem de comentários foi recebida pelo hub |
| UserId       | `{iot hub name}` |
| ContentType  | `application/vnd.microsoft.iothub.feedback.json` |

O corpo é uma JSON serializado matriz de registos, cada um com as seguintes propriedades:

| Propriedade           | Descrição |
| ------------------ | ----------- |
| EnqueuedTimeUtc    | Um carimbo que indica quando ocorreu o resultado da mensagem (por exemplo, o hub recebeu a mensagem de comentários ou a mensagem original expirou) |
| OriginalMessageId  | O *MessageId* da mensagem de cloud para o dispositivo ao qual está associado estas informações de comentários |
| StatusCode         | Uma cadeia necessária, utilizada nas mensagens de comentários que são geradas pelo IoT hub: <br/> *Êxito* <br/> *Expirou* <br/> *DeliveryCountExceeded* <br/> *Rejected* <br/> *Removidos* |
| Descrição        | Valores de cadeias de caracteres *StatusCode* |
| DeviceId           | O *DeviceId* do dispositivo de destino da mensagem de cloud para o dispositivo ao qual está associado esta parte de comentários |
| DeviceGenerationId | O *DeviceGenerationId* do dispositivo de destino da mensagem de cloud para o dispositivo ao qual está associado esta parte de comentários |

Para a mensagem de cloud-para-dispositivo correlacionar os seus comentários com a mensagem original, o serviço tem de especificar um *MessageId*.

O corpo de uma mensagem de comentários é mostrado no seguinte código:

```json
[
  {
    "OriginalMessageId": "0987654321",
    "EnqueuedTimeUtc": "2015-07-28T16:24:48.789Z",
    "StatusCode": 0,
    "Description": "Success",
    "DeviceId": "123",
    "DeviceGenerationId": "abcdefghijklmnopqrstuvwxyz"
  },
  {
    ...
  },
  ...
]
```

## <a name="cloud-to-device-configuration-options"></a>Opções de configuração de cloud-para-dispositivo

Cada hub IoT expõe as seguintes opções de configuração para a cloud para o dispositivo de mensagens:

| Propriedade                  | Descrição | Intervalo e predefinido |
| ------------------------- | ----------- | ----------------- |
| defaultTtlAsIso8601       | TTL predefinido para mensagens na cloud para o dispositivo | Intervalo de ISO_8601 até 2 dias (mínimo 1 minuto); predefinição: 1 hora |
| maxDeliveryCount          | Contagem máxima de entrega para filas de cloud-para-dispositivo por dispositivo | 1 a 100; predefinição: 10 |
| feedback.ttlAsIso8601     | Período de retenção de mensagens de comentários do serviço ligado | Intervalo de ISO_8601 até 2 dias (mínimo 1 minuto); predefinição: 1 hora |
| feedback.maxDeliveryCount | Contagem máxima de entrega para a fila de comentários | 1 a 100; predefinição: 100 |

Para obter mais informações sobre como definir estas opções de configuração, consulte [os hubs IoT criar](iot-hub-create-through-portal.md).

## <a name="next-steps"></a>Passos Seguintes

Para obter informações sobre os SDKs que pode utilizar para receber mensagens da cloud para o dispositivo, consulte [do Azure IoT SDKs](iot-hub-devguide-sdks.md).

Para experimentar o recebimento de mensagens da cloud para o dispositivo, consulte a [enviar cloud-para-dispositivo](iot-hub-csharp-csharp-c2d.md) tutorial.
