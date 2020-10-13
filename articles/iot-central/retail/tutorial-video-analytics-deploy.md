---
title: Como implementar a análise de vídeo - objeto e deteção de movimento Azure IoT Modelo de aplicação central
description: Este guia resume os passos para implementar uma aplicação Azure IoT Central utilizando o modelo de aplicação de deteção de objetos e movimentos.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: how-to
ms.author: nandab
author: KishorIoT
ms.date: 07/31/2020
ms.openlocfilehash: decfa7020be7778e8ca64a9fb0cb4aac1657da27
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91873341"
---
# <a name="how-to-deploy-an-iot-central-application-using-the-video-analytics---object-and-motion-detection-application-template"></a>Como implementar uma aplicação IoT Central usando o modelo de aplicação de deteção de objetos e movimentos

Para uma visão geral da análise de vídeo chave - componentes de aplicação *de deteção de objetos e movimentos,* consulte [a arquitetura de aplicações de análise de vídeo de deteção de objetos e movimentos](architecture-video-analytics.md).

O vídeo a seguir dá uma análise de como usar a análise de vídeo - modelo de _aplicação de deteção de objetos e movimentos_ para implementar uma solução IoT Central:

> [!VIDEO https://www.youtube.com/embed/Bo3FziU9bSA]

## <a name="deploy-the-application"></a>Implementar a aplicação

Preencha os seguintes passos para implementar uma aplicação IoT Central utilizando o modelo de aplicação de análise de vídeo:

1. Preencha uma [aplicação de análise de vídeo em Azure IoT Central (YOLO v3)](tutorial-video-analytics-create-app-yolo-v3.md) ou a Criar uma análise de vídeo no tutorial [Azure IoT Central &trade; (OpenVINO)](tutorial-video-analytics-create-app-openvino.md) para:
    - Criar uma conta dos Azure Media Services.
    - Crie a aplicação IoT Central a partir da análise de vídeo - modelo de aplicação de deteção de objetos e movimentos.
    - Configure um dispositivo de gateway na aplicação IoT Central. O gateway permite que os dispositivos de câmara se conectem à aplicação.

1. Complete ou [a instância Criar uma instância IoT Edge para análise de vídeo (Linux VM)](tutorial-video-analytics-iot-edge-vm.md) ou o [Tutorial: Criar uma instância IoT Edge para análise de vídeo (Intel NUC)](tutorial-video-analytics-iot-edge-nuc.md) tutorial para:
    - Crie um Azure VM com o tempo de execução Azure IoT Edge instalado.- Prepare a instalação IoT Edge para hospedar o módulo de análise de vídeo.
    - Ligue o dispositivo IoT Edge à sua aplicação IoT Central.

1. Complete o Monitor e gere um tutorial [de aplicação de análise de vídeo](tutorial-video-analytics-manage.md) para:
    - Adicione câmaras de deteção de objetos e movimentos ao gateway na sua aplicação IoT Central.
    - Inicie o processamento da câmara.
    - Instale um leitor de media local para ver o vídeo capturado na AMS.
    - Veja o vídeo capturado que mostra objetos detetados.
    - Arrume tudo.

## <a name="next-steps"></a>Passos seguintes

Agora tem uma visão geral dos passos para implementar e utilizar o modelo de aplicação de análise de vídeo, ver [Criar uma aplicação de análise de vídeo no Azure IoT Central (YOLO v3)](tutorial-video-analytics-create-app-yolo-v3.md) ou [criar uma análise de vídeo em Azure IoT Central (OpenVINO) &trade; ](tutorial-video-analytics-create-app-openvino.md) para começar.
