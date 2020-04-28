---
title: Utilize o hub IoT existente com solução de simulação de dispositivo - Azure / Microsoft Docs
description: Este artigo descreve como configurar o acelerador de solução de simulação de dispositivo para utilizar um Hub IoT existente.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/25/2018
ms.topic: conceptual
ms.openlocfilehash: 1f89e23d7bb279e7cce5c104060cc7898517f8b7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "73889187"
---
# <a name="use-an-existing-iot-hub-with-the-device-simulation-solution-accelerator"></a>Utilizar um hub IoT existente com o acelerador de soluções de Simulação de Dispositivo

Quando implementa a Simulação do Dispositivo, também pode optar por implantar um hub IoT para utilizar na sua simulação. Esta opção implanta um [hub IoT de nível S2 com uma unidade de escala única](../iot-hub/iot-hub-scaling.md). Se implementar este hub ioT opcional, ainda pode optar por direcionar outro Hub IoT para uma corrida de simulação.

Se optar por não implementar o Hub IoT opcional, deve utilizar o seu próprio hub para quaisquer simulações que executar.

Se não tiver um hub IoT, pode sempre criar um novo a partir do [portal Azure.](https://portal.azure.com)

Para utilizar um hub ioT pré-existente, você precisa da cadeia de ligação para a política de acesso partilhado **iothubowner.** Pode obter esta cadeia de ligação a partir do [portal Azure:](https://portal.azure.com)

1. Na página de configuração do hub no portal, clique em políticas de **acesso partilhado.**

1. Clique **em iothubowner**.

1. Copie a cadeia de ligação primária ou secundária.

[![Obter corda de ligação](./media/iot-accelerators-device-simulation-choose-hub/connectionstring-inline.png)](./media/iot-accelerators-device-simulation-choose-hub/connectionstring-expanded.png#lightbox)

Utilize a corda de ligação que copiou quando configurar a simulação:

![Configurar simulação](./media/iot-accelerators-device-simulation-choose-hub/configuresimulation.png)

### <a name="next-steps"></a>Passos seguintes

Neste guia de como guiar, aprendeu a usar um hub ioT existente numa simulação. Em seguida, poderá querer aprender a [criar um modelo avançado](iot-accelerators-device-simulation-advanced-device.md) de dispositivo para uma simulação.
