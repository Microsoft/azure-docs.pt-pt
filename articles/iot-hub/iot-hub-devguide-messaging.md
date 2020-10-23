---
title: Compreenda as mensagens do Azure IoT Hub Microsoft Docs
description: Guia do desenvolvedor - mensagens de dispositivo para nuvem e nuvem-para-dispositivo com IoT Hub. Inclui informações sobre formatos de mensagens e protocolos de comunicações suportados.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.custom:
- 'Role: Cloud Development'
ms.openlocfilehash: e25b9d6cbe8446fb2f10150f17f7e2b0ccf85d3c
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2020
ms.locfileid: "92147627"
---
# <a name="send-device-to-cloud-and-cloud-to-device-messages-with-iot-hub"></a>Enviar mensagens de dispositivo para nuvem e nuvem-para-dispositivo com IoT Hub

O IoT Hub permite uma comunicação bidis com os seus dispositivos. Utilize mensagens IoT Hub para comunicar com os seus dispositivos, enviando mensagens dos seus dispositivos para as suas soluções de volta e enviando comandos das suas soluções IoT para trás para os seus dispositivos. Saiba mais sobre o [formato de mensagem IoT Hub](iot-hub-devguide-messages-construct.md).

## <a name="sending-device-to-cloud-messages-to-iot-hub"></a>Envio de mensagens dispositivo-nuvem para IoT Hub

O IoT Hub tem um ponto final de serviço incorporado que pode ser usado por serviços de back-end para ler mensagens de telemetria dos seus dispositivos. Este ponto final é compatível com [os Centros de Eventos](../event-hubs/index.yml) e pode utilizar SDKs IoT Hub standard para [ler a partir deste ponto final incorporado.](iot-hub-devguide-messages-read-builtin.md)

O IoT Hub também suporta [pontos finais personalizados](iot-hub-devguide-endpoints.md#custom-endpoints) que podem ser definidos pelos utilizadores para enviar dados e eventos de telemetria do dispositivo para os serviços Azure utilizando [o encaminhamento de mensagens](iot-hub-devguide-messages-d2c.md).

## <a name="sending-cloud-to-device-messages-from-iot-hub"></a>Envio de mensagens nuvem-para-dispositivo a partir do IoT Hub

Pode enviar mensagens [nuvem-a-dispositivo](iot-hub-devguide-messages-c2d.md) a partir da extremidade traseira da solução para os seus dispositivos.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

As propriedades fundamentais da funcionalidade de mensagens IoT Hub são a fiabilidade e durabilidade das mensagens. Estas propriedades permitem resiliência à conectividade intermitente no lado do dispositivo e para carregar picos no processamento de eventos no lado da nuvem. O IoT Hub implementa *pelo menos uma vez* as garantias de entrega para mensagens de dispositivo para nuvem e nuvem-para-dispositivo.

## <a name="choosing-the-right-type-of-iot-hub-messaging"></a>Escolher o tipo certo de mensagens IoT Hub

Utilize mensagens de dispositivo para nuvem para enviar telemetria de séries de tempo e alertas a partir da aplicação do seu dispositivo, e mensagens nuvem-a-dispositivo para notificações unidirecionais para a aplicação do seu dispositivo.

* Consulte [a orientação de comunicação dispositivo-nuvem para](./iot-hub-devguide-d2c-guidance.md) escolher entre mensagens de dispositivo para nuvem, propriedades relatadas ou upload de ficheiros.

* Consulte a [orientação de comunicação Nuvem-a-dispositivo](./iot-hub-devguide-c2d-guidance.md) para escolher entre mensagens nuvem-dispositivo, propriedades desejadas ou métodos diretos.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre o [encaminhamento de mensagens](iot-hub-devguide-messages-d2c.md)IoT Hub .

* Saiba mais sobre [mensagens nuvem-para-dispositivo](iot-hub-devguide-messages-c2d.md)do IoT Hub .