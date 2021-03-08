---
title: 'Quickstart: A bordo defender para IoT para uma solução baseada em agente'
description: Neste arranque rápido, você aprenderá a bordo e ativará o serviço de segurança Defender para IoT no seu Azure IoT Hub.
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
ms.openlocfilehash: d30a03aa7b7715a8792e7b70a0571270c6ad7b37
ms.sourcegitcommit: f6193c2c6ce3b4db379c3f474fdbb40c6585553b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/08/2021
ms.locfileid: "102449684"
---
# <a name="quickstart-onboard-defender-for-iot-to-an-agent-based-solution"></a>Quickstart: A bordo defender para IoT para uma solução baseada em agente

Este artigo explica como ativar o serviço Defender para IoT no seu IoT Hub existente. Se não tiver atualmente um Hub IoT, consulte [Criar um Hub IoT utilizando o portal Azure](../iot-hub/iot-hub-create-through-portal.md) para começar.

Você pode gerir a sua segurança IoT através do IoT Hub in Defender para IoT. O portal de gestão localizado no IoT Hub permite-lhe fazer o seguinte: 

- Gerir a segurança do IoT Hub.

- Gestão básica da segurança de um dispositivo IoT sem instalar um agente baseado na telemetria IoT Hub. 

- Gestão avançada para a segurança de um dispositivo IoT baseado no micro-agente.

> [!NOTE]
> Atualmente, o Defender for IoT apenas suporta os hubs IoT de nível padrão.

## <a name="prerequisites"></a>Pré-requisitos

Nenhum

## <a name="onboard-defender-for-iot-to-an-iot-hub"></a>Defender a bordo para IoT para um hub IoT

Para todos os novos hubs IoT, o Defender for IoT está definido para **On** por padrão. Pode verificar se o Defender for IoT está ligado a **On** durante o processo de criação do IoT Hub.

Para verificar o toggle está definido para **:**

1. Navegue para o portal do Azure.

1. Selecione **IoT Hub** da lista de serviços Azure.

1. Selecione **Criar**.

    :::image type="content" source="media/quickstart-onboard-iot-hub/create-iot-hub.png" alt-text="Selecione o botão de criar a partir da barra de ferramentas superior." lightbox="media/quickstart-onboard-iot-hub/create-iot-hub-expanded.png":::

1. Selecione o separador **Gestão** e verifique se o defender para o **toggle IoT** está definido para **On**.

    :::image type="content" source="media/quickstart-onboard-iot-hub/management-tab.png" alt-text="Certifique-se de que o Defender para o toggle IoT está ligado.":::

## <a name="onboard-defender-for-iot-to-an-existing-iot-hub"></a>Defender a bordo para IoT para um hub IoT existente

Pode embarcar o Defender for IoT para um Hub IoT existente, onde pode monitorizar a gestão da identidade do dispositivo, dispositivo para nuvem e nuvem para padrões de comunicação do dispositivo.

Para embarcar Defender para IoT para um hub IoT existente:

1. Navegue até ao Hub IoT. 

1. Selecione o Hub IoT para estar a bordo.

1. Selecione qualquer opção na secção **Segurança.**

1. Clique **em Secure your IoT solution** and complete the   onboarding form. 

    :::image type="content" source="media/quickstart-onboard-iot-hub/secure-your-iot-solution.png" alt-text="Selecione o botão de solução IoT para fixar a sua solução.":::

O botão **Secure a sua solução IoT** só aparecerá se o Hub IoT ainda não tiver sido a bordo, ou se durante o embarque deixou o Defender para o IoT a **alternar** desligado .

:::image type="content" source="media/quickstart-onboard-iot-hub/toggle-is-off.png" alt-text="Se o seu toggle foi programado para fora durante o embarque.":::

## <a name="next-steps"></a>Passos seguintes

Avance para o próximo artigo para configurar a sua solução...

> [!div class="nextstepaction"]
> [Criar um módulo de micro-agente Defender Iot twin (Preview)](quickstart-create-micro-agent-module-twin.md)
