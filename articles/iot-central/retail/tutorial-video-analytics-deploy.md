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
ms.openlocfilehash: d661df57e4409c1d7fe196c7f136965191421bd4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88719714"
---
# <a name="how-to-deploy-an-iot-central-application-using-the-video-analytics---object-and-motion-detection-application-template"></a>Como implementar uma aplicação IoT Central usando o modelo de aplicação de deteção de objetos e movimentos

Para uma visão geral da análise de vídeo chave - componentes de aplicação *de deteção de objetos e movimentos,* consulte [a arquitetura de aplicações de análise de vídeo de deteção de objetos e movimentos](architecture-video-analytics.md).

## <a name="deploy-the-application"></a>Implementar a aplicação

Preencha os seguintes passos para implementar uma aplicação IoT Central utilizando o modelo de aplicação de análise de vídeo:

1. Complete a [aplicação De vídeo-análise em Azure IoT Central](tutorial-video-analytics-create-app.md) tutorial para:
    - Criar uma conta dos Azure Media Services.
    - Crie a aplicação IoT Central a partir da análise de vídeo - modelo de aplicação de deteção de objetos e movimentos.
    - Configure um dispositivo de gateway na aplicação IoT Central. O gateway permite que os dispositivos de câmara se conectem à aplicação.

1. Complete o tutorial [Criar uma instância IoT Edge para análise de vídeo (Linux VM)](tutorial-video-analytics-iot-edge-vm.md) para:
    - Crie um Azure VM com o tempo de execução Azure IoT Edge instalado.- Prepare a instalação IoT Edge para hospedar o módulo de análise de vídeo.
    - Ligue o dispositivo IoT Edge à sua aplicação IoT Central.

1. Complete o Monitor e gere um tutorial [de aplicação de análise de vídeo](tutorial-video-analytics-manage.md) para:
    - Adicione câmaras de deteção de objetos e movimentos ao gateway na sua aplicação IoT Central.
    - Inicie o processamento da câmara.
    - Instale um leitor de media local para ver o vídeo capturado na AMS.
    - Veja o vídeo capturado que mostra objetos detetados.
    - Arrume tudo.

## <a name="next-steps"></a>Passos seguintes

Agora tem uma visão geral dos passos para implementar e usar o modelo de aplicação de análise de vídeo, consulte [Criar uma aplicação de análise de vídeo no Azure IoT Central](tutorial-video-analytics-create-app.md) para começar.
