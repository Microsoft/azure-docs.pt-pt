---
title: Opções azure IoT Hub cloud-to-device [ Azure IoT Hub cloud-to-device] Microsoft Docs
description: Guia de desenvolvedores - orientação sobre quando usar métodos diretos, propriedades de dispositivo sinuosas ou mensagens cloud-to-device para comunicações cloud-to-device.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.openlocfilehash: a2ca9a167d50619ed2963b13515c0a772d712570
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77591273"
---
# <a name="cloud-to-device-communications-guidance"></a>Orientação de comunicações cloud-to-device

O IoT Hub oferece três opções para aplicações de dispositivos exporem funcionalidades a uma aplicação de back-end:

* [Métodos diretos](iot-hub-devguide-direct-methods.md) para comunicações que requerem confirmação imediata do resultado. Os métodos diretos são frequentemente utilizados para o controlo interativo de dispositivos como ligar um ventilador.

* As [propriedades desejadas pela Twin](iot-hub-devguide-device-twins.md) para comandos de longa duração destinados a colocar o dispositivo num certo estado desejado. Por exemplo, detete teto de telemetria enviar intervalo para 30 minutos.

* [Mensagens cloud-to-device](iot-hub-devguide-messages-c2d.md) para notificações de ida para a aplicação do dispositivo.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Aqui está uma comparação detalhada das várias opções de comunicação cloud-to-device.

|  | Métodos diretos | Propriedades desejadas de Twin | Mensagens cloud-to-device |
| ---- | ------- | ---------- | ---- |
| Cenário | Comandos que requerem confirmação imediata, como ligar um ventilador. | Comandos de longa duração destinados a colocar o dispositivo num certo estado desejado. Por exemplo, detete teto de telemetria enviar intervalo para 30 minutos. | Notificações de ida para a aplicação do dispositivo. |
| Fluxo de dados | Nos dois sentidos. A aplicação do dispositivo pode responder imediatamente ao método. A solução de fundo recebe o resultado contexicamente ao pedido. | Só de ida. A aplicação do dispositivo recebe uma notificação com a alteração da propriedade. | Só de ida. A aplicação do dispositivo recebe a mensagem
| Durabilidade | Os dispositivos desligados não são contactados. A extremidade traseira da solução é notificada de que o dispositivo não está ligado. | Os valores da propriedade são preservados no dispositivo twin. O dispositivo irá lê-lo na próxima reconexão. Os valores da propriedade são recuperáveis com a linguagem de [consulta IoT Hub.](iot-hub-devguide-query-language.md) | As mensagens podem ser retidas pelo IoT Hub até 48 horas. |
| Destinos | Dispositivo único utilizando **dispositivoId**, ou vários dispositivos utilizando [trabalhos](iot-hub-devguide-jobs.md). | Dispositivo único utilizando **dispositivoId**, ou vários dispositivos utilizando [trabalhos](iot-hub-devguide-jobs.md). | Dispositivo único por **dispositivoId**. |
| Tamanho | O tamanho máximo da carga direta do método é de 128 KB. | O tamanho máximo das propriedades desejadas é de 32 KB. | Até 64 mensagens KB. |
| Frequência | Elevada. Para mais informações, consulte [os limites do Hub IoT](iot-hub-devguide-quotas-throttling.md). | Média. Para mais informações, consulte [os limites do Hub IoT](iot-hub-devguide-quotas-throttling.md). | Baixo. Para mais informações, consulte [os limites do Hub IoT](iot-hub-devguide-quotas-throttling.md). |
| Protocolo | Disponível através de MQTT ou AMQP. | Disponível através de MQTT ou AMQP. | Disponível em todos os protocolos. O dispositivo deve fazer sondagens ao utilizar HTTPS. |

Saiba como utilizar métodos diretos, propriedades desejadas e mensagens cloud-to-device nos seguintes tutoriais:

* [Utilizar métodos diretos](quickstart-control-device-node.md)
* [Utilize as propriedades desejadas para configurar dispositivos](tutorial-device-twins.md) 
* [Enviar mensagens da cloud para o dispositivo](iot-hub-node-node-c2d.md)
