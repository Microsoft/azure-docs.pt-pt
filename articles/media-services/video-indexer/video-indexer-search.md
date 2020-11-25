---
title: Procure momentos exatos em vídeos com Video Indexer
titleSuffix: Azure Media Services
description: Saiba como procurar momentos exatos em vídeos utilizando o Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 11/23/2019
ms.author: juliako
ms.openlocfilehash: a1ca7c677e5f1eb55d74cca45e757676674f303c
ms.sourcegitcommit: 2e9643d74eb9e1357bc7c6b2bca14dbdd9faa436
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96030529"
---
# <a name="search-for-exact-moments-in-videos-with-video-indexer"></a>Procure momentos exatos em vídeos com Video Indexer

Este tópico mostra-lhe como usar o site do Índice de Vídeo para procurar momentos exatos em vídeos.

1. Vá ao site do [Video Indexer](https://www.videoindexer.ai/) e inscreva-se.
1. Especifique as palavras-chave de pesquisa e a pesquisa será realizada entre todos os vídeos na biblioteca da sua conta. 

    Pode filtrar a sua pesquisa selecionando **Filtros.** Em seguida, procuramos "Microsoft" que aparece apenas como um texto no ecrã (OCR).

    :::image type="content" source="./media/video-indexer-search/filter.png" alt-text="Filtro, apenas texto":::
1. **Pressione a pesquisa** para ver o resultado.

    :::image type="content" source="./media/video-indexer-search/results.png" alt-text="Resultado da pesquisa de vídeo":::

    Se selecionar um dos resultados, o jogador leva-o a esse momento exato no vídeo.
1. Veja e procure as ideias resumidas do vídeo clicando em **Reproduzir** o vídeo ou selecione um dos seus resultados de pesquisa originais. 

    Pode ver, pesquisar, editar os **insights.** Quando seleciona uma das ideias, o jogador leva-o a esse momento exato no vídeo.  

    :::image type="content" source="./media/video-indexer-search/insights.png" alt-text="Ver, pesquisar e editar as ideias do vídeo":::

    Se incorporar o vídeo através de widgets do Video Indexer, pode alcançar a visão do jogador/insights e a sincronização na sua aplicação. Para obter mais informações, consulte [os widgets do Indexer de Vídeo Incorporado na sua aplicação.](video-indexer-embed-widgets.md)
1. Pode visualizar, pesquisar e editar as transcrições clicando no **separador Timeline.** 

    :::image type="content" source="./media/video-indexer-search/timeline.png" alt-text="Ver, pesquisar e editar as transcrições do vídeo":::

    Para editar o texto, **selecione Editar** a partir do canto superior direito e altere o texto conforme necessário. 

    Também pode traduzir e baixar as transcrições selecionando a opção apropriada a partir do canto superior direito. 

## <a name="embed-download-create-projects"></a>Incorporar, descarregar, criar projetos

Pode incorporar o seu vídeo selecionando **</>Embed** no seu vídeo. Para mais detalhes, consulte [widgets visuais incorporados na sua aplicação](video-indexer-embed-widgets.md).

Pode descarregar o vídeo de origem, as informações do vídeo, as transcrições clicando **em Baixar** no seu vídeo.

Pode criar um clip baseado no seu vídeo de linhas e momentos específicos clicando **em Abrir no editor**. Em seguida, editar o vídeo, e salvar o projeto. Para mais detalhes, consulte [as informações profundas dos seus vídeos.](use-editor-create-project.md)

:::image type="content" source="./media/video-indexer-search/embed-download-create-projects.png" alt-text="Incorporar, baixar, criar projetos do vídeo":::

## <a name="next-steps"></a>Passos seguintes

[Processar conteúdo com a API REST do Video Indexer](video-indexer-use-apis.md)
