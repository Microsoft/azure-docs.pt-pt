---
title: Usar grupos de dispositivos em seu aplicativo de IoT Central do Azure | Microsoft Docs
description: Como um operador, saiba como usar grupos de dispositivos para analisar a telemetria de dispositivos em seu aplicativo de IoT Central do Azure.
author: dominicbetts
ms.author: dobett
ms.date: 10/29/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpfr
ms.openlocfilehash: 0f222cf9907dbf768a5f93842ff3df3fddf224a4
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73180871"
---
# <a name="tutorial-use-device-groups-to-analyze-device-telemetry-preview-features"></a>Tutorial: usar grupos de dispositivos para analisar a telemetria do dispositivo (recursos de visualização)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Este artigo descreve como, como um operador, usar grupos de dispositivos para analisar a telemetria do dispositivo em seu aplicativo de IoT Central do Azure.

Um grupo de dispositivos é uma lista de dispositivos agrupados, pois correspondem a alguns critérios especificados. Os grupos de dispositivos ajudam a gerenciar, Visualizar e analisar dispositivos em escala, agrupando dispositivos em grupos lógicos menores. Por exemplo, você pode criar um grupo de dispositivos para listar todos os dispositivos de ar-condicionado em Seattle para permitir que um técnico encontre os dispositivos para os quais eles são responsáveis.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar um grupo de dispositivos
> * Usar um grupo de dispositivos para analisar a telemetria do dispositivo

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, você deve concluir a [criação de um aplicativo de IOT central do Azure](./quick-deploy-iot-central-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) e [Adicionar um dispositivo simulado aos seus](./quick-create-pnp-device-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) guias de início rápido do aplicativo IOT central para criar o modelo de dispositivo do **sensor de ambiente** com o qual trabalhar.

## <a name="create-simulated-devices"></a>Criar dispositivos simulados

Antes de criar um grupo de dispositivos, adicione pelo menos cinco dispositivos simulados do modelo de dispositivo do **sensor de ambiente** a ser usado neste tutorial:

![Cinco dispositivos simulados do sensor ambiental](./media/tutorial-use-device-groups-pnp/simulated-devices.png)

Para quatro dos dispositivos do sensor ambiental, use a exibição **Propriedades do sensor ambiental** para definir o nome do cliente como **contoso**:

![Definir o nome do cliente para contoso](./media/tutorial-use-device-groups-pnp/customer-name.png)

## <a name="create-a-device-group"></a>Criar um grupo de dispositivos

Para criar um grupo de dispositivos:

1. Escolha **grupos de dispositivos** no painel esquerdo.

1. Selecione **+ novo**.

    ![Novo grupo de dispositivos](media/tutorial-use-device-groups-pnp/image1.png)

1. Dê um nome ao seu grupo de dispositivos, como **dispositivos contoso**. Você também pode adicionar uma descrição. Um grupo de dispositivos só pode conter dispositivos de um único modelo de dispositivo. Escolha o modelo de dispositivo do **sensor ambiental** a ser usado para este grupo.

1. Crie a consulta para identificar os dispositivos pertencentes à **contoso** para o grupo de dispositivos, selecionando a propriedade **nome do cliente** , o operador de comparação **Equals** e **contoso** como o valor. Você pode adicionar várias consultas e dispositivos que atendem a **todos** os critérios são colocados no grupo de dispositivos. O grupo de dispositivos que você cria pode ser acessado por qualquer pessoa que tenha acesso ao aplicativo, para que qualquer pessoa possa exibir, modificar ou excluir o grupo de dispositivos.

    ![Consulta do grupo de dispositivos](media/tutorial-use-device-groups-pnp/image2.png)

    > [!NOTE]
    > O grupo de dispositivos é uma consulta dinâmica. Toda vez que você exibir a lista de dispositivos, pode haver diferentes dispositivos na lista. A lista depende de quais dispositivos atendem atualmente aos critérios da consulta.

1. Escolha **Guardar**.

> [!NOTE]
> Para dispositivos Azure IoT Edge, selecione modelos de Azure IoT Edge para criar um grupo de dispositivos.

## <a name="analytics"></a>Análise

Você pode usar a **análise** com um grupo de dispositivos para analisar a telemetria dos dispositivos no grupo. Por exemplo, você pode plotar a temperatura média relatada por todos os sensores ambientais da contoso.

Para analisar a telemetria de um grupo de dispositivos:

1. Escolha **análise** no painel esquerdo.

1. Selecione o grupo de dispositivos da **contoso** que você criou. Em seguida, adicione os tipos de telemetria **temperatura** e **umidade** :

    ![Criar análise](./media/tutorial-use-device-groups-pnp/create-analysis.png)

    Use os ícones de roda de engrenagem ao lado dos tipos de telemetria para selecionar um tipo de agregação. O padrão é **Average**. Use **dividir por** para alterar a forma como os dados agregados são mostrados. Por exemplo, se você dividir por ID do dispositivo, verá uma plotagem para cada dispositivo quando selecionar **analisar**.

1. Selecione **analisar** para exibir os valores médios de telemetria:

    ![Exibir análise](./media/tutorial-use-device-groups-pnp/view-analysis.png)

    Você pode personalizar a exibição, alterar o período de tempo mostrado e exportar os dados.

## <a name="next-steps"></a>Passos seguintes

Agora que você aprendeu a usar grupos de dispositivos em seu aplicativo IoT Central do Azure, aqui está a próxima etapa sugerida:

> [!div class="nextstepaction"]
> [Como criar regras de telemetria](tutorial-create-telemetry-rules-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
