---
title: incluir ficheiro
description: incluir ficheiro
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 10/06/2020
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: de916fcbe0623185821e2f5da15a8f9cf71dfd4e
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94426782"
---
### <a name="publish-the-device-template"></a>Publicar o modelo de dispositivo

Antes de poder adicionar um dispositivo à aplicação, tem de publicar o modelo do dispositivo:

1. No modelo do dispositivo **LVA Edge Gateway v2,** selecione **Publicar**.

1. No modelo de publicação deste dispositivo para a página **de aplicação,** selecione **Publicar**.

**LVA Edge Gateway v2** está agora disponível como tipo de dispositivo para usar na página **dispositivos** na aplicação.

## <a name="migrate-the-gateway-device"></a>Migrar o dispositivo de gateway

O dispositivo **gateway-001** existente utiliza o modelo do dispositivo **LVA Edge Gateway.** Para utilizar o seu novo manifesto de implantação, migrar o dispositivo para o novo modelo do dispositivo:

Para migrar o dispositivo **gateway-001:**

1. Navegue na página **Dispositivos** e selecione o dispositivo **gateway-001** para realçá-lo na lista.

1. Selecione **Migrar**. Se o ícone **de Migração** não estiver visível, selecione... para ver mais opções. **...**

    :::image type="content" source="media/iot-central-video-analytics-part4/migrate-device.png" alt-text="Migrar o dispositivo gateway para uma nova versão":::

1. Na lista do diálogo **Migrar,** selecione **LVA Edge Gateway v2** e, em seguida, selecione **Migrar**.

Após alguns segundos, a migração termina. O seu dispositivo está agora a utilizar o modelo do dispositivo **LVA Edge Gateway v2** com o seu manifesto de implementação personalizado.

### <a name="get-the-device-credentials"></a>Obtenha as credenciais do dispositivo

Precisa das credenciais que permitem que o dispositivo se conecte à sua aplicação IoT Central. Obter as credenciais do dispositivo:

1. Na página **Dispositivos,** selecione o dispositivo **gateway-001.**

1. Selecione **Connect** (Ligar).

1. Na página **de ligação** do dispositivo, tome nota no ficheiro *scratchpad.txt* do **ID Scope,** do **ID do dispositivo** e da chave **primária** do dispositivo . Usa estes valores mais tarde.

1. Certifique-se de que o método de ligação está definido para **a assinatura de acesso partilhado**.

1. Selecione **Fechar**.

## <a name="next-steps"></a>Passos seguintes

Criou agora uma aplicação IoT Central utilizando o modelo de aplicação **de deteção de objetos e movimentos,** criou um modelo de dispositivo para o dispositivo gateway e adicionou um dispositivo de gateway à aplicação.

Se quiser experimentar a análise de vídeo - aplicação de deteção de objetos e movimentos utilizando módulos IoT Edge que executam um VM em nuvem com streams de vídeo simulados:

> [!div class="nextstepaction"]
> [Crie uma instância IoT Edge para análise de vídeo (Linux VM)](../articles/iot-central/retail/tutorial-video-analytics-iot-edge-vm.md)

Se quiser experimentar a análise de vídeo - aplicação de deteção de objetos e movimentos utilizando módulos IoT Edge que executam um dispositivo real com uma câmara **ONVIF** real:

> [!div class="nextstepaction"]
> [Crie uma instância IoT Edge para análise de vídeo (Intel NUC)](../articles/iot-central/retail/tutorial-video-analytics-iot-edge-nuc.md)
