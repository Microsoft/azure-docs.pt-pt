---
title: Um preset para codificação consciente de conteúdos - Azure Media Services
description: Este artigo discute a codificação consciente do conteúdo no Microsoft Azure Media Services v3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 01/24/2020
ms.author: juliako
ms.custom: ''
ms.openlocfilehash: 3ea6c4226a59ba020a477cc5811033ff3dc3c2e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76772110"
---
# <a name="use-the-content-aware-encoding-preset-to-find-the-optimal-bitrate-value-for-a-given-resolution"></a>Utilize o predefinição de codificação consciente do conteúdo para encontrar o valor bitrate ideal para uma determinada resolução

Para preparar o conteúdo para entrega através de streaming de [bitrate adaptativo,](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)o vídeo precisa de ser codificado a várias bit-rates (alto a baixo). Isto garante uma degradação graciosa da qualidade, uma vez que o bitrate é reduzido, assim como a resolução do vídeo. Esta codificação de várias bits faz uso da chamada escada de codificação – uma tabela de resoluções e bitrates, ver os serviços de [comunicação incorporados presets](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#encodernamedpreset).

Deve estar ciente do conteúdo que está a processar e personalizar/afinar a escada de codificação para a complexidade do vídeo individual. Em cada resolução, há um bitrate para além do qual qualquer aumento de qualidade não é percetivo – o codificador opera a este valor bitrate ideal. O próximo nível de otimização é selecionar as resoluções com base no conteúdo – por exemplo, um vídeo de uma apresentação do PowerPoint não beneficia de ir abaixo dos 720p. Indo mais longe, o codificador pode ser incumbido de otimizar as definições para cada tiro dentro do vídeo. 

O predefinido de [Streaming Adaptativo](autogen-bitrate-ladder.md) da Microsoft aborda parcialmente o problema da variabilidade na qualidade e resolução dos vídeos de origem. Os nossos clientes têm uma mistura variada de conteúdo, alguns a 1080p, outros a 720p, e alguns em SD e resoluções mais baixas. Além disso, nem todos os conteúdos de origem são mezaninos de alta qualidade de estúdios de cinema ou tv. O predefinido de Streaming Adaptativo aborda estes problemas garantindo que a escada bitrate nunca excede a resolução ou a bitrate média do mezanino de entrada. No entanto, este preset não examina propriedades de origem que não a resolução e a bitrate.

## <a name="the-content-aware-encoding"></a>A codificação consciente do conteúdo 

O preconjunto de codificação consciente do conteúdo alarga o mecanismo de "streaming de bitrate adaptativo", incorporando uma lógica personalizada que permite ao codificador procurar o valor bitrate ideal para uma determinada resolução, mas sem necessitar de uma análise computacional extensiva. Este preset produz um conjunto de MP4 alinhados com gop. Dado qualquer conteúdo de entrada, o serviço realiza uma análise leve inicial do conteúdo de entrada, e utiliza os resultados para determinar o número ideal de camadas, bitrate apropriado e definições de resolução para entrega através de streaming adaptativo. Este preset é particularmente eficaz para vídeos de baixa e média complexidade, onde os ficheiros de saída estarão em bitrates mais baixos do que o pré-definido de Streaming Adaptativo, mas a uma qualidade que ainda oferece uma boa experiência aos espectadores. A saída conterá ficheiros MP4 com vídeo e áudio interleaved

Os gráficos de amostra seguem mostrar a comparação utilizando métricas de qualidade como [PSNR](https://en.wikipedia.org/wiki/Peak_signal-to-noise_ratio) e [VMAF](https://en.wikipedia.org/wiki/Video_Multimethod_Assessment_Fusion). A fonte foi criada através da concatenação de pequenos clips de imagens de alta complexidade de filmes e séries de TV, destinadas a realçar o codificador. Por definição, este preset produz resultados que variam de conteúdo para conteúdo – significa também que, para alguns conteúdos, pode não haver uma redução significativa do bitrate ou melhoria da qualidade.

![Curva de distorção de taxas (RD) utilizando PSNR](media/content-aware-encoding/msrv1.png)

**Figura 1: Curva de distorção de taxas (RD) utilizando métrica PSNR para fonte de alta complexidade**

![Curva de distorção de taxas (RD) utilizando VMAF](media/content-aware-encoding/msrv2.png)

**Figura 2: Curva de distorção de taxas (RD) utilizando a métrica VMAF para fonte de alta complexidade**

Abaixo estão os resultados de outra categoria de conteúdo de origem, onde o codificador foi capaz de determinar que a entrada era de má qualidade (muitos artefactos de compressão devido à baixa bitrate). Note que, com o predefinido consciente do conteúdo, o codificador decidiu produzir apenas uma camada de saída – a um bitrate suficientemente baixo para que a maioria dos clientes fosse capaz de jogar o stream sem empatar.

![Curva RD usando PSNR](media/content-aware-encoding/msrv3.png)

**Figura 3: Curva RD utilizando PSNR para entrada de baixa qualidade (a 1080p)**

![Curva RD usando VMAF](media/content-aware-encoding/msrv4.png)

**Figura 4: Curva RD utilizando VMAF para entrada de baixa qualidade (a 1080p)**

## <a name="how-to-use-the-content-aware-encoding-preset"></a>Como utilizar o predefinição de codificação consciente do conteúdo 

Pode criar transformações que utilizam este preset da seguinte forma. 

> [!TIP]
> Consulte a secção [de passos Seguintes](#next-steps) para tutoriais que utilizem saídas de transforme. O ativo de saída pode ser entregue a partir de pontos finais de streaming de Serviços de Media em protocolos como MPEG-DASH e HLS (como mostram os tutoriais).


```csharp
TransformOutput[] output = new TransformOutput[]
{
   new TransformOutput
   {
      // The preset for the Transform is set to one of Media Services built-in sample presets.
      // You can customize the encoding settings by changing this to use "StandardEncoderPreset" class.
      Preset = new BuiltInStandardEncoderPreset()
      {
         // This sample uses the new preset for content-aware encoding
         PresetName = EncoderNamedPreset.ContentAwareEncoding
      }
   }
};
```

> [!NOTE]
> Os trabalhos `ContentAwareEncoding` de codificação utilizando o preset estão a ser faturados com base nos minutos de saída. 

## <a name="next-steps"></a>Passos seguintes

* [Tutorial: Upload, codificação e streaming de vídeos com Media Services v3](stream-files-tutorial-with-api.md)
* [Tutorial: Codificar um ficheiro remoto com base em URL e transmitir o vídeo - REST](stream-files-tutorial-with-rest.md)
* [Tutorial: Codificar um ficheiro remoto com base no URL e transmitir o vídeo - CLI](stream-files-cli-quickstart.md)
* [Tutorial: Codificar um ficheiro remoto com base em URL e transmitir o vídeo - .NET](stream-files-dotnet-quickstart.md)
* [Tutorial: Codificar um ficheiro remoto com base em URL e transmitir o vídeo - Node.js](stream-files-nodejs-quickstart.md)
