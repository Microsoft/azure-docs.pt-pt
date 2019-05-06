---
title: Predefinir uma experimental para a codificação de conteúdo com suporte para - Azure | Documentos da Microsoft
description: Este artigo aborda a codificação com reconhecimento de conteúdo nos serviços de multimédia do Azure
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
ms.openlocfilehash: ddb7bfd2437af806c8db75068c50545e69867ea0
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65151011"
---
# <a name="experimental-preset-for-content-aware-encoding"></a>Configuração predefinida para a codificação de conteúdo com suporte para experimentais

Para preparar o conteúdo para a entrega por [transmissão em fluxo de velocidade de transmissão adaptável](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming), vídeo tem de ser codificado em vários bits taxas de (em cima para baixo). Para garantir que a redução gradual de qualidade, como a velocidade de transmissão é reduzida é por isso, a resolução do vídeo. Isso resulta numa escada codificação chamada – uma tabela de resoluções e velocidades de transmissão; ver os serviços de multimédia [configurações predefinidas de codificação incorporadas](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#encodernamedpreset).

## <a name="overview"></a>Descrição geral

Aumentou de interesse numa abordagem de uma configuração predefinida-ajusta-se-all-vídeos se mover após a publicação de Netflix seus [blogue](https://medium.com/netflix-techblog/per-title-encode-optimization-7e99442b62a2) em Dezembro de 2015. Desde então, várias soluções para a codificação de conteúdo com suporte para tenham sido publicadas no marketplace; ver [este artigo](https://www.streamingmedia.com/Articles/Editorial/Featured-Articles/Buyers-Guide-to-Per-Title-Encoding-130676.aspx) para uma descrição geral. A idéia é ter em consideração os conteúdos, para personalizar ou ajustar a escala de bits de codificação para a complexidade do vídeo individual. Cada resolução, há uma velocidade de transmissão para além dos quais qualquer aumento na qualidade não é visão perspicaz – o codificador opera neste valor de velocidade de transmissão ideal. O próximo nível de otimização é selecionar as resoluções com base no conteúdo – por exemplo, um vídeo de uma apresentação do PowerPoint não beneficiam das vai abaixo 720p. Vá mais longe, o codificador pode tarefa para otimizar as definições para cada captura no vídeo. Netflix descrito [essa abordagem](https://medium.com/netflix-techblog/optimized-shot-based-encodes-now-streaming-4b9464204830) em 2018.

No início de 2017, a Microsoft lançou o [transmissão em fluxo adaptável](autogen-bitrate-ladder.md) predefinidas para resolver o problema de variabilidade na qualidade e resolução de vídeos de origem. Os nossos clientes tinham uma mistura de diferentes de conteúdo, algumas em 1080p, outras pessoas em 720p e alguns no SD e resoluções inferiores. Além disso, nem todos os conteúdos de origem foi mezzanines de alta qualidade de filme ou estúdios de TV. A Adaptive Streaming endereços predefinidos esses problemas, garantindo que a escala de bits nunca excede a resolução ou a velocidade de transmissão de média do mezanino de entrada.

A predefinição de codificação de conteúdo com suporte para experimental estende esse mecanismo, ao incorporar lógica personalizada que permite que o codificador de buscar o valor de velocidade de transmissão ideal para uma resolução determinada, mas sem a necessidade de análise de computacional extensa. O resultado líquido é que esta predefinição de novos produz uma saída com velocidade de transmissão mais baixa do que a configuração predefinida de transmissão em fluxo adaptável, mas numa maior qualidade. Ver os gráficos de exemplo seguinte, que mostram a uso de métricas de qualidade, como de comparação [PSNR](https://en.wikipedia.org/wiki/Peak_signal-to-noise_ratio) e [VMAF](https://en.wikipedia.org/wiki/Video_Multimethod_Assessment_Fusion). A origem foi criada pela concatenação clipes curtos de capturas de complexidade alta de filmes e TV mostra, a finalidade de enfatizar o codificador. Por definição, esta produz predefinidas resultados que variam de conteúdo para conteúdos, isso também significa que para algum conteúdo, pode não haver uma redução significativa na velocidade de transmissão ou melhoria na qualidade.

![Curva de taxa-distorção (RD) usando PSNR](media/cae-experimental/msrv1.png)

**Figura 1: Curva de taxa-distorção (RD) através de métrica PSNR para a origem de complexidade alta**

![Curva de taxa-distorção (RD) usando VMAF](media/cae-experimental/msrv2.png)

**Figura 2: Curva de taxa-distorção (RD) através de métrica VMAF para a origem de complexidade alta**

A configuração predefinida é atualmente ajustada para complexidade alta, vídeos de origem de alta qualidade (filmes, programas de TV). Trabalho está em curso para se adaptar ao conteúdo de complexidade baixa (por exemplo, apresentações do PowerPoint), bem como a pior vídeos de qualidade. Esta configuração predefinida também utiliza o mesmo conjunto de resoluções como a transmissão em fluxo adaptável a configuração predefinida. A Microsoft está trabalhando em métodos para selecionar o conjunto mínimo de resoluções com base no conteúdo. São os seguintes resultados para outra categoria de conteúdo de origem, em que o codificador foi capaz de determinar se a entrada foi de baixa qualidade (muitos artefatos de compressão devido a baixa velocidade de transmissão). Tenha em atenção que, com o experimental, configuração predefinida, o codificador decidiu produzir apenas uma camada de saída – numa velocidade de transmissão baixa o suficiente para que a maioria dos clientes seria capaz de reproduzir o fluxo sem interrupção.

![Curva de área de trabalho remota usando PSNR](media/cae-experimental/msrv3.png)

**Figura 3: Curva de área de trabalho remota com PSNR para entrada de baixa qualidade (em 1080p)**

![Curva de área de trabalho remota usando VMAF](media/cae-experimental/msrv4.png)

**Figura 4: Curva de área de trabalho remota com VMAF para entrada de baixa qualidade (em 1080p)**

## <a name="use-the-experimental-preset"></a>Utilizar a configuração predefinida de experimental

Pode criar transformações que utilizam esta configuração predefinida da seguinte forma. Se utilizar um tutorial [como esta](stream-files-tutorial-with-api.md), pode atualizar o código da seguinte forma:

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
> O prefixo "experimental" é utilizado aqui para sinalizar que os algoritmos subjacentes ainda estão em evolução. Pode existir e vão ocorrer alterações ao longo do tempo para a lógica utilizada para gerar ladders de velocidade de transmissão, com o objetivo de convergindo um algoritmo que é robusto e adapta-se a uma grande variedade de condições de entrada. Codificação de tarefas utilizando o esta irá predefinida ainda ser minutos de saída com base no faturadas e o elemento de saída pode ser enviado do nosso pontos finais de transmissão em fluxo em protocolos como DASH e HLS.

## <a name="next-steps"></a>Passos Seguintes

Agora que aprendeu sobre esta nova opção de otimizar os seus vídeos, convidamos a experimentá-lo. Pode enviar comentários através das ligações no final deste artigo ou interagir com-nos mais diretamente em <amsved@microsoft.com>.
