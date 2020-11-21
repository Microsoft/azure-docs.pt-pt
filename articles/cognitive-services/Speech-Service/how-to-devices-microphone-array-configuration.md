---
title: Como configurar um microfone array - Serviço de Fala
titleSuffix: Azure Cognitive Services
description: Aprenda a configurar um Conjunto de Microfones para que os Dispositivos de Fala SDK possam usá-lo.
services: cognitive-services
author: mswellsi
manager: yanbo
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/01/2020
ms.author: wellsi
ms.openlocfilehash: cf0580c96f5bf78f0444b2bb39088f2a417fd658
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2020
ms.locfileid: "95025066"
---
# <a name="how-to-configure-a-microphone-array"></a>Como configurar uma Matriz do Microfone

Neste artigo, aprende-se a configurar uma [matriz de microfone](./speech-devices-sdk-microphone.md). Inclui a definição do ângulo de trabalho e como selecionar o microfone utilizado para os Dispositivos de Fala SDK.

O Speech Devices SDK funciona melhor com uma matriz de microfone que foi desenhada de acordo com [as nossas diretrizes.](./speech-devices-sdk-microphone.md) A configuração da matriz do microfone pode ser fornecida pelo Sistema Operativo ou fornecida através de um dos seguintes métodos.

Os dispositivos de fala SDK inicialmente suportavam as matrizes de microfone selecionando a partir de um conjunto fixo de configurações.

```java
private static String DeviceGeometry = "Circular6+1"; // "Circular6+1", "Linear4",
private static String SelectedGeometry = "Circular6+1"; // "Circular6+1", "Circular3+1", "Linear4", "Linear2"
```

No Windows, a configuração da matriz do microfone é fornecida pelo controlador de áudio.

A partir de v1.11.0, o SDK dos Dispositivos de Fala também suporta a configuração a partir de um [ficheiro JSON](https://aka.ms/sdsdk-micarray-json).


## <a name="windows"></a>Windows
No Windows, a informação de geometria da matriz do microfone é obtida automaticamente a partir do controlador de áudio. Assim, as `DeviceGeometry`  `SelectedGeometry` propriedades, e `MicArrayGeometryConfigFile` são opcionais. Utilizamos o [ficheiro JSON](https://aka.ms/sdsdk-micarray-json) fornecido para `MicArrayGeometryConfigFile` obter apenas a gama de formação de feixes.

Se for especificada uma matriz de `AudioConfig::FromMicrophoneInput` microfone, utilizamos o microfone especificado. Se um microfone não for especificado ou `AudioConfig::FromDefaultMicrophoneInput` for chamado, utilizamos o microfone predefinido, especificado nas definições de som no Windows.
A Microsoft Audio Stack nos Dispositivos de Fala SDK só suporta amostras para taxas de amostra que são múltiplos integrais de 16 KHz.

## <a name="linux"></a>Linux
No Linux, a informação sobre geometria do microfone deve ser fornecida. O uso `DeviceGeometry` e `SelectedGeometry` os restos mortais apoiados. Também pode ser fornecido através do ficheiro JSON usando a `MicArrayGeometryConfigFile` propriedade. Semelhante ao Windows, a gama de formas de feixe pode ser fornecida pelo ficheiro JSON.

Se for especificada uma matriz de `AudioConfig::FromMicrophoneInput` microfone, utilizamos o microfone especificado. Se um microfone não for especificado ou `AudioConfig::FromDefaultMicrophoneInput` for chamado, gravamos a partir do dispositivo ALSA nomeado *predefinido*. Por predefinição, *o predefinição* aponta sempre para o dispositivo 0 do cartão 0, mas os utilizadores podem alterá-lo no `asound.conf` ficheiro. 

A Microsoft Audio Stack nos Dispositivos de Fala SDK só suporta a amostra para taxas de amostra que são múltiplos integrais de 16 KHz. Além disso, os seguintes formatos são suportados: 32 bits IEEE little endian float, 32-bit pequeno endian assinado int, 24-bit pequeno endian assinado int, 16-bit pouco endian assinado int, e 8-bit assinado int.

## <a name="android"></a>Android
Atualmente apenas [Roobo v1](./speech-devices-sdk-quickstart.md?pivots=platform-android%253fpivots%253dplatform-android) é suportado pelo Speech Devices SDK. O comportamento é o mesmo que as versões anteriores, exceto agora `MicArrayGeometryConfigFile` que a propriedade pode ser usada para especificar o ficheiro JSON que contém alcance de formação de feixes.

## <a name="microphone-array-configuration-json"></a>Configuração da matriz do microfone JSON

O ficheiro JSON para configuração de geometria da matriz do microfone seguirá o [esquema JSON](https://aka.ms/sdsdk-micarray-json). Seguem-se alguns exemplos que seguem o esquema.


```json
{
    "micArrayType": "Linear",
    "geometry": "Linear4"
}
```


Ou


```json
{
    "micArrayType": "Planar",
    "horizontalAngleBegin": 0,
    "horizontalAngleEnd": 360,
    "numberOfMicrophones": 4,
    "micCoord": [
        {
            "xCoord": 0,
            "yCoord": 0,
            "zCoord": 0
        },
        {
            "xCoord": 40,
            "yCoord": 0,
            "zCoord": 0
        },
        {
            "xCoord": -20,
            "yCoord": -35,
            "zCoord": 0
        },
        {
            "xCoord": -20,
            "yCoord": 35,
            "zCoord": 0
        }
    ]
}
```

Ou

```json
{
    "micArrayType": "Linear",
    "horizontalAngleBegin": 70,
    "horizontalAngleEnd": 110
}
```


## <a name="next-steps"></a>Próximos passos

> [!div class="nextstepaction"]
> [Escolha o seu dispositivo de fala](get-speech-devices-sdk.md)