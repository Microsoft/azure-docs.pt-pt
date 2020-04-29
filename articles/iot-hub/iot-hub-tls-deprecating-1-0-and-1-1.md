---
title: Deprecating TLS 1.0 e 1.1 no IoT Hub Microsoft Docs
description: Orientações relativas à depreciação de TLS 1.0 e 1.1 e cifras apoiadas no Hub IoT.
author: jlian
ms.author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: a887dd4df44ba58b0e6646ffb1c10eb21edf3e69
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81381304"
---
# <a name="deprecation-of-tls-10-and-11-in-iot-hub"></a>Depreciação de TLS 1.0 e 1.1 no Hub IoT

Para fornecer encriptação de melhor classe, o IoT Hub está a mover-se para transport Layer Security (TLS) 1.2 como o mecanismo de encriptação de eleição para dispositivos e serviços IoT. 

## <a name="timeline"></a>Linha cronológica

O IoT Hub continuará a apoiar o TLS 1.0/1.1 até novo aviso. No entanto, recomendamos que todos os clientes migram para TLS 1.2 o mais rápido possível.

## <a name="supported-ciphers"></a>Cifras apoiadas

A linha temporal para a disponibilidade de várias cifras utilizadas no aperto de mão tLS é a seguinte:

* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256 (atualmente apoiado)
* TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384 (será apoiado no segundo semestre de 2020)
* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 (será apoiado no segundo semestre de 2020)
* TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384 (será apoiado no segundo semestre de 2020)

## <a name="customer-feedback"></a>Feedback do cliente

Embora a aplicação da tls 1.2 seja uma escolha de encriptação de melhor classe em toda a indústria e seja ativada como planeado, gostaríamos ainda de ouvir os clientes sobre as suas implementações específicas e dificuldades em adotar tLS 1.2. Para o efeito, pode enviar [iot_tls1_deprecation@microsoft.com](mailto:iot_tls1_deprecation@microsoft.com)os seus comentários para .
