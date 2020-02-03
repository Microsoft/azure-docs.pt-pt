---
title: Depreciação do TLS 1.0 e 1.1 no IoT Hub e No Serviço de Provisionamento de Dispositivos (DPS)  Microsoft Docs
description: Diretrizes sobre a substituição de TLS 1,0 e 1,1 e codificações com suporte no Hub IoT e no DPS.
author: rezasherafat
ms.author: rezas
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/16/2019
ms.openlocfilehash: 95733f579740f2928cda917eec0023bf00d53076
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76722788"
---
# <a name="deprecation-of-tls-10-and-11-in-iot-hub-and-device-provisioning-service"></a>Depreciação de TLS 1.0 e 1.1 no IoT Hub e Serviço de Provisionamento de Dispositivos

Para fornecer a melhor criptografia, o Hub IoT e o DPS (serviço de provisionamento de dispositivos) estão mudando para o protocolo TLS 1,2 como o mecanismo de criptografia de escolha para dispositivos e serviços de IoT. Como tal, o apoio legado para TLS 1.0 e TLS 1.1, bem como várias cifras legados não recomendadas serão depreciados em 1 de julho de **2020.**


## <a name="impact"></a>Impacto
Com base nas circunstâncias e configurações específicas dos clientes, a substituição de TLS 1,0 e 1,1 e de codificações herdadas não recomendadas pode ser uma alteração impactante para seus dispositivos e serviços de IoT se comunicando com o Hub IoT ou com o DPS. Em alguns casos, os dispositivos e serviços incompatíveis com essas alterações não poderão se conectar ao Hub IoT ou ao DPS após a data de fechamento mencionada.


## <a name="supported-ciphers"></a>Codificações com suporte

Apenas são permitidas as seguintes cifras durante o aperto de mão TLS:

* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
* TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
* TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384


## <a name="customer-feedback"></a>Feedback do cliente

Embora a aplicação da tls 1.2 seja uma escolha de encriptação de melhor classe em toda a indústria e seja ativada como planeado, gostaríamos ainda de ouvir os clientes sobre as suas implementações específicas e dificuldades em adotar tLS 1.2. Para o efeito, pode enviar os seus comentários para [iot_tls1_deprecation@microsoft.com](mailto:iot_tls1_deprecation@microsoft.com).