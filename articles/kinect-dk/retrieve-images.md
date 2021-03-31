---
title: Recuperar dados de imagem do Azure Kinect
description: Saiba como recuperar os dados de imagem do Azure Kinect utilizando o Sensor Kinect SDK.
author: cdedmonds
ms.author: cedmonds
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: kinect, azul, recuperar, sensor, câmera, sdk, profundidade, rgb, imagens, cor, captura, resolução, tampão
ms.openlocfilehash: fed5c7340d287d9103ba35f0fd3d80c0fff6e3ec
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "87538919"
---
# <a name="retrieve-azure-kinect-image-data"></a>Recuperar dados de imagem do Azure Kinect

Esta página fornece detalhes sobre como recuperar imagens do Azure Kinect. O artigo demonstra como capturar e aceder a imagens coordenadas entre a cor e a profundidade do dispositivo. Para aceder às imagens, primeiro tem de abrir e configurar o dispositivo, para depois poder capturar imagens.
Antes de configurar e capturar uma imagem, tem de [encontrar e abrir o dispositivo.](find-then-open-device.md)

Também pode consultar o [Exemplo de Streaming SDK](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/streaming) que demonstra como utilizar as funções deste artigo.

As seguintes funções são abrangidas:

- [`k4a_device_start_cameras()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaad7a85e1e5471810262442fc4a8e217a.html#gaad7a85e1e5471810262442fc4a8e217a)
- [`k4a_device_get_capture()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga4dac757a33657f4d3dbf1ae8b21c158a.html#ga4dac757a33657f4d3dbf1ae8b21c158a)
- [`k4a_capture_get_depth_image()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gafa03513da96bf6b8c254fc11a04ee6d6.html#gafa03513da96bf6b8c254fc11a04ee6d6)
- [`k4a_image_get_buffer()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga2ef070cf4e543cd0f726478af332546e.html#ga2ef070cf4e543cd0f726478af332546e)
- [`k4a_image_release()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga27c81863b13fafc3934a32935a014e9f.html#ga27c81863b13fafc3934a32935a014e9f)
- [`k4a_capture_release()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga0ed6f74ec403c3eac1b8ef3afb32cee6.html#ga0ed6f74ec403c3eac1b8ef3afb32cee6)
- [`k4a_device_stop_cameras()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga4fa0e0a011a7105309ad97f081a5d6b8.html#ga4fa0e0a011a7105309ad97f081a5d6b8)
  
## <a name="configure-and-start-the-device"></a>Configurar e iniciar o dispositivo

As duas câmaras disponíveis no seu dispositivo Kinect suportam vários modos, resoluções e formatos de saída. Para obter uma lista completa, consulte as [especificações](hardware-specification.md)do Kit de Desenvolvimento da Azure Kinect .

A configuração de streaming é definida utilizando valores na [`k4a_device_configuration_t`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__device__configuration__t.html) estrutura.

```C
k4a_device_configuration_t config = K4A_DEVICE_CONFIG_INIT_DISABLE_ALL;
config.camera_fps = K4A_FRAMES_PER_SECOND_30;
config.color_format = K4A_IMAGE_FORMAT_COLOR_MJPG;
config.color_resolution = K4A_COLOR_RESOLUTION_2160P;
config.depth_mode = K4A_DEPTH_MODE_NFOV_UNBINNED;

if (K4A_RESULT_SUCCEEDED != k4a_device_start_cameras(device, &config))
{
    printf("Failed to start device\n");
    goto Exit;
}
```

Assim que as câmaras forem iniciadas, continuarão a capturar dados até [`k4a_device_stop_cameras()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga4fa0e0a011a7105309ad97f081a5d6b8.html#ga4fa0e0a011a7105309ad97f081a5d6b8) serem chamados ou o dispositivo estar fechado.

## <a name="stabilization"></a>Estabilização

Ao iniciar dispositivos utilizando a função de sincronização de vários dispositivos, é altamente recomendado fazê-lo utilizando uma definição de exposição fixa.
Com um conjunto de exposição manual, pode levar até oito capturas do dispositivo antes que as imagens e o framerate estabilizem. Com a exposição automática, pode levar até 20 capturas antes que as imagens e o framerate estabilizem.

## <a name="get-a-capture-from-the-device"></a>Obtenha uma captura do dispositivo

As imagens são captadas do dispositivo de forma correlacionada. Cada imagem capturada contém uma imagem de profundidade, uma imagem de IR, uma imagem a cores ou uma combinação de imagens.

Por predefinição, a API só devolverá uma captura depois de ter recebido todas as imagens solicitadas para o modo de streaming. Pode configurar a API para devolver capturas parciais apenas com imagens de profundidade ou cores assim que estiverem disponíveis, limpando o [`synchronized_images_only`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__device__configuration__t_a8208974f05d89fc1362c6a0900bdef4d.html#a8208974f05d89fc1362c6a0900bdef4d) parâmetro do [`k4a_device_configuration_t`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__device__configuration__t.html) .

```C
// Capture a depth frame
switch (k4a_device_get_capture(device, &capture, TIMEOUT_IN_MS))
{
case K4A_WAIT_RESULT_SUCCEEDED:
    break;
case K4A_WAIT_RESULT_TIMEOUT:
    printf("Timed out waiting for a capture\n");
    continue;
    break;
case K4A_WAIT_RESULT_FAILED:
    printf("Failed to read a capture\n");
    goto Exit;
}
```

Uma vez que a API tenha devolvido com sucesso uma captura, deve ligar [`k4a_capture_release()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga0ed6f74ec403c3eac1b8ef3afb32cee6.html#ga0ed6f74ec403c3eac1b8ef3afb32cee6) quando tiver concluído o objeto de captura.

## <a name="get-an-image-from-the-capture"></a>Obtenha uma imagem da captura

Para recuperar uma imagem capturada, ligue para a função adequada para cada tipo de imagem. Um dos seguintes:

- [`k4a_capture_get_color_image()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga683e440b5f22215a2de58d7fa140488c.html#ga683e440b5f22215a2de58d7fa140488c)
- [`k4a_capture_get_depth_image()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gafa03513da96bf6b8c254fc11a04ee6d6.html#gafa03513da96bf6b8c254fc11a04ee6d6)
- [`k4a_capture_get_ir_image()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga1531c3fa76a7c174b8f2eab24de91794.html#ga1531c3fa76a7c174b8f2eab24de91794)

Tem de recorrer [`k4a_image_release()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga27c81863b13fafc3934a32935a014e9f.html#ga27c81863b13fafc3934a32935a014e9f) a qualquer [`k4a_image_t`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__image__t.html) manípulo devolvido por estas funções assim que terminar de usar a imagem.

## <a name="access-image-buffers"></a>Tampão de imagem de acesso

[`k4a_image_t`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__image__t.html) tem muitas funções acessórias para obter propriedades da imagem.

Para aceder ao tampão de memória da imagem, utilize [k4a_image_get_buffer](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga2ef070cf4e543cd0f726478af332546e.html#ga2ef070cf4e543cd0f726478af332546e).

O exemplo a seguir demonstra como aceder a uma imagem de profundidade capturada. Este mesmo princípio aplica-se a outros tipos de imagem. No entanto, certifique-se de que substitui a variável do tipo de imagem pelo tipo de imagem correto, como o IR ou a cor.

```C
// Access the depth16 image
k4a_image_t image = k4a_capture_get_depth_image(capture);
if (image != NULL)
{
    printf(" | Depth16 res:%4dx%4d stride:%5d\n",
            k4a_image_get_height_pixels(image),
            k4a_image_get_width_pixels(image),
            k4a_image_get_stride_bytes(image));

    // Release the image
    k4a_image_release(image);
}

// Release the capture
k4a_capture_release(capture);
```

## <a name="next-steps"></a>Passos seguintes

Agora sabe capturar e coordenar as imagens das câmaras entre a cor e a profundidade, utilizando o seu dispositivo Azure Kinect. Também pode:

>[!div class="nextstepaction"]
>[Obter exemplos de IMU](retrieve-imu-samples.md)

>[!div class="nextstepaction"]
>[Microfones de acesso](access-mics.md)
