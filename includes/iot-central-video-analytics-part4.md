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
ms.openlocfilehash: 164f5803b6e9e62447423735e98f6e4c36c73f13
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91877208"
---
### <a name="add-relationships"></a>Adicionar relacionamentos

No modelo do dispositivo **LVA Edge Gateway,** em **Módulos/Módulo gateway de borda LVA,** selecione **Relacionamentos**. Selecione **+ Adicione a relação** e adicione as seguintes duas relações:

|Nome a Apresentar               |Nome          |Destino |
|-------------------------- |------------- |------ |
|Detetor de movimento lVA Edge   |Utilizar predefinição   |Dispositivo de detetor de movimento de borda LVA |
|Detetor de Objetos de Borda LVA   |Utilizar predefinição   |Dispositivo de detetor de objetos de borda LVA |

Em seguida, selecione **Guardar**.

:::image type="content" source="media/iot-central-video-analytics-part4/relationships.png" alt-text="Adicionar relacionamentos":::

### <a name="add-views"></a>Adicionar vistas

O modelo do dispositivo **LVA Edge Gateway** não inclui definições de visualização.

Para adicionar uma vista ao modelo do dispositivo:

1. No modelo do dispositivo **LVA Edge Gateway,** navegue para **Visualizações** e **selecione o azulejo do dispositivo visualizando.**

1. Introduza o *dispositivo LVA Edge Gateway* como o nome de visualização.

1. Adicione os seguintes azulejos à vista:

    * Um azulejo com as propriedades de **Info dispositivo:** **Modelo de dispositivo,** **fabricante,** **sistema operativo,** **arquitetura de processador,** **versão de software,** **memória total**e **armazenamento total**.
    * Um gráfico de linha com os **valores de telemetria de memória livre** e de **batimentos cardíacos** do sistema.
    * Um azulejo histórico do evento com os seguintes eventos: **Criar Câmara,** **Eliminar Câmara,** **Reiniciar módulos,** **Módulo Iniciado,** **Módulo parado**.
    * Um último azulejo de valor conhecido 2x1 mostrando a telemetria **do Estado cliente central IoT.**
    * Um último azulejo de valor conhecido 2x1 mostrando a telemetria **do Estado do Módulo.**
    * Um último azulejo de valor conhecido 1x1 mostrando a telemetria **do batimento cardíaco do sistema.**
    * Um último azulejo de valor conhecido 1x1 mostrando a telemetria **das câmaras conectadas.**

    :::image type="content" source="media/iot-central-video-analytics-part4/gateway-dashboard.png" alt-text="Adicionar relacionamentos":::

1. Selecione **Guardar**.

### <a name="publish-the-device-template"></a>Publicar o modelo de dispositivo

Antes de poder adicionar um dispositivo à aplicação, tem de publicar o modelo do dispositivo:

1. No modelo do dispositivo **LVA Edge Gateway,** selecione **Publicar**.

1. No modelo de publicação deste dispositivo para a página **de aplicação,** selecione **Publicar**.

**LVA Edge Gateway** está agora disponível como tipo de dispositivo para usar na página **dispositivos** na aplicação.

## <a name="add-a-gateway-device"></a>Adicione um dispositivo de gateway

Para adicionar um dispositivo **LVA Edge Gateway** à aplicação:

1. Navegue na página **dispositivos** e selecione o modelo do dispositivo **LVA Edge Gateway.**

1. Selecione **+ Novo**.

1. No Diálogo do **dispositivo Criar um novo dispositivo,** mude o nome do dispositivo para *LVA Gateway 001*, e altere o ID do dispositivo para *lva-gateway-001*.

    > [!NOTE]
    > O ID do dispositivo deve ser único na aplicação.

1. Selecione **Criar**.

O estado do dispositivo está **registado.**

### <a name="get-the-device-credentials"></a>Obtenha as credenciais do dispositivo

Precisa das credenciais que permitem que o dispositivo se conecte à sua aplicação IoT Central. Obter as credenciais do dispositivo:

1. Na página **Dispositivos,** selecione o dispositivo **Lva-gateway-001** que criou.

1. Selecione **Ligar**.

1. Na página **de ligação** do dispositivo, tome nota no ficheiro *scratchpad.txt* do **ID Scope,** do **ID do dispositivo**e da chave **primária**do dispositivo . Usa estes valores mais tarde.

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
