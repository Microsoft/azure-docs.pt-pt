---
title: Resolução de problemas Erro do Hub Azure IoT 401003 IoTHubUnauthorized
description: Entenda como corrigir o erro 401003 IoTHubUnauthorized
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81759588"
---
# <a name="401003-iothubunauthorized"></a>401003 IoTHubUnauthorized

Este artigo descreve as causas e soluções para **erros 401003 IoTHubUnauthorized.**

## <a name="symptoms"></a>Sintomas

### <a name="symptom-1"></a>Sintoma 1

Nos registos de diagnóstico, vê-se um padrão de dispositivos desligados com **o IoTHubUnauthorized 401003**, seguido de **404104 DeviceConnectionClosedRemotely**, e depois ligar-se com sucesso pouco depois.

### <a name="symptom-2"></a>Sintoma 2

Os pedidos ao IoT Hub falham com uma das seguintes mensagens de erro:

* Cabeçalho de autorização em falta
* IotHub\*' ' não contém o\*dispositivo especificado ' '
* Regra de\*autorização ' '\*não permite o acesso para ' '
* A autenticação falhou para este dispositivo, renovar ficha ou certificado e reconectar
* A impressão digital não corresponde à configuração:\*Impressão digital:\*SHA1Hash=, SHA2Hash= ; Configuração: Impressão PrimaryThumb=\*, Impressão Secundária Do Polegar=\*

## <a name="cause"></a>Causa

### <a name="cause-1"></a>Causa 1

Para o MQTT, alguns SDKs contam com o IoT Hub para emitir a desconexão quando o token SAS expirar para saber quando relançá-lo. Então 

1. O token SAS expira
1. IoT Hub nota a expiração e desliga o dispositivo com **401003 IoTHubUnauthorized**
1. O dispositivo completa a desconexão com **404104 DispositivoConnectionClosedRemotely**
1. O IoT SDK gera um novo token SAS
1. O dispositivo reconecta-se com sucesso com o IoT Hub

### <a name="cause-2"></a>Causa 2

O IoT Hub não conseguiu autenticar o cabeçalho, regra ou chave.

## <a name="solution"></a>Solução

### <a name="solution-1"></a>Solução 1

Não é necessária qualquer ação se utilizar o IoT SDK para a ligação utilizando a cadeia de ligação do dispositivo. IoT SDK regenera o novo símbolo para reconectar-se na expiração do token SAS. 

Se o volume de erros for uma preocupação, mude para o C SDK, que renova o token SAS antes de expirar. Além disso, para amqP o token SAS pode refrescar-se sem desconexão.

### <a name="solution-2"></a>Solução 2

Em geral, a mensagem de erro apresentada deve explicar como corrigir o erro. Se, por alguma razão, não tiver acesso ao detalhe da mensagem de erro, certifique-se de:

- O SAS ou outro sinal de segurança que usas não expirou. 
- A credencial de autorização está bem formada para o protocolo que usa. Para saber mais, consulte o controlo de [acesso ioT Hub](iot-hub-devguide-security.md).
- A regra de autorização utilizada tem a permissão para a operação solicitada.

## <a name="next-steps"></a>Passos seguintes

Para facilitar a autenticação ao IoT Hub, recomendamos a utilização de [SDKs Azure IoT](iot-hub-devguide-sdks.md).