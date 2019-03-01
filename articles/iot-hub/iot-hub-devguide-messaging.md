---
title: Compreender o sistema de mensagens do IoT Hub do Azure | Documentos da Microsoft
description: Guia do programador - dispositivo-para-cloud e na cloud para o dispositivo de mensagens com o IoT Hub. Inclui informações sobre formatos de mensagens e protocolos de comunicações suportados.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.openlocfilehash: f56332fa7f53c729ffaa28ea375f043d1b4a3678
ms.sourcegitcommit: 15e9613e9e32288e174241efdb365fa0b12ec2ac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2019
ms.locfileid: "57010876"
---
# <a name="send-device-to-cloud-and-cloud-to-device-messages-with-iot-hub"></a>Enviar mensagens dispositivo-para-cloud e na cloud para o dispositivo com o IoT Hub

IoT Hub permite a comunicação bidirecional com os seus dispositivos. Hub de IoT de utilização de mensagens para comunicar com os seus dispositivos através do envio de mensagens dos seus dispositivos para terminam de volta a suas soluções e enviar comandos a partir do seu IoT soluções de back-end para os seus dispositivos. Saiba mais sobre o [formato de mensagem do IoT Hub](iot-hub-devguide-messages-construct.md).

## <a name="sending-device-to-cloud-messages-to-iot-hub"></a>Enviar mensagens dispositivo-para a nuvem ao IoT Hub

IoT Hub tem um ponto de extremidade de serviço interno que pode ser utilizado pelos serviços de back-end para ler mensagens de telemetria dos seus dispositivos. Este ponto final é compatível com [os Hubs de eventos](https://docs.microsoft.com/azure/event-hubs/) e pode usar o padrão SDKs do IoT Hub para [ler a partir deste ponto final incorporado](iot-hub-devguide-messages-read-builtin.md).

IoT Hub também suporta [os pontos finais personalizados](iot-hub-devguide-endpoints.md#custom-endpoints) que podem ser definidos por utilizadores para enviar dados de telemetria do dispositivo e eventos para serviços do Azure com [roteamento de mensagens](iot-hub-devguide-messages-d2c.md).

## <a name="sending-cloud-to-device-messages-from-iot-hub"></a>Enviar mensagens de cloud para o dispositivo do IoT Hub

Pode enviar [cloud-para-dispositivo](iot-hub-devguide-messages-c2d.md) mensagens da solução de back-end para os seus dispositivos.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Propriedades de núcleo da funcionalidade de mensagens do IoT Hub são a fiabilidade e durabilidade de mensagens. Estas propriedades permitem maior resiliência às conectividade intermitente no lado do dispositivo e para carregar os picos no evento de processamento no lado da nuvem. Implementa o IoT Hub *, pelo menos, uma vez* garante a entrega de mensagens dispositivo-para-cloud e cloud-para-dispositivo.

## <a name="choosing-the-right-type-of-iot-hub-messaging"></a>Escolher o tipo certo do IoT Hub de mensagens

Utilize mensagens de dispositivo-para-cloud para o envio de telemetria de série de tempo e os alertas da sua aplicação de dispositivo e mensagens de cloud-para-dispositivo para as notificações unidirecionais para a sua aplicação de dispositivo.

* Consulte a [documentação de orientação do dispositivo-para-cloud comunicação](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-d2c-guidance) para escolher entre as mensagens do dispositivo para a cloud, propriedades comunicadas ou carregamento do ficheiro.

* Consulte a [documentação de orientação de comunicação de Cloud-para-dispositivo](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-c2d-guidance) para escolher entre mensagens cloud para o dispositivo, as propriedades pretendidas ou métodos diretos.

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre o IoT Hub [roteamento de mensagens](iot-hub-devguide-messages-d2c.md).

* Saiba mais sobre o IoT Hub [mensagens cloud-para-dispositivo](iot-hub-devguide-messages-c2d.md).