---
title: Módulo de segurança e gémeos de dispositivo
description: Conheça o conceito de gémeos módulos de segurança e como são usados no Defender para IoT.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/24/2019
ms.author: mlottner
ms.openlocfilehash: b33c456d47426a3721e8582f24ffd603db0429c9
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96340038"
---
# <a name="security-module"></a>Módulo de segurança

Este artigo explica como o Defender para IoT utiliza gémeos e módulos do dispositivo.

## <a name="device-twins"></a>Gémeos do dispositivo

Para as soluções IoT construídas em Azure, os gémeos de dispositivo desempenham um papel fundamental tanto na gestão do dispositivo como na automatização de processos.

O Defender for IoT oferece uma integração completa com a sua plataforma de gestão de dispositivos IoT existente, permitindo-lhe gerir o estado de segurança do dispositivo, bem como utilizar as capacidades de controlo de dispositivos existentes. A integração é conseguida utilizando o mecanismo gémeo IoT Hub.

Saiba mais sobre o conceito de [gémeos dispositivos](../iot-hub/iot-hub-devguide-device-twins.md) no Azure IoT Hub.

## <a name="security-module-twins"></a>Gémeos módulos de segurança

O Defender for IoT mantém um módulo de segurança twin para cada dispositivo no serviço.
O módulo de segurança twin contém todas as informações relevantes para a segurança do dispositivo para cada dispositivo específico na sua solução.
As propriedades de segurança do dispositivo são mantidas num módulo de segurança dedicado twin para uma comunicação mais segura e para permitir atualizações e manutenção que requerem menos recursos.

Consulte [Criar o módulo de segurança twin](quickstart-create-security-twin.md) e [configurar agentes de segurança](how-to-agent-configuration.md) para aprender a criar, personalizar e configurar o gémeo. Consulte [os gémeos módulos understanding](../iot-hub/iot-hub-devguide-module-twins.md) para saber mais sobre o conceito de gémeos módulos no IoT Hub.

## <a name="see-also"></a>Ver também

- [Defender para visão geral do IoT](overview.md)
- [Implementar agentes de segurança](how-to-deploy-agent.md)
- [Métodos de autenticação de agentes de segurança](concept-security-agent-authentication-methods.md)