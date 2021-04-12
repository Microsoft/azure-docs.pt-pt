---
title: Atualização do dispositivo para os requisitos de rede IoT Hub | Microsoft Docs
description: A Atualização do Dispositivo para IoT Hub utiliza uma variedade de portas de rede para diferentes finalidades.
author: philmea
ms.author: philmea
ms.date: 1/11/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: 0512308fbaa0a725c6ecca573c70c90d8c04e247
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105558385"
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

[Saiba mais](../iot-hub/iot-hub-devguide-protocols.md) sobre a lista atual de protocolos suportados.
