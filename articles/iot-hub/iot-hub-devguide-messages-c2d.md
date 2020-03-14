---
title: Compreenda as mensagens cloud-to-device do Azure IoT Hub Microsoft Docs
description: Este guia de desenvolvedores discute como usar mensagens cloud-to-device com o seu hub IoT. Inclui informações sobre o ciclo de vida da mensagem e opções de configuração.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/15/2018
ms.openlocfilehash: 3a7254cc9de89a297811792b4dd64b4b669ba8e4
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79271242"
---
# <a name="send-cloud-to-device-messages-from-an-iot-hub"></a>Envie mensagens cloud-to-device a partir de um hub IoT

Para enviar notificações de sentido único para uma aplicação de dispositivo a partir da sua solução na parte de trás, envie mensagens cloud-to-device do seu hub IoT para o seu dispositivo. Para uma discussão sobre outras opções cloud-to-device suportadas pelo Azure IoT Hub, consulte a orientação de [comunicações Cloud-to-device](iot-hub-devguide-c2d-guidance.md).

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Envia mensagens cloud-to-device através de um ponto final virado para o serviço, */mensagens/dispositivos ligados*. Um dispositivo recebe então as mensagens através de um ponto final específico do dispositivo, */dispositivos/{deviceId}/messages/devicebound*.

Para direcionar cada mensagem cloud-to-device num único dispositivo, o seu hub IoT define a propriedade **para** */dispositivos/{deviceId}/messages/devicebound*.

Cada fila de dispositivos contém, no máximo, 50 mensagens cloud-to-device. Tentar enviar mais mensagens para o mesmo dispositivo resulta num erro.

## <a name="the-cloud-to-device-message-life-cycle"></a>O ciclo de vida da mensagem nuvem-a-dispositivo

Para garantir a entrega de mensagens pelo menos uma vez, o seu hub IoT persiste mensagens cloud-to-device em filas por dispositivo. Para que o hub IoT retire as mensagens da fila, os dispositivos devem reconhecer explicitamente a *conclusão*. Esta abordagem garante resiliência contra a conectividade e falhas do dispositivo.

O gráfico do estado do ciclo de vida é apresentado no seguinte diagrama:

![Ciclo de vida da mensagem nuvem-a-dispositivo](./media/iot-hub-devguide-messages-c2d/lifecycle.png)

Quando o serviço de hub IoT envia uma mensagem para um dispositivo, o serviço define o estado de mensagem para *Enqueued*. Quando um dispositivo quer *receber* uma mensagem, o hub IoT *bloqueia* a mensagem definindo o estado como *Invisível*. Este estado permite que outros fios no dispositivo comecem a receber outras mensagens. Quando um fio de dispositivo completa o processamento de uma mensagem, ele notifica o hub IoT *completando* a mensagem. O centro ioT, em seguida, define o estado para *Concluído*.

Um dispositivo também pode:

* *Rejeite* a mensagem, que faz com que o centro de IoT a coloque no estado *letrado dos Mortos.* Os dispositivos que se ligam ao protocolo de transporte de telemetria de telemetria (MQTT) não podem rejeitar mensagens cloud-to-device.

* *Abandone* a mensagem, que faz com que o centro de IoT volte a colocar a mensagem na fila, com o estado definido para *Enqueued*. Os dispositivos que se ligam ao protocolo MQTT não podem abandonar as mensagens cloud-to-device.

Um fio pode não processar uma mensagem sem notificar o centro ioT. Neste caso, as mensagens transitam automaticamente do estado *invisível* de volta para o estado *enqueued* após um intervalo de *visibilidade* (ou tempo de *bloqueio).* O valor deste intervalo é de um minuto e não pode ser alterado.

