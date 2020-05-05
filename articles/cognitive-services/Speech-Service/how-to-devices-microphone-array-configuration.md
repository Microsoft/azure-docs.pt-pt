---
title: Como configurar um Array de Microfone - Serviço de Discurso
titleSuffix: Azure Cognitive Services
description: Aprenda a configurar um Array de Microfone para que o SDK dos Dispositivos de Fala possa usá-lo.
services: cognitive-services
author: mswellsi
manager: yanbo
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/01/2020
ms.author: wellsi
ms.openlocfilehash: a2652bed6c8e7dec0a6fe8f9471793c3873646bf
ms.sourcegitcommit: 31236e3de7f1933be246d1bfeb9a517644eacd61
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82781759"
---
# <a name="how-to-configure-a-microphone-array"></a>Como configurar um Array de Microfone

Neste artigo, aprende-se a configurar uma matriz de [microfones](https://aka.ms/sdsdk-microphone). Inclui a definição do ângulo de trabalho e a forma de selecionar qual o microfone utilizado para o SDK dos Dispositivos de Fala.

O SDK de Dispositivos de Fala funciona melhor com uma matriz de microfone que foi desenhada de acordo com [as nossas diretrizes](https://aka.ms/sdsdk-microphone). A configuração da matriz do microfone pode ser fornecida pelo Sistema Operativo ou fornecida através de um dos seguintes métodos.

O SDK dos Dispositivos de Fala inicialmente suportava as matrizes do microfone selecionando a partir de um conjunto fixo de configurações.

```java
private static String DeviceGeometry = "Circular6+1"; // "Circular6+1", "Linear4",
private static String SelectedGeometry = "Circular6+1"; // "Circular6+1", "Circular3+1", "Linear4", "Linear2"
```

No Windows, a configuração da matriz do microfone é fornecida pelo controlador de áudio.

A partir de v1.11.0, o SDK dispositivos de fala também suporta a configuração a partir de um [ficheiro JSON](https://aka.ms/sdsdk-micarray-json).


## <a name="windows"></a>Windows
No Windows, a informação de geometria da matriz do microfone é obtida automaticamente a partir do controlador de áudio. Assim, as `DeviceGeometry`propriedades, `SelectedGeometry` `MicArrayGeometryConfigFile` e são opcionais. Utilizamos o [ficheiro JSON](https://aka.ms/sdsdk-micarray-json) fornecido utilizando `MicArrayGeometryConfigFile` apenas para obter o alcance de formação de feixes.

Se for especificado um `AudioConfig::FromMicrophoneInput`conjunto de microfones utilizando- utilize-se o microfone especificado. Se um microfone não `AudioConfig::FromDefaultMicrophoneInput` for especificado ou for chamado, utilizamos o microfone predefinido, especificado nas definições de Som no Windows.
A Microsoft Audio Stack no SDK dos Dispositivos de Fala apenas suporta a amostragem para baixo para taxas de amostra que são múltiplos integrais de 16 KHz.

## <a name="linux"></a>Linux
No Linux, a informação de geometria do microfone deve ser fornecida. A `DeviceGeometry` utilização `SelectedGeometry` e continua a ser apoiada. Também pode ser fornecido através do ficheiro `MicArrayGeometryConfigFile` JSON utilizando a propriedade. Semelhante ao Windows, a gama de feixes pode ser fornecida pelo ficheiro JSON.

Se for especificado um `AudioConfig::FromMicrophoneInput`conjunto de microfones utilizando- utilize-se o microfone especificado. Se um microfone não `AudioConfig::FromDefaultMicrophoneInput` for especificado ou for chamado, gravamos a partir do dispositivo ALSA chamado *padrão*. Por predefinição, o *predefinido* aponta sempre para o `asound.conf` dispositivo 0 do cartão 0, mas os utilizadores podem alterá-lo no ficheiro. 

A Microsoft Audio Stack no SDK dos Dispositivos de Fala apenas suporta a redução de amostragem para as taxas de amostra que são múltiplos integrais de 16 KHz. Adicionalmente, os seguintes formatos são suportados: boia de 32 bits IEEE pequeno, 32 pouco endian assinou int, 24 little endian assinou int, 16 little endian assinou int, e 8 bits assinado int.

## <a name="android"></a>Android
Atualmente, apenas [roobo v1](speech-devices-sdk-android-quickstart.md) é suportado pelo SDK dispositivos de fala. O comportamento é o mesmo `MicArrayGeometryConfigFile` que os lançamentos anteriores, exceto que agora a propriedade pode ser usada para especificar o ficheiro JSON contendo gama de feixes.

## <a name="microphone-array-configuration-json"></a>Configuração de configuração de matriz de microfone JSON

O ficheiro JSON para a configuração de geometria da matriz do microfone seguirá o [esquema JSON](https://aka.ms/sdsdk-micarray-json). Seguem-se alguns exemplos que seguem o esquema.


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


## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Escolha o seu dispositivo de fala](get-speech-devices-sdk.md)
