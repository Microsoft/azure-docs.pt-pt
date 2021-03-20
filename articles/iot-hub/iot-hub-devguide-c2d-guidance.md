---
title: Opções de nuvem-para-dispositivo Azure IoT Hub | Microsoft Docs
description: Guia do programador - orientação sobre quando utilizar métodos diretos, propriedades desejadas do device twin ou mensagens nuvem-a-dispositivo para comunicações nuvem-dispositivo.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.custom:
- amqp
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
ms.openlocfilehash: ad4f5dcd137a9be6dfc764385802792026c0297d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "101093015"
---
# <a name="cloud-to-device-communications-guidance"></a>Orientação de comunicações nuvem-para-dispositivo

O IoT Hub oferece três opções para as aplicações do dispositivo exporem a funcionalidade a uma aplicação de back-end:

* [Métodos diretos](iot-hub-devguide-direct-methods.md) para comunicações que requerem uma confirmação imediata do resultado. Os métodos diretos são frequentemente utilizados para o controlo interativo de dispositivos, tais como ligar um ventilador.

* [As propriedades desejadas da Twin](iot-hub-devguide-device-twins.md) para comandos de longa duração destinam-se a colocar o dispositivo num determinado estado desejado. Por exemplo, desaponte o intervalo de envio da telemetria para 30 minutos.

* [Mensagens em nuvem para dispositivo](iot-hub-devguide-messages-c2d.md) para notificações unidirecionais para a aplicação do dispositivo.

Para saber como [a Azure IoT Plug and Play](../iot-pnp/overview-iot-plug-and-play.md) utiliza estas opções para controlar dispositivos IoT Plug e Play, consulte o guia de desenvolvimento de [serviços IoT Plug e Play](../iot-pnp/concepts-developer-guide-service.md).

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Aqui está uma comparação detalhada das várias opções de comunicação nuvem-a-dispositivo.

| Categorias | Métodos diretos | As propriedades desejadas de Twin | Mensagens nuvem-para-dispositivo |
| ---------- | -------------- | ------------------------- | ------------------------ |
| Scenario | Comandos que requerem confirmação imediata, como ligar um ventilador. | Comandos de longa duração destinados a colocar o dispositivo num determinado estado desejado. Por exemplo, desaponte o intervalo de envio da telemetria para 30 minutos. | Notificações unidirecionais para a aplicação do dispositivo. |
| Fluxo de dados | De dois sentidos. A aplicação do dispositivo pode responder ao método de imediato. A solução back end recebe o resultado contexicamente ao pedido. | Só de ida. A aplicação do dispositivo recebe uma notificação com a alteração da propriedade. | Só de ida. A aplicação do dispositivo recebe a mensagem
| Durabilidade | Os dispositivos desligados não são contactados. A solução traseira é notificada de que o dispositivo não está ligado. | Os valores patrimoniais são preservados no dispositivo twin. O dispositivo lê-o na próxima reconexão. Os valores de propriedade são recuperáveis com a [linguagem de consulta IoT Hub](iot-hub-devguide-query-language.md). | As mensagens podem ser retidas pelo IoT Hub até 48 horas. |
| Targets | Dispositivo único utilizando **dispositivoId**, ou vários dispositivos que utilizem [trabalhos](iot-hub-devguide-jobs.md). | Dispositivo único utilizando **dispositivoId**, ou vários dispositivos que utilizem [trabalhos](iot-hub-devguide-jobs.md). | Dispositivo único por **dispositivoId**. |
| Tamanho | O tamanho máximo da carga útil do método direto é de 128 KB. | O tamanho máximo das propriedades desejadas é de 32 KB. | Até 64 mensagens KB. |
| Frequência | Elevada. Para mais informações, consulte [os limites do IoT Hub.](iot-hub-devguide-quotas-throttling.md) | Média. Para mais informações, consulte [os limites do IoT Hub.](iot-hub-devguide-quotas-throttling.md) | Baixo. Para mais informações, consulte [os limites do IoT Hub.](iot-hub-devguide-quotas-throttling.md) |
| Protocolo | Disponível usando MQTT ou AMQP. | Disponível usando MQTT ou AMQP. | Disponível em todos os protocolos. O dispositivo deve fazer sondagem ao utilizar HTTPS. |

Saiba como utilizar métodos diretos, propriedades desejadas e mensagens nuvem-a-dispositivo nos seguintes tutoriais:

* [Utilizar métodos diretos](quickstart-control-device-node.md)
* [Utilize propriedades desejadas para configurar dispositivos](tutorial-device-twins.md) 
* [Enviar mensagens da cloud para o dispositivo](iot-hub-node-node-c2d.md)
