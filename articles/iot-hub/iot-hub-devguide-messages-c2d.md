---
title: Compreender a mensagens de cloud para dispositivo IoT Hub do Azure | Documentos da Microsoft
description: Guia para programadores - como utilizar o cloud-para-dispositivo de mensagens com o IoT Hub. Inclui informações sobre o ciclo de vida de mensagem e opções de configuração.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/15/2018
ms.openlocfilehash: c8424743f30ec1bbf8d8096f6630c7451bc910c8
ms.sourcegitcommit: 15e9613e9e32288e174241efdb365fa0b12ec2ac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2019
ms.locfileid: "57010247"
---
# <a name="send-cloud-to-device-messages-from-iot-hub"></a>Enviar mensagens de cloud para dispositivo a partir do Hub IoT

Para enviar notificações unidirecionais para a aplicação de dispositivo da sua solução de back-end, envie mensagens de cloud para dispositivos do seu hub IoT para o seu dispositivo. Para uma discussão sobre outras opções de cloud para dispositivos suportados pelo IoT Hub, veja [orientações de comunicações da Cloud-para-dispositivo](iot-hub-devguide-c2d-guidance.md).

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Enviar mensagens da cloud para o dispositivo através de um ponto de extremidade de serviço com acesso à (**/mensagens/devicebound**). Um dispositivo, em seguida, recebe as mensagens por meio de um ponto de extremidade específicos do dispositivo (**/devices/ {deviceId} / mensagens/devicebound**).

Define o IoT Hub para cada mensagem de cloud para o dispositivo num único dispositivo de destino, o **para** propriedade **/devices/ {deviceId} / mensagens/devicebound**.

Cada fila de dispositivo contém mensagens de cloud para o dispositivo no máximo 50. A tentar enviar mais mensagens para os mesmos resultados de dispositivo num erro.

## <a name="the-cloud-to-device-message-lifecycle"></a>O ciclo de vida de mensagem da cloud para dispositivo

Para garantir a entrega de mensagens pelo-menos-uma vez, o IoT Hub mantém as mensagens da cloud para o dispositivo em filas de por dispositivo. Dispositivos tem de confirmar explicitamente *conclusão* para o IoT Hub para removê-los a partir da fila. Essa abordagem garante resiliência contra falhas de dispositivo e de conectividade.

O diagrama seguinte mostra o gráfico de estado do ciclo de vida de uma mensagem de cloud para o dispositivo no IoT Hub.

![Ciclo de vida de mensagem da cloud para dispositivo](./media/iot-hub-devguide-messages-c2d/lifecycle.png)

Quando o serviço IoT Hub envia uma mensagem a um dispositivo, o serviço define o estado de mensagem como **colocados em fila**. Quando um dispositivo deseja *receber* uma mensagem, o IoT Hub *bloqueios* a mensagem (definindo o estado como **invisível**), que permite que outros threads no dispositivo para começar a receber outras mensagens. Quando um thread de dispositivo concluir o processamento de uma mensagem, notifica o IoT Hub por *concluir* a mensagem. IoT Hub, em seguida, define o estado como **concluído**.

Um dispositivo também pode optar por:

* *Rejeitar* a mensagem, que faz com que o IoT Hub para configurá-lo para o **inutilizado lettered** estado. Dispositivos que ligam através do protocolo MQTT não podem rejeitar mensagens cloud para o dispositivo.

* *Abandonar* a mensagem, que faz com que o IoT Hub colocar a mensagem de volta na fila, com o estado definido como **colocados em fila**. Dispositivos que ligam através do protocolo MQTT não é possível abandonar mensagens cloud para o dispositivo.

Um thread pode não conseguir processar uma mensagem sem notificar o IoT Hub. Neste caso, as mensagens são automaticamente transição da **invisível** estado para o **colocados em fila** Estado após um *tempo limite de visibilidade (ou bloqueio)*. O valor predefinido de tempo limite é de um minuto.

