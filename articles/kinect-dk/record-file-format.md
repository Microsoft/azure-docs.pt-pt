---
title: Utilize o Sensor Azure Kinect SDK para gravar o formato de ficheiro
description: Compreenda como utilizar o formato de ficheiro SDK gravado no Sensor Kinect Azure.
author: xthexder
ms.author: jawirth
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: reference
keywords: kinect, azul, sensor, sdk, profundidade, rgb, record, reprodução, matroska, mkv
ms.openlocfilehash: f4fa14b0841cb76b2ba191310ecbca312d29f805
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97654596"
---
# <a name="use-azure-kinect-sensor-sdk-to-record-file-format"></a>Utilize o Sensor Azure Kinect SDK para gravar o formato de ficheiro

Para registar os dados do sensor, é utilizado o formato do recipiente Matroska (.mkv), que permite armazenar várias faixas utilizando uma vasta gama de codecs. O ficheiro de gravação contém faixas para armazenar Color, Depth, IMAGENS IR e IMU.

Detalhes de baixo nível do formato do contentor .mkv podem ser encontrados no [Site matroska](https://www.matroska.org/index.html).

| Nome da faixa | Formato Codec                          |
|------------|---------------------------------------|
| Cor      | Mode-Dependent (MJPEG, NV12 ou YUY2) |
| PROFUNDIDADE      | b16g (16-bit Grayscale, Big-endian)   |
| IR         | b16g (16-bit Grayscale, Big-endian)   |
| IMU        | Estrutura personalizada, consulte [a estrutura da amostra IMU](record-file-format.md#imu-sample-structure) abaixo. |

## <a name="using-third-party-tools"></a>Usando ferramentas de terceiros

Ferramentas como `ffmpeg` ou o comando do kit de `mkvinfo` ferramentas [MKVToolNix](https://mkvtoolnix.download/) podem ser usadas para visualizar e extrair informações de ficheiros de gravação.

Por exemplo, o seguinte comando extrairá a faixa de profundidade como uma sequência de PNGs de 16 bits para a mesma pasta:

```
ffmpeg -i output.mkv -map 0:1 -vsync 0 depth%04d.png
```

O `-map 0:1` parâmetro extrairá o índice de faixa 1, que para a maioria das gravações será a profundidade. Se a gravação não contiver uma faixa de cores, `-map 0:0` será usada.

O `-vsync 0` parâmetro força o ffmpeg a extrair quadros como-é em vez de tentar combinar com um framerate de 30 fps, 15 fps ou 5 fps.

## <a name="imu-sample-structure"></a>Estrutura de amostra do IMU

Se os dados da IMU forem extraídos do ficheiro sem utilizar a API de reprodução, os dados estarão em forma binária.
A estrutura dos dados da IMU está abaixo. Todos os campos são de pouca ponta.

| Campo                        | Tipo     |
|------------------------------|----------|
| Acelerómetro Timestamp (μs) | uint64   |
| Dados do acelerómetro (x, y, z) | flutuador[3] |
| Gyroscope Timestamp (μs)     | uint64   |
| Dados do giroscópio (x, y, z)     | flutuador[3] |

## <a name="identifying-tracks"></a>Identificação de faixas

Pode ser necessário identificar que faixa contém Cor, Profundidade, IR, e assim por diante. A identificação das faixas é necessária ao trabalhar com ferramentas de terceiros para ler um ficheiro Matroska.
Os números de faixa variam em função do modo de câmara e do conjunto de faixas ativadas. As etiquetas são usadas para identificar o significado de cada faixa.

A lista de tags abaixo está anexada a um elemento matroska específico, e pode ser usada para procurar a faixa ou anexo correspondente.

Estas etiquetas são visualizais com ferramentas como `ffmpeg` e `mkvinfo` .
A lista completa de tags está listada na página [De Gravação e Reprodução.](record-playback-api.md)

| Nome da Etiqueta             | Alvo de etiqueta             | Valor da etiqueta             |
|----------------------|------------------------|-----------------------|
| K4A_COLOR_TRACK      | Faixa de cor            | Matroska Track UID    |
| K4A_DEPTH_TRACK      | Pista de Profundidade            | Matroska Track UID    |
| K4A_IR_TRACK         | Via IR               | Matroska Track UID    |
| K4A_IMU_TRACK        | Pista IMU              | Matroska Track UID    |
| K4A_CALIBRATION_FILE | Acessório de calibração | Nome de arquivo anexo   |

## <a name="next-steps"></a>Passos seguintes

[Record e Playback](record-playback-api.md)
