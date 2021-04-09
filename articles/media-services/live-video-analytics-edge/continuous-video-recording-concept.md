---
title: Gravação contínua de vídeo - Azure
description: Gravação contínua de vídeo (CVR) refere-se ao processo de gravação contínua do vídeo a partir de uma fonte de vídeo. Este tópico discute o que é CVR.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: f8b25d74db279f8cfff68f08efeab2975484a0c5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "102453755"
---
# <a name="continuous-video-recording"></a>Gravação de vídeo contínua  

## <a name="suggested-pre-reading"></a>Pré-leitura sugerida  

* [Conceito de gráfico de mídia](media-graph-concept.md)
* [Conceito de gravação de vídeo](video-recording-concept.md)

## <a name="overview"></a>Descrição Geral

Gravação contínua de vídeo (CVR) refere-se ao processo de gravação contínua do vídeo a partir de uma fonte de vídeo. Live Video Analytics on IoT Edge suporta a gravação de vídeo continuamente, numa base de 24x7, a partir de uma câmara CCTV através de um [gráfico de mídia](media-graph-concept.md) composto por um nó de origem RTSP e um nó de lavatório de ativos. O diagrama abaixo mostra uma representação gráfica de tal gráfico mediático. A representação JSON da [topologia de gráficos](media-graph-concept.md#media-graph-topologies-and-instances) de tal gráfico mediático pode ser encontrada [aqui](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/cvr-asset).

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/continuous-video-recording/continuous-video-recording-overview.svg" alt-text="Gravação de vídeo contínua":::

O gráfico de mídia acima representado pode ser executado num dispositivo de borda, com o vídeo de gravação do lavatório do ativo para um [ativo](terminology.md#asset)da Azure Media Services . O vídeo será gravado enquanto o gráfico de mídia permanecer no estado ativado. Uma vez que o vídeo está a ser gravado como um ativo, pode ser reproduzido utilizando as capacidades de streaming existentes dos Media Services. Consulte [a Reprodução de conteúdos gravados](video-playback-concept.md) para obter mais detalhes.

## <a name="resilient-recording"></a>Gravação resiliente

O Live Video Analytics on IoT Edge suporta operar em condições de rede menos perfeitas, onde o dispositivo de borda pode ocasionalmente perder conectividade com a nuvem ou experimentar uma queda na largura de banda disponível. Para responder a isto, o vídeo da fonte é gravado localmente numa cache e é automaticamente sincronizado com o ativo numa base periódica. Se examinar a topologia do [gráfico JSON,](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/cvr-asset/topology.json)verá que tem as seguintes propriedades definidas:

```
"segmentLength": "PT30S",
"localMediaCacheMaximumSizeMiB": "2048",
"localMediaCachePath": "/var/lib/azuremediaservices/tmp/",
```

As duas últimas propriedades são relevantes para a gravação resiliente (ambas são também propriedades necessárias para um nó de pia de ativo). A propriedade localMediaCachePath diz ao ativo para usar esse caminho de pasta para cache dados de mídia antes de carregar para o ativo. Pode ver [este](../../iot-edge/how-to-access-host-storage-from-module.md) artigo para entender como o módulo edge pode fazer uso do armazenamento local do seu dispositivo. A propriedade localMediaCacheMaximumSizeMiB define quanto espaço de disco o lavatório do ativo pode usar como cache (1 MiB = 1024 * 1024 bytes). 

Se o módulo edge perde conectividade por muito tempo e o conteúdo armazenado na pasta cache atinge o valor localMediaCacheMaximumSizeMiB, o lavatório de ativos começará a descartar dados da cache, a partir dos dados mais antigos. Por exemplo, se o dispositivo perdeu a conectividade às 10:00 e a cache atingir o limite máximo às 18:00, então o lavatório do ativo começa a apagar os dados registados às 10:00. 

Quando a conectividade da rede for restaurada, o lavatório de ativos começará a ser carregado a partir da cache, novamente a partir dos dados mais antigos. No exemplo acima, suponha que 5 minutos de vídeo tiveram de ser descartados da cache quando a conectividade foi restaurada (digamos às 18:02), então o lavatório do ativo começará a ser carregado a partir da marca das 10:05.

Se mais tarde examinar o ativo utilizando [estas](playback-recordings-how-to.md) APIs, verá que existe uma lacuna no ativo entre as 10h e as 10h05.

## <a name="segmented-recording"></a>Gravação segmentada  

Como discutido acima, o nó da pia do ativo gravará o vídeo para uma cache local e carregará periodicamente o vídeo para a nuvem. A propriedade segmentLength (mostrada na secção acima) irá ajudá-lo a controlar o custo de transações de escrita associados à escrita de dados na sua conta de armazenamento onde o ativo está a ser registado. Por exemplo, se aumentar o período de upload de 30 segundos para 5 minutos, então o número de transações de armazenamento diminuirá em um fator de 10 (5*60/30).

A propriedade segmentLength garante que o módulo de borda irá carregar o vídeo no máximo uma vez por segmentoS de comprimento. Esta propriedade tem um valor mínimo de 30 segundos (também o padrão), e pode ser aumentada em incrementos de 30 segundos para um máximo de 5 minutos.

> [!NOTE]
> Consulte o artigo [de gravação de reprodução](playback-recordings-how-to.md) para obter o efeito que o segmentoLength tem na reprodução.

## <a name="see-also"></a>Ver também

* [Gravação de vídeo baseada em eventos](event-based-video-recording-concept.md)
* [Reprodução de conteúdo gravado](video-playback-concept.md)

## <a name="next-steps"></a>Passos seguintes

[Tutorial: gravação contínua de vídeo](continuous-video-recording-tutorial.md)
