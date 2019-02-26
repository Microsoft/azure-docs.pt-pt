---
title: Localizar momentos exatos nos vídeos - indexador de vídeo
titlesuffix: Azure Media Services
description: Este tópico demonstra como localizar momentos exatos nos vídeos, utilizar o Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.topic: article
ms.date: 02/10/2019
ms.author: juliako
ms.openlocfilehash: 6c93c0228425b2ff020e5b4301e68eae5b1062a3
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2019
ms.locfileid: "56807835"
---
# <a name="find-exact-moments-within-videos"></a>Localizar momentos exatos nos vídeos

Este tópico mostra-lhe as opções de pesquisa que lhe permitem localizar momentos exatos nos vídeos.

1. Aceda ao site do [Video Indexer](https://www.videoindexer.ai/) e inicie sessão.
2. Pesquise entre todos os vídeos na sua conta.

    No exemplo a seguir, vamos procurar todos os vídeos que falam sobre segurança e em que Satya aparece,

    ![Pesquisa](./media/video-indexer-search/video-indexer-search01.png)
3. Pesquise as informações resumidas do vídeo.

    Em seguida, pode procurar dentro de um vídeo ao clicar em **reproduzir** no vídeo. Em seguida, pode pesquisar no vídeo, selecionando o **pesquisa** separador. 

    No exemplo a seguir, vamos procurar "seguro" dentro de vídeo selecionado.

    ![Pesquisa](./media/video-indexer-search/video-indexer-search02.png)

    Se clicar em um dos resultados, o jogador traz até para esse momento no vídeo. Pode obter a vista de leitor/informações e a sincronização em seu aplicativo. Para obter mais informações, consulte [widgets de incorporar o indexador de vídeos na sua aplicação](video-indexer-embed-widgets.md). 
4. Pesquise a divisão detalhada do vídeo.

    Se quiser criar seu próprio divisão com base no vídeo que encontrou, prima a **editar** botão. Esta página mostra a estrutura completa de um vídeo. Fazer uma pesquisa na divisão para mostrar apenas as linhas que está interessado. Para obter mais informações, consulte [ver e editar informações do indexador de vídeo](video-indexer-view-edit.md).

    Neste exemplo, podemos pesquisado o texto "identity protection". Também Aplicamos filtros adicionais, conforme mostrado no ecrã abaixo.

    ![Pesquisa](./media/video-indexer-search/video-indexer-search03.png)

## <a name="next-steps"></a>Passos Seguintes 

Depois de localizar o vídeo que pretende trabalhar com, pode continuar a processar o vídeo, conforme descrito em um dos seguintes tópicos: 

- [Criar novas informações de vídeo com base no vídeo existente](video-indexer-create-new.md)
- [Processar conteúdo com a API REST do Video Indexer](video-indexer-use-apis.md)
- [Incorporar widgets visuais na sua aplicação](video-indexer-embed-widgets.md)

## <a name="see-also"></a>Consulte também

[Descrição geral do Video Indexer](video-indexer-overview.md)
