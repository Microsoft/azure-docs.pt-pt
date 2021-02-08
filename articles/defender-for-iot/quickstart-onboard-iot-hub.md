---
title: A bordo do Defender para solução baseada em agente IoT
description: Aprenda a bordo e ative o serviço de segurança IoT no seu Azure IoT Hub.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: shhazam-ms
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/20/2021
ms.author: shhazam
ms.openlocfilehash: 127e439a7740cb97cbe126071aaaa5245cd85782
ms.sourcegitcommit: 4784fbba18bab59b203734b6e3a4d62d1dadf031
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/08/2021
ms.locfileid: "99809138"
---
# <a name="onboard-to-defender-for-iot-agent-based-solution"></a>A bordo do Defender para solução baseada em agente IoT

Este artigo explica como ativar o serviço Defender para IoT no seu IoT Hub existente. Se não tiver atualmente um Hub IoT, consulte [Criar um Hub IoT utilizando o portal Azure](../iot-hub/iot-hub-create-through-portal.md) para começar.

Você pode gerir a sua segurança IoT através do IoT Hub in Defender para IoT. O portal de gestão localizado no IoT Hub permite-lhe fazer o seguinte: 

- Gerir a segurança do IoT Hub.

- Gestão básica da segurança de um dispositivo IoT sem instalar um agente baseado na telemetria IoT Hub. 

- Gestão avançada para a segurança de um dispositivo IoT baseado no micro-agente.

> [!NOTE]
> Atualmente, o Defender for IoT apenas suporta os hubs IoT de nível padrão.

## <a name="onboard-to-defender-for-iot-in-iot-hub"></a>A bordo do Defender para IoT no IoT Hub

Para todos os novos hubs IoT, o Defender for IoT está definido para **On** por padrão. Pode verificar se o Defender for IoT está ligado a **On** durante o processo de criação do IoT Hub.

Para verificar o toggle está definido para **:**

1. Navegue para o portal do Azure.

1. Selecione **IoT Hub** da lista de serviços Azure.

1. Selecione **Criar**.

    :::image type="content" source="media/quickstart-onboard-iot-hub/create-iot-hub.png" alt-text="Selecione o botão de criar a partir da barra de ferramentas superior." lightbox="media/quickstart-onboard-iot-hub/create-iot-hub-expanded.png":::

1. Selecione o separador **Gestão** e verifique se o defender para o **toggle IoT** está definido para **On**.

    :::image type="content" source="media/quickstart-onboard-iot-hub/management-tab.png" alt-text="Certifique-se de que o Defender para o toggle IoT está ligado.":::

## <a name="onboard-defender-for-iot-to-an-existing-iot-hub"></a>Defender a bordo para IoT para um hub IoT existente

Pode monitorizar a gestão da identidade do seu dispositivo, dispositivo para nuvem e nuvem para padrões de comunicação do dispositivo, fazer o seguinte para iniciar o serviço: 

1. Navegue até ao Hub IoT. 

1. Selecione o menu **de visão geral de segurança.**   

1. Clique em Secure your IoT solution and complete the onboarding form. 


## <a name="next-steps"></a>Passos seguintes

Avance para o próximo artigo para configurar a sua solução...

> [!div class="nextstepaction"]
> [Criar um módulo de micro-agente Defender Iot twin (Preview)](quickstart-create-micro-agent-module-twin.md)
