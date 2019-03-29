---
title: Compreender o ASC duplos de módulo de segurança de IoT pré-visualização | Documentos da Microsoft
description: Saiba mais sobre o conceito de duplos de módulo de segurança e como eles são usados no ASC para IoT.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: a5c25cba-59a4-488b-abbe-c37ff9b151f9
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2019
ms.author: mlottner
ms.openlocfilehash: d766b17c9d49792d2e8192a952e8e6e559a8acd3
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2019
ms.locfileid: "58579381"
---
# <a name="security-module"></a>Módulo de segurança

> [!IMPORTANT]
> ASC para IoT está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Este artigo explica como o ASC para IoT utiliza dispositivos duplos e módulos. 

## <a name="device-twins"></a>Dispositivos duplos

Para soluções de IoT criadas no Azure, os dispositivos duplos desempenham um papel fundamental na gestão de dispositivos e automatização de processos.  

ASC para IoT oferece integração completa com a sua plataforma gestão de dispositivos da IoT existente, permitindo-lhe gerir o seu estado de segurança do dispositivo, bem como a marca utilizar capacidades de controle de dispositivo existentes. Integração é conseguida fazendo uso do IoT Hub duplo mecanismo.  

Saiba mais sobre o conceito de [dispositivos duplos](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-device-twins) no IoT Hub do Azure. 

## <a name="security-module-twins"></a>Duplos de módulo de segurança

ASC para IoT mantém um duplo do módulo de segurança para cada dispositivo no serviço.
O duplo do módulo de segurança armazena todas as informações relevantes para a segurança do dispositivo para cada dispositivo específico na sua solução.
Propriedades de segurança do dispositivo são mantidas num duplo do módulo de segurança dedicado para comunicação mais segura e para ativar as atualizações e manutenção que necessitam de menos recursos.  

Ver [duplos de módulo de segurança de Create](quickstart-create-security-twin.md) e [configurar os agentes de segurança](how-to-agent-configuration.md) para saber como criar, personalizar e configurar o duplo. Ver [duplos de módulo de compreensão](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-module-twins) para saber mais sobre o conceito de duplos de módulo do IoT Hub. 
 

## <a name="see-also"></a>Consulte também
- [ASC para pré-visualização do IoT](overview.md)
- [Implementar agentes de segurança](how-to-deploy-agent.md)
- [Métodos de autenticação do agente de segurança](concept-security-agent-authentication-methods.md)