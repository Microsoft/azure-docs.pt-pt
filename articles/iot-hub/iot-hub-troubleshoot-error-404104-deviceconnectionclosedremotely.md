---
title: Resolução de problemas Erro do Hub Azure IoT 404104 DeviceConnectionClosedRemotely
description: Entenda como corrigir o erro 404104 DeviceConnectionClosedRemotely
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.custom: mqtt
ms.openlocfilehash: 673a76417739fa59a91979cca7c6807a584868f0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92538260"
---
# <a name="404104-deviceconnectionclosedremotely"></a>404104 DeviceConnectionClosedRemotely

Este artigo descreve as causas e soluções para **erros de 404104 DeviceConnectionClosedRemotely.**

## <a name="symptoms"></a>Sintomas

### <a name="symptom-1"></a>Sintoma 1

Os dispositivos desligam-se num intervalo regular (a cada 65 minutos, por exemplo) e vê **404104 DeviceConnectionClosedRemotely** em registos de recursos IoT Hub. Por vezes, também se vê **401003 IoTHubUnautized** e um evento de conexão de dispositivo bem sucedido menos de um minuto depois.

### <a name="symptom-2"></a>Sintoma 2

Os dispositivos desligam-se aleatoriamente e vê **404104 DeviceConnectionClosedRemotely** em registos de recursos do IoT Hub.

### <a name="symptom-3"></a>Sintoma 3

Muitos dispositivos desligam-se ao mesmo tempo, vê-se um mergulho na [métrica dos dispositivos ligados (connectedDeviceCount),](monitor-iot-hub-reference.md)e existem mais **404104 DeviceConnectionClosedRemotely** e [500xxx Erros internos](iot-hub-troubleshoot-error-500xxx-internal-errors.md) em Registos monitores Azure do que o habitual.

## <a name="causes"></a>Causas

### <a name="cause-1"></a>Motivo 1

O [símbolo SAS usado para ligar ao IoT Hub](iot-hub-devguide-security.md#security-tokens) expirou, o que faz com que o IoT Hub desligue o dispositivo. A ligação é restabelecida quando o token é atualizado pelo dispositivo. Por exemplo, [o token SAS expira a cada hora por padrão para C SDK](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-authentication), o que pode levar a desconexões regulares.

Para saber mais, consulte [a causa ioTHubUnautizada 401003.](iot-hub-troubleshoot-error-401003-iothubunauthorized.md#cause-1)

### <a name="cause-2"></a>Motivo 2

Algumas possibilidades incluem:

- O dispositivo perdeu a conectividade subjacente da rede por mais tempo do que a [MQTT manter-se vivo,](iot-hub-mqtt-support.md#default-keep-alive-timeout)resultando num tempo de inatividade remoto. A definição MQTT de manter-se viva pode ser diferente por dispositivo.

- O dispositivo enviou um reset de nível TCP/IP, mas não enviou um nível de aplicação `MQTT DISCONNECT` . Basicamente, o dispositivo fechou abruptamente a ligação da tomada subjacente. Por vezes, este problema é causado por bugs em versões mais antigas do Azure IoT SDK.

- A aplicação do lado do dispositivo despenhou-se.

### <a name="cause-3"></a>Causa 3

O IoT Hub pode estar a passar por um problema transitório. Consulte a [causa do erro do servidor interno do IoT Hub](iot-hub-troubleshoot-error-500xxx-internal-errors.md#cause).

## <a name="solutions"></a>Soluções

### <a name="solution-1"></a>Solução 1

Ver [solução IoTHubUnautized 401003](iot-hub-troubleshoot-error-401003-iothubunauthorized.md#solution-1)

### <a name="solution-2"></a>Solução 2

- Certifique-se de que o dispositivo tem uma boa conectividade com o IoT Hub [testando a ligação](tutorial-connectivity.md). Se a rede não for fiável ou intermitente, não recomendamos o aumento do valor de manter vivo porque pode resultar na deteção (através de alertas do Azure Monitor, por exemplo) demorando mais tempo. 

- Utilize as versões mais recentes dos [SDKs IoT](iot-hub-devguide-sdks.md).

### <a name="solution-3"></a>Solução 3

Consulte [soluções para erros internos do servidor IoT Hub](iot-hub-troubleshoot-error-500xxx-internal-errors.md#solution).

## <a name="next-steps"></a>Passos seguintes

Recomendamos a utilização de SDKs de dispositivo Azure IoT para gerir as ligações de forma fiável. Para saber mais, consulte [Gerir a conectividade e mensagens fiáveis utilizando SDKs de dispositivoS Azure IoT Hub](iot-hub-reliability-features-in-sdks.md)