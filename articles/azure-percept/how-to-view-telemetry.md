---
title: Veja a telemetria de inferência do seu modelo Azure Percept DK
description: Saiba como ver o seu modelo de visão Azure Percept DK inferência telemetria em Azure IoT Explorer
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/17/2021
ms.custom: template-how-to
ms.openlocfilehash: 07abbc5f5e85c75b73774d11b6b81dd2085735b7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102095328"
---
# <a name="view-your-azure-percept-dks-model-inference-telemetry"></a>Veja a telemetria de inferência do seu modelo Azure Percept DK

Siga este guia para ver a telemetria do seu modelo de visão Azure Percept DK no [Azure IoT Explorer](https://github.com/Azure/azure-iot-explorer/releases).

## <a name="prerequisites"></a>Pré-requisitos

- Azure Percept DK (devkit)
- [Subscrição do Azure](https://azure.microsoft.com/free/)
- Experiência de [configuração Azure Percept DK](./quickstart-percept-dk-set-up.md): ligou o seu devkit a uma rede Wi-Fi, criou um Hub IoT e ligou o seu devkit ao IoT Hub
- [O modelo Vision AI foi implantado no seu Azure Percept DK](./how-to-deploy-model.md)

## <a name="view-telemetry"></a>Ver telemetria

1. Energia no seu devkit.

1. Faça o download e instale [o Azure IoT Explorer](https://github.com/Azure/azure-iot-explorer/releases). Se for um utilizador do Windows, selecione o ficheiro .msi.

    :::image type="content" source="./media/how-to-view-telemetry/azure-iot-explorer-download.png" alt-text="Descarregue o ecrã para Azure IoT Explorer.":::

1. Ligue o seu Hub IoT ao Azure IoT Explorer:

    1. Aceda ao [Portal do Azure](https://portal.azure.com).

    1. Selecione **Todos os recursos**.

        :::image type="content" source="./media/how-to-view-telemetry/azure-portal.png" alt-text="Página inicial do portal Azure.":::

    1. Selecione o Hub IoT ao qual o seu Azure Percept DK está ligado.

        :::image type="content" source="./media/how-to-view-telemetry/iot-hub.png" alt-text="Lista do IoT Hub no portal Azure.":::

    1. No lado esquerdo da sua página IoT Hub, selecione **Políticas de acesso partilhado**.

        :::image type="content" source="./media/how-to-view-telemetry/shared-access-policies.png" alt-text="Página do IoT Hub mostrando políticas de acesso compartilhado.":::

    1. Clique no **iothubowner**.

        :::image type="content" source="./media/how-to-view-telemetry/iothubowner.png" alt-text="Ecrã de políticas de acesso compartilhado com iothubowner em destaque.":::

    1. Clique no ícone de cópia azul ao lado **da cadeia Connection — tecla primária**.

        :::image type="content" source="./media/how-to-view-telemetry/connection-string.png" alt-text="Janela iothubowner com botão de cópia de cadeia de ligação realçado.":::

    1. Abra o Azure IoT Explorer e clique **+ Adicione a ligação**.

    1. Cole o fio de ligação na caixa **de cordas Connection** na janela de cadeia de **ligação Adicionar** e clique em **Guardar**.

        :::image type="content" source="./media/how-to-view-telemetry/add-connection-string.png" alt-text="Janela Azure Iot Explorer com caixa para colar fio de ligação.":::

    1. Aponte o Vision SoM num objeto para inferencção do modelo.

    1. Selecione **Telemetria**.

    1. Clique **em Iniciar** a visualização de eventos de telemetria a partir do dispositivo.

## <a name="next-steps"></a>Passos seguintes
Saiba como ver o seu [fluxo de vídeo Azure Percept DK](./how-to-view-video-stream.md).