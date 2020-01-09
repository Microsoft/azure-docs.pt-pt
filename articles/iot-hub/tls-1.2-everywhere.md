---
title: TLS 1,0 e 1,1 de substituição no Hub IoT e no DPS (serviço de provisionamento de dispositivos) | Microsoft Docs
description: Diretrizes sobre a substituição de TLS 1,0 e 1,1 e codificações com suporte no Hub IoT e no DPS.
author: rezas
ms.author: reza
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/16/2019
ms.openlocfilehash: f1ee3156e5639ae47d5bb323cf992586580668f5
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75485913"
---
# <a name="tls-10-and-11-deprecation-in-iot-hub-and-device-provisioning-service"></a>TLS 1,0 e 1,1 de substituição no Hub IoT e no serviço de provisionamento de dispositivos

Para fornecer a melhor criptografia, o Hub IoT e o DPS (serviço de provisionamento de dispositivos) estão mudando para o protocolo TLS 1,2 como o mecanismo de criptografia de escolha para dispositivos e serviços de IoT. Assim, o suporte herdado para TLS 1,0 e TLS 1,1, bem como várias codificações herdadas não recomendadas, será preterido em **1º de julho de 2020**.


## <a name="impact"></a>Impacto
Com base nas circunstâncias e configurações específicas dos clientes, a substituição de TLS 1,0 e 1,1 e de codificações herdadas não recomendadas pode ser uma alteração impactante para seus dispositivos e serviços de IoT se comunicando com o Hub IoT ou com o DPS. Em alguns casos, os dispositivos e serviços incompatíveis com essas alterações não poderão se conectar ao Hub IoT ou ao DPS após a data de fechamento mencionada.


## <a name="supported-ciphers"></a>Codificações com suporte

Somente as seguintes codificações serão permitidas durante o handshake de TLS:

* TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
* TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
* TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
* TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
* TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
* TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384


## <a name="customer-feedback"></a>Comentários do cliente

Embora a imposição do TLS 1.2 seja a melhor opção de encriptação ao nível do setor e seja ativada como planeada, gostaríamos de saber a opinião dos clientes relativamente às implementações específicas e dificuldades ao adotarem o TLS 1.2. Para essa finalidade, você pode enviar seus comentários para [iot_tls1_deprecation@microsoft.com](mailto:iot_tls1_deprecation@microsoft.com).