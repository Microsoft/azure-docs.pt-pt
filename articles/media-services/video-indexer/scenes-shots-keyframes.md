---
title: Cenas, fotos e quadros de índice de vídeo
titleSuffix: Azure Media Services
description: Este tópico dá uma visão geral das cenas, fotos e quadros-chave do Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 07/05/2019
ms.author: juliako
ms.openlocfilehash: 248799d70e0741efcaea1714c12f4d92a42cef25
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "93041905"
---
# <a name="scenes-shots-and-keyframes"></a>Cenas, capturas e keyframes

O Video Indexer suporta a segmentação de vídeos em unidades temporais com base em propriedades estruturais e semânticas. Esta capacidade permite que os clientes naveguem, gerem e editem facilmente os seus conteúdos de vídeo com base em granularidades variadas. Por exemplo, com base em cenas, fotos e quadros-chave, descritos neste tópico.   

![Cenas, capturas e keyframes](./media/scenes-shots-keyframes/scenes-shots-keyframes.png)
 
## <a name="scene-detection"></a>Deteção de cena  
 
O Indexer de Vídeo determina quando uma cena muda em vídeo com base em pistas visuais. Uma cena retrata um único evento e é composta por uma série de tiros consecutivos, que são semi-relacionados. Uma miniatura de cena é o primeiro quadro-chave do seu tiro subjacente. O indexante de vídeo segmenta um vídeo em cenas baseadas na coerência da cor através de imagens consecutivas e recupera o início e o fim de cada cena. A deteção de cenas é considerada uma tarefa desafiadora, uma vez que envolve quantificar aspetos semânticos dos vídeos.

> [!NOTE]
> Aplicável a vídeos que contenham pelo menos 3 cenas.

## <a name="shot-detection"></a>Deteção de tiro

O Video Indexer determina quando um tiro muda no vídeo com base em pistas visuais, rastreando transições abruptas e graduais no esquema de cores dos quadros adjacentes. Os metadados do tiro incluem um tempo de início e fim, bem como a lista de quadros-chave incluídos nessa foto. As fotos são quadros consecutivos tirados da mesma câmara ao mesmo tempo.

## <a name="keyframe-detection"></a>Deteção de quadros-chave

O Indexante de Vídeo seleciona o quadro(s) que melhor representa cada tiro. Os quadros-chave são os quadros representativos selecionados de todo o vídeo com base em propriedades estéticas (por exemplo, contraste e estável). O Video Indexer recupera uma lista de IDs de foto-chave como parte dos metadados do shot, com base nos quais os clientes podem extrair o quadro-chave como uma imagem de alta resolução.  

### <a name="extracting-keyframes"></a>Extração de quadros-chave

Para extrair quadros-chave de alta resolução para o seu vídeo, tem primeiro de carregar e indexar o vídeo.

![Quadros-chave](./media/scenes-shots-keyframes/extracting-keyframes.png)

#### <a name="with-the-video-indexer-website"></a>Com o site do Indexer de Vídeo

Para extrair quadros-chave utilizando o website do Indexer de Vídeo, faça upload e indexe o seu vídeo. Assim que o trabalho de indexação estiver concluído, clique no botão **Descarregar** e selecione **Artefactos (ZIP)**. Isto irá transferir a pasta de artefactos para o seu computador. 

![Screenshot que mostra o drop-down "Download" com "Artefactos" selecionados.](./media/scenes-shots-keyframes/extracting-keyframes2.png)
 
Desaperte e abra a pasta. Na *pasta _KeyframeThumbnail,* encontrará todos os quadros-chave que foram extraídos do seu vídeo. 

#### <a name="with-the-video-indexer-api"></a>Com a API indexante de vídeo

Para obter quadros-chave utilizando a API do Indexer de Vídeo, faça upload e indexe o seu vídeo utilizando a chamada [de Vídeo upload.](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Upload-Video?) Assim que o trabalho de indexação estiver concluído, ligue [para o Índice de Vídeo](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Video-Index?). Isto irá dar-lhe todas as informações que o Video Indexer extraiu do seu conteúdo num ficheiro JSON.  

Receberá uma lista de identificações do quadro-chave como parte dos metadados de cada foto. 

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

Agora terá de executar cada uma destas identificações do quadro-chave na chamada [Get Thumbnails.](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Video-Thumbnail?) Isto irá transferir cada uma das imagens do quadro-chave para o seu computador. 

## <a name="editorial-shot-type-detection"></a>Deteção de tipo de tiro editorial

Os quadros-chave estão associados com tiros na saída JSON. 

O tipo de tiro associado a um tiro individual nas ideias JSON representa o seu tipo editorial. Pode achar estas características do tipo shot úteis ao editar vídeos em clips, trailers ou quando procura um estilo específico de quadro-chave para fins artísticos. Os diferentes tipos são determinados com base na análise do primeiro quadro-chave de cada tiro. As imagens são identificadas pela escala, tamanho e localização dos rostos que aparecem no primeiro quadro. 

O tamanho e escala do tiro são determinados com base na distância entre a câmara e os rostos que aparecem na moldura. Utilizando estas propriedades, o Video Indexer deteta os seguintes tipos de tiro:

* Largo: mostra o corpo de uma pessoa inteira.
* Médio: mostra a parte superior do corpo e o rosto de uma pessoa.
* Close-up: principalmente mostra o rosto de uma pessoa.
* Close-up extremo: mostra o rosto de uma pessoa a encher o ecrã. 

Os tipos de tiro também podem ser determinados pela localização dos caracteres do sujeito no que diz respeito ao centro da moldura. Esta propriedade define os seguintes tipos de tiro no Índice de Vídeo:

* Face esquerda: uma pessoa aparece no lado esquerdo da moldura.
* Face central: uma pessoa aparece na região central do quadro.
* Face direita: uma pessoa aparece no lado direito da moldura.
* Exterior: uma pessoa aparece em ambiente ao ar livre.
* Interior: uma pessoa aparece em um ambiente interior.

Características adicionais:

* Dois tiros: mostra os rostos de duas pessoas de tamanho médio.
* Rostos múltiplos: mais de duas pessoas.


## <a name="next-steps"></a>Passos seguintes

[Examinar a produção do Indexer de Vídeo produzido pela API](video-indexer-output-json-v2.md#scenes)
