---
title: Cenas, fotos e quadros-chave do Indexer de vídeo
titleSuffix: Azure Media Services
description: Este tópico dá uma visão geral das cenas, fotos e quadros-chave do Indexer de Vídeo.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 07/05/2019
ms.author: juliako
ms.openlocfilehash: a833fd808049cfce95b182910e50e38d3c39f4e5
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78363942"
---
# <a name="scenes-shots-and-keyframes"></a>Cenas, capturas e keyframes

O Indexer de vídeo suporta segmentar vídeos em unidades temporais baseadas em propriedades estruturais e semânticas. Esta capacidade permite que os clientes naveguem, gerem e editem facilmente os seus conteúdos de vídeo com base em granularidades variadas. Por exemplo, com base em cenas, fotos e quadros-chave, descritos neste tópico.   

![Cenas, capturas e keyframes](./media/scenes-shots-keyframes/scenes-shots-keyframes.png)
 
## <a name="scene-detection"></a>Deteção de cena  
 
O Indexer de vídeo determina quando uma cena muda em vídeo com base em pistas visuais. Uma cena retrata um único evento e é composta por uma série de imagens consecutivas, que são semânticamente relacionadas. Uma miniatura de cena é o primeiro quadro chave do seu tiro subjacente. O indexante de vídeo segmenta um vídeo em cenas baseadas na coerência de cores através de imagens consecutivas e recupera o início e o tempo final de cada cena. A deteção de cenas é considerada uma tarefa desafiante, uma vez que envolve quantificar aspetos semânticos de vídeos.

> [!NOTE]
> Aplicável a vídeos que contenham pelo menos 3 cenas.

## <a name="shot-detection"></a>Deteção de tiro

O Indexer de vídeo determina quando um tiro muda no vídeo com base em pistas visuais, rastreando transições abruptas e graduais no esquema de cores dos quadros adjacentes. Os metadados do tiro incluem um tempo de início e fim, bem como a lista de quadros-chave incluídos nesse tiro. As fotos são quadros consecutivos tirados da mesma câmara ao mesmo tempo.

## <a name="keyframe-detection"></a>Deteção de quadros-chave

O Indexer de vídeo seleciona os quadros que melhor representam cada disparo. Os quadros-chave são os quadros representativos selecionados de todo o vídeo com base em propriedades estéticas (por exemplo, contraste e estável). O Indexer de vídeo recupera uma lista de IDs de keyframe como parte dos metadados da imagem, com base no qual os clientes podem extrair o quadro-chave como uma imagem de alta resolução.  

### <a name="extracting-keyframes"></a>Extrair quadros-chave

Para extrair quadros de alta resolução para o seu vídeo, tem primeiro de carregar e indexar o vídeo.

![Quadros-chave](./media/scenes-shots-keyframes/extracting-keyframes.png)

#### <a name="with-the-video-indexer-website"></a>Com o site do Indexer de Vídeo

Para extrair quadros-chave utilizando o website do Indexer de Vídeo, carregue e indexe o seu vídeo. Uma vez concluída a função de indexação, clique no botão **Descarregamento** e selecione **Artefactos (ZIP)** . Isto irá transferir a pasta de artefactos para o seu computador. 

![Quadros-chave](./media/scenes-shots-keyframes/extracting-keyframes2.png)
 
Desaperte e abra a pasta. Na pasta *_KeyframeThumbnail,* e encontrará todos os quadros-chave que foram extraídos do seu vídeo. 

#### <a name="with-the-video-indexer-api"></a>Com a API do Indexer de Vídeo

Para obter quadros de chave utilizando a API do Indexer de Vídeo, carregue e indexe o seu vídeo utilizando a chamada [Upload Video.](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Upload-Video?) Assim que o trabalho de indexação estiver concluído, ligue para [o Get Video Index](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Video-Index?). Isto irá dar-lhe todas as informações que o Indexer de Vídeo extraiu do seu conteúdo num ficheiro JSON.  

Você receberá uma lista de iDs chave como parte dos metadados de cada tiro. 

```json
"shots":[  
    {  
      "id":0,
      "keyFrames":[  
          {  
            "id":0,
            "instances":[  
                {  
                  "thumbnailId":"00000000-0000-0000-0000-000000000000",
                  "start":"0:00:00.209",
                  "end":"0:00:00.251",
                  "duration":"0:00:00.042"
                }
            ]
          },
          {  
            "id":1,
            "instances":[  
                {  
                  "thumbnailId":"00000000-0000-0000-0000-000000000000",
                  "start":"0:00:04.755",
                  "end":"0:00:04.797",
                  "duration":"0:00:00.042"
                }
            ]
          }
      ],
      "instances":[  
          {  
            "start":"0:00:00",
            "end":"0:00:06.34",
            "duration":"0:00:06.34"
          }
      ]
    },

]
```

Agora terá de executar cada uma destas identificações de chave na chamada [Get Miniaturas.](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Video-Thumbnail?) Isto irá transferir cada uma das imagens do quadro-chave para o seu computador. 

## <a name="editorial-shot-type-detection"></a>Deteção de tipo de tiro editorial

Os quadros-chave estão associados a tiros na saída JSON. 

O tipo de tiro associado a um tiro individual nos insights JSON representa o seu tipo editorial. Pode achar estas características do tipo de tiro úteis ao editar vídeos em clips, trailers ou quando procura um estilo específico de teclado para fins artísticos. Os diferentes tipos são determinados com base na análise do primeiro quadro-chave de cada tiro. Os tiros são identificados pela escala, tamanho e localização dos rostos que aparecem no seu primeiro quadro-chave. 

O tamanho e a escala do tiro são determinados com base na distância entre a câmara e as faces que aparecem na moldura. Utilizando estas propriedades, o Indexante de Vídeo deteta os seguintes tipos de tiro:

* Larga: mostra o corpo de uma pessoa inteira.
* Médio: mostra a parte superior do corpo e o rosto de uma pessoa.
* Close up: principalmente mostra o rosto de uma pessoa.
* Close-up extremo: mostra o rosto de uma pessoa a encher o ecrã. 

Os tipos de tiro também podem ser determinados pela localização dos caracteres sujeitos no que diz respeito ao centro da moldura. Esta propriedade define os seguintes tipos de tiro no Indexer de Vídeo:

* Face esquerda: uma pessoa aparece no lado esquerdo da moldura.
* Rosto central: uma pessoa aparece na região central da moldura.
* Cara direita: uma pessoa aparece no lado direito da moldura.
* Ao ar livre: uma pessoa aparece em ambiente ao ar livre.
* Interior: uma pessoa aparece em ambiente interior.

Características adicionais:

* Dois tiros: mostra o rosto de duas pessoas de tamanho médio.
* Múltiplas faces: mais de duas pessoas.


## <a name="next-steps"></a>Passos seguintes

[Examine a saída do Indexer de Vídeo produzida pela API](video-indexer-output-json-v2.md#scenes)
