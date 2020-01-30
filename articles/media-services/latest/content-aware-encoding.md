---
title: Um preset para codificação consciente de conteúdos - Azure Media Services
description: Este artigo discute a codificação com reconhecimento de conteúdo no Serviços de Mídia do Microsoft Azure v3.
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
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2020
ms.locfileid: "76772110"
---
# <a name="use-the-content-aware-encoding-preset-to-find-the-optimal-bitrate-value-for-a-given-resolution"></a>Utilize o predefinição de codificação consciente do conteúdo para encontrar o valor bitrate ideal para uma determinada resolução

Para preparar o conteúdo para entrega por [streaming de taxa de bits adaptável](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming), o vídeo precisa ser codificado em taxas de bits múltiplas (alta para baixa). Isto garante uma degradação graciosa da qualidade, uma vez que o bitrate é reduzido, assim como a resolução do vídeo. Esta codificação de várias bits faz uso da chamada escada de codificação – uma tabela de resoluções e bitrates, ver os serviços de [comunicação incorporados presets](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#encodernamedpreset).

Deve estar ciente do conteúdo que está a processar e personalizar/afinar a escada de codificação para a complexidade do vídeo individual. Em cada resolução, há uma taxa de bits além da qual qualquer aumento na qualidade não é um tanto enganoso – o codificador opera com esse valor de taxa de bits ideal. O próximo nível de otimização é selecionar as resoluções com base no conteúdo – por exemplo, um vídeo de uma apresentação do PowerPoint não se beneficia da sua saída abaixo de 720p. Indo além, o codificador pode ser transtarefado para otimizar as configurações de cada uma das capturas dentro do vídeo. 

O predefinido de [Streaming Adaptativo](autogen-bitrate-ladder.md) da Microsoft aborda parcialmente o problema da variabilidade na qualidade e resolução dos vídeos de origem. Os nossos clientes têm uma mistura variada de conteúdo, alguns a 1080p, outros a 720p, e alguns em SD e resoluções mais baixas. Além disso, nem todos os conteúdos de origem são mezaninos de alta qualidade de estúdios de cinema ou tv. A predefinição de streaming adaptável resolve esses problemas, garantindo que a escada de taxa de bits nunca exceda a resolução ou a taxa de bits média da mezanino de entrada. No entanto, este preset não examina propriedades de origem que não a resolução e a bitrate.

## <a name="the-content-aware-encoding"></a>A codificação consciente do conteúdo 

O preconjunto de codificação consciente do conteúdo alarga o mecanismo de "streaming de bitrate adaptativo", incorporando uma lógica personalizada que permite ao codificador procurar o valor bitrate ideal para uma determinada resolução, mas sem necessitar de uma análise computacional extensiva. Essa predefinição produz um conjunto de MP4s alinhado a GOP. Dado qualquer conteúdo de entrada, o serviço executa uma análise leve inicial do conteúdo de entrada e usa os resultados para determinar o número ideal de camadas, taxa de bits apropriada e configurações de resolução para entrega por streaming adaptável. Essa predefinição é particularmente eficaz para vídeos de complexidade baixa e média, em que os arquivos de saída estarão em taxas de bits menores do que a predefinição de streaming adaptável, mas com uma qualidade que ainda oferece uma boa experiência para os visualizadores. A saída conterá arquivos MP4 com vídeo e áudio intercalados

Os gráficos de amostra seguem mostrar a comparação utilizando métricas de qualidade como [PSNR](https://en.wikipedia.org/wiki/Peak_signal-to-noise_ratio) e [VMAF](https://en.wikipedia.org/wiki/Video_Multimethod_Assessment_Fusion). A fonte foi criada por meio da concatenação de pequenos clipes de capturas de alta complexidade de filmes e programas de TV, destinados a enfatizar o codificador. Por definição, essa predefinição produz resultados que variam de conteúdo para conteúdo – isso também significa que, para algum conteúdo, pode não haver uma redução significativa na taxa de bits ou melhoria na qualidade.

![Curva de taxa de distorção (RD) usando PSNR](media/content-aware-encoding/msrv1.png)

**Figura 1: curva de taxa de distorção (RD) usando a métrica PSNR para fonte de alta complexidade**

![Curva de taxa de distorção (RD) usando VMAF](media/content-aware-encoding/msrv2.png)

**Figura 2: curva de taxa de distorção (RD) usando a métrica VMAF para fonte de alta complexidade**

Abaixo estão os resultados de outra categoria de conteúdo de origem, em que o codificador foi capaz de determinar que a entrada era de baixa qualidade (muitos artefatos de compactação devido à baixa taxa de bits). Observe que, com a predefinição de reconhecimento de conteúdo, o codificador decidiu produzir apenas uma camada de saída – em uma taxa de bits baixa o suficiente para que a maioria dos clientes possa reproduzir o fluxo sem interrupções.

![Curva de RD usando PSNR](media/content-aware-encoding/msrv3.png)

**Figura 3: Curva RD utilizando PSNR para entrada de baixa qualidade (a 1080p)**

![Curva de RD usando VMAF](media/content-aware-encoding/msrv4.png)

**Figura 4: Curva RD utilizando VMAF para entrada de baixa qualidade (a 1080p)**

## <a name="how-to-use-the-content-aware-encoding-preset"></a>Como utilizar o predefinição de codificação consciente do conteúdo 

Você pode criar transformações que usam essa predefinição da seguinte maneira. 

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
> Os trabalhos de codificação utilizando o predefinição `ContentAwareEncoding` estão a ser faturados com base nos minutos de saída. 

## <a name="next-steps"></a>Passos seguintes

* [Tutorial: Upload, codificação e streaming de vídeos com Media Services v3](stream-files-tutorial-with-api.md)
* [Tutorial: codificar um arquivo remoto com base na URL e transmitir o vídeo-REST](stream-files-tutorial-with-rest.md)
* [Tutorial: Codificar um ficheiro remoto com base no URL e transmitir o vídeo - CLI](stream-files-cli-quickstart.md)
* [Tutorial: Codificar um ficheiro remoto com base em URL e transmitir o vídeo - .NET](stream-files-dotnet-quickstart.md)
* [Tutorial: Codificar um ficheiro remoto com base em URL e transmitir o vídeo - Node.js](stream-files-nodejs-quickstart.md)
