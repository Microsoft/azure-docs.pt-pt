---
title: Depreciação do TLS 1.0 e 1.1 no IoT Hub e No Serviço de Provisionamento de Dispositivos (DPS)  Microsoft Docs
description: Orientações relativas à depreciação de TLS 1.0 e 1.1 e cifras apoiadas em Hub IoT e DPS.
author: rezasherafat
ms.author: rezas
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/16/2019
ms.openlocfilehash: 8e2f8fd7f99c359949b02959cc442f2f3d3ebfff
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2020
ms.locfileid: "77912159"
---
# <a name="deprecation-of-tls-10-and-11-in-iot-hub-and-device-provisioning-service"></a>Depreciação de TLS 1.0 e 1.1 no IoT Hub e Serviço de Provisionamento de Dispositivos

Para fornecer encriptação de melhor classe, o IoT Hub e o Device Provisioning Service (DPS) estão a mover-se para transport layer security (TLS) 1.2 como o mecanismo de encriptação de eleição para dispositivos e serviços IoT. Como tal, o apoio legado para TLS 1.0 e TLS 1.1, bem como várias cifras legados não recomendadas serão depreciados em 1 de julho de **2020.**


## <a name="impact"></a>Impacto
Com base nas circunstâncias e configurações específicas dos clientes, a depreciação de TLS 1.0 e 1.1 e cifras legados não recomendadas podem ser uma mudança impactante para os seus dispositivos e serviços IoT que comunicam com o IoT Hub ou DPS. Em alguns casos, dispositivos e serviços incompatíveis com estas alterações não poderão ligar-se ao IoT Hub ou DPS após a data de corte acima referida.


## <a name="supported-ciphers"></a>Cifras apoiadas

A linha temporal para a disponibilidade de várias cifras utilizadas no aperto de mão tLS é a seguinte:

* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256 (atualmente apoiado)
* TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384 (será apoiado no segundo semestre de 2020)
* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 (será apoiado no segundo semestre de 2020)
* TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384 (será apoiado no segundo semestre de 2020)


## <a name="customer-feedback"></a>Feedback do cliente

Embora a aplicação da tls 1.2 seja uma escolha de encriptação de melhor classe em toda a indústria e seja ativada como planeado, gostaríamos ainda de ouvir os clientes sobre as suas implementações específicas e dificuldades em adotar tLS 1.2. Para o efeito, pode enviar os seus comentários para [iot_tls1_deprecation@microsoft.com](mailto:iot_tls1_deprecation@microsoft.com).