A propriedade de **contagem de entregas máximas** no hub IoT determina o número máximo de vezes que uma mensagem pode transitar entre os estados *Enqueued* e *Invisible.* Após esse número de transições, o centro ioT define o estado da mensagem para *Dead lettered*. Da mesma forma, o centro ioT define o estado de uma mensagem para *Dead lettered* após o seu tempo de validade. Para mais informações, consulte [o Tempo para viver.](#message-expiration-time-to-live)

O [Como enviar mensagens cloud-to-device com](iot-hub-csharp-csharp-c2d.md) o artigo do IoT Hub mostra-lhe como enviar mensagens cloud-to-device a partir da nuvem e recebê-las num dispositivo.

Um dispositivo normalmente completa uma mensagem cloud-to-device quando a perda da mensagem não afeta a lógica da aplicação. Um exemplo disso pode ser quando o dispositivo tiver persistido o conteúdo da mensagem localmente ou tiver executado com sucesso uma operação. A mensagem também poderia transportar informações transitórias, cuja perda não afetaria a funcionalidade da aplicação. Às vezes, para tarefas de longa duração, pode::

* Complete a mensagem cloud-to-device depois de o dispositivo ter persistido a descrição da tarefa no armazenamento local.

* Notifique a solução na extremidade traseira com uma ou mais mensagens de dispositivo-para-nuvem em várias fases do progresso da tarefa.

## <a name="message-expiration-time-to-live"></a>Expiração da mensagem (hora de viver)

Cada mensagem nuvem-a-dispositivo tem um tempo de validade. Este tempo é definido por qualquer um dos seguintes:

* A propriedade **ExpiraTimeUtc** no serviço
* O hub IoT, usando o tempo padrão *para viver* que é especificado como uma propriedade ioT hub

Consulte [as opções de configuração Cloud-to-device](#cloud-to-device-configuration-options).

Uma forma comum de aproveitar a expiração de uma mensagem e evitar o envio de mensagens para dispositivos desligados é definir pouco tempo para valores *de vida.* Esta abordagem obtém o mesmo resultado que manter o estado de ligação do dispositivo, mas é mais eficiente. Quando solicita reconhecimentos de mensagens, o hub IoT notifica quais os dispositivos:

* Capaz de receber mensagens.
* Não estão online ou falharam.

## <a name="message-feedback"></a>Feedback da mensagem

Ao enviar uma mensagem cloud-to-device, o serviço pode solicitar a entrega de feedback por mensagem sobre o estado final dessa mensagem. Faça-o definindo a propriedade de aplicação **iothub-ack** na mensagem cloud-to-device que está a ser enviada para um dos quatro valores seguintes:

| Valor da propriedade ack | Comportamento |
| ------------ | -------- |
| nenhuma     | O hub IoT não gera uma mensagem de feedback (comportamento predefinido). |
| positiva | Se a mensagem cloud-to-device atingir o estado *concluído,* o hub IoT gera uma mensagem de feedback. |
| negativo | Se a mensagem cloud-to-device chegar ao estado *letrado Dead,* o centro IoT gera uma mensagem de feedback. |
| cheio     | O centro ioT gera uma mensagem de feedback em qualquer dos casos. |

Se o valor **Ack** estiver *cheio*, e não receber uma mensagem de feedback, significa que a mensagem de feedback expirou. O serviço não pode saber o que aconteceu com a mensagem original. Na prática, um serviço deve garantir que pode processar o feedback antes de expirar. O prazo máximo de validade é de dois dias, o que deixa tempo para que o serviço volte a funcionar se ocorrer uma falha.

Como explicado em [Endpoints,](iot-hub-devguide-endpoints.md)o hub IoT fornece feedback através de um ponto final virado para o serviço, */mensagens/servicebound/feedback*, como mensagens. A semântica para receber feedback é a mesma que para mensagens cloud-to-device. Sempre que possível, o feedback da mensagem é loteado numa única mensagem, com o seguinte formato:

| Propriedade     | Descrição |
| ------------ | ----------- |
| EnqueuedTime | Uma marca de tempo que indica quando a mensagem de feedback foi recebida pelo centro |
| UserId       | `{iot hub name}` |
| ContentType  | `application/vnd.microsoft.iothub.feedback.json` |

O corpo é um conjunto de registos em série JSON, cada um com as seguintes propriedades:

| Propriedade           | Descrição |
| ------------------ | ----------- |
| EnqueuedTimeUtc    | Uma marca de tempo que indica quando o resultado da mensagem aconteceu (por exemplo, o centro recebeu a mensagem de feedback ou a mensagem original expirou) |
| OriginalMessageId  | O *MessageId* da mensagem cloud-to-device a que esta informação de feedback diz |
| StatusCode         | Uma corda necessária, usada em mensagens de feedback que são geradas pelo centro IoT: <br/> *Sucesso* <br/> *Expirado* <br/> *DeliveryCountExceeded* <br/> *Rejeitado* <br/> *Purgado* |
| Descrição        | Valores de cadeia para *StatusCode* |
| DeviceId           | O *DispositivoId* do dispositivo-alvo da mensagem cloud-to-device a que se refere este pedaço de feedback |
| DeviceGenerationId | O *DispositivoGeração Id* do dispositivo-alvo da mensagem cloud-to-device a que se refere este pedaço de feedback |

Para que a mensagem cloud-to-device correlacionar o seu feedback com a mensagem original, o serviço deve especificar um *MessageId*.

O corpo de uma mensagem de feedback é mostrado no seguinte código:

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

**Feedback pendente para dispositivos eliminados**

Quando um dispositivo é eliminado, qualquer feedback pendente também é eliminado. O feedback do dispositivo é enviado em lotes. Se um dispositivo for eliminado na janela estreita (muitas vezes menos de 1 segundo) entre quando o dispositivo confirmar a receção da mensagem e quando o próximo lote de feedback for preparado, o feedback não ocorrerá.

Pode abordar este comportamento esperando um período de tempo para que o feedback pendente chegue antes de apagar o seu dispositivo. O feedback da mensagem relacionado deve ser assumido perdido uma vez que um dispositivo é eliminado.

## <a name="cloud-to-device-configuration-options"></a>Opções de configuração cloud-to-device

Cada hub IoT expõe as seguintes opções de configuração para mensagens cloud-to-device:

| Propriedade                  | Descrição | Alcance e padrão |
| ------------------------- | ----------- | ----------------- |
| defaultTtlAsIso8601       | TTL padrão para mensagens cloud-to-device | ISO_8601 intervalo até 2 dias (mínimo 1 minuto); padrão: 1 hora |
| maxDeliveryCount          | Contagem máxima de entrega para filas de cloud-to-device por dispositivo | 1 a 100; padrão: 10 |
| feedback.ttlAsIso8601     | Retenção de mensagens de feedback ligadas ao serviço | ISO_8601 intervalo até 2 dias (mínimo 1 minuto); padrão: 1 hora |
| feedback.maxDeliveryCount | Contagem máxima de entrega para a fila de comentários | 1 a 100; padrão: 10 |
| feedback.lockDurationAsIso8601 | Contagem máxima de entrega para a fila de comentários | ISO_8601 intervalo de 5 a 300 segundos (mínimo 5 segundos); predefinição: 60 segundos. |

Pode definir as opções de configuração de uma das seguintes formas:

* **Portal Azure**: Em **Definições** no seu hub IoT, selecione **pontos finais incorporados** e expanda **cloud para mensagens**de dispositivos . (A definição das propriedades **feedback.maxDeliveryCount** e **feedback.lockDurationIso8601** não é suportada atualmente no portal Azure.)

    ![Definir opções de configuração para mensagens cloud-to-device no portal](./media/iot-hub-devguide-messages-c2d/c2d-configuration-portal.png)

* **Azure CLI**: Utilize o comando de atualização do [hub az iot:](https://docs.microsoft.com/cli/azure/iot/hub?view=azure-cli-latest#az-iot-hub-update)

    ```azurecli
    az iot hub update --name {your IoT hub name} \
        --set properties.cloudToDevice.defaultTtlAsIso8601=PT1H0M0S

    az iot hub update --name {your IoT hub name} \
        --set properties.cloudToDevice.maxDeliveryCount=10

    az iot hub update --name {your IoT hub name} \
        --set properties.cloudToDevice.feedback.ttlAsIso8601=PT1H0M0S

    az iot hub update --name {your IoT hub name} \
        --set properties.cloudToDevice.feedback.maxDeliveryCount=10

    az iot hub update --name {your IoT hub name} \
        --set properties.cloudToDevice.feedback.lockDurationAsIso8601=PT0H1M0S
    ```

## <a name="next-steps"></a>Passos seguintes

Para obter informações sobre os SDKs que pode utilizar para receber mensagens cloud-to-device, consulte [SDKs Azure IoT](iot-hub-devguide-sdks.md).

Para experimentar receber mensagens cloud-to-device, consulte o tutorial [cloud-to-device.](iot-hub-csharp-csharp-c2d.md)
