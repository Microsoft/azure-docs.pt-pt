---
title: Módulo de segurança e gémeos de dispositivo
description: Conheça o conceito de gémeos módulos de segurança e como são usados no Azure Security Center para IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: a5c25cba-59a4-488b-abbe-c37ff9b151f9
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/24/2019
ms.author: mlottner
ms.openlocfilehash: d598d291612c6e4f58caf77e1b213b2bc3f42820
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "81311449"
---
# <a name="security-module"></a>Módulo de segurança

Este artigo explica como o Azure Security Center para IoT utiliza gémeos e módulos de dispositivos.

## <a name="device-twins"></a>Gémeos do dispositivo

Para as soluções IoT construídas em Azure, os gémeos de dispositivo desempenham um papel fundamental tanto na gestão do dispositivo como na automatização de processos.

O Azure Security Center for IoT oferece uma integração completa com a sua plataforma de gestão de dispositivos IoT existente, permitindo-lhe gerir o estado de segurança do dispositivo, bem como utilizar as capacidades de controlo de dispositivos existentes. A integração é conseguida utilizando o mecanismo gémeo IoT Hub.

Saiba mais sobre o conceito de [gémeos dispositivos](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-device-twins) no Azure IoT Hub.

## <a name="security-module-twins"></a>Gémeos módulos de segurança

O Azure Security Center for IoT mantém um módulo de segurança twin para cada dispositivo no serviço.
O módulo de segurança twin contém todas as informações relevantes para a segurança do dispositivo para cada dispositivo específico na sua solução.
As propriedades de segurança do dispositivo são mantidas num módulo de segurança dedicado twin para uma comunicação mais segura e para permitir atualizações e manutenção que requerem menos recursos.

Consulte [Criar o módulo de segurança twin](quickstart-create-security-twin.md) e [configurar agentes de segurança](how-to-agent-configuration.md) para aprender a criar, personalizar e configurar o gémeo. Consulte [os gémeos módulos understanding](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-module-twins) para saber mais sobre o conceito de gémeos módulos no IoT Hub.

## <a name="see-also"></a>Ver também

- [Centro de Segurança Azure para visão geral do IoT](overview.md)
- [Implementar agentes de segurança](how-to-deploy-agent.md)
- [Métodos de autenticação de agentes de segurança](concept-security-agent-authentication-methods.md)
