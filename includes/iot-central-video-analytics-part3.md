---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 10/06/2020
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 60a62733a17d1a3dcc4ba80ed7ceb1c37c8ac5d6
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91877207"
---
## <a name="create-the-azure-iot-edge-gateway-device"></a>Crie o dispositivo de gateway Azure IoT Edge

A análise de vídeo - aplicação de deteção de objetos e movimentos inclui um modelo de detetor **de objetos de borda LVA** e um modelo de **dispositivo de deteção de movimento de borda LVA.** Nesta secção, cria-se um modelo de dispositivo de gateway utilizando o manifesto de implantação e adiciona o dispositivo gateway à sua aplicação IoT Central.

### <a name="create-a-device-template-for-the-lva-edge-gateway"></a>Crie um modelo de dispositivo para o Gateway LVA Edge

Para importar o manifesto de implantação e criar o modelo do dispositivo **LVA Edge Gateway:**

1. Na sua aplicação IoT Central, navegue para **modelos de dispositivo,** e selecione **+ Novo**.

1. Na página **de tipo de modelo Select,** selecione o azulejo **Azure IoT Edge.** Em seguida, **selecione Seguinte: Personalize**.

1. Na página manifesto de **implementação do Azure IoT Edge,** insira o *LVA Edge Gateway* como o nome do modelo e verifique o **dispositivo Gateway com dispositivos a jusante**.

    Não navegue pelo manifesto de implantação ainda. Se o fizer, o assistente de implementação espera uma interface para cada módulo, mas só precisa de expor a interface para o **LvaEdgeGatewayModule**. Faça o upload do manifesto num passo posterior.

    :::image type="content" source="./media/iot-central-video-analytics-part3/upload-deployment-manifest.png" alt-text="Não carre deixe de carregar o manifesto de implantação":::

    Selecione **Seguinte: Revisão**.

1. Na página **'Revisão',** **selecione Criar**.

### <a name="import-the-device-capability-model"></a>Importar o modelo de capacidade do dispositivo

O modelo do dispositivo deve incluir um modelo de capacidade do dispositivo. Na página **LVA Edge Gateway,** selecione o **azulejo do modelo de capacidade de importação.** Navegue para a pasta *de configuração de Iva* que criou anteriormente e selecione as *LvaEdgeGatewayDcm.jsno* ficheiro.

O modelo do dispositivo **LVA Edge Gateway** inclui agora o **Módulo LVA Edge Gateway** juntamente com três interfaces: **Informações do dispositivo,** **Definições de Gateway de Borda LVA**e **Interface de Gateway de Borda LVA**.
