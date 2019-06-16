---
title: Utilizar um hub IoT com a solução de simulação do dispositivo - Azure | Documentos da Microsoft
description: Este artigo descreve como configurar o acelerador de solução de simulação do dispositivo para utilizar um IoT Hub.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/25/2018
ms.topic: conceptual
ms.openlocfilehash: 38cde750ce07741a433baa1b8607a584f94ad9b1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "62123663"
---
# <a name="use-an-existing-iot-hub-with-the-device-simulation-solution-accelerator"></a>Utilizar um hub IoT com o solution accelerator de simulação do dispositivo

Ao implementar simulação do dispositivo, também pode optar por implementar um hub IoT para utilizar na simulação. Esta opção implementa uma [hub de IoT do escalão S2 com uma unidade de escala único](../iot-hub/iot-hub-scaling.md). Se implementar este hub IoT opcional, ainda pode optar por outro IoT Hub para uma execução de simulação de destino.

Se optar por não implementar o IoT Hub opcional, tem de utilizar o seu próprio hub para qualquer simulações executar.

Se não tiver um hub IoT, pode sempre criar uma nova a partir da [portal do Azure](https://portal.azure.com).

Para utilizar um hub IoT já existente, tem da cadeia de ligação para o **iothubowner** partilhado a política de acesso. Pode obter esta cadeia de ligação a partir da [portal do Azure](https://portal.azure.com):

1. Na página de configuração do hub no portal, clique em **políticas de acesso partilhado**.

1. Clique em **iothubowner**.

1. Copie a cadeia de ligação primária ou secundária.

[![Obter cadeia de ligação](./media/iot-accelerators-device-simulation-choose-hub/connectionstring-inline.png)](./media/iot-accelerators-device-simulation-choose-hub/connectionstring-expanded.png#lightbox)

Utilize a cadeia de ligação que copiou quando configurar a simulação:

![Configurar a simulação](./media/iot-accelerators-device-simulation-choose-hub/configuresimulation.png)

### <a name="next-steps"></a>Passos Seguintes

Este guia de procedimentos, aprendeu a utilizar o hub IoT existente numa simulação. Em seguida, pode querer saber como [criar um modelo de dispositivo avançados](iot-accelerators-device-simulation-advanced-device.md) para uma simulação.
