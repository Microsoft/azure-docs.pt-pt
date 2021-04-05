---
title: Predefinição para codificação consciente do conteúdo
description: Este artigo discute a codificação de conteúdos na Microsoft Azure Media Services v3.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: ce18e71ced320c408933caeb39b469d5885bd6ba
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101095937"
---
# <a name="use-the-content-aware-encoding-preset-to-find-the-optimal-bitrate-value-for-a-given-resolution"></a>Utilize a predefinição de codificação consciente do conteúdo para encontrar o valor bitrate ideal para uma determinada resolução

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Para preparar o conteúdo para a entrega através do [streaming de bitrate adaptativo,](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)o vídeo precisa de ser codificado a várias taxas de bit (de alto a baixo). Isto garante uma degradação graciosa da qualidade, uma vez que o bitrate é reduzido assim como a resolução do vídeo. Esta codificação de bit-rate múltipla faz uso de uma chamada escada codificadora – uma tabela de resoluções e bitrates, ver as [predefinições codificadoras dos](/rest/api/media/transforms/createorupdate#encodernamedpreset)Media Services.

Deve estar ciente do conteúdo que está a processar e personalizar/afinar a escada de codificação para a complexidade do vídeo individual. Em cada resolução, há um bitrate para além do qual qualquer aumento de qualidade não é percetivo – o codificador opera a este valor bitrate ideal. O próximo nível de otimização é selecionar as resoluções com base no conteúdo – por exemplo, um vídeo de uma apresentação do PowerPoint não beneficia de ir abaixo dos 720p. Indo mais longe, o codificador pode ser incumbido de otimizar as definições para cada filmagem dentro do vídeo. 

A predefinição de [Streaming Adaptativo](autogen-bitrate-ladder.md) da Microsoft aborda parcialmente o problema da variabilidade na qualidade e resolução dos vídeos de origem. Os nossos clientes têm uma mistura variada de conteúdo, alguns a 1080p, outros a 720p, e alguns em SD e resoluções mais baixas. Além disso, nem todos os conteúdos de origem são mezaninos de alta qualidade de estúdios de cinema ou tv. A predefinição de Streaming Adaptativo aborda estes problemas garantindo que a escada de bitrate nunca excede a resolução ou a bitrate média da mezanino de entrada. No entanto, esta predefinição não examina propriedades de origem que não a resolução e o bitrate.

## <a name="the-content-aware-encoding"></a>A codificação consciente do conteúdo

A predefinição de codificação consciente do conteúdo alarga o mecanismo de "streaming de bitrate adaptativo", incorporando uma lógica personalizada que permite ao codificante procurar o valor bitrate ideal para uma determinada resolução, mas sem exigir uma análise computacional extensiva. Esta predefinição produz um conjunto de MP4s alinhados com GOP. Dado qualquer conteúdo de entrada, o serviço realiza uma análise leve inicial do conteúdo de entrada, e utiliza os resultados para determinar o número ideal de camadas, definições de bitrate e resolução apropriadas para entrega através do streaming adaptativo. Esta predefinição é particularmente eficaz para vídeos de baixa e média complexidade, onde os ficheiros de saída estarão em bitrates mais baixos do que a predefinição de Streaming Adaptive, mas com uma qualidade que ainda oferece uma boa experiência aos espectadores. A saída conterá ficheiros MP4 com vídeo e áudio intercalados

Os gráficos de amostra que se seguem mostram a comparação utilizando métricas de qualidade como [PSNR](https://en.wikipedia.org/wiki/Peak_signal-to-noise_ratio) e [VMAF](https://en.wikipedia.org/wiki/Video_Multimethod_Assessment_Fusion). A fonte foi criada através da concatenação de pequenos clips de imagens de alta complexidade de filmes e séries de TV, destinadas a realçar o codificadora. Por definição, esta predefinição produz resultados que variam de conteúdo para conteúdo – também significa que, para alguns conteúdos, pode não haver uma redução significativa do bitrate ou melhoria na qualidade.

![Curva de distorção de taxa (RD) utilizando PSNR](media/content-aware-encoding/msrv1.png)

**Figura 1: Curva de distorção de taxas (RD) utilizando a métrica PSNR para fonte de alta complexidade**

![Curva de distorção de taxa (RD) utilizando VMAF](media/content-aware-encoding/msrv2.png)

**Figura 2: Curva de distorção de taxas (RD) utilizando a métrica VMAF para fonte de alta complexidade**

Seguem-se os resultados de outra categoria de conteúdo de origem, onde o codificadores foi capaz de determinar que a entrada era de má qualidade (muitos artefactos de compressão devido ao bitrate baixo). Note-se que, com a predefinição consciente do conteúdo, o codificadora decidiu produzir apenas uma camada de saída – a uma bitrate suficientemente baixa para que a maioria dos clientes pudesse reproduzir o stream sem empatar.

![Curva RD usando PSNR](media/content-aware-encoding/msrv3.png)

**Figura 3: Curva RD utilizando PSNR para entrada de baixa qualidade (a 1080p)**

![Curva RD usando VMAF](media/content-aware-encoding/msrv4.png)

**Figura 4: Curva RD utilizando VMAF para entrada de baixa qualidade (a 1080p)**

## <a name="how-to-use-the-content-aware-encoding-preset"></a>Como utilizar a predefinição de codificação consciente do conteúdo 

Pode criar transformações que utilizem esta predefinição da seguinte forma. 

Consulte a secção [etapas seguintes](#next-steps) para tutoriais que usam saídas de transformação. O ativo de saída pode ser entregue a partir de pontos finais de streaming dos Media Services em protocolos como MPEG-DASH e HLS (como mostrado nos tutoriais).

> [!NOTE]
> Certifique-se de que utiliza o **predefinição de contentAwareEncodingExperimental.**

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
> Os trabalhos de codificação utilizando a `ContentAwareEncoding` predefinição estão a ser faturados com base nas atas de saída. 
  
## <a name="next-steps"></a>Passos seguintes

* [Tutorial: Carregar, codificar e transmitir vídeos com o Media Services v3](stream-files-tutorial-with-api.md)
* [Tutorial: Encode a remote file based on URL and stream the video - REST](stream-files-tutorial-with-rest.md) (Codificar ficheiros remotos com base no URL e transmitir o vídeo em fluxo - REST)
* [Tutorial: Codificar um ficheiro remoto baseado em URL e transmitir o vídeo - CLI](stream-files-cli-quickstart.md)
* [Tutorial: Codificar um ficheiro remoto com base no URL e transmitir o vídeo - .NET](stream-files-dotnet-quickstart.md)
* [Tutorial: Codificar um ficheiro remoto baseado em URL e transmitir o vídeo - Node.js](stream-files-nodejs-quickstart.md)
