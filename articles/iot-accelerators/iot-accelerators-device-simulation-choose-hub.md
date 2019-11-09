---
title: Usar o Hub IoT existente com a solução de simulação de dispositivo – Azure | Microsoft Docs
description: Este artigo descreve como configurar o acelerador de solução de simulação de dispositivo para usar um hub IoT existente.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/25/2018
ms.topic: conceptual
ms.openlocfilehash: 1f89e23d7bb279e7cce5c104060cc7898517f8b7
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2019
ms.locfileid: "73889187"
---
# <a name="use-an-existing-iot-hub-with-the-device-simulation-solution-accelerator"></a>Usar um hub IoT existente com o acelerador de solução de simulação de dispositivo

Ao implantar a simulação de dispositivo, você também pode optar por implantar um hub IoT para usar em sua simulação. Essa opção implanta um [Hub IOT de camada S2 com uma única unidade de escala](../iot-hub/iot-hub-scaling.md). Se você implantar esse Hub IoT opcional, ainda poderá optar por direcionar outro hub IoT para uma execução de simulação.

Se optar por não implantar o Hub IoT opcional, você deverá usar seu próprio Hub para as simulações executadas.

Se você não tiver um hub IoT, sempre poderá criar um novo no [portal do Azure](https://portal.azure.com).

Para usar um hub IoT já existente, você precisa da cadeia de conexão para a política de acesso compartilhado **iothubowner** . Você pode obter essa cadeia de conexão do [portal do Azure](https://portal.azure.com):

1. Na página de configuração do Hub, no portal, clique em **políticas de acesso compartilhado**.

1. Clique em **iothubowner**.

1. Copie a cadeia de conexão primária ou secundária.

[![obter cadeia de conexão](./media/iot-accelerators-device-simulation-choose-hub/connectionstring-inline.png)](./media/iot-accelerators-device-simulation-choose-hub/connectionstring-expanded.png#lightbox)

Use a cadeia de conexão que você copiou ao configurar a simulação:

![Configurar simulação](./media/iot-accelerators-device-simulation-choose-hub/configuresimulation.png)

### <a name="next-steps"></a>Passos seguintes

Neste guia de instruções, você aprendeu a usar um hub IoT existente em uma simulação. Em seguida, talvez você queira aprender a [criar um modelo de dispositivo avançado](iot-accelerators-device-simulation-advanced-device.md) para uma simulação.
