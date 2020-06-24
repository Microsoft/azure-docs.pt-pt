---
title: Quickstart- Construa a sua primeira aplicação Azure Kinect
description: Este quickstart guia o utilizador Azure Kinect DK através do processo de criação de uma nova aplicação.
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: quickstart
keywords: kinect, azul, sensor, sdk, microfone, microfones de acesso, dados de microfone
ms.openlocfilehash: 3632145b3f3b63023e0c66e3cf99903231802edf
ms.sourcegitcommit: fe6c9a35e75da8a0ec8cea979f9dec81ce308c0e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "85277972"
---
# <a name="quickstart-build-your-first-azure-kinect-application"></a>Quickstart: Construa a sua primeira aplicação Azure Kinect

Começar com o Azure Kinect DK? Este arranque rápido vai pôr-te a funcionar com o dispositivo!

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

As seguintes funções são abrangidas:

- [`k4a_device_get_installed_count()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaf7d19df0f73f8e4dfaa21e1b4b719ecc.html#gaf7d19df0f73f8e4dfaa21e1b4b719ecc)
- [`k4a_device_open()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga3d4eb5dfbf4d576d4978b66ea419f113.html#ga3d4eb5dfbf4d576d4978b66ea419f113)
- [`k4a_device_get_serialnum()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga798489af207ff1c99f2285ff6b08bc22.html#ga798489af207ff1c99f2285ff6b08bc22)
- [`k4a_device_start_cameras()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaad7a85e1e5471810262442fc4a8e217a.html#gaad7a85e1e5471810262442fc4a8e217a)
- [`k4a_device_stop_cameras()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga4fa0e0a011a7105309ad97f081a5d6b8.html#ga4fa0e0a011a7105309ad97f081a5d6b8)
- [`k4a_device_close()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga7a3931d9a690b3971caaac83b43f9423.html#ga7a3931d9a690b3971caaac83b43f9423)

## <a name="prerequisites"></a>Pré-requisitos

1. [Configurar o dispositivo Azure Kinect DK](set-up-azure-kinect-dk.md).
2. [Faça o download](sensor-sdk-download.md) e instale o Sensor Kinect Azure SDK.

## <a name="headers"></a>Cabeçalhos

Só precisas de um cabeçalho, e isso `k4a.h` é... Certifique-se de que o seu compilador de eleição está configurado com a lib do SDK e inclua pastas. Também vai precisar dos `k4a.lib` `k4a.dll` ficheiros ligados. Talvez queira consultar a [adição da biblioteca Azure Kinect ao seu projeto.](add-library-to-project.md)

```C
#include <k4a/k4a.h>
```

## <a name="finding-an-azure-kinect-dk-device"></a>Encontrar um dispositivo DK Azure Kinect

Vários dispositivos DK Azure Kinect podem ser ligados ao seu computador. Primeiro começaremos por descobrir quantos, ou se algum está ligado usando a [`k4a_device_get_installed_count()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaf7d19df0f73f8e4dfaa21e1b4b719ecc.html#gaf7d19df0f73f8e4dfaa21e1b4b719ecc) função. Esta função deve funcionar imediatamente, sem qualquer configuração adicional.

```C
uint32_t count = k4a_device_get_installed_count();
```

Uma vez determinado que há um dispositivo ligado ao computador, pode abri-lo [`k4a_device_open()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga3d4eb5dfbf4d576d4978b66ea419f113.html#ga3d4eb5dfbf4d576d4978b66ea419f113) utilizando. Pode fornecer o índice do dispositivo que pretende abrir, ou pode apenas usar `K4A_DEVICE_DEFAULT` para o primeiro.

```C
// Open the first plugged in Kinect device
k4a_device_t device = NULL;
k4a_device_open(K4A_DEVICE_DEFAULT, &device);
```
Como a maioria das coisas na biblioteca Azure Kinect, quando se abre algo, também deve fechá-lo quando terminar! Quando desligar, lembre-se de fazer uma ligação [`k4a_device_close()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga7a3931d9a690b3971caaac83b43f9423.html#ga7a3931d9a690b3971caaac83b43f9423) para.

```C
k4a_device_close(device);
```

Assim que o dispositivo estiver aberto, podemos fazer um teste para garantir que está a funcionar. Vamos ler o número de série do dispositivo!

```C
// Get the size of the serial number
size_t serial_size = 0;
k4a_device_get_serialnum(device, NULL, &serial_size);

// Allocate memory for the serial, then acquire it
char *serial = (char*)(malloc(serial_size));
k4a_device_get_serialnum(device, serial, &serial_size);
printf("Opened device: %s\n", serial);
free(serial);
```

## <a name="starting-the-cameras"></a>A ligar as câmaras

Uma vez aberto o dispositivo, terá de configurar a câmara com um [`k4a_device_configuration_t`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__device__configuration__t.html) objeto. A configuração da câmara tem várias opções diferentes. Escolha as definições que melhor se adaptem ao seu próprio cenário.

```C
// Configure a stream of 4096x3072 BRGA color data at 15 frames per second
k4a_device_configuration_t config = K4A_DEVICE_CONFIG_INIT_DISABLE_ALL;
config.camera_fps       = K4A_FRAMES_PER_SECOND_15;
config.color_format     = K4A_IMAGE_FORMAT_COLOR_BGRA32;
config.color_resolution = K4A_COLOR_RESOLUTION_3072P;

// Start the camera with the given configuration
k4a_device_start_cameras(device, &config);

// ...Camera capture and application specific code would go here...

// Shut down the camera when finished with application logic
k4a_device_stop_cameras(device);
```

## <a name="error-handling"></a>Processamento de erros

Por uma questão de brevidade e clareza, não mostramos manipulação de erros em alguns exemplos inline. No entanto, o manuseamento de erros é sempre importante! Muitas funções retornarão um tipo geral de sucesso/falha, [`k4a_result_t`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___enumerations_ga4b419a99aa2220b076a4520dc2afd1e5.html#ga4b419a99aa2220b076a4520dc2afd1e5) ou uma variante mais específica com informações detalhadas tais como [`k4a_wait_result_t`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___enumerations_ga44c7c0c1cfba7c879e9e2da1a869e4ee.html#ga44c7c0c1cfba7c879e9e2da1a869e4ee) . Consulte os docs ou o IntelliSense para cada função para ver que mensagens de erro deve esperar ver dela!

Pode utilizar as [`K4A_SUCCEEDED`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___macros_ga8e5b48150bc243c6052793bd830c2fcd.html#ga8e5b48150bc243c6052793bd830c2fcd) macros e [`K4A_FAILED`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___macros_ga7c2e32349135d008b6f836c571d434b4.html#ga7c2e32349135d008b6f836c571d434b4) as macros para verificar o resultado de uma função. Então, em vez de apenas abrir um dispositivo Azure Kinect DK, podemos guardar a chamada de função como esta:

```C
// Open the first plugged in Kinect device
k4a_device_t device = NULL;
if ( K4A_FAILED( k4a_device_open(K4A_DEVICE_DEFAULT, &device) ) )
{
    printf("Failed to open k4a device!\n");
    return;
}
```

## <a name="full-source"></a>Fonte completa

```C
#pragma comment(lib, "k4a.lib")
#include <k4a/k4a.h>

#include <stdio.h>
#include <stdlib.h>

int main()
{
    uint32_t count = k4a_device_get_installed_count();
    if (count == 0)
    {
        printf("No k4a devices attached!\n");
        return 1;
    }

    // Open the first plugged in Kinect device
    k4a_device_t device = NULL;
    if (K4A_FAILED(k4a_device_open(K4A_DEVICE_DEFAULT, &device)))
    {
        printf("Failed to open k4a device!\n");
        return 1;
    }

    // Get the size of the serial number
    size_t serial_size = 0;
    k4a_device_get_serialnum(device, NULL, &serial_size);

    // Allocate memory for the serial, then acquire it
    char *serial = (char*)(malloc(serial_size));
    k4a_device_get_serialnum(device, serial, &serial_size);
    printf("Opened device: %s\n", serial);
    free(serial);

    // Configure a stream of 4096x3072 BRGA color data at 15 frames per second
    k4a_device_configuration_t config = K4A_DEVICE_CONFIG_INIT_DISABLE_ALL;
    config.camera_fps       = K4A_FRAMES_PER_SECOND_15;
    config.color_format     = K4A_IMAGE_FORMAT_COLOR_BGRA32;
    config.color_resolution = K4A_COLOR_RESOLUTION_3072P;

    // Start the camera with the given configuration
    if (K4A_FAILED(k4a_device_start_cameras(device, &config)))
    {
        printf("Failed to start cameras!\n");
        k4a_device_close(device);
        return 1;
    }

    // Camera capture and application specific code would go here

    // Shut down the camera when finished with application logic
    k4a_device_stop_cameras(device);
    k4a_device_close(device);

    return 0;
}

```

## <a name="next-steps"></a>Passos seguintes

Saiba como encontrar e abrir um dispositivo DK Azure Kinect utilizando o Sensor SDK
> [!div class="nextstepaction"]
>[Encontre e abra um dispositivo](find-then-open-device.md)
