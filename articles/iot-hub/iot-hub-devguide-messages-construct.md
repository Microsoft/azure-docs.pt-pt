---
title: Compreender o formato de mensagem do IoT Hub do Azure | Documentos da Microsoft
description: Guia do programador - descibes o formato e o conteúdo esperado de mensagens do IoT Hub.
author: ash2017
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/13/2018
ms.author: asrastog
ms.openlocfilehash: f5d25232ebbdb6f8cf07839cc51485dd53381cd9
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56733848"
---
# <a name="create-and-read-iot-hub-messages"></a>Criar e ler mensagens do Hub IoT

Para suportar a perfeita interoperabilidade entre protocolos, o IoT Hub define um formato de mensagem comum para todos os protocolos de voltado para o dispositivo. Esse formato de mensagem é usado para ambos [dispositivo-para-cloud encaminhamento](iot-hub-devguide-messages-d2c.md) e [cloud-para-dispositivo](iot-hub-devguide-messages-c2d.md) mensagens. 

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

IoT Hub implementa com um padrão de mensagens de transmissão em fluxo de mensagens do dispositivo para a cloud. Mensagens do dispositivo-para-cloud do IoT Hub são mais semelhantes [os Hubs de eventos](/azure/event-hubs/) *eventos* que [do Service Bus](/azure/service-bus-messaging/) *mensagens* em que existe um grande volume de eventos, passando por meio do serviço que pode ser lidos por vários leitores.

Uma mensagem do IoT Hub é composta por:

* Um conjunto predeterminado de *propriedades do sistema* conforme indicado abaixo.

* Um conjunto de *propriedades da aplicação*. Um dicionário de propriedades de cadeia de caracteres que pode definir o aplicativo e de acesso, sem a necessidade de desserializar o corpo da mensagem. IoT Hub nunca modifica essas propriedades.

* Um corpo de binário opaco.

Valores e nomes de propriedade só podem conter carateres alfanuméricos ASCII, além de ``{'!', '#', '$', '%, '&', ''', '*', '+', '-', '.', '^', '_', '`', '|', '~'}`` quando envia mensagens dispositivo-para-cloud com o HTTPS de protocolo ou enviam mensagens da cloud para o dispositivo.

Dispositivo-para-cloud mensagens com o IoT Hub com as seguintes características:

* Mensagens dispositivo-para-cloud são mantidos no padrão de um hub IoT e durável **mensagens/eventos** ponto final até sete dias.

* Mensagens do dispositivo para a cloud podem ter um máximo de 256 KB e podem ser agrupadas em lotes para otimizar envia. Lotes podem ser, no máximo, 256 KB.

* IoT Hub não permite a criação de partições arbitrário. Mensagens do dispositivo para cloud são particionadas com base no respetivo tipo de origem **deviceId**.

* Conforme explicado [controlar o acesso ao IoT Hub](iot-hub-devguide-security.md), o IoT Hub permite o controlo de acesso e autenticação por dispositivo.

Para obter mais informações sobre como codificar e decodificar as mensagens enviadas através de protocolos diferentes, consulte [do Azure IoT SDKs](iot-hub-devguide-sdks.md).

A tabela seguinte lista o conjunto de propriedades do sistema de mensagens do IoT Hub.

