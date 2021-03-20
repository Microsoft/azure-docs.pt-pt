---
title: Veja o seu fluxo de vídeo RTSP do Azure Percept DK
description: Saiba como ver o fluxo de vídeo RTSP da Azure Percept DK
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/12/2021
ms.custom: template-how-to
ms.openlocfilehash: 5f77e99dc5c34867fef2b0ac47c709824fa4477d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102096008"
---
# <a name="view-your-azure-percept-dks-rtsp-video-stream"></a>Veja o seu fluxo de vídeo RTSP do Azure Percept DK

Siga este guia para ver o fluxo de vídeo RTSP do Azure Percept DK dentro do Azure Percept Studio. A inferenção dos modelos de IA de visão implantados no seu dispositivo será visível no stream da web.

## <a name="prerequisites"></a>Pré-requisitos

- Azure Percept DK (devkit)
- [Subscrição do Azure](https://azure.microsoft.com/free/)
- Experiência de [configuração Azure Percept DK](./quickstart-percept-dk-set-up.md): ligou o seu devkit a uma rede Wi-Fi, criou um Hub IoT e ligou o seu devkit ao IoT Hub

## <a name="view-the-rtsp-video-stream"></a>Ver o fluxo de vídeo RTSP

1. Energia no seu devkit.

1. Navegue para [o Azure Percept Studio.](https://go.microsoft.com/fwlink/?linkid=2135819)

1. No lado esquerdo da página geral, clique em **Dispositivos**.

    :::image type="content" source="./media/how-to-view-video-stream/overview-devices-inline.png" alt-text="Ecrã geral do Azure Percept Studio." lightbox="./media/how-to-view-video-stream/overview-devices.png":::

1. Selecione o seu devkit na lista.

    :::image type="content" source="./media/how-to-view-video-stream/select-device.png" alt-text="Screenshot dos dispositivos disponíveis no Azure Percept Studio.":::

1. Clique **em Ver o fluxo do seu dispositivo**.

    :::image type="content" source="./media/how-to-view-video-stream/view-device-stream.png" alt-text="Screenshot da página do dispositivo mostrando as ações disponíveis do projeto de visão.":::

    Isto abre um separador mostrando o stream web ao vivo do seu Azure Percept DK.

    :::image type="content" source="./media/how-to-view-video-stream/webstream.png" alt-text="Screenshot do web stream do dispositivo.":::

## <a name="next-steps"></a>Passos seguintes

Saiba como ver a sua [telemetria Azure Percept DK](./how-to-view-telemetry.md).