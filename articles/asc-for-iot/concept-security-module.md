---
title: Entender a central de segurança do Azure para o módulo de segurança de IoT gêmeos | Microsoft Docs
description: Saiba mais sobre o conceito de gêmeos do módulo de segurança e como eles são usados na central de segurança do Azure para IoT.
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
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68596494"
---
# <a name="security-module"></a>Módulo de segurança


Este artigo explica como a central de segurança do Azure para IoT usa módulos e gêmeos de dispositivo. 

## <a name="device-twins"></a>Dispositivo gêmeos

Para soluções de IoT criadas no Azure, o dispositivo gêmeos desempenha um papel fundamental no gerenciamento de dispositivos e na automação de processos.  

A central de segurança do Azure para IoT oferece integração total com sua plataforma de gerenciamento de dispositivo IoT existente, permitindo que você gerencie o status de segurança do dispositivo e use os recursos de controle de dispositivo existentes. A integração é obtida por meio do uso do mecanismo de entrelaçamento do Hub IoT.  

Saiba mais sobre o conceito de [dispositivo gêmeos](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-device-twins) no Hub IOT do Azure. 

## <a name="security-module-twins"></a>Gêmeos do módulo de segurança

A central de segurança do Azure para IoT mantém um módulo de segurança para cada dispositivo no serviço.
O módulo de segurança de tudo contém todas as informações relevantes à segurança do dispositivo para cada dispositivo específico em sua solução.
As propriedades de segurança do dispositivo são mantidas em um módulo de segurança dedicado para comunicação mais segura e para habilitar atualizações e manutenção que exigem menos recursos.  

Confira [Criar módulo de segurança](quickstart-create-security-twin.md) e [Configurar agentes de segurança](how-to-agent-configuration.md) para saber como criar, personalizar e configurar o entrelaçamento. Consulte [entendendo o módulo gêmeos](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-module-twins) para saber mais sobre o conceito de módulo gêmeos no Hub IOT. 
 

## <a name="see-also"></a>Consulte também
- [Visão geral da central de segurança do Azure para IoT](overview.md)
- [Implantar agentes de segurança](how-to-deploy-agent.md)
- [Métodos de autenticação do agente de segurança](concept-security-agent-authentication-methods.md)