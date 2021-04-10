---
title: Capture Azure Kinect device synchronization
description: Aprenda a sincronizar dispositivos de captura Azure Kinect utilizando o Sensor Azure Kinect SDK.
author: xthexder
ms.author: jawirth
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: kinect, azul, sensor, sdk, profundidade, rgb, interno, externo, sincronização, cadeia de margaridas, offset de fase
ms.openlocfilehash: ce0c72d3d708d5696a9775b3885f278f0c23cac1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102034320"
---
# <a name="capture-azure-kinect-device-synchronization"></a>Capture Azure Kinect device synchronization

O hardware Azure Kinect pode alinhar o tempo de captura de imagens de cor e profundidade. O alinhamento entre as câmaras no mesmo dispositivo é **a sincronização interna**. O alinhamento do tempo de captura entre vários dispositivos conectados é **sincronização externa**. A matriz do microfone funciona independentemente das câmaras de cor e profundidade.

## <a name="device-internal-synchronization"></a>Sincronização interna do dispositivo

A captura de imagens entre as câmaras individuais é sincronizada em hardware. Em cada [k4a_capture_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__capture__t.html) que contém imagens do sensor de cor e profundidade, os timetamps das imagens estão alinhados com base no modo de funcionamento do hardware. Por defeito, as imagens de uma captura são o centro de exposição alinhado. O tempo relativo de capturas de profundidade e cor pode ser ajustado utilizando o `depth_delay_off_color_usec` campo de [k4a_device_configuration_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__device__configuration__t.html).

## <a name="device-external-synchronization"></a>Sincronização externa do dispositivo

Consulte [a sincronização externa de configuração](https://support.microsoft.com/help/4494429/sync-multiple-azure-kinect-dk-devices) para configuração de hardware.

O software de cada dispositivo ligado deve ser configurado para funcionar num modo **principal** ou **subordinado.** Esta definição está configurada no [k4a_device_configuration_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__device__configuration__t.html).

Ao utilizar a sincronização externa, as câmaras subordinadas devem ser sempre iniciadas antes que o comandante se alinhe corretamente.

### <a name="subordinate-mode"></a>Modo subordinado

```C
k4a_device_configuration_t deviceConfig;
deviceConfig.wired_sync_mode = K4A_WIRED_SYNC_MODE_SUBORDINATE
```

### <a name="master-mode"></a>Modo master

```C
k4a_device_configuration_t deviceConfig;
deviceConfig.wired_sync_mode = K4A_WIRED_SYNC_MODE_MASTER;
```

### <a name="retrieving-synchronization-jack-state"></a>Recuperação do estado do jack de sincronização

Para recuperar programáticamente o estado atual da entrada de sincronização e tomadas de saída de sincronização, utilize a função [k4a_device_get_sync_jack.](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga0209ac87bfd055163677321b0304e962.html#ga0209ac87bfd055163677321b0304e962)

## <a name="next-steps"></a>Passos seguintes

Agora sabe como ativar e capturar a sincronização do dispositivo. Também pode rever como usar 

>[!div class="nextstepaction"]
>[Azure Kinect sensor SDK grava e reproduz a API](record-playback-api.md)
