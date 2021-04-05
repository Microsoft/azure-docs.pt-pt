---
title: Utilize o hub IoT existente com solução de simulação de dispositivo - Azure | Microsoft Docs
description: Este artigo descreve como configurar o acelerador de solução de simulação de dispositivo para utilizar um hub IoT existente.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/25/2018
ms.topic: conceptual
ms.openlocfilehash: 1f89e23d7bb279e7cce5c104060cc7898517f8b7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96009369"
---
# <a name="use-an-existing-iot-hub-with-the-device-simulation-solution-accelerator"></a>Utilizar um hub IoT existente com o acelerador de soluções de Simulação de Dispositivo

Ao implementar a Simulação do Dispositivo, também pode optar por implantar um hub IoT para utilizar na sua simulação. Esta opção implanta um [hub IoT de nível S2 com uma unidade de escala única](../iot-hub/iot-hub-scaling.md). Se implementar este hub IoT opcional, ainda pode optar por escolher o alvo de outro Hub IoT para uma execução de simulação.

Se optar por não implementar o Hub IoT opcional, deve utilizar o seu próprio hub para quaisquer simulações que execute.

Se não tiver um hub IoT, pode sempre criar um novo a partir do [portal Azure.](https://portal.azure.com)

Para utilizar um hub IoT pré-existente, você precisa da cadeia de ligação para a política de acesso **compartilhado iothubowner.** Pode obter esta cadeia de ligação a partir do [portal Azure:](https://portal.azure.com)

1. Na página de configuração do hub no portal, clique em **Políticas de acesso Partilhado**.

1. Clique **em iothubowner**.

1. Copie a cadeia de ligação primária ou secundária.

[![Obtenha a cadeia de conexão](./media/iot-accelerators-device-simulation-choose-hub/connectionstring-inline.png)](./media/iot-accelerators-device-simulation-choose-hub/connectionstring-expanded.png#lightbox)

Utilize a cadeia de ligação que copiou quando configurar a simulação:

![Simulação de configuração](./media/iot-accelerators-device-simulation-choose-hub/configuresimulation.png)

### <a name="next-steps"></a>Passos seguintes

Neste guia de como guiar, aprendeu a usar um hub IoT existente numa simulação. Em seguida, pode querer aprender a [criar um modelo avançado](iot-accelerators-device-simulation-advanced-device.md) de dispositivo para uma simulação.