| Propriedade | Descrição | É utilizador definível? |
| --- | --- | --- |
| id da mensagem |Um identificador de utilizador definível para a mensagem utilizado para padrões de solicitação-resposta. Formato: Uma cadeia de maiúsculas e minúsculas (até 128 carateres de comprimento) de carateres de alfanuméricos ASCII de 7 bits + `{'-', ':', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}`. | Sim |
| sequence-number |Um número de (exclusivo por fila de dispositivo) atribuído pelo IoT Hub para cada mensagem de cloud-para-dispositivo. | Não para mensagens de C2D; Sim, caso contrário. |
| para |Um destino especificado na [Cloud-para-dispositivo](iot-hub-devguide-c2d-guidance.md) mensagens. | Não para mensagens de C2D; Sim, caso contrário. |
| absolute-expiry-time |Data e hora de expiração de mensagem. | Sim |
| iothub-enqueuedtime |Data e hora a [Cloud-para-dispositivo](iot-hub-devguide-c2d-guidance.md) mensagem foi recebida pelo IoT Hub. | Não para mensagens de C2D; Sim, caso contrário. |
| correlation-id |Uma propriedade de cadeia de caracteres numa mensagem de resposta que contém, normalmente, a MessageId da solicitação, em padrões de solicitação-resposta. | Sim |
| id de utilizador |Um ID utilizado para especificar a origem de mensagens. Quando as mensagens são geradas pelo IoT Hub, ele é definido como `{iot hub name}`. | Não |
| iothub-ack |Um gerador de mensagem de comentários. Esta propriedade é utilizada nas mensagens de cloud para o dispositivo ao IoT Hub para gerar mensagens de comentários como resultado o consumo da mensagem do pedido pelo dispositivo. Valores possíveis: **none** (predefinição): nenhuma mensagem de comentários é gerada, **positivo**: receber uma mensagem de comentários, se a mensagem foi concluída, **negativo**: receba uma mensagem de comentários, se a mensagem expirou (ou contagem máxima de entrega foi atingida) sem que está a ser concluída por dispositivo, ou **completo**: positivos e negativos. 
<!-- robinsh For more information, see [Message feedback][lnk-feedback].--> | Sim |
| iothub-connection-device-id |Um ID definido pelo IoT Hub nas mensagens do dispositivo para a cloud. Contém os **deviceId** do dispositivo que enviou a mensagem. | Não para mensagens D2C; Sim, caso contrário. |
| iothub-connection-auth-generation-id |Um ID definido pelo IoT Hub nas mensagens do dispositivo para a cloud. Contém os **generationId** (como por [propriedades de identidade de dispositivo](iot-hub-devguide-identity-registry.md#device-identity-properties)) do dispositivo que enviou a mensagem. | Não para mensagens D2C; Sim, caso contrário. |
| iothub-connection-auth-method |Um método de autenticação definido pelo IoT Hub nas mensagens do dispositivo para a cloud. Esta propriedade contém informações sobre o método de autenticação utilizado para autenticar o dispositivo que envia a mensagem. <!-- ROBINSH For more information, see [Device to cloud anti-spoofing][lnk-antispoofing].--> | Não para mensagens D2C; Sim, caso contrário. |
| iothub-creation-time-utc | Data e hora que a mensagem foi criada num dispositivo. Um dispositivo tem de definir este valor explicitamente. | Sim |

## <a name="message-size"></a>Tamanho da mensagem

IoT Hub mede o tamanho da mensagem de forma independente de protocolo, Considerando apenas a carga real. O tamanho em bytes é calculado como a soma dos seguintes procedimentos:

* O tamanho do corpo em bytes.
* O tamanho em bytes de todos os valores de propriedades do sistema de mensagem.
* O tamanho em bytes de todos os valores e nomes de propriedade do utilizador.

Os nomes das propriedades e os valores são limitadas aos caracteres ASCII, para que o comprimento das cadeias de caracteres é igual ao tamanho em bytes.

## <a name="anti-spoofing-properties"></a>Propriedades anti-spoofing

Para evitar falsificação nas mensagens do dispositivo para a cloud, IoT Hub do dispositivo os carimbos de todas as mensagens com as seguintes propriedades:

* **iothub-connection-device-id**
* **iothub-connection-auth-generation-id**
* **iothub-connection-auth-method**

Os dois primeiros contêm os **deviceId** e **generationId** do dispositivo de origem, como por [propriedades de identidade de dispositivo](iot-hub-devguide-identity-registry.md#device-identity-properties).

O **iothub-ligação-auth-method** propriedade contém um objeto JSON serializado com as seguintes propriedades:

```json
{
  "scope": "{ hub | device }",
  "type": "{ symkey | sas | x509 }",
  "issuer": "iothub"
}
```

## <a name="next-steps"></a>Passos Seguintes

* Para obter informações sobre limites de tamanho de mensagem do IoT Hub, veja [IoT Hub quotas e limitação](iot-hub-devguide-quotas-throttling.md).

* Para saber como criar e ler mensagens em várias linguagens de programação de IoT Hub, veja a [inícios Rápidos](quickstart-send-telemetry-node.md).
