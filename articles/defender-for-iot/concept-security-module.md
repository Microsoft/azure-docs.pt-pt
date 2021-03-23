---
title: Defender-IoT-micro-agente e gémeos dispositivo
description: Conheça o conceito de gémeos Defender-IoT-micro-agent e como são usados no Defender para IoT.
ms.topic: conceptual
ms.date: 07/24/2019
ms.openlocfilehash: 1ed6faf03d168ed7a2a2f07733cb7e238d234915
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2021
ms.locfileid: "104779176"
---
# <a name="defender-iot-micro-agent"></a>Defender-IoT-micro-agente

Este artigo explica como o Defender para IoT utiliza gémeos e módulos do dispositivo.

## <a name="device-twins"></a>Gémeos do dispositivo

Para as soluções IoT construídas em Azure, os gémeos de dispositivo desempenham um papel fundamental tanto na gestão do dispositivo como na automatização de processos.

O Defender for IoT oferece uma integração completa com a sua plataforma de gestão de dispositivos IoT existente, permitindo-lhe gerir o estado de segurança do dispositivo, bem como utilizar as capacidades de controlo de dispositivos existentes. A integração é conseguida utilizando o mecanismo gémeo IoT Hub.

Saiba mais sobre o conceito de [gémeos dispositivos](../iot-hub/iot-hub-devguide-device-twins.md) no Azure IoT Hub.

## <a name="defender-iot-micro-agent-twins"></a>Gémeos Defender-IoT-micro-agente

O Defender for IoT mantém um duplo Defender-IoT-micro-agente para cada dispositivo no serviço.
O Defender-IoT-micro-agente twin contém todas as informações relevantes para a segurança do dispositivo para cada dispositivo específico na sua solução.
As propriedades de segurança do dispositivo são mantidas num gémeo defensor-ioT-micro-agente para uma comunicação mais segura e para permitir atualizações e manutenção que requerem menos recursos.

Consulte [Create Defender-IoT-micro-agent twin](quickstart-create-security-twin.md) e [Configure agentes de segurança](how-to-agent-configuration.md) para aprender a criar, personalizar e configurar o gémeo. Consulte [os gémeos módulos understanding](../iot-hub/iot-hub-devguide-module-twins.md) para saber mais sobre o conceito de gémeos módulos no IoT Hub.

## <a name="see-also"></a>Ver também

- [Defender para visão geral do IoT](overview.md)
- [Implementar agentes de segurança](how-to-deploy-agent.md)
- [Métodos de autenticação de agentes de segurança](concept-security-agent-authentication-methods.md)