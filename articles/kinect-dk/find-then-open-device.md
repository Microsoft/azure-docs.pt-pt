---
title: Encontre então abra o dispositivo Azure Kinect
description: Aprenda a encontrar e abrir um dispositivo Azure Kinect utilizando o Azure Kinect Senor SDK.
author: cedmonds
ms.author: cedmonds
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: kinect, azul, sensor, sdk, profundidade, rgb, dispositivo, encontrar, abrir
ms.openlocfilehash: 67fc93b924d5d663bb43098969c54d1975bd5895
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85277474"
---
# <a name="find-then-open-the-azure-kinect-device"></a>Encontre então abra o dispositivo Azure Kinect

Este artigo descreve como pode encontrar e, em seguida, abra o seu Azure Kinect DK. O artigo explica como lidar com a caixa onde existem vários dispositivos ligados à sua máquina.

Também pode consultar o [Exemplo enumerado SDK](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/enumerate) que demonstra como utilizar as funções deste artigo.

As seguintes funções são abrangidas:
 * [`k4a_device_get_installed_count()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaf7d19df0f73f8e4dfaa21e1b4b719ecc.html#gaf7d19df0f73f8e4dfaa21e1b4b719ecc)
 * [`k4a_device_open()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga3d4eb5dfbf4d576d4978b66ea419f113.html#ga3d4eb5dfbf4d576d4978b66ea419f113)
 * [`k4a_device_get_serialnum()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga798489af207ff1c99f2285ff6b08bc22.html#ga798489af207ff1c99f2285ff6b08bc22)
 * [`k4a_device_close()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga7a3931d9a690b3971caaac83b43f9423.html#ga7a3931d9a690b3971caaac83b43f9423)

## <a name="discover-the-number-of-connected-devices"></a>Descubra o número de dispositivos conectados

Primeiro obtenha a contagem de dispositivos Azure Kinect atualmente ligados usando [`k4a_device_get_installed_count()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaf7d19df0f73f8e4dfaa21e1b4b719ecc.html#gaf7d19df0f73f8e4dfaa21e1b4b719ecc) .

```C
uint32_t device_count = k4a_device_get_installed_count();

printf("Found %d connected devices:\n", device_count);
```

## <a name="open-a-device"></a>Abra um dispositivo

Para obter informações sobre um dispositivo, ou para ler dados dele, é necessário abrir primeiro uma pega ao dispositivo utilizando [`k4a_device_open()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga3d4eb5dfbf4d576d4978b66ea419f113.html#ga3d4eb5dfbf4d576d4978b66ea419f113) .

```C
k4a_device_t device = NULL;

for (uint8_t deviceIndex = 0; deviceIndex < device_count; deviceIndex++)
{
    if (K4A_RESULT_SUCCEEDED != k4a_device_open(deviceIndex, &device))
    {
        printf("%d: Failed to open device\n", deviceIndex);
        continue;
    }

    ...

    k4a_device_close(device);
}
```

O `index` parâmetro indica qual o dispositivo a abrir se houver mais de um [`k4a_device_open()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga3d4eb5dfbf4d576d4978b66ea419f113.html#ga3d4eb5dfbf4d576d4978b66ea419f113) ligado. Se esperar apenas que um único dispositivo esteja ligado, pode passar um argumento de `K4A_DEVICE_DEFAULT` ou 0 para indicar o primeiro dispositivo.

Sempre que abrir um dispositivo, tem de ligar [`k4a_device_close()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga7a3931d9a690b3971caaac83b43f9423.html#ga7a3931d9a690b3971caaac83b43f9423) quando terminar de usar a pega. Não podem ser abertas outras pegas para o mesmo dispositivo até que tenha fechado a pega.

## <a name="identify-a-specific-device"></a>Identificar um dispositivo específico

Os dispositivos de encomenda enumerados por índice não serão alterados até que os dispositivos sejam ligados ou separados. Para identificar um dispositivo físico, deve utilizar o número de série do dispositivo.

Para ler o número de série do dispositivo, utilize a [`k4a_device_get_serialnum()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga798489af207ff1c99f2285ff6b08bc22.html#ga798489af207ff1c99f2285ff6b08bc22) função depois de ter aberto uma pega.

Este exemplo demonstra como alocar a quantidade certa de memória para armazenar o número de série.

```C
char *serial_number = NULL;
size_t serial_number_length = 0;

if (K4A_BUFFER_RESULT_TOO_SMALL != k4a_device_get_serialnum(device, NULL, &serial_number_length))
{
    printf("%d: Failed to get serial number length\n", deviceIndex);
    k4a_device_close(device);
    device = NULL;
    continue;
}

serial_number = malloc(serial_number_length);
if (serial_number == NULL)
{
    printf("%d: Failed to allocate memory for serial number (%zu bytes)\n", deviceIndex, serial_number_length);
    k4a_device_close(device);
    device = NULL;
    continue;
}

if (K4A_BUFFER_RESULT_SUCCEEDED != k4a_device_get_serialnum(device, serial_number, &serial_number_length))
{
    printf("%d: Failed to get serial number\n", deviceIndex);
    free(serial_number);
    serial_number = NULL;
    k4a_device_close(device);
    device = NULL;
    continue;
}

printf("%d: Device \"%s\"\n", deviceIndex, serial_number);
```

## <a name="open-the-default-device"></a>Abra o dispositivo predefinido

Na maioria das aplicações, haverá apenas um único Azure Kinect DK ligado ao mesmo computador. Se precisar apenas de se ligar ao único dispositivo esperado, pode ligar [`k4a_device_open()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga3d4eb5dfbf4d576d4978b66ea419f113.html#ga3d4eb5dfbf4d576d4978b66ea419f113) para abrir o primeiro `index` `K4A_DEVICE_DEFAULT` dispositivo.

```C
k4a_device_t device = NULL;
uint32_t device_count = k4a_device_get_installed_count();

if (device_count != 1)
{
    printf("Unexpected number of devices found (%d)\n", device_count);
    goto Exit;
}

if (K4A_RESULT_SUCCEEDED != k4a_device_open(K4A_DEVICE_DEFAULT, &device))
{
    printf("Failed to open device\n");
    goto Exit;
}
```

## <a name="next-steps"></a>Passos seguintes

>[!div class="nextstepaction"]
>[Recuperar imagens](retrieve-images.md)

>[!div class="nextstepaction"]
>[Recuperar amostras de IMU](retrieve-imu-samples.md)

