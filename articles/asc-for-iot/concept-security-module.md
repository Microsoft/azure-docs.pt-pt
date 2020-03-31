---
title: Compreenda o Centro de Segurança Azure para os gémeos do módulo de segurança IoT. Microsoft Docs
description: Saiba mais sobre o conceito de gémeos módulos de segurança e como são usados no Azure Security Center para IoT.
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
ms.openlocfilehash: ab3b6e740e644a1ed1495eb776045888be448047
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "68596494"
---
# <a name="security-module"></a>Módulo de segurança


Este artigo explica como o Azure Security Center for IoT utiliza gémeos e módulos de dispositivos. 

## <a name="device-twins"></a>Gémeos dispositivo

Para soluções IoT construídas em Azure, os gémeos dispositivos desempenham um papel fundamental tanto na gestão de dispositivos como na automatização de processos.  

O Azure Security Center for IoT oferece total integração com a sua plataforma de gestão de dispositivos IoT existente, permitindo-lhe gerir o estado de segurança do seu dispositivo, bem como utilizar as capacidades de controlo de dispositivos existentes. A integração é conseguida através da utilização do mecanismo gémeo IoT Hub.  

Saiba mais sobre o conceito de [gémeos dispositivos](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-device-twins) no Hub Azure IoT. 

## <a name="security-module-twins"></a>Gémeos módulo de segurança

O Azure Security Center for IoT mantém um módulo de segurança twin para cada dispositivo no serviço.
O módulo de segurança twin detém todas as informações relevantes para a segurança do dispositivo para cada dispositivo específico na sua solução.
As propriedades de segurança do dispositivo são mantidas num módulo de segurança dedicado twin para uma comunicação mais segura e para permitir atualizações e manutenção que requerem menos recursos.  

Consulte Criar agentes de [segurança twin](quickstart-create-security-twin.md) e [Configure módulo](how-to-agent-configuration.md) de segurança para aprender a criar, personalizar e configurar o gémeo. Consulte os [gémeos do módulo Understanding](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-module-twins) para saber mais sobre o conceito de gémeos módulo sinos no IoT Hub. 
 

## <a name="see-also"></a>Consulte também
- [Centro de Segurança Azure para visão geral do IoT](overview.md)
- [Implementar agentes de segurança](how-to-deploy-agent.md)
- [Métodos de autenticação de agentes de segurança](concept-security-agent-authentication-methods.md)