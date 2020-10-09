---
title: Azure Kinect reprodução API
description: Aprenda a utilizar o Sensor Kinect Azure SDK para abrir um ficheiro de gravação utilizando a API de reprodução.
author: xthexder
ms.author: jawirth
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: kinect, azul, sensor, sdk, profundidade, rgb, record, reprodução, matroska, mkv
ms.openlocfilehash: fe403f314c1df415537d090433f34627eb1249e7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85277468"
---
# <a name="the-azure-kinect-playback-api"></a>A AZure Kinect reprodução API

O sensor SDK fornece uma API para registar dados do dispositivo a um ficheiro Matroska (.mkv). O formato do contentor Matroska armazena faixas de vídeo, amostras de IMU e calibração do dispositivo. As gravações podem ser geradas utilizando o utilitário de linha de comando [k4arecorder](record-sensor-streams-file.md) fornecido. As gravações também podem ser personalizadas e gravadas diretamente utilizando a API disco.

Para obter mais informações sobre a API de gravação, consulte [`k4a_record_create()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gae14f4181e9688e710d1c80b215413831.html#gae14f4181e9688e710d1c80b215413831) .

Para obter mais informações sobre as especificações do formato de ficheiro Matroska, consulte a página [de Formato de Ficheiro de Gravação.](record-file-format.md)

## <a name="use-the-playback-api"></a>Use a API de reprodução

Os ficheiros de gravação podem ser abertos utilizando a API de reprodução. A API de reprodução fornece acesso aos dados dos sensores no mesmo formato que o resto do sensor SDK.

### <a name="open-a-record-file"></a>Abrir um ficheiro de gravação

No exemplo seguinte, abrimos uma gravação [`k4a_playback_open()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gacb254ac941b2ab3c202ca68f4537f368.html#gacb254ac941b2ab3c202ca68f4537f368) utilizando, imprimamos o comprimento de gravação e, em seguida, fechamos o ficheiro com [`k4a_playback_close()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga76f415f2076f1c8c544e094a649306ff.html#ga76f415f2076f1c8c544e094a649306ff) .

```C
k4a_playback_t playback_handle = NULL;
if (k4a_playback_open("recording.mkv", &playback_handle) != K4A_RESULT_SUCCEEDED)
{
    printf("Failed to open recording\n");
    return 1;
}

uint64_t recording_length = k4a_playback_get_last_timestamp_usec(playback_handle);
printf("Recording is %lld seconds long\n", recording_length / 1000000);

k4a_playback_close(playback_handle);
```

### <a name="read-captures"></a>Ler capturas

Assim que o ficheiro estiver aberto, podemos começar a ler capturas a partir da gravação. Este próximo exemplo irá ler cada uma das capturas no ficheiro.

```C
k4a_capture_t capture = NULL;
k4a_stream_result_t result = K4A_STREAM_RESULT_SUCCEEDED;
while (result == K4A_STREAM_RESULT_SUCCEEDED)
{
    result = k4a_playback_get_next_capture(playback_handle, &capture);
    if (result == K4A_STREAM_RESULT_SUCCEEDED)
    {
        // Process capture here
        k4a_capture_release(capture);
    }
    else if (result == K4A_STREAM_RESULT_EOF)
    {
        // End of file reached
        break;
    }
}
if (result == K4A_STREAM_RESULT_FAILED)
{
    printf("Failed to read entire recording\n");
    return 1;
}
```

### <a name="seek-within-a-recording"></a>Procurar dentro de uma gravação

Assim que chegarmos ao fim do ficheiro, talvez queiramos voltar e lê-lo novamente. Este processo poderia ser feito lendo para trás com [`k4a_playback_get_previous_capture()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga54732e3aa0717e1ca4eb76ee385e878c.html#ga54732e3aa0717e1ca4eb76ee385e878c) , mas pode ser muito lento dependendo do comprimento da gravação.
Em vez disso, podemos usar a [`k4a_playback_seek_timestamp()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaea748994a121543bd77f90417cf428f6.html#gaea748994a121543bd77f90417cf428f6) função para ir a um ponto específico no ficheiro.

Neste exemplo, especificamos os tempos em microsegundos para procurar vários pontos no ficheiro.

```C
// Seek to the beginning of the file
if (k4a_playback_seek_timestamp(playback_handle, 0, K4A_PLAYBACK_SEEK_BEGIN) != K4A_RESULT_SUCCEEDED)
{
    return 1;
}

// Seek to the end of the file
if (k4a_playback_seek_timestamp(playback_handle, 0, K4A_PLAYBACK_SEEK_END) != K4A_RESULT_SUCCEEDED)
{
    return 1;
}

// Seek to 10 seconds from the start
if (k4a_playback_seek_timestamp(playback_handle, 10 * 1000000, K4A_PLAYBACK_SEEK_BEGIN) != K4A_RESULT_SUCCEEDED)
{
    return 1;
}

// Seek to 10 seconds from the end
if (k4a_playback_seek_timestamp(playback_handle, -10 * 1000000, K4A_PLAYBACK_SEEK_END) != K4A_RESULT_SUCCEEDED)
{
    return 1;
}
```

### <a name="read-tag-information"></a>Ler informações de etiquetas

As gravações também podem conter vários metadados, tais como o número de série do dispositivo e as versões de firmware. Estes metadados são armazenados em tags de gravação, que podem ser acedidas através da [`k4a_playback_get_tag()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga320f966fc89b4ba0d758f787f70d5143.html#ga320f966fc89b4ba0d758f787f70d5143) função.

```C
// Print the serial number of the device used to record
char serial_number[256];
size_t serial_number_size = 256;
k4a_buffer_result_t buffer_result = k4a_playback_get_tag(playback_handle, "K4A_DEVICE_SERIAL_NUMBER", &serial_number, &serial_number_size);
if (buffer_result == K4A_BUFFER_RESULT_SUCCEEDED)
{
    printf("Device serial number: %s\n", serial_number);
}
else if (buffer_result == K4A_BUFFER_RESULT_TOO_SMALL)
{
    printf("Device serial number too long.\n");
}
else
{
    printf("Tag does not exist. Device serial number was not recorded.\n");
}
```

### <a name="record-tag-list"></a>Lista de etiquetas de gravação

Abaixo está uma lista de todas as tags predefinidos que podem ser incluídas num ficheiro de gravação. Muitos destes valores estão disponíveis como parte da [`k4a_record_configuration_t`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__record__configuration__t.html) estrutura, e podem ser lidos com a [`k4a_playback_get_record_configuration()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaab54a85c1f1e98d170d009042b449255.html#gaab54a85c1f1e98d170d009042b449255) função.

Se uma etiqueta não existe, presume-se que tem o valor padrão.

| Nome da Etiqueta                     | Valor Predefinido      | [`k4a_record_configuration_t`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__record__configuration__t.html) Campo | Notas     |
|------------------------------|--------------------|--------------------------------------|----------------------------------------------------------------------------------------------------------------|
| `K4A_COLOR_MODE`             | "OFF"              | `color_format` / `color_resolution`  | Valores possíveis: "OFF", "MJPG_1080P", "NV12_720P", "YUY2_720P", e assim por diante                                      |
| `K4A_DEPTH_MODE`             | "OFF"              | `depth_mode` / `depth_track_enabled` | Valores possíveis: "OFF, "NFOV_UNBINNED", "PASSIVE_IR", e assim por diante                                                |
| `K4A_IR_MODE`                | "OFF"              | `depth_mode` / `ir_track_enabled`    | Valores possíveis: "OFF", "ATIVE", "PASSIVO"                                                                    |
| `K4A_IMU_MODE`               | "OFF"              | `imu_track_enabled`                  | Valores possíveis: "ON", "OFF"                                                                                   |
| `K4A_CALIBRATION_FILE`       | "calibration.jsligado" | N/D                                  | Ver [`k4a_device_get_raw_calibration()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga8c4e46642cee3115aeb0b33e2b43b24f.html#ga8c4e46642cee3115aeb0b33e2b43b24f) |
| `K4A_DEPTH_DELAY_NS`         | "0"                | `depth_delay_off_color_usec`         | Valor armazenado em nanossegundos, a API fornece microsegundos.                                                        |
| `K4A_WIRED_SYNC_MODE`        | "AUTÓNOMO"       | `wired_sync_mode`                    | Valores possíveis: "AUTÓNOMO", "MESTRE", "SUBORDINADO"                                                         |
| `K4A_SUBORDINATE_DELAY_NS`   | "0"                | `subordinate_delay_off_master_usec`  | Valor armazenado em nanossegundos, a API fornece microsegundos.                                                        |
| `K4A_COLOR_FIRMWARE_VERSION` | ""                 | N/D                                  | Versão de firmware de cor do dispositivo, por exemplo "1.x.xx"                                                            |
| `K4A_DEPTH_FIRMWARE_VERSION` | ""                 | N/D                                  | Versão de firmware de profundidade do dispositivo, por exemplo "1.x.xx"                                                            |
| `K4A_DEVICE_SERIAL_NUMBER`   | ""                 | N/D                                  | Número de série do dispositivo de gravação                                                                                 |
| `K4A_START_OFFSET_NS`        | "0"                | `start_timestamp_offset_usec`        | Consulte a [sincronização do timetamp](record-playback-api.md#timestamp-synchronization) abaixo.                       |
| `K4A_COLOR_TRACK`            | Nenhum               | N/D                                  | Ver [Formato de Ficheiro de Gravação - Identificação de faixas](record-file-format.md#identifying-tracks).                     |
| `K4A_DEPTH_TRACK`            | Nenhum               | N/D                                  | Ver [Formato de Ficheiro de Gravação - Identificação de faixas](record-file-format.md#identifying-tracks).                     |
| `K4A_IR_TRACK`               | Nenhum               | N/D                                  | Ver [Formato de Ficheiro de Gravação - Identificação de faixas](record-file-format.md#identifying-tracks).                     |
| `K4A_IMU_TRACK`              | Nenhum               | N/D                                  | Ver [Formato de Ficheiro de Gravação - Identificação de faixas](record-file-format.md#identifying-tracks).                     |

## <a name="timestamp-synchronization"></a>Sincronização do timetamp

O formato Matroska requer que as gravações comecem com um tempotando de zero. Ao [sincronizar as câmaras externamente,](record-external-synchronized-units.md)o primeiro tempo de tempo de cada dispositivo pode não ser zero.

Para preservar os intervalos de tempo originais dos dispositivos entre a gravação e a reprodução, o ficheiro armazena uma compensação para aplicar aos tempos.

A `K4A_START_OFFSET_NS` etiqueta é utilizada para especificar uma compensação de marca de tempo para que os ficheiros possam ser ressincronizados após a gravação. Esta compensação de tempo pode ser adicionada a cada timetamp no ficheiro para reconstruir os tempos do dispositivo original.

O offset inicial também está disponível na [`k4a_record_configuration_t`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__record__configuration__t.html) estrutura.
