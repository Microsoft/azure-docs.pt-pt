---
title: Compreenda as mensagens nuvem-para-dispositivo do Azure IoT Hub | Microsoft Docs
description: Este guia de desenvolvedores discute como usar mensagens nuvem-dispositivo com o seu hub IoT. Inclui informações sobre o ciclo de vida da mensagem e opções de configuração.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/15/2018
ms.custom: mqtt, devx-track-azurecli
ms.openlocfilehash: 7bb3ca2b31eaef5c0639f30e0f2a329a37dfe7e0
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107761786"
---
# <a name="send-cloud-to-device-messages-from-an-iot-hub"></a>Envie mensagens nuvem-para-dispositivo a partir de um hub IoT

Para enviar notificações unidirecionais para uma aplicação de dispositivo a partir da sua solução traseira, envie mensagens nuvem-dispositivo do seu hub IoT para o seu dispositivo. Para uma discussão de outras opções cloud-to-device suportadas pelo Azure IoT Hub, consulte [a orientação de comunicações Cloud-to-device](iot-hub-devguide-c2d-guidance.md).

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Envia mensagens nuvem-a-dispositivo através de um ponto final virado para o serviço, */mensagens/dispositivos.* Em seguida, um dispositivo recebe as mensagens através de um ponto final específico do dispositivo, */dispositivos/{deviceId}/messages/devicebound*.

Para direcionar cada mensagem nuvem-a-dispositivo num único dispositivo, **o** seu hub IoT define a propriedade para */dispositivos/{deviceId}/messages/devicebound*.

Cada fila de dispositivos contém, no máximo, 50 mensagens nuvem-dispositivo. Tentar enviar mais mensagens para o mesmo dispositivo resulta num erro.

## <a name="the-cloud-to-device-message-life-cycle"></a>O ciclo de vida da mensagem nuvem-para-dispositivo

Para garantir a entrega de mensagens pelo menos uma vez, o seu hub IoT persiste em mensagens nuvem-dispositivo em filas por dispositivo. Para que o hub IoT remova as mensagens da fila, os dispositivos devem reconhecer explicitamente *a conclusão*. Esta abordagem garante resiliência contra falhas de conectividade e dispositivos.

O gráfico de estado do ciclo de vida é apresentado no seguinte diagrama:

![Ciclo de vida de mensagem nuvem-para-dispositivo](./media/iot-hub-devguide-messages-c2d/lifecycle.png)

Quando o serviço de hub IoT envia uma mensagem para um dispositivo, o serviço define o estado da mensagem para *Enqueued*. Quando um dispositivo quer *receber* uma mensagem, o hub IoT *bloqueia* a mensagem definindo o estado para *Invisível*. Este estado permite que outras linhas do dispositivo comecem a receber outras mensagens. Quando um fio do dispositivo completa o processamento de uma mensagem, notifica o hub IoT *completando a* mensagem. O hub IoT, em seguida, define o estado para *Concluído*.

Um dispositivo também pode:

* *Rejeite* a mensagem, que faz com que o centro de IoT a coloque no estado *de letras mortas.* Os dispositivos que se ligam através do Protocolo de Transporte de Telemetria de Fila de Mensagens (MQTT) não podem rejeitar mensagens nuvem-dispositivo.

* *Abandone* a mensagem, o que faz com que o hub IoT volte a colocar a mensagem na fila, com o estado definido para *Enqueued*. Os dispositivos que se ligam ao longo do Protocolo MQTT não podem abandonar mensagens nuvem-dispositivo.

Um fio pode não processar uma mensagem sem notificar o hub IoT. Neste caso, as mensagens transitam automaticamente do estado *invisível* de volta para o estado *Enqueued* após um tempo de *visibilidade* (ou tempo de *bloqueio).* O valor deste tempo de intervalo é de um minuto e não pode ser alterado.

