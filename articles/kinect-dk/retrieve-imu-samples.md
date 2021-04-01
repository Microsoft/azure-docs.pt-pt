---
title: Recuperar amostras de IMU de Azure Kinect
description: Saiba como recuperar amostras de IMU Azure Kinect utilizando o Azure Kinect SDK.
author: qm13
ms.author: cedmonds
ms.date: 06/26/2019
ms.prod: kinect-dk
ms.topic: conceptual
keywords: kinect, azul, configuração, profundidade, cor, RBG, câmara, sensor, sdk, IMU, sensor de movimento, movimento, giroscópio, giroscópio, acelerómetro, FPS
ms.openlocfilehash: 649dd5b9de62d43d59e74d53adff1ec7de8dfd32
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "85277498"
---
# <a name="retrieve-azure-kinect-imu-samples"></a>Recuperar amostras de IMU de Azure Kinect

O dispositivo Azure Kinect fornece acesso a Unidades de Movimento Inercial (IMUs), incluindo os tipos de acelerómetro e giroscópio. Para aceder às amostras de IMUs, primeiro tem de abrir e configurar o seu dispositivo e, em seguida, capturar dados da IMU. Para obter mais informações, consulte [o dispositivo find and open](find-then-open-device.md).

As amostras de IMU são geradas numa frequência muito mais elevada do que as imagens. As amostras são reportadas ao hospedeiro a um ritmo mais baixo do que são amostradas. Quando se espera por uma amostra de IMU, várias amostras serão frequentemente disponíveis ao mesmo tempo.

Consulte a especificação de [hardware](hardware-specification.md) DK Azure Kinect para obter detalhes sobre a taxa de reporte da IMU.

## <a name="configure-and-start-cameras"></a>Configurar e iniciar câmaras

> [!NOTE]
> Os sensores IMU só podem funcionar quando a cor e/ou as câmaras de profundidade estão a funcionar. Os sensores IMU não funcionam sozinhos.

Para ligar as câmaras, utilize [k4a_device_start_cameras()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaad7a85e1e5471810262442fc4a8e217a.html#gaad7a85e1e5471810262442fc4a8e217a).

```C
k4a_device_configuration_t config = K4A_DEVICE_CONFIG_INIT_DISABLE_ALL;
config.camera_fps = K4A_FRAMES_PER_SECOND_30;
config.color_format = K4A_IMAGE_FORMAT_COLOR_MJPG;
config.color_resolution = K4A_COLOR_RESOLUTION_2160P;

if (K4A_RESULT_SUCCEEDED != k4a_device_start_cameras(device, &config))
{
    printf("Failed to start cameras\n");
    goto Exit;
}

if (K4A_RESULT_SUCCEEDED != k4a_device_start_imu(device))
{
    printf("Failed to start imu\n");
    goto Exit;
}
```

## <a name="access-imu-samples"></a>Aceder amostras do IMU

 Cada [k4a_imu_sample_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__imu__sample__t.html#details) contém um acelerómetro e leitura de giroscópio capturado quase ao mesmo tempo.

Pode obter as amostras de IMU no mesmo fio que obtém capturas de imagem, ou em fios separados.

Para recuperar as amostras da IMU assim que estiverem disponíveis, é melhor ligar para [`k4a_device_get_imu_sample()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga8e5913b3bb94a453c7143bbd6e399a0e.html#ga8e5913b3bb94a453c7143bbd6e399a0e) o seu próprio fio. A API também tem filas internas suficientes para permitir que você apenas verifique amostras após cada captura de imagem ser devolvida.

Como há algumas filas internas de amostras de IMU, podes usar o seguinte padrão sem deixar cair quaisquer dados:

1. Aguarde uma captura, a qualquer taxa de fotogramas.
2. Processe a captura.
3. Recupere todas as amostras de IMU em fila.
4. Repita a espera da próxima captura.

Para recuperar todas as amostras de IMU atualmente em fila, pode ligar [`k4a_device_get_imu_sample()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga8e5913b3bb94a453c7143bbd6e399a0e.html#ga8e5913b3bb94a453c7143bbd6e399a0e) com um `timeout_in_ms` de 0 em loop até que a função retorne `K4A_WAIT_RESULT_TIMEOUT` . `K4A_WAIT_RESULT_TIMEOUT` indica que não há amostras em fila e nenhuma chegou no intervalo especificado.

## <a name="usage-example"></a>Exemplo de utilização

```C
k4a_imu_sample_t imu_sample;

// Capture a imu sample
switch (k4a_device_get_imu_sample(device, &imu_sample, TIMEOUT_IN_MS))
{
case K4A_WAIT_RESULT_SUCCEEDED:
    break;
case K4A_WAIT_RESULT_TIMEOUT:
    printf("Timed out waiting for a imu sample\n");
    continue;
    break;
case K4A_WAIT_RESULT_FAILED:
    printf("Failed to read a imu sample\n");
    goto Exit;
}

// Access the accelerometer readings
if (imu_sample != NULL)
{
    printf(" | Accelerometer temperature:%.2f x:%.4f y:%.4f z: %.4f\n",
            imu_sample.temperature,
            imu_sample.acc_sample.xyz.x,
            imu_sample.acc_sample.xyz.y,
            imu_sample.acc_sample.xyz.z);
}

```

## <a name="next-steps"></a>Passos seguintes

Agora sabe como trabalhar com amostras de IMU, também pode
>[!div class="nextstepaction"]
>[Aceder aos dados de entrada do microfone](access-mics.md)
