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
ms.openlocfilehash: a5382313c837482f116f498f3a05c36447062b0a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90940945"
---
# <a name="security-module"></a>Módulo de segurança

Este artigo explica como o Defender para IoT utiliza gémeos e módulos do dispositivo.

## <a name="device-twins"></a>Gémeos do dispositivo

Para as soluções IoT construídas em Azure, os gémeos de dispositivo desempenham um papel fundamental tanto na gestão do dispositivo como na automatização de processos.

O Defender for IoT oferece uma integração completa com a sua plataforma de gestão de dispositivos IoT existente, permitindo-lhe gerir o estado de segurança do dispositivo, bem como utilizar as capacidades de controlo de dispositivos existentes. A integração é conseguida utilizando o mecanismo gémeo IoT Hub.

Saiba mais sobre o conceito de [gémeos dispositivos](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-device-twins) no Azure IoT Hub.

## <a name="security-module-twins"></a>Gémeos módulos de segurança

O Defender for IoT mantém um módulo de segurança twin para cada dispositivo no serviço.
O módulo de segurança twin contém todas as informações relevantes para a segurança do dispositivo para cada dispositivo específico na sua solução.
As propriedades de segurança do dispositivo são mantidas num módulo de segurança dedicado twin para uma comunicação mais segura e para permitir atualizações e manutenção que requerem menos recursos.

Consulte [Criar o módulo de segurança twin](quickstart-create-security-twin.md) e [configurar agentes de segurança](how-to-agent-configuration.md) para aprender a criar, personalizar e configurar o gémeo. Consulte [os gémeos módulos understanding](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-module-twins) para saber mais sobre o conceito de gémeos módulos no IoT Hub.

## <a name="see-also"></a>Consulte também

- [Defender para visão geral do IoT](overview.md)
- [Implementar agentes de segurança](how-to-deploy-agent.md)
- [Métodos de autenticação de agentes de segurança](concept-security-agent-authentication-methods.md)
