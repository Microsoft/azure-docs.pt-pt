---
title: Uma predefinição experimental para codificação com reconhecimento de conteúdo – Azure | Microsoft Docs
description: Este artigo discute a codificação com reconhecimento de conteúdo no Serviços de Mídia do Microsoft Azure v3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 04/05/2019
ms.author: juliako
ms.custom: ''
ms.openlocfilehash: 9389466b6291542563c068706479bf981c5880da
ms.sourcegitcommit: 2f8ff235b1456ccfd527e07d55149e0c0f0647cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/07/2020
ms.locfileid: "75692761"
---
# <a name="experimental-preset-for-content-aware-encoding"></a>Predefinição experimental para codificação com reconhecimento de conteúdo

Para preparar o conteúdo para entrega por [streaming de taxa de bits adaptável](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming), o vídeo precisa ser codificado em taxas de bits múltiplas (alta para baixa). A fim de garantir a degradação normal da qualidade, como a taxa de bits é diminuída, é a resolução do vídeo. Isso resulta em uma chamada de escada de codificação – uma tabela de resoluções e taxas de bits; Consulte as [predefinições de codificação interna](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#encodernamedpreset)dos serviços de mídia.

## <a name="overview"></a>Visão geral

O interesse em passar além de uma abordagem de um-predefinido-All-vídeos aumentou depois que o Netflix publicou seu [blog](https://medium.com/netflix-techblog/per-title-encode-optimization-7e99442b62a2) em dezembro de 2015. Desde então, várias soluções para codificação com reconhecimento de conteúdo foram liberadas no Marketplace; consulte [Este artigo](https://www.streamingmedia.com/Articles/Editorial/Featured-Articles/Buyers-Guide-to-Per-Title-Encoding-130676.aspx) para obter uma visão geral. A ideia é estar ciente do conteúdo – para personalizar ou ajustar a escada de codificação à complexidade do vídeo individual. Em cada resolução, há uma taxa de bits além da qual qualquer aumento na qualidade não é um tanto enganoso – o codificador opera com esse valor de taxa de bits ideal. O próximo nível de otimização é selecionar as resoluções com base no conteúdo – por exemplo, um vídeo de uma apresentação do PowerPoint não se beneficia da sua saída abaixo de 720p. Indo além, o codificador pode ser transtarefado para otimizar as configurações de cada uma das capturas dentro do vídeo. Netflix descreveu [essa abordagem](https://medium.com/netflix-techblog/optimized-shot-based-encodes-now-streaming-4b9464204830) em 2018.

No início de 2017, a Microsoft lançou a predefinição de [streaming adaptável](autogen-bitrate-ladder.md) para resolver o problema da variabilidade na qualidade e na resolução dos vídeos de origem. Nossos clientes tinham uma mistura variada de conteúdo, alguns na 1080p, outros em 720p e alguns no SD e em resoluções inferiores. Além disso, nem todo o conteúdo de origem era a mezanino de alta qualidade de estúdios de filmes ou de TV. A predefinição de streaming adaptável resolve esses problemas, garantindo que a escada de taxa de bits nunca exceda a resolução ou a taxa de bits média da mezanino de entrada.

A predefinição de codificação experimental com reconhecimento de conteúdo estende esse mecanismo, incorporando uma lógica personalizada que permite ao codificador buscar o valor ideal de taxa de bits para uma determinada resolução, mas sem a necessidade de uma análise computacional extensiva. O resultado é que essa nova predefinição produz uma saída com taxa de bits inferior à predefinição de streaming adaptável, mas com uma qualidade mais alta. Consulte os gráficos de exemplo a seguir que mostram a comparação usando métricas de qualidade como [PSNR](https://en.wikipedia.org/wiki/Peak_signal-to-noise_ratio) e [VMAF](https://en.wikipedia.org/wiki/Video_Multimethod_Assessment_Fusion). A fonte foi criada por meio da concatenação de pequenos clipes de capturas de alta complexidade de filmes e programas de TV, destinados a enfatizar o codificador. Por definição, essa predefinição produz resultados que variam de conteúdo para conteúdo – isso também significa que, para algum conteúdo, pode não haver uma redução significativa na taxa de bits ou melhoria na qualidade.

![Curva de taxa de distorção (RD) usando PSNR](media/cae-experimental/msrv1.png)

**Figura 1: curva de taxa de distorção (RD) usando a métrica PSNR para fonte de alta complexidade**

![Curva de taxa de distorção (RD) usando VMAF](media/cae-experimental/msrv2.png)

**Figura 2: curva de taxa de distorção (RD) usando a métrica VMAF para fonte de alta complexidade**

Atualmente, a predefinição está ajustada para alta complexidade, vídeos de origem de alta qualidade (filmes, programas de TV). O trabalho está em andamento para se adaptar ao conteúdo de baixa complexidade (por exemplo, apresentações do PowerPoint), bem como a vídeos com qualidade inferior. Essa predefinição também usa o mesmo conjunto de resoluções que a predefinição de streaming adaptável. A Microsoft está trabalhando em métodos para selecionar o conjunto mínimo de resoluções com base no conteúdo. Como segue, os resultados de outra categoria de conteúdo de origem, em que o codificador foi capaz de determinar que a entrada era de baixa qualidade (muitos artefatos de compactação devido à baixa taxa de bits). Observe que, com a predefinição experimental, o codificador decidiu produzir apenas uma camada de saída – em uma taxa de bits baixa o suficiente para que a maioria dos clientes possa reproduzir o fluxo sem interrupções.

![Curva de RD usando PSNR](media/cae-experimental/msrv3.png)

**Figura 3: curva RD usando PSNR para entrada de baixa qualidade (em 1080p)**

![Curva de RD usando VMAF](media/cae-experimental/msrv4.png)

**Figura 4: curva RD usando VMAF para entrada de baixa qualidade (em 1080p)**

## <a name="use-the-experimental-preset"></a>Usar a predefinição experimental

Você pode criar transformações que usam essa predefinição da seguinte maneira. Se estiver usando um tutorial como [este](stream-files-tutorial-with-api.md), você poderá atualizar o código da seguinte maneira:

```csharp
TransformOutput[] output = new TransformOutput[]
{
   new TransformOutput
   {
      // The preset for the Transform is set to one of Media Services built-in sample presets.
      // You can customize the encoding settings by changing this to use "StandardEncoderPreset" class.
      Preset = new BuiltInStandardEncoderPreset()
      {
         // This sample uses the new experimental preset for content-aware encoding
         PresetName = EncoderNamedPreset.ContentAwareEncodingExperimental
      }
   }
};
```

> [!NOTE]
> O prefixo "experimental" é usado aqui para sinalizar que os algoritmos subjacentes ainda estão em evolução. Pode haver mudanças ao longo do tempo para a lógica usada para gerar esgotamentos de taxa de bits, com o objetivo de convergir em um algoritmo robusto e se adapta a uma ampla variedade de condições de entrada. Os trabalhos de codificação que usam essa predefinição ainda serão cobrados com base nos minutos de saída, e o ativo de saída poderá ser entregue de nossos pontos de extremidade de streaming em protocolos como DASH e HLS.

## <a name="next-steps"></a>Passos seguintes

Agora que você aprendeu sobre essa nova opção de otimização de seus vídeos, convidamos você a experimentá-lo. Você pode enviar comentários usando os links no final deste artigo ou nos envolver mais diretamente em <amsved@microsoft.com>.