A propriedade **de contagem de entrega máxima** no hub IoT determina o número máximo de vezes que uma mensagem pode transitar entre os estados *Enqueued* e *Invisible.* Após esse número de transições, o hub IoT define o estado da mensagem para *Dead lettered*. Da mesma forma, o hub IoT define o estado de uma mensagem para *Dead lettered* após o seu tempo de expiração. Para mais informações, consulte [a hora de viver.](#message-expiration-time-to-live)

O [Como enviar mensagens cloud-to-device com](iot-hub-csharp-csharp-c2d.md) o artigo do IoT Hub mostra-lhe como enviar mensagens cloud-to-device a partir da nuvem e recebê-las num dispositivo.

Um dispositivo normalmente completa uma mensagem nuvem-dispositivo quando a perda da mensagem não afeta a lógica da aplicação. Um exemplo disso pode ser quando o dispositivo tenha persistido o conteúdo da mensagem localmente ou tenha executado com sucesso uma operação. A mensagem também pode transportar informações transitórias, cuja perda não afetaria a funcionalidade da aplicação. Às vezes, para tarefas de longa duração, pode:

* Preencha a mensagem nuvem-dispositivo depois de o dispositivo ter insistido na descrição da tarefa no armazenamento local.

* Notifique a solução final com uma ou mais mensagens dispositivo-a-nuvem em várias fases de progresso da tarefa.

## <a name="message-expiration-time-to-live"></a>Expiração da mensagem (tempo de vida)

Cada mensagem nuvem-a-dispositivo tem um tempo de validade. Este tempo é definido por qualquer um dos seguintes:

* A **propriedade ExpiryTimeUtc** no serviço
* O hub IoT, usando o tempo padrão *para viver* que é especificado como uma propriedade de hub IoT

Consulte [as opções de configuração Cloud-to-device](#cloud-to-device-configuration-options).

Uma forma comum de aproveitar a expiração da mensagem e evitar o envio de mensagens para dispositivos desligados é definir pouco *tempo para valores vivos.* Esta abordagem obtém o mesmo resultado que manter o estado de ligação do dispositivo, mas é mais eficiente. Quando solicita reconhecimentos de mensagens, o hub IoT notifica quais os dispositivos que são:

* Capaz de receber mensagens.
* Não estão online ou falharam.

## <a name="message-feedback"></a>Feedback da mensagem

Quando envia uma mensagem nuvem-a-dispositivo, o serviço pode solicitar a entrega de feedback por mensagem sobre o estado final dessa mensagem. Fá-lo definindo a propriedade da aplicação **iothub-ack** na mensagem nuvem-para-dispositivo que está a ser enviada para um dos seguintes quatro valores:

| Valor da propriedade Ack | Comportamento |
| ------------ | -------- |
| nenhum     | O hub IoT não gera uma mensagem de feedback (comportamento predefinido). |
| positivo | Se a mensagem nuvem-para-dispositivo chegar ao estado *preenchido,* o hub IoT gera uma mensagem de feedback. |
| negativo | Se a mensagem nuvem-para-dispositivo chegar ao estado *de letras Mortas,* o hub IoT gera uma mensagem de feedback. |
| cheio     | O hub IoT gera uma mensagem de feedback em qualquer dos casos. |

Se o valor **Ack** estiver *completo* e não receber uma mensagem de feedback, significa que a mensagem de feedback expirou. O serviço não pode saber o que aconteceu com a mensagem original. Na prática, um serviço deve garantir que pode processar o feedback antes de expirar. O prazo máximo de validade é de dois dias, o que deixa tempo para que o serviço volte a funcionar se ocorrer uma falha.

Como explicado em [Endpoints,](iot-hub-devguide-endpoints.md)o hub IoT fornece feedback através de um ponto final virado para o serviço, */messages/servicebound/feedback*, como mensagens. A semântica para receber feedback é a mesma que para mensagens cloud-to-device. Sempre que possível, o feedback da mensagem é loteado numa única mensagem, com o seguinte formato:

| Propriedade     | Descrição |
| ------------ | ----------- |
| Tempo Enqueutado | Um tempotampia que indica quando a mensagem de feedback foi recebida pelo centro |
| IDUtilizador       | `{iot hub name}` |
| ConteúdoType  | `application/vnd.microsoft.iothub.feedback.json` |

O sistema enviará o feedback quando o lote chegar a 64 mensagens, ou em 15 segundos da última enviada, seja qual for a primeira. 

O corpo é um conjunto de registos serializados por JSON, cada um com as seguintes propriedades:

| Propriedade           | Descrição |
| ------------------ | ----------- |
| EnqueuedTimeUtc    | Um tempotando que indica quando o resultado da mensagem aconteceu (por exemplo, o hub recebeu a mensagem de feedback ou a mensagem original expirou) |
| OriginalMessageId  | O *MessageId* da mensagem nuvem-para-dispositivo a que esta informação de feedback se relaciona |
| Código de Estado         | Uma cadeia necessária, usada em mensagens de feedback que são geradas pelo hub IoT: <br/> *Com êxito* <br/> *Expirada* <br/> *DeliveryCountExceeded* <br/> *Rejeitado* <br/> *Purgado* |
| Description        | Valores de cadeia para *StatusCode* |
| DeviceId           | O *DispositivoId* do dispositivo-alvo da mensagem nuvem-a-dispositivo a que este pedaço de feedback se relaciona |
| GenerationId do dispositivo | O *DispositivoGenerationId* do dispositivo-alvo da mensagem nuvem-a-dispositivo a que este pedaço de feedback se relaciona |

Para que a mensagem nuvem-dispositivo correlacione o seu feedback com a mensagem original, o serviço deve especificar um *MessageId*.

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

Pode abordar este comportamento esperando um período de tempo para que o feedback pendente chegue antes de apagar o seu dispositivo. O feedback da mensagem relacionada deve ser assumido perdido uma vez que um dispositivo é eliminado.

## <a name="cloud-to-device-configuration-options"></a>Opções de configuração nuvem-para-dispositivo

Cada hub IoT expõe as seguintes opções de configuração para mensagens nuvem-dispositivo:

| Propriedade                  | Descrição | Alcance e incumprimento |
| ------------------------- | ----------- | ----------------- |
| defaultTtlAsIso8601       | TTL padrão para mensagens nuvem-dispositivo | intervalo ISO_8601 até 2 dias (mínimo de 1 minuto); padrão: 1 hora |
| maxDeliveryCount          | Contagem máxima de entrega para filas de nuvem-para-dispositivo por dispositivo | 1 a 100; padrão: 10 |
| feedback.ttlAsIso8601     | Retenção para mensagens de feedback ligadas ao serviço | intervalo ISO_8601 até 2 dias (mínimo de 1 minuto); padrão: 1 hora |
| feedback.maxDeliveryCount | Contagem máxima de entrega para a fila de feedback | 1 a 100; padrão: 10 |
| feedback.lockDurationAsIso8601 | Contagem máxima de entrega para a fila de feedback | intervalo de ISO_8601 de 5 a 300 segundos (mínimo 5 segundos); padrão: 60 segundos. |

Pode definir as opções de configuração de uma das seguintes formas:

* **Portal Azure**: Em **Definições** no seu hub IoT, selecione **pontos finais incorporados** e expanda **a Cloud para mensagens de dispositivos**. (Definindo as propriedades **feedback.maxDeliveryCount** e **feedback.lockDurationAsIso8601** não é atualmente suportado no portal Azure.)

    ![Definir opções de configuração para mensagens nuvem-para-dispositivo no portal](./media/iot-hub-devguide-messages-c2d/c2d-configuration-portal.png)

* **Azure CLI**: Utilize o comando [de atualização do hub az iot:](/cli/azure/iot/hub#az_iot_hub_update)

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

Para obter informações sobre os SDKs que pode utilizar para receber mensagens cloud-to-device, consulte [Azure IoT SDKs](iot-hub-devguide-sdks.md).

Para experimentar a receção de mensagens nuvem-dispositivo, consulte o tutorial [enviar cloud-to-device.](iot-hub-csharp-csharp-c2d.md)
