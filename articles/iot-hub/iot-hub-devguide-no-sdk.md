---
title: Desenvolver sem um Azure IoT SDK / Microsoft Docs
description: Guia do desenvolvedor - informações sobre tópicos que pode usar para construir aplicações de dispositivos e aplicações de back-end sem usar um Azure IoT SDK.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/12/2020
ms.custom:
- mqtt
- amqp
- 'Role: IoT Device'
- 'Role: Cloud Development'
ms.openlocfilehash: 3968f19329536169c3fb3eb1fbbaff99e99c293d
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92079665"
---
# <a name="develop-without-using-an-azure-iot-hub-sdk"></a>Desenvolver sem usar um Azure IoT Hub SDK

Este tópico fornece informações e links úteis para desenvolvedores que pretendem desenvolver aplicações de dispositivo ou back-end sem usar os SDKs Azure IoT.

A Microsoft aconselha vivamente a utilização de um Azure IoT SDK. O dispositivo ESDKs de serviço Azure IoT é publicado em muitas plataformas populares. Os SDKs fornecem uma camada de conveniência que lida com grande parte da complexidade do protocolo de comunicação subjacente, incluindo a ligação do dispositivo e a reconexão, e a política de repreensão. Os SDKs são regularmente atualizados para fornecer as mais recentes funcionalidades expostas pelo IoT Hub, bem como atualizações de segurança. A utilização dos SDKs pode ajudá-lo a reduzir o tempo e o tempo de desenvolvimento dedicados à manutenção de códigos. Para saber mais sobre os SDKs Azure IoT, consulte [os SDKs de Dispositivo IoT Azure.](iot-hub-devguide-sdks.md) Para obter mais detalhes sobre as vantagens de usar um Azure IoT SDK, consulte os [benefícios de usar os SDKs Azure IoT e armadilhas para evitar se não](https://azure.microsoft.com/en-us/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/) publicar no blog.

Embora o IoT Hub suporte AMQP, AMQP sobre WebSockets, HTTPS, MQTT e MQTT sobre WebSockets para comunicação com dispositivos, recomendamos a utilização de MQTT se o seu dispositivo o suportar.

## <a name="development-prerequisites"></a>Pré-requisitos de desenvolvimento

Antes de começar o desenvolvimento, deverá ter um conhecimento profundo do IoT Hub e das funcionalidades que pretende que o seu dispositivo ou app de back-end implemente. Aqui está uma lista muito abreviada de tópicos que deve estar familiarizado:

* Certifique-se de que compreende os pontos finais expostos pelo IoT Hub e os protocolos suportados em cada ponto final. Para saber mais, consulte [os pontos finais do IoT Hub](iot-hub-devguide-endpoints.md).

* Quando se trata de uma escolha de protocolo para aplicações de dispositivos, recomendamos vivamente que utilize mQTT. No entanto, antes de escolher um protocolo, certifique-se de compreender as limitações impostas por cada um. Para saber mais, consulte [Escolha um protocolo de comunicação.](iot-hub-devguide-protocols.md)

* Para compreender a autenticação com o IoT Hub, consulte [o Control access to IoT Hub](iot-hub-devguide-security.md).

[!INCLUDE [iot-hub-include-x509-ca-signed-support-note](../../includes/iot-hub-include-x509-ca-signed-support-note.md)]

## <a name="help-on-different-protocols"></a>Ajuda em diferentes protocolos

Para ajudar a utilizar os seguintes protocolos sem um Azure IoT SDK:

* Aplicações de dispositivo ou back-end em **AMQP,** consulte [o suporte amQP](iot-hub-amqp-support.md).

* Aplicativos para dispositivos em **MQTT,** consulte [suporte MQTT](iot-hub-mqtt-support.md). A maior parte deste tópico trata o uso do protocolo MQTT diretamente. Contém também informações sobre a utilização do [repositório da amostra IoT MQTT.](https://github.com/Azure-Samples/IoTMQTTSample) Este repositório contém amostras C que usam a biblioteca Eclipse Mosquitto para enviar mensagens para o IoT Hub.

* Aplicações de dispositivo ou back-end em **HTTPS,** consulte as APIs do [Hub Azure IoT](https://docs.microsoft.com/rest/api/iothub/). Esteja ciente, como indicado nos [pré-requisitos do Desenvolvimento,](#development-prerequisites)que não pode utilizar a autenticação da Autoridade de Certificados X.509 (CA) com HTTPS.

Para dispositivos, recomendamos vivamente a utilização de MQTT se o seu dispositivo o suportar.

## <a name="next-steps"></a>Passos seguintes

* [Suporte para MQTT](iot-hub-mqtt-support.md)
