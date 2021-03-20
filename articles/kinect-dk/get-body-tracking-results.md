---
title: Azure Kinect obtém resultados de rastreio corporal
description: Aprenda a obter resultados de rastreio corporal usando o Azure Kinect Body Tracking SDK.
author: qm13
ms.prod: kinect-dk
ms.author: quentinm
ms.reviewer: yijwan
ms.date: 06/26/2019
ms.topic: conceptual
keywords: kinect, azul, sensor, sdk, corpo, rastreamento, articulação
ms.openlocfilehash: 1b62022242144d5db51455a32ac04b67c3e5dd7a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "85277559"
---
# <a name="get-body-tracking-results"></a>Obter resultados de deteção de movimentos

O Body Tracking SDK usa um objeto de rastreador corporal para processar capturas de DK do Azure Kinect e gera resultados de rastreio do corpo. Também mantém o estatuto global do rastreador, das filas de processamento e da fila de saída. Há três passos na utilização do localizador do corpo:

- Criar um localizador
- Capture profundidade e imagens de IR usando Azure Kinect DK
- Ensoar a captura e estalar os resultados.

## <a name="create-a-tracker"></a>Criar um localizador


O primeiro passo na utilização do rastreio do corpo é criar um localizador e requer a passagem na estrutura de calibração [k4a_calibration_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__calibration__t.html) sensor. A calibração do sensor pode ser consultada utilizando a função Azure Kinect Sensor SDK [k4a_device_get_calibration().](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga4e43940d8d8db48da266c7a7842c8d78.html#ga4e43940d8d8db48da266c7a7842c8d78)

```C
k4a_calibration_t sensor_calibration;
if (K4A_RESULT_SUCCEEDED != k4a_device_get_calibration(device, device_config.depth_mode, K4A_COLOR_RESOLUTION_OFF, &sensor_calibration))
{
    printf("Get depth camera calibration failed!\n");
    return 0;
}

k4abt_tracker_t tracker = NULL;
k4abt_tracker_configuration_t tracker_config = K4ABT_TRACKER_CONFIG_DEFAULT;
if (K4A_RESULT_SUCCEEDED != k4abt_tracker_create(&sensor_calibration, tracker_config, &tracker))
{
    printf("Body tracker initialization failed!\n");
    return 0;
}
```

## <a name="capture-depth-and-ir-images"></a>Captar profundidade e imagens irs

A captura de imagens utilizando o DK Azure Kinect é coberta na página [de imagens de recuperação.](retrieve-images.md)

>[!NOTE]
> `K4A_DEPTH_MODE_NFOV_UNBINNED` ou `K4A_DEPTH_MODE_WFOV_2X2BINNED` os modos são recomendados para o melhor desempenho e precisão. Não utilize os `K4A_DEPTH_MODE_OFF` modos ou `K4A_DEPTH_MODE_PASSIVE_IR` modos.

Os modos DK Azure Kinect suportados são descritos na especificação de [hardware](hardware-specification.md) DK Azure Kinect e [k4a_depth_mode_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___enumerations_ga3507ee60c1ffe1909096e2080dd2a05d.html#ga3507ee60c1ffe1909096e2080dd2a05d) enumerações.

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

## <a name="enqueue-the-capture-and-pop-the-results"></a>Enquesa a captura e pop os resultados

O rastreador mantém internamente uma fila de entrada e uma fila de saída para processar assíncronamente a captura do DK Azure Kinect de forma mais eficiente. Utilize a função [k4abt_tracker_enqueue_capture](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_ga093becd9bb4a63f5f4d56f58097a7b1e.html#ga093becd9bb4a63f5f4d56f58097a7b1e) para adicionar uma nova captura à fila de entrada. Utilize a [função k4abt_tracker_pop_result()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_gaaf446fb1579cbbe0b6af824ee0a7458b.html#gaaf446fb1579cbbe0b6af824ee0a7458b) o pop um resultado da fila de saída. A utilização do valor de tempo limite depende da aplicação e controla o tempo de espera da fila.

### <a name="real-time-processing"></a>Processamento em tempo real
Utilize este padrão para aplicações de rosca única que precisam de resultados em tempo real e podem acomodar quadros caídos. A `simple_3d_viewer` amostra localizada em [GitHub Azure-Kinect-Samples](https://github.com/microsoft/Azure-Kinect-Samples) é um exemplo de processamento em tempo real.

```C
k4a_wait_result_t queue_capture_result = k4abt_tracker_enqueue_capture(tracker, sensor_capture, 0);
k4a_capture_release(sensor_capture); // Remember to release the sensor capture once you finish using it
if (queue_capture_result == K4A_WAIT_RESULT_FAILED)
{
    printf("Error! Adding capture to tracker process queue failed!\n");
    break;
}

k4abt_frame_t body_frame = NULL;
k4a_wait_result_t pop_frame_result = k4abt_tracker_pop_result(tracker, &body_frame, 0);
if (pop_frame_result == K4A_WAIT_RESULT_SUCCEEDED)
{
    // Successfully popped the body tracking result. Start your processing
    ...

    k4abt_frame_release(body_frame); // Remember to release the body frame once you finish using it
}
```

### <a name="synchronous-processing"></a>Processamento sincronizado
Utilize este padrão para aplicações que não necessitam de resultados em tempo real ou que não podem acomodar quadros caídos.

A produção de processamento pode ser limitada.

A `simple_sample.exe` amostra localizada em [GitHub Azure-Kinect-Samples](https://github.com/microsoft/Azure-Kinect-Samples) é um exemplo de processamento sincronizado.

```C
k4a_wait_result_t queue_capture_result = k4abt_tracker_enqueue_capture(tracker, sensor_capture, K4A_WAIT_INFINITE);
k4a_capture_release(sensor_capture); // Remember to release the sensor capture once you finish using it
if (queue_capture_result != K4A_WAIT_RESULT_SUCCEEDED)
{
    // It should never hit timeout or error when K4A_WAIT_INFINITE is set.
    printf("Error! Adding capture to tracker process queue failed!\n");
    break;
}

k4abt_frame_t body_frame = NULL;
k4a_wait_result_t pop_frame_result = k4abt_tracker_pop_result(tracker, &body_frame, K4A_WAIT_INFINITE);
if (pop_frame_result != K4A_WAIT_RESULT_SUCCEEDED)
{
    // It should never hit timeout or error when K4A_WAIT_INFINITE is set.
    printf("Error! Popping body tracking result failed!\n");
    break;
}
// Successfully popped the body tracking result. Start your processing
...

k4abt_frame_release(body_frame); // Remember to release the body frame once you finish using it
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
>[Aceder aos dados na moldura do corpo](access-data-body-frame.md)
