---
title: Sistemas de coordenadas Azure Kinect DK
description: Descrição dos sistemas de coordenadas Azure Kinect DK associada aos sensores Azure DK
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: kinect, azul, sensor, sdk, câmara de profundidade, tof, princípios, desempenho, invalidação
ms.openlocfilehash: 4bb1c3b79862b918870cff786042d9b4c66270d1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "85277447"
---
# <a name="azure-kinect-dk-coordinate-systems"></a>Sistemas de coordenadas Azure Kinect DK

Neste artigo, descrevemos convenções utilizadas para sistemas de coordenadas 2D e 3D.  Existem sistemas de coordenadas separados associados ao dispositivo de cada sensor e as funções de [calibração permitidas](use-calibration-functions.md) para transformar pontos entre eles. As [funções de transformação](use-image-transformation.md) transformam imagens inteiras entre sistemas de coordenadas.  

## <a name="2d-coordinate-systems"></a>Sistemas de coordenadas 2D

 As câmaras de profundidade e de cores estão associadas a um sistema de coordenadas 2D independente. Uma coordenada [x,y]é representada em unidades de pixels onde *x* varia de 0 a largura-1 e *y* varia de 0 a altura-1. A largura e a altura dependem do modo escolhido em que as câmaras de profundidade e de cor são operadas. A coordenada pixel `[0,0]` corresponde ao pixel superior esquerdo da imagem. As coordenadas de pixel podem ser fracionárias representando coordenadas subpixel.

O sistema de coordenadas 2D é centrado em 0, ou seja, a coordenada subpixel `[0.0, 0.0]` representa o centro e o canto inferior direito do `[0.5,0.5]` pixel, como mostrado abaixo.

   ![Sistema de coordenadas 2D](./media/concepts/concepts-coordinate-systems/coordinate-systems-sdk-2d-system.png)

## <a name="3d-coordinate-systems"></a>Sistemas de coordenadas 3D

Cada câmara, o acelerómetro e o giroscópio estão associados a um sistema espacial de coordenadas 3D independente.

Os pontos nos sistemas de coordenadas 3D são representados como trigémeos de coordenadas métricas [X,Y,Z], com unidades em milímetros.

### <a name="depth-and-color-camera"></a>Câmara de profundidade e cor

A origem `[0,0,0]` está localizada no ponto focal da câmara. O sistema de coordenadas é orientado de tal forma que o eixo X positivo aponta para a direita, o eixo Y positivo aponta para baixo, e o eixo Z positivo aponta para a frente.

A câmara de profundidade está inclinada 6 graus para baixo da câmara de cores, como mostrado abaixo. 

Há dois iluminadores usados pela câmara de profundidade. O iluminador utilizado em modos estreitos de campo de vista (NFOV) está alinhado com a caixa da câmara de profundidade, pelo que o iluminador não está inclinado. O iluminador utilizado em modos de campo de vista largo (WFOV) é inclinado mais 1,3 graus para baixo em relação à câmara de profundidade.

![Convenções de coordenadas 3D](./media/concepts/concepts-coordinate-systems/coordinate-systems-camera-features.png)

### <a name="gyroscope-and-accelerometer"></a>Giroscópio e acelerómetro

A origem do giroscópio `[0,0,0]` é idêntica à origem da câmara de profundidade. A origem do acelerómetro coincide com a sua localização física. Tanto os sistemas de coordenadas do acelerómetro como do giroscópio são destros. O eixo X positivo do sistema de coordenadas aponta para trás, os pontos positivos do eixo Y à esquerda, e o eixo Z positivo aponta para baixo, como mostrado abaixo.

![Sistema de coordenadas IMU](./media/concepts/concepts-coordinate-systems/coordinate-systems-gyroscope.png)

## <a name="next-steps"></a>Passos Seguintes

[Saiba mais sobre o Sensor Azure Kinect SDK](about-sensor-sdk.md)