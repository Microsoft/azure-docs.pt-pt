---
title: Compreender as mensagens do Hub Azure IoT Microsoft Docs
description: Guia de desenvolvedores - mensagens de dispositivo-para-nuvem e nuvem-a-dispositivo com IoT Hub. Inclui informações sobre formatos de mensagens e protocolos de comunicações suportados.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.openlocfilehash: f56332fa7f53c729ffaa28ea375f043d1b4a3678
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "60626252"
---
# <a name="send-device-to-cloud-and-cloud-to-device-messages-with-iot-hub"></a>Envie mensagens de dispositivo-a-nuvem e nuvem-a-dispositivo com IoT Hub

O IoT Hub permite uma comunicação bidirecional com os seus dispositivos. Utilize mensagens IoT Hub para comunicar com os seus dispositivos enviando mensagens dos seus dispositivos para as suas soluções para trás e enviando comandos das suas soluções IoT para trás para os seus dispositivos. Saiba mais sobre o formato de [mensagem IoT Hub](iot-hub-devguide-messages-construct.md).

## <a name="sending-device-to-cloud-messages-to-iot-hub"></a>Envio de mensagens de dispositivo-para-nuvem para IoT Hub

O IoT Hub tem um ponto final de serviço incorporado que pode ser usado por serviços de back-end para ler mensagens de telemetria dos seus dispositivos. Este ponto final é compatível com Hubs de [Eventos](https://docs.microsoft.com/azure/event-hubs/) e você pode usar SDKs IoT Hub padrão para [ler a partir deste ponto final incorporado](iot-hub-devguide-messages-read-builtin.md).

O IoT Hub também suporta [pontos finais personalizados](iot-hub-devguide-endpoints.md#custom-endpoints) que podem ser definidos pelos utilizadores para enviar dados e eventos de telemetria para os serviços Dotato usando [o encaminhamento de mensagens.](iot-hub-devguide-messages-d2c.md)

## <a name="sending-cloud-to-device-messages-from-iot-hub"></a>Envio de mensagens cloud-to-device do IoT Hub

Pode enviar mensagens [cloud-to-device](iot-hub-devguide-messages-c2d.md) da extremidade traseira da solução para os seus dispositivos.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

As propriedades centrais da funcionalidade de mensagens IoT Hub são a fiabilidade e durabilidade das mensagens. Estas propriedades permitem a resiliência à conectividade intermitente do lado do dispositivo e para carregar picos no processamento de eventos no lado da nuvem. O IoT Hub implementa *pelo menos uma vez* garantias de entrega para mensagens de dispositivo-a-nuvem e nuvem-a-dispositivo.

## <a name="choosing-the-right-type-of-iot-hub-messaging"></a>Escolher o tipo certo de mensagens IoT Hub

Utilize mensagens dispositivo-cloud para enviar telemetria da série de tempo e alertas da aplicação do seu dispositivo e mensagens cloud-to-device para notificações de sentido único para a sua aplicação de dispositivo.

* Consulte a orientação de [comunicação Dispositivo-nuvem](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-d2c-guidance) para escolher entre mensagens dispositivo-nuvem, propriedades reportadas ou upload de ficheiros.

* Consulte a [orientação de comunicação Cloud-to-device](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-c2d-guidance) para escolher entre mensagens cloud-to-device, propriedades desejadas ou métodos diretos.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre o [encaminhamento](iot-hub-devguide-messages-d2c.md)de mensagens IoT Hub .

* Saiba mais sobre [as mensagens nuvem-a-dispositivo](iot-hub-devguide-messages-c2d.md)Do IoT Hub.