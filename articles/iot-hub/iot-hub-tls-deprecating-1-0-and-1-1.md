---
title: Depreciando TLS 1.0 e 1.1 no IoT Hub / Microsoft Docs
description: Orientações relativas à depreciação dos TLS 1.0 e 1.1 e cifras apoiadas no Hub IoT.
author: jlian
ms.author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: 5c717a02c2008436617d16f08625a1cecc204340
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "83849523"
---
# <a name="deprecation-of-tls-10-and-11-in-iot-hub"></a>Depreciação de TLS 1.0 e 1.1 no IoT Hub

Para fornecer encriptação de melhor classe, o IoT Hub está a mover-se para Transport Layer Security (TLS) 1.2 como o mecanismo de encriptação de escolha para dispositivos e serviços IoT. 

## <a name="timeline"></a>Linha cronológica

O IoT Hub continuará a apoiar o TLS 1.0/1.1 até novo aviso. No entanto, recomendamos que todos os clientes migram para TLS 1.2 o mais rapidamente possível.

## <a name="deprecating-tls-11-ciphers"></a>Deprecisante TLS 1.1 cifras

* `TLS_ECDHE_RSA_WITH_AES_256_CBC_SH`
* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA`
* `TLS_RSA_WITH_AES_256_CBC_SHA`
* `TLS_RSA_WITH_AES_128_CBC_SHA`
* `TLS_RSA_WITH_3DES_EDE_CBC_SHA`

## <a name="deprecating-tls-10-ciphers"></a>Deprecisante TLS 1.0 cifras

* `TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA`
* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA`
* `TLS_RSA_WITH_AES_256_CBC_SHA`
* `TLS_RSA_WITH_AES_128_CBC_SHA`
* `TLS_RSA_WITH_3DES_EDE_CBC_SHA`

## <a name="tls-12-ciphers"></a>TLS 1.2 cifras

Ver [cifras recomendadas IoT Hub TLS 1.2](iot-hub-tls-support.md#recommended-ciphers).
 
## <a name="customer-feedback"></a>Feedback do cliente

Embora a aplicação do TLS 1.2 seja uma escolha de encriptação de primeira classe em toda a indústria e seja ativada como planeado, ainda gostaríamos de ouvir os clientes sobre as suas implementações específicas e dificuldades na adoção de TLS 1.2. Para o efeito, pode enviar os seus comentários para [iot_tls1_deprecation@microsoft.com](mailto:iot_tls1_deprecation@microsoft.com) .
