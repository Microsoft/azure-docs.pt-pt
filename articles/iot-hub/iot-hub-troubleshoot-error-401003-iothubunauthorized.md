---
title: Resolução de problemas Erro do Hub Azure IoT 401003 IoTHubUnauthorized
description: Entenda como corrigir o erro 401003 IoTHubUnautized
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: f46d41c8287d03cbe9582ed560244cbd85cdeeaa
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "81759588"
---
# <a name="401003-iothubunauthorized"></a>401003 IoTHubUnauthorized

Este artigo descreve as causas e soluções para **erros ioTHubUnautizados 401003.**

## <a name="symptoms"></a>Sintomas

### <a name="symptom-1"></a>Sintoma 1

Nos registos de diagnóstico, vê-se um padrão de dispositivos desligados com **401003 IoTHubUnautized**, seguido de **404104 DeviceConnectionClosedRemotely**, e, em seguida, conectando-se com sucesso pouco depois.

### <a name="symptom-2"></a>Sintoma 2

Os pedidos ao IoT Hub falham com uma das seguintes mensagens de erro:

* Cabeçalho de autorização em falta
* IotHub \* ' não contém o dispositivo especificado \* ' '
* A regra de autorização \* ' ' não permite o acesso para \* '
* A autenticação falhou para este dispositivo, renovar o token ou o certificado e reconectar
* A impressão digital não corresponde à configuração: Impressão digital: SHA1Hash= \* , SHA2Hash= \* Configuração: PrimaryThumbprint= \* , SecondaryThumbprint=\*

## <a name="cause"></a>Causa

### <a name="cause-1"></a>Causa 1

Para o MQTT, alguns SDKs confiam no IoT Hub para emitir a desconexão quando o token SAS expirar para saber quando o refrescar. Desta forma, 

1. O símbolo SAS expira
1. IoT Hub nota a expiração e desliga o dispositivo com **401003 IoTHubUnautized**
1. O dispositivo completa a desconexão com **404104 DeviceConnectionClosedRemotely**
1. O IoT SDK gera um novo token SAS
1. O dispositivo reconecta-se com o IoT Hub com sucesso

### <a name="cause-2"></a>Causa 2

IoT Hub não podia autenticar o cabeçalho, regra ou chave do auth.

## <a name="solution"></a>Solução

### <a name="solution-1"></a>Solução 1

Não é necessária qualquer ação se utilizar o IoT SDK para ligação utilizando a cadeia de ligação do dispositivo. IoT SDK regenera o novo token para reconectar-se na expiração do token SAS. 

Se o volume de erros for uma preocupação, mude para o C SDK, que renova o token SAS antes de expirar. Além disso, para AMQP o token SAS pode refrescar-se sem desconexão.

### <a name="solution-2"></a>Solução 2

Em geral, a mensagem de erro apresentada deve explicar como corrigir o erro. Se por alguma razão não tiver acesso ao detalhe da mensagem de erro, certifique-se de que:

- O SAS ou outro sinal de segurança que usa não expirou. 
- A credencial de autorização está bem formada para o protocolo que utiliza. Para saber mais, consulte [o controlo de acesso do IoT Hub.](iot-hub-devguide-security.md)
- A regra de autorização utilizada tem a autorização para a operação solicitada.

## <a name="next-steps"></a>Passos seguintes

Para facilitar a autenticação ao IoT Hub, recomendamos a utilização de [SDKs Azure IoT](iot-hub-devguide-sdks.md).