---
title: Resolução de problemas Erro do Hub Azure IoT 404104 DispositivoConnectionClosedRemotely
description: Entenda como corrigir o erro 404104 DispositivoConnectionClosedRemotely
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.custom: mqtt
ms.openlocfilehash: c8cb91aa0c7ce1610320d4107db282d3c34407ba
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758721"
---
# <a name="404104-deviceconnectionclosedremotely"></a>404104 DeviceConnectionClosedRemotely

Este artigo descreve as causas e soluções para **erros 404104 DeviceConnectionClosedRemotely.**

## <a name="symptoms"></a>Sintomas

### <a name="symptom-1"></a>Sintoma 1

Os dispositivos desligam-se regularmente (a cada 65 minutos, por exemplo) e vê **404104 DispositivoSConnectionClosedRemotely** em registos de diagnóstico do IoT Hub. Por vezes, também vê **401003 IoTHubUnauthorized** e um evento de conexão de dispositivos bem sucedido menos de um minuto depois.

### <a name="symptom-2"></a>Sintoma 2

Os dispositivos desligam-se aleatoriamente e **vê-se o 404104 DeviceConnectionClosedRemotely** nos registos de diagnóstico do IoT Hub.

### <a name="symptom-3"></a>Sintoma 3

Muitos dispositivos desligam-se ao mesmo tempo, vê-se um mergulho na métrica dos [dispositivos conectados](iot-hub-metrics.md), e existem mais **404104 DispositivoSConnectionClosedRemotely** e [500xxx Erros internos](iot-hub-troubleshoot-error-500xxx-internal-errors.md) em registos de diagnóstico do que o habitual.

## <a name="causes"></a>Causas

### <a name="cause-1"></a>Causa 1

O [token SAS usado para ligar ao IoT Hub](iot-hub-devguide-security.md#security-tokens) expirou, o que faz com que o IoT Hub desligue o dispositivo. A ligação é restabelecida quando o token é refrescado pelo dispositivo. Por exemplo, [o token SAS expira de hora em hora por padrão para C SDK](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-authentication), o que pode levar a desconexões regulares.

Para saber mais, consulte [a causa 401003 IoTHubNão-unauthorized](iot-hub-troubleshoot-error-401003-iothubunauthorized.md#cause-1).

### <a name="cause-2"></a>Causa 2

Algumas possibilidades incluem:

- O dispositivo perdeu a conectividade subjacente à rede por mais tempo do que o [mQTT manter-vivo,](iot-hub-mqtt-support.md#default-keep-alive-timeout)resultando num tempo de paragem remoto. A definição de manutenção do MQTT pode ser diferente por dispositivo.

- O dispositivo enviou um reset de nível TCP/IP, `MQTT DISCONNECT`mas não enviou um nível de aplicação . Basicamente, o dispositivo fechou abruptamente a ligação da tomada subjacente. Por vezes, este problema é causado por bugs em versões mais antigas do Azure IoT SDK.

- A aplicação lateral do dispositivo despenhou-se.

### <a name="cause-3"></a>Causa 3

O IoT Hub pode estar a passar por um problema transitório. Consulte a causa de [erro do servidor interno Do IoT Hub](iot-hub-troubleshoot-error-500xxx-internal-errors.md#cause).

## <a name="solutions"></a>Soluções

### <a name="solution-1"></a>Solução 1

Ver [401003 IoTHubSNãosada solução 1](iot-hub-troubleshoot-error-401003-iothubunauthorized.md#solution-1)

### <a name="solution-2"></a>Solução 2

- Certifique-se de que o dispositivo tem uma boa conectividade com o IoT Hub [testando a ligação](tutorial-connectivity.md). Se a rede não for fiável ou intermitente, não recomendamos aumentar o valor de manutenção viva porque pode resultar na deteção (via alertas Do Monitor Azure, por exemplo) demorando mais tempo. 

- Utilize as versões mais recentes dos [SDKs IoT](iot-hub-devguide-sdks.md).

### <a name="solution-3"></a>Solução 3

Consulte [soluções para erros do servidor interno IoT Hub](iot-hub-troubleshoot-error-500xxx-internal-errors.md#solution).

## <a name="next-steps"></a>Passos seguintes

Recomendamos a utilização de SDKs do dispositivo Azure IoT para gerir as ligações de forma fiável. Para saber mais, consulte [Gerir a conectividade e mensagens fiáveis utilizando sDKs do dispositivo Azure IoT Hub](iot-hub-reliability-features-in-sdks.md)