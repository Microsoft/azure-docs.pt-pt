---
title: Depreciação do TLS 1.0 e 1.1 no IoT Hub e No Serviço de Provisionamento de Dispositivos (DPS)  Microsoft Docs
description: Orientações relativas à depreciação de TLS 1.0 e 1.1 e cifras apoiadas em Hub IoT e DPS.
author: rezasherafat
ms.author: rezas
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/16/2019
ms.openlocfilehash: d61ca8fe7c6f5e7cc400714d7c31a0a7e50b8a88
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2020
ms.locfileid: "78402785"
---
# <a name="deprecation-of-tls-10-and-11-in-iot-hub-and-device-provisioning-service"></a>Depreciação de TLS 1.0 e 1.1 no IoT Hub e Serviço de Provisionamento de Dispositivos

Para fornecer encriptação de melhor classe, o IoT Hub e o Device Provisioning Service (DPS) estão a mover-se para transport layer security (TLS) 1.2 como o mecanismo de encriptação de eleição para dispositivos e serviços IoT. 

## <a name="supported-ciphers"></a>Cifras apoiadas

A linha temporal para a disponibilidade de várias cifras utilizadas no aperto de mão tLS é a seguinte:

* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256 (atualmente apoiado)
* TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384 (será apoiado no segundo semestre de 2020)
* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 (será apoiado no segundo semestre de 2020)
* TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384 (será apoiado no segundo semestre de 2020)


## <a name="customer-feedback"></a>Feedback do cliente

Embora a aplicação da tls 1.2 seja uma escolha de encriptação de melhor classe em toda a indústria e seja ativada como planeado, gostaríamos ainda de ouvir os clientes sobre as suas implementações específicas e dificuldades em adotar tLS 1.2. Para o efeito, pode enviar os seus comentários para [iot_tls1_deprecation@microsoft.com](mailto:iot_tls1_deprecation@microsoft.com).
