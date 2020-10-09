---
title: Sobre a Azure Kinect Sensor SDK
description: Visão geral do kit de desenvolvimento de software Azure Kinect Sensor (SDK), suas funcionalidades e ferramentas.
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: article
keywords: azul, kinect, rgb, IR, gravação, sensor, sdk, acesso, profundidade, vídeo, câmara, imu, movimento, sensor, áudio, microfone, matroska, sensor sdk, download
ms.openlocfilehash: 17c1b33120eacb5d0c6d3c02e692d1488ef474e6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85277530"
---
# <a name="about-azure-kinect-sensor-sdk"></a>Sobre a Azure Kinect Sensor SDK

Este artigo fornece uma visão geral do kit de desenvolvimento de software Azure Kinect Sensor (SDK), suas funcionalidades e ferramentas.

## <a name="features"></a>Funcionalidades

O Azure Kinect Sensor SDK fornece acesso transversal de baixo nível para a configuração do dispositivo Azure Kinect e fluxos de sensores de hardware, incluindo:

- Profundidade do acesso à câmara e do controlo do modo (um modo IR passivo, mais modos de profundidade de campo de visão largo e estreito) 
- Acesso e controlo da câmara RGB (por exemplo, exposição e equilíbrio branco) 
- Acesso ao sensor de movimento (giroscópio e acelerómetro) 
- Streaming de câmara sincronizada Depth-RGB com atraso configurável entre câmaras 
- Controlo de sincronização de dispositivos externos com compensação de atraso configurável entre dispositivos 
- Acesso de meta-dados de quadro de câmara para resolução de imagem, hora de fixação, etc. 
- Acesso de dados de calibração do dispositivo 

## <a name="tools"></a>Ferramentas

- Um [visualizador Azure Kinect](azure-kinect-viewer.md) para monitorizar os fluxos de dados do dispositivo e configurar diferentes modos.
- Um [gravador Azure Kinect](azure-kinect-recorder.md) e leitor de reprodução API que utiliza o formato do [recipiente Matroska](record-file-format.md).
- Uma ferramenta de [atualização de firmware](azure-kinect-firmware-tool.md)Azure Kinect DK .

## <a name="sensor-sdk"></a>Sensor SDK

- [Baixar sensor SDK](sensor-sdk-download.md).
- O Sensor SDK está disponível em [fonte aberta no GitHub.](https://github.com/microsoft/Azure-Kinect-Sensor-SDK)
- Para obter mais informações sobre a utilização, consulte [a documentação da API do Sensor SDK](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/index.html).

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu sobre o sensor Azure Kinect SDK, também pode:
>[!div class="nextstepaction"]
>[Baixar o código SDK do sensor](sensor-sdk-download.md)

>[!div class="nextstepaction"]
>[Localizar e abrir dispositivo](find-then-open-device.md)
