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
ms.openlocfilehash: af057750e81086bf691b87057da97af3de19cd3b
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/29/2020
ms.locfileid: "92909646"
---
# <a name="401003-iothubunauthorized"></a>401003 IoTHubUnauthorized

Este artigo descreve as causas e soluções para **erros ioTHubUnautizados 401003.**

## <a name="symptoms"></a>Sintomas

### <a name="symptom-1"></a>Sintoma 1

Nos registos, vê-se um padrão de dispositivos desligados com **401003 IoTHubUnautized** , seguido de **404104 DeviceConnectionClosedRemotely** , e, em seguida, conectando-se com sucesso pouco depois.

### <a name="symptom-2"></a>Sintoma 2

Os pedidos ao IoT Hub falham com uma das seguintes mensagens de erro:

* Cabeçalho de autorização em falta
* IotHub \* ' não contém o dispositivo especificado \* ' '
* A regra de autorização \* ' ' não permite o acesso para \* '
* A autenticação falhou para este dispositivo, renovar o token ou o certificado e reconectar
* A impressão digital não corresponde à configuração: Impressão digital: SHA1Hash= \* , SHA2Hash= \* Configuração: PrimaryThumbprint= \* , SecondaryThumbprint=\*

## <a name="cause"></a>Causa

### <a name="cause-1"></a>Motivo 1

Para o MQTT, alguns SDKs confiam no IoT Hub para emitir a desconexão quando o token SAS expirar para saber quando o refrescar. Desta forma, 

1. O símbolo SAS expira
1. IoT Hub nota a expiração e desliga o dispositivo com **401003 IoTHubUnautized**
1. O dispositivo completa a desconexão com **404104 DeviceConnectionClosedRemotely**
1. O IoT SDK gera um novo token SAS
1. O dispositivo reconecta-se com o IoT Hub com sucesso

### <a name="cause-2"></a>Motivo 2

IoT Hub não podia autenticar o cabeçalho, regra ou chave do auth. Isto pode ser devido a qualquer uma das razões citadas nos sintomas.

## <a name="solution"></a>Solução

### <a name="solution-1"></a>Solução 1

Não é necessária qualquer ação se utilizar o IoT SDK para ligação utilizando a cadeia de ligação do dispositivo. IoT SDK regenera o novo token para reconectar-se na expiração do token SAS. 

Se o volume de erros for uma preocupação, mude para o C SDK, que renova o token SAS antes de expirar. Além disso, para AMQP o token SAS pode refrescar-se sem desconexão.

### <a name="solution-2"></a>Solução 2

Em geral, a mensagem de erro apresentada deve explicar como corrigir o erro. Se por alguma razão não tiver acesso ao detalhe da mensagem de erro, certifique-se de que:

- O SAS ou outro sinal de segurança que usa não expirou.
- Para a autenticação do certificado X.509, o certificado do dispositivo ou o certificado de AC associado ao dispositivo não estão caducados. Para saber como registar os certificados X.509 CA com ioT Hub, consulte [configurar a segurança X.509 no seu hub Azure IoT](iot-hub-security-x509-get-started.md).
- Para a autenticação de impressão digital do certificado X.509, a impressão digital do certificado do dispositivo está registada no IoT Hub.
- A credencial de autorização está bem formada para o protocolo que utiliza. Para saber mais, consulte [o Control access to IoT Hub](iot-hub-devguide-security.md).
- A regra de autorização utilizada tem a autorização para a operação solicitada.

## <a name="next-steps"></a>Passos seguintes

- Para facilitar a autenticação ao IoT Hub, recomendamos a utilização de [SDKs Azure IoT](iot-hub-devguide-sdks.md).
- Para obter detalhes sobre a autenticação com o IoT Hub, consulte [o Control Access to IoT Hub](iot-hub-devguide-security.md).
