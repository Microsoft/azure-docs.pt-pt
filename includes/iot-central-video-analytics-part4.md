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
ms.openlocfilehash: d9c2aea284a2ab84b5d45fe35a35785adfc88123
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "99832078"
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

1. Selecione **Migrar**. Se o ícone **de Migração** não estiver visível, selecione... para ver mais opções. 

    :::image type="content" source="media/iot-central-video-analytics-part4/migrate-device.png" alt-text="Migrar o dispositivo gateway para uma nova versão":::

1. Na lista do diálogo **Migrar,** selecione **LVA Edge Gateway v2** e, em seguida, selecione **Migrar**.

Após alguns segundos, a migração termina. O seu dispositivo está agora a utilizar o modelo do dispositivo **LVA Edge Gateway v2** com o seu manifesto de implementação personalizado.

Não existem agora dispositivos que utilizem o modelo original do dispositivo **LVA Edge Gateway.** Elimine este modelo de dispositivo:

1. Navegue na página **de modelos do dispositivo** e selecione o modelo do dispositivo **LVA Edge Gateway.**

1. **Selecione Eliminar** para eliminar o modelo do dispositivo.

### <a name="get-the-device-credentials"></a>Obtenha as credenciais do dispositivo

Precisa das credenciais que permitem que o dispositivo se conecte à sua aplicação IoT Central. Obter as credenciais do dispositivo:

1. Na página **Dispositivos,** selecione o dispositivo **gateway-001.**

1. Selecione **Ligar**.

1. Na página **de ligação** do dispositivo, tome nota no ficheiro *scratchpad.txt* do **ID Scope,** do **ID do dispositivo** e da chave **primária** do dispositivo . Usa estes valores mais tarde.

1. Certifique-se de que o método de ligação está definido para **a assinatura de acesso partilhado**.

1. Selecione **Fechar**.