O **máximo de contagem de entrega** propriedade no IoT Hub determina o número máximo de vezes que uma mensagem pode fazer a transição entre os **colocados em fila** e **invisível** Estados. Após esse número de transições, o IoT Hub define o estado da mensagem a **inutilizado lettered**. Da mesma forma, o IoT Hub define o estado de uma mensagem **inutilizado lettered** após a hora de expiração (consulte [TTL](#message-expiration-time-to-live)).

O [como enviar mensagens da cloud para o dispositivo com o IoT Hub](iot-hub-csharp-csharp-c2d.md) mostra-lhe como enviar mensagens da cloud para dispositivo a partir da nuvem e recebê-las num dispositivo.

Normalmente, um dispositivo conclui uma mensagem de cloud-para-dispositivo quando a perda da mensagem não afeta a lógica do aplicativo. Por exemplo, quando o dispositivo se manteve a mensagem conteúda localmente ou com êxito tem executada uma operação. A mensagem também poderia levar a informações temporárias, cuja perda não afete a funcionalidade do aplicativo. Às vezes, para tarefas de longa execução, pode:

* Conclua a mensagem de cloud-para-dispositivo depois de persistir a descrição da tarefa no armazenamento local.

* Notificar a solução de back-end com uma ou mais mensagens do dispositivo para a cloud em vários estágios de progresso da tarefa.

## <a name="message-expiration-time-to-live"></a>Expiração da mensagem (tempo até ao live)

Todas as mensagens da cloud para o dispositivo tem um prazo de expiração. Este tempo é definido por um dos:

* O **ExpiryTimeUtc** propriedade no serviço.
* IoT Hub com a predefinição *TTL* especificada como uma propriedade do IoT Hub.

Ver [opções de configuração de Cloud-para-dispositivo](#cloud-to-device-configuration-options).

Uma maneira comum de tirar partido da expiração da mensagem e evitar o envio de mensagens para dispositivos desligados, é definir o curto período de tempo TTL valores. Essa abordagem alcança o mesmo resultado que manter o estado de ligação do dispositivo, ao mesmo tempo a ser mais eficiente. Quando solicitar as confirmações de mensagem, o IoT Hub notifica-o que os dispositivos são:

* É possível receber mensagens.
* Não estão online ou ter falhado.

## <a name="message-feedback"></a>Comentários de mensagem

Quando envia uma mensagem de cloud para o dispositivo, o serviço pode solicitar o fornecimento de comentários por mensagem sobre o estado final dessa mensagem.

| Propriedade de ACK | Comportamento |
| ------------ | -------- |
| **positive** | Se a mensagem de cloud-para-dispositivo atingir a **concluído** de estado, o IoT Hub gera uma mensagem de comentários. |
| **negative** | Se a mensagem de cloud-para-dispositivo atingir a **inutilizado lettered** de estado, o IoT Hub gera uma mensagem de comentários. |
| **full**     | IoT Hub gera uma mensagem de comentários em ambos os casos. |

Se **Ack** é **completo**e não receberá uma mensagem de comentários, significa que a mensagem de comentários expirou. O serviço não é possível saber o que aconteceu com a mensagem original. Na prática, um serviço deve certificar-se de que consegue processar os comentários antes de expirar. O tempo de expiração máximo é dois dias, o que deixa o tempo para obter o serviço de executar novamente se ocorrer uma falha.

Conforme explicado [pontos de extremidade](iot-hub-devguide-endpoints.md), o IoT Hub fornece comentários por meio de um ponto de extremidade de serviço com acesso à (**/messages/servicebound/feedback**) como mensagens. A semântica para o recebimento de comentários é iguais aos mensagens cloud para o dispositivo. Sempre que possível, os comentários da mensagem é em batch numa única mensagem, com o seguinte formato:

| Propriedade     | Descrição |
| ------------ | ----------- |
| EnqueuedTime | Timestamp indicando quando a mensagem de comentários foi recebida pelo hub. |
| UserId       | `{iot hub name}` |
| ContentType  | `application/vnd.microsoft.iothub.feedback.json` |

O corpo é uma JSON serializado matriz de registos, cada um com as seguintes propriedades:

| Propriedade           | Descrição |
| ------------------ | ----------- |
| EnqueuedTimeUtc    | Timestamp que indica quando ocorreu o resultado da mensagem. Por exemplo, o hub recebeu a mensagem de comentários ou a mensagem original expirou. |
| OriginalMessageId  | **MessageId** da mensagem de cloud para o dispositivo ao qual está associado estas informações de comentários. |
| StatusCode         | Cadeia de caracteres necessária. Utilizado em mensagens de comentários geradas pelo IoT Hub. <br/> 'Êxito' <br/> 'Expired' <br/> 'DeliveryCountExceeded' <br/> 'Rejected' <br/> 'Purged' |
| Descrição        | Valores de cadeias de caracteres **StatusCode**. |
| DeviceId           | **DeviceId** do dispositivo de destino da mensagem de cloud para o dispositivo ao qual está associado esta parte de comentários. |
| DeviceGenerationId | **DeviceGenerationId** do dispositivo de destino da mensagem de cloud para o dispositivo ao qual está associado esta parte de comentários. |

O serviço tem de especificar um **MessageId** para a mensagem de cloud-para-dispositivo poder correlacionar os seus comentários com a mensagem original.

O exemplo seguinte mostra o corpo de uma mensagem de comentários.

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
| defaultTtlAsIso8601       | TTL predefinido para mensagens na cloud para o dispositivo. | Intervalo de ISO_8601 até 2D (no mínimo 1 minuto). Predefinição: 1 hora. |
| maxDeliveryCount          | Contagem máxima de entrega para filas de cloud-para-dispositivo por dispositivo. | 1 a 100. Predefinição: 10. |
| feedback.ttlAsIso8601     | Período de retenção de mensagens de comentários do serviço ligado. | Intervalo de ISO_8601 até 2D (no mínimo 1 minuto). Predefinição: 1 hora. |
| feedback.maxDeliveryCount |Contagem máxima de entrega de fila de comentários. | 1 a 100. Predefinição: 100. |

Para obter mais informações sobre como definir estas opções de configuração, consulte [os hubs IoT criar](iot-hub-create-through-portal.md).

## <a name="next-steps"></a>Passos Seguintes

Para informações sobre os SDKs que pode utilizar para receber mensagens da cloud para o dispositivo, consulte [do Azure IoT SDKs](iot-hub-devguide-sdks.md).

Para experimentar o recebimento de mensagens da cloud para o dispositivo, consulte a [enviar cloud-para-dispositivo](iot-hub-csharp-csharp-c2d.md) tutorial.