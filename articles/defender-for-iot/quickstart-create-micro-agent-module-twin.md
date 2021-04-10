---
title: Criar um módulo de micro agente Defender IoT twin (Pré-visualização)
description: Saiba como criar gémeos individuais do módulo DefenderIotMicroAgent para novos dispositivos.
ms.date: 1/20/2021
ms.topic: quickstart
ms.openlocfilehash: 5036eefbd77a22d492f6ce7d3c7d15f50a081490
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104781063"
---
# <a name="create-a-defender-iot-micro-agent-module-twin-preview"></a>Criar um módulo de micro agente Defender IoT twin (Pré-visualização)

Pode criar gémeos módulos **DefenderIotMicroAgent** para novos dispositivos. Também pode criar módulos gémeos para todos os dispositivos num Hub IoT. 

## <a name="device-twins"></a>Gémeos do dispositivo 

Para as soluções IoT construídas em Azure, os gémeos de dispositivo desempenham um papel fundamental tanto na gestão do dispositivo como na automatização de processos. 

O Defender for IoT tem a capacidade de se integrar plenamente com a sua plataforma de gestão de dispositivos IoT existente. A integração total permite-lhe gerir o estado de segurança do seu dispositivo e permite-lhe utilizar todas as capacidades de controlo de dispositivos existentes. A integração é conseguida utilizando o mecanismo gémeo IoT Hub. 

Saiba mais sobre o conceito de [gémeos dispositivos](../iot-hub/iot-hub-devguide-device-twins.md)   no Azure IoT Hub. 

## <a name="defender-iot-micro-agent-twins"></a>Gémeos Defender-IoT-micro-agente 

O Defender for IoT utiliza um duplo Defender-IoT-micro-agente para cada dispositivo. O Defender-IoT-micro-agente twin contém todas as informações que são relevantes para a segurança do dispositivo, para cada dispositivo específico na sua solução. As propriedades de segurança do dispositivo são configuradas através de um gémeo defender-ioT-micro-agente dedicado para uma comunicação mais segura, para permitir atualizações e manutenção que requer menos recursos. 

## <a name="understanding-defenderiotmicroagent-module-twins"></a>Compreender os gémeos módulos DefenderIotMicroAgent 

Os gémeos do dispositivo desempenham um papel fundamental tanto na gestão do dispositivo como na automatização de processos, para soluções IoT que são integradas no Azure.

O Defender for IoT oferece a capacidade de integrar totalmente a sua plataforma de gestão de dispositivos IoT existente, permitindo-lhe gerir o estado de segurança do dispositivo e utilizar as capacidades de controlo do dispositivo existentes. Pode integrar o seu Defender para IoT utilizando o mecanismo gémeo IoT Hub.  

Para saber mais sobre o conceito geral de gémeos módulos no Azure IoT Hub, consulte [os gémeos módulos IoT Hub.](../iot-hub/iot-hub-devguide-module-twins.md)

O Defender for IoT utiliza o mecanismo gémeo do módulo e mantém um duplo Defender-IoT-micro-agente nomeado `DefenderIotMicroAgent` para cada um dos seus dispositivos. 

Para tirar o máximo partido de todas as funcionalidades do Defender para ioT, é necessário criar, configurar e utilizar os gémeos Defender-IoT-micro-agente para todos os dispositivos do serviço. 

## <a name="create-defenderiotmicroagent-module-twin"></a>Criar o módulo DefenderIotMicroAgent gémeo 

Os gémeos do módulo **DefenderIotMicroAgent** podem ser criados editando manualmente cada módulo twin para incluir configurações específicas para cada dispositivo. 

Para criar manualmente um novo módulo **DefenderIotMicroAgent** twin para um dispositivo: 

1. No seu Hub IoT, localize e selecione o dispositivo para criar um gémeo Defender-IoT-micro-agente. 

1. Selecione **Adicionar identidade do módulo**. 

1. No campo **Nome de Identidade** do   Módulo, e insira  `DefenderIotMicroAgent` . 

1.  **Selecione Guardar**. 

## <a name="verify-the-creation-of-a-module-twin"></a>Verifique a criação de um módulo gémeo 

Para verificar se existe um gémeo Defender-IoT-micro-agente para um dispositivo específico: 

1. No seu Azure IoT Hub, selecione **dispositivos IoT**   do menu **Explorers.**   

1. Introduza o ID do dispositivo ou selecione uma opção no campo do **dispositivo de consulta** e selecione **dispositivos de consulta**.  

    :::image type="content" source="media/quickstart-create-micro-agent-module-twin/iot-devices.png" alt-text="Selecione dispositivos de consulta para obter uma lista dos seus dispositivos.":::

1. Selecione o dispositivo e abra a página **de detalhes do Dispositivo.** 

1. Selecione o menu **de identidades do Módulo** e   confirme a existência do módulo **DefenderIotMicroAgent** na lista de identidades do módulo associadas ao dispositivo.  

    :::image type="content" source="media/quickstart-create-micro-agent-module-twin/device-details-module.png" alt-text="Selecione as identidades do módulo a partir do separador.":::

## <a name="next-steps"></a>Passos seguintes 

Avance para o próximo artigo para saber como [investigar recomendações de segurança](quickstart-investigate-security-recommendations.md).
