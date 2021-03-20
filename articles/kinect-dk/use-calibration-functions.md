---
title: Utilize funções de calibração Azure Kinect
description: Aprenda a utilizar as funções de calibração para Azure Kinect DK.
author: cdedmonds
ms.author: cedmonds
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: kinect, azul, sensor, sdk, sistema de coordenadas, calibração, funções, câmara, intrínseco, extrínseco, projeto, nãoprojecto, transformação, rgb-d, nuvem de ponto
ms.openlocfilehash: a501765222154bde4ed35e878a9d18042893fca2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "85277495"
---
# <a name="use-azure-kinect-calibration-functions"></a>Utilize funções de calibração Azure Kinect

As funções de calibração permitem a transformação de pontos entre os sistemas de coordenadas de cada sensor no dispositivo Azure Kinect. As aplicações que exijam a conversão de imagens inteiras podem tirar partido das operações aceleradas disponíveis nas [funções de transformação](use-image-transformation.md).

## <a name="retrieve-calibration-data"></a>Recuperar dados de calibração

É necessário recuperar a calibração do dispositivo para executar as transformações do sistema de coordenadas. Os dados de calibração são armazenados no [tipo de dados k4a_calibration_t.](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__calibration__t.html#details) É obtido a partir do dispositivo através da função [k4a_device_get_calibration()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga4e43940d8d8db48da266c7a7842c8d78.html#ga4e43940d8d8db48da266c7a7842c8d78). Os dados de calibração não são apenas específicos de cada dispositivo, mas também do modo de funcionamento das câmaras. Por [isso, k4a_device_get_calibration requer](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga4e43940d8d8db48da266c7a7842c8d78.html#ga4e43940d8d8db48da266c7a7842c8d78) os `depth_mode` parâmetros e os `color_resolution` parâmetros como entrada.

### <a name="opencv-compatibility"></a>Compatibilidade openCV

Os parâmetros de calibração são compatíveis com [o OpenCV](https://opencv.org/). Para obter mais informações sobre os parâmetros de calibração de cada câmara, consulte também [a documentação do OpenCV](https://docs.opencv.org/3.2.0/d9/d0c/group__calib3d.html#gga7041b2a9c8f9f8ee93a2796981bc5546a204766e24f2e413e7a7c9f8b9e93f16c). Consulte também o exemplo de [compatibilidade do OpenCV](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/opencv_compatibility) do SDK que demonstra a conversão entre o tipo [k4a_calibration_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__calibration__t.html#details) e as correspondentes estruturas de dados OpenCV.

## <a name="coordinate-transformation-functions"></a>Coordenar funções de transformação

A figura abaixo mostra os diferentes sistemas de coordenadas de Azure Kinect, bem como as funções a converter entre eles. Omitimos os sistemas de coordenadas 3D de giroscópio e acelerómetro para manter a figura simples.

   ![Coordenar a transformação](./media/how-to-guides/coordinate-transformation.png)

Observação sobre a distorção da lente: as coordenadas 2D referem-se sempre à imagem distorcida no SDK. O exemplo de [indistor do](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/undistort) SDK demonstra a falta de imagem. Em geral, os pontos 3D nunca serão afetados por distorção da lente.

### <a name="convert-between-3d-coordinate-systems"></a>Converter entre sistemas de coordenadas 3D

A função [k4a_calibration_3d_to_3d()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaacd1eab997ef964b5e436afa5295726e.html#gaacd1eab997ef964b5e436afa5295726e) converte um ponto 3D do sistema de coordenadas de origem para um ponto 3D do sistema de coordenadas alvo utilizando a calibração extrínseca da câmara. A origem e o alvo podem ser definidos em qualquer um dos quatro sistemas de coordenadas 3D, isto é, câmara de cores, câmara de profundidade, giroscópio ou acelerómetro. Se a fonte e o alvo forem idênticos, a entrada não modificada 3D é devolvida como saída.

### <a name="convert-between-2d-and-3d-coordinate-systems"></a>Converter entre sistemas de coordenadas 2D e 3D

A função [k4a_calibration_3d_to_2d()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga2ed8b51d727425caa942aab190fc2ba9.html#ga2ed8b51d727425caa942aab190fc2ba9) converte um ponto 3D do sistema de coordenadas de origem para uma coordenada de pixel 2D da câmara-alvo. Esta função é frequentemente referida como função do projeto. Embora a fonte possa ser definida em qualquer um dos quatro sistemas de coordenadas 3D, o alvo deve ser a profundidade ou a câmara de cores. Se a fonte e o alvo forem diferentes, o ponto 3D de entrada é convertido para o sistema de coordenadas 3D da câmara-alvo utilizando [k4a_calibration_3d_to_3d()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaacd1eab997ef964b5e436afa5295726e.html#gaacd1eab997ef964b5e436afa5295726e). Uma vez que o ponto 3D é representado no sistema de coordenadas da câmara-alvo, as coordenadas de pixel 2D correspondentes são calculadas usando a calibração intrínseca da câmara-alvo. Se um ponto 3D cair da área visível da câmara-alvo, o valor válido é definido para 0.

A função [k4a_calibration_2d_to_3d converte](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga664602bdb48dab38117a6c1d14b880de.html#ga664602bdb48dab38117a6c1d14b880de) uma coordenada de pixel 2D da câmara de origem para um ponto 3D do sistema de coordenadas da câmara-alvo. A fonte deve ser de cor ou câmara de profundidade. O alvo pode ser definido para qualquer um dos quatro sistemas de coordenadas 3D. Para além da coordenada de pixel 2D, o valor de profundidade do pixel (em milímetros) na imagem da câmara de origem é necessário como entrada para a função, uma forma de obter o valor de profundidade na geometria da câmara de cor é utilizar a função [k4a_transformation_depth_image_to_color_camera()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gafacffb5f781a9c2df30d4a16241cd514.html#gafacffb5f781a9c2df30d4a16241cd514). A função calcula o raio 3D que conduz do ponto focal da câmara de origem através da coordenada pixel especificada utilizando a calibração intrínseca da câmara de origem. O valor de profundidade é então usado para encontrar a localização exata do ponto 3D neste raio. Esta operação é frequentemente referida como função não projete. Se as câmaras de origem e alvo forem diferentes, a função transforma o ponto 3D para o sistema de coordenadas do alvo através [k4a_calibration_3d_to_3d()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaacd1eab997ef964b5e436afa5295726e.html#gaacd1eab997ef964b5e436afa5295726e). Se uma coordenada de pixel 2D cair da área visível da câmara de origem, o valor válido é definido para 0.

### <a name="converting-between-2d-coordinate-systems"></a>Conversão entre sistemas de coordenadas 2D

A função [k4a_calibration_2d_to_2d converte](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga3b6bf6dedbfe67468e2f895dcce68ed4.html#ga3b6bf6dedbfe67468e2f895dcce68ed4) uma coordenada de pixel 2D da câmara de origem para uma coordenada de pixel 2D da câmara-alvo. A origem e o alvo devem ser definidos para a cor ou a câmara de profundidade. A função requer o valor de profundidade do pixel (em milímetros) na imagem da câmara de origem como entrada, uma forma de obter o valor de profundidade na geometria da câmara de cor é utilizar a função [k4a_transformation_depth_image_to_color_camera()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gafacffb5f781a9c2df30d4a16241cd514.html#gafacffb5f781a9c2df30d4a16241cd514). Chama [k4a_calibration_2d_to_3d](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga664602bdb48dab38117a6c1d14b880de.html#ga664602bdb48dab38117a6c1d14b880de) para converter para um ponto 3D do sistema de câmaras de origem. Em seguida, chama [k4a_calibration_3d_to_2d para](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga2ed8b51d727425caa942aab190fc2ba9.html#ga2ed8b51d727425caa942aab190fc2ba9) converter para uma coordenada de pixel 2D da imagem da câmara alvo. O valor válido é definido para 0, se [k4a_calibration_2d_to_3d ou](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga664602bdb48dab38117a6c1d14b880de.html#ga664602bdb48dab38117a6c1d14b880ded) [k4a_calibration_3d_to_2d()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga2ed8b51d727425caa942aab190fc2ba9.html#ga2ed8b51d727425caa942aab190fc2ba9) der um resultado inválido.

## <a name="related-samples"></a>Amostras relacionadas

- [Exemplo de compatibilidade do OpenCV](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/opencv_compatibility)
- [Exemplo de indistorção](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/undistort)
- [Exemplo de nuvem de ponto rápido](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/fastpointcloud)

## <a name="next-steps"></a>Passos seguintes

Agora que sabe sobre calibrações de câmaras, também pode aprender a
>[!div class="nextstepaction"]
>[Capturar a sincronização do dispositivo](capture-device-synchronization.md)

Também pode rever

[Sistemas de coordenadas](coordinate-systems.md)
