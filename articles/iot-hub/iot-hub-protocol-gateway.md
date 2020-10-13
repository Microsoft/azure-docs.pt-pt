---
title: Gateway de protocolo Azure IoT / Microsoft Docs
description: Como utilizar uma porta de entrada de protocolo Azure IoT para alargar as capacidades do IoT Hub e o suporte protocolar para permitir que os dispositivos se conectem ao seu hub usando protocolos não suportados pelo IoT Hub de forma nativa.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/11/2017
ms.custom:
- amqp
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
ms.openlocfilehash: 315d76715973b79a971f3ca15bcc5186d20135a2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87325037"
---
# <a name="support-additional-protocols-for-iot-hub"></a>Apoiar protocolos adicionais para o IoT Hub

O Azure IoT Hub suporta a comunicação sobre os protocolos MQTT, AMQP e HTTPS. Em alguns casos, dispositivos ou gateways de campo podem não ser capazes de usar um destes protocolos padrão e exigir uma adaptação protocolar. Nesses casos, pode utilizar um gateway personalizado. Um gateway personalizado permite a adaptação protocolar para pontos finais do IoT Hub, fazendo a ponte entre o tráfego de e para o IoT Hub. Você pode usar o [gateway de protocolo Azure IoT](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md) como uma porta de entrada personalizada para permitir a adaptação do protocolo para ioT Hub.

## <a name="azure-iot-protocol-gateway"></a>Gateway de protocolo Azure IoT

O gateway de protocolo Azure IoT é um quadro para a adaptação do protocolo que é projetado para comunicação de dispositivos bidirecionais de alta escala com o IoT Hub. O portal do protocolo é um componente de passagem que aceita ligações do dispositivo através de um protocolo específico. Faz a ponte entre o tráfego e o IoT Hub sobre amqp 1.0.

Pode implementar o portal de protocolo em Azure de uma forma altamente escalável utilizando as funções de trabalhadores do Azure Service Fabric, Azure Cloud Services ou Windows Virtual Machines. Além disso, o portal do protocolo pode ser implantado em ambientes no local, como portas de campo.

O gateway de protocolo Azure IoT inclui um adaptador de protocolo MQTT que lhe permite personalizar o comportamento do protocolo MQTT se necessário. Uma vez que o IoT Hub fornece suporte integrado para o protocolo MQTT v3.1.1, só deve considerar a utilização do adaptador de protocolo MQTT se forem necessárias personalizações de protocolo ou requisitos específicos para funcionalidades adicionais.

O adaptador MQTT também demonstra o modelo de programação para a construção de adaptadores de protocolos para outros protocolos. Além disso, o modelo de programação do protocolo Azure IoT permite-lhe ligar componentes personalizados para processamento especializado, tais como autenticação personalizada, transformações de mensagens, compressão/descompressão ou encriptação/desencriptação de tráfego entre os dispositivos e o IoT Hub.

Para flexibilidade, o gateway de protocolo Azure IoT e a implementação de MQTT são fornecidos num projeto de software de código aberto. Pode utilizar o projeto de código aberto para adicionar suporte a vários protocolos e versões protocolares ou personalizar a implementação para o seu cenário. 

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre o portal de protocolo Azure IoT e como usá-lo e implantá-lo como parte da sua solução IoT, consulte:

* [Repositório de gateway de protocolo Azure IoT no GitHub](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md)

* [Guia de desenvolvimento de gateway de protocolo Azure IoT](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/docs/DeveloperGuide.md)

Para saber mais sobre o planeamento da sua implantação no IoT Hub, consulte:

* [Compare com centros de eventos](iot-hub-compare-event-hubs.md)

* [Escala, alta disponibilidade e recuperação de desastres](iot-hub-scaling.md)

* [Guia de desenvolvimento do IoT Hub](iot-hub-devguide.md)