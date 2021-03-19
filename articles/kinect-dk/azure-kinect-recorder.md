---
title: Gravador Azure Kinect DK
description: Compreenda como gravar fluxos de dados do sensor SDK para um ficheiro utilizando o gravador Azure Kinect.
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: kinect, record, reprodução, leitor, matroska, mkv, streams, profundidade, rgb, câmera, cor, imu, áudio
ms.openlocfilehash: 5fb6895d4102956a991c67ffab836a26b7a3abb0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "85277514"
---
# <a name="azure-kinect-dk-recorder"></a>Gravador Azure Kinect DK

Este artigo cobre como pode utilizar o `k4arecorder` utilitário da linha de comando para registar fluxos de dados do sensor SDK para um ficheiro.

>[!NOTE]
>O gravador Azure Kinect não grava áudio.

## <a name="recorder-options"></a>Opções de gravador

`k4arecorder`Tem vários argumentos de linha de comando para especificar o ficheiro de saída e os modos de gravação.

As gravações são armazenadas no [formato Matroska .mkv](record-file-format.md). A gravação utiliza várias faixas de vídeo para colorir e profundidade, e também informações adicionais, como calibração de câmaras e metadados.

```console
k4arecorder [options] output.mkv

 Options:
  -h, --help              Prints this help
  --list                  List the currently connected K4A devices
  --device                Specify the device index to use (default: 0)
  -l, --record-length     Limit the recording to N seconds (default: infinite)
  -c, --color-mode        Set the color sensor mode (default: 1080p), Available options:
                            3072p, 2160p, 1536p, 1440p, 1080p, 720p, 720p_NV12, 720p_YUY2, OFF
  -d, --depth-mode        Set the depth sensor mode (default: NFOV_UNBINNED), Available options:
                            NFOV_2X2BINNED, NFOV_UNBINNED, WFOV_2X2BINNED, WFOV_UNBINNED, PASSIVE_IR, OFF
  --depth-delay           Set the time offset between color and depth frames in microseconds (default: 0)
                            A negative value means depth frames will arrive before color frames.
                            The delay must be less than 1 frame period.
  -r, --rate              Set the camera frame rate in Frames per Second
                            Default is the maximum rate supported by the camera modes.
                            Available options: 30, 15, 5
  --imu                   Set the IMU recording mode (ON, OFF, default: ON)
  --external-sync         Set the external sync mode (Master, Subordinate, Standalone default: Standalone)
  --sync-delay            Set the external sync delay off the master camera in microseconds (default: 0)
                            This setting is only valid if the camera is in Subordinate mode.
  -e, --exposure-control  Set manual exposure value (-11 to 1) for the RGB camera (default: auto exposure)
```

## <a name="record-files"></a>Registar ficheiros

Exemplo 1. Modo Record Depth NFOV un encadernado (640x576), RGB 1080p a 30 fps com IMU.
Pressione as teclas **CTRL-C** para parar de gravar.

```
k4arecorder.exe output.mkv
```

Exemplo 2. Recorde WFOV não-vinculado (1MP), RGB 3072p a 15 fps sem IMU, durante 10 segundos.

```
k4arecorder.exe -d WFOV_UNBINNED -c 3072p -r 15 -l 10 --imu OFF output.mkv
```

Exemplo 3. Recorde WFOV 2x2 preso a 30 fps durante 5 segundos, e economize para output.mkv.

```
k4arecorder.exe -d WFOV_2X2BINNED -c OFF --imu OFF -l 5 output.mkv
```

>[!TIP]
>Pode utilizar [o Azure Kinect Viewer](azure-kinect-viewer.md) para configurar os controlos da câmara RGB antes de gravar (por exemplo, para definir o equilíbrio branco manual).

## <a name="verify-recording"></a>Verificar gravação

Pode abrir o ficheiro output .mkv com [o Azure Kinect Viewer](azure-kinect-viewer.md).

Para extrair faixas ou ver informações de ficheiros, ferramentas como `mkvinfo` estas estão disponíveis como parte do kit de ferramentas [MKVToolNix.](https://mkvtoolnix.download/)

## <a name="next-steps"></a>Passos seguintes

[Utilização de gravador com unidades sincronizadas externas](record-external-synchronized-units.md)