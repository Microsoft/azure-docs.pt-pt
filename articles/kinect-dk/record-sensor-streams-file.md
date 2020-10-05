---
title: Quickstart- Gravar o sensor Azure Kinect flui para um ficheiro
description: Neste arranque rápido, irá aprender a gravar fluxos de dados do Sensor SDK para um ficheiro.
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: quickstart
keywords: azul, kinect, record, play back, reader, matroska, mkv, streams, profundidade, rgb, câmera, cor, imu, áudio, sensor
ms.openlocfilehash: 3dab147b593bf012bd6cd9c95d0195e84a2cbcf1
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/05/2020
ms.locfileid: "85277976"
---
# <a name="quickstart-record-azure-kinect-sensor-streams-to-a-file"></a>Quickstart: Gravar fluxos de sensores Azure Kinect para um ficheiro

Este quickstart fornece informações sobre como pode utilizar a ferramenta de [gravador Azure Kinect](azure-kinect-recorder.md) para gravar fluxos de dados do Sensor SDK para um ficheiro.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Este quickstart assume:

- Tem o Azure Kinect DK ligado ao seu PC anfitrião e alimentado corretamente.
- Terminou de [configurar](set-up-azure-kinect-dk.md) o hardware.

## <a name="create-recording"></a>Criar gravação

1. Abra um pedido de comando e forneça o caminho para o [gravador Azure Kinect,](azure-kinect-recorder.md)localizado no diretório de ferramentas instalado como `k4arecorder.exe` . Por exemplo: `C:\Program Files\Azure Kinect SDK\tools\k4arecorder.exe`.
2. Recorde 5 segundos.

    `k4arecorder.exe -l 5 %TEMP%\output.mkv`

3. Por predefinição, o gravador utiliza o modo de profundidade não encadernado NFOV e produz 1080p RGB a 30 fps, incluindo dados da IMU. Para obter uma visão geral completa das opções e dicas de gravação, consulte o [gravador Azure Kinect](azure-kinect-recorder.md).

## <a name="play-back-recording"></a>Reproduzir a gravação

Pode utilizar o [Azure Kinect Viewer](azure-kinect-viewer.md) para reproduzir uma gravação.

1. Lançamento [`k4aviewer.exe`](azure-kinect-viewer.md)
2. Desdobre o **separador 'Gravação Aberta'** e abra a gravação.

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu a gravar fluxos de sensores para um ficheiro, é hora de

> [!div class="nextstepaction"]
> [Criar a sua primeira aplicação do Azure Kinect](build-first-app.md)
