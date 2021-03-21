---
title: Atualização do dispositivo para os requisitos de rede IoT Hub | Microsoft Docs
description: A Atualização do Dispositivo para IoT Hub utiliza uma variedade de portas de rede para diferentes finalidades.
author: philmea
ms.author: philmea
ms.date: 1/11/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: e72ff144a56f44ccaa695b7dab328e42052fce39
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "101680092"
---
# <a name="ports-used-with-device-update-for-iot-hub"></a>Portas utilizadas com atualização do dispositivo para ioT hub
A ADU utiliza uma variedade de portas de rede para diferentes finalidades.

## <a name="default-ports"></a>Portas Predefinidos

Objetivo|Número da Porta |
---|---
Download a partir de Redes/CDNs  | 80 (Protocolo HTTP)
Download a partir de MCC/CDNs | 80 (Protocolo HTTP)
Ligação do agente da ADU ao Azure IoT Hub  | 8883 (Protocolo MQTT)

## <a name="use-azure-iot-hub-supported-protocols"></a>Use protocolos suportados pelo Azure IoT Hub
O agente ADU pode ser modificado para utilizar qualquer um dos protocolos suportados do Azure IoT Hub.

[Saiba mais](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-protocols#:~:text=Table%202%20%20%20,%201%20more%20rows) sobre a lista atual de protocolos suportados.
