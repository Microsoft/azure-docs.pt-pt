---
title: Localizar momentos exatos em vídeos-Video Indexer
titleSuffix: Azure Media Services
description: Este tópico demonstra como localizar momentos exatos em vídeos usando Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: juliako
ms.openlocfilehash: 8ebf7606d1a0932cc2254e14a40a2fff550fd35f
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73833802"
---
# <a name="find-exact-moments-within-videos"></a>Localizar momentos exatos nos vídeos

Este tópico mostra as opções de pesquisa que permitem que você encontre exatamente um instante nos vídeos.

1. Aceda ao site do [Video Indexer](https://www.videoindexer.ai/) e inicie sessão.
2. Pesquisar entre todos os vídeos em sua conta.

    No exemplo a seguir, pesquisamos todos os vídeos que falam sobre segurança e em que Satya aparece,

    ![Pesquisa](./media/video-indexer-search/video-indexer-search01.png)
3. Pesquisar as informações resumidas do vídeo.

    Em seguida, você pode pesquisar em um vídeo clicando em **reproduzir** no vídeo. Em seguida, você pode pesquisar dentro do vídeo selecionando a guia **Pesquisar** . 

    No exemplo a seguir, pesquisamos "seguro" dentro do vídeo selecionado.

    ![Pesquisa](./media/video-indexer-search/video-indexer-search02.png)

    Se você clicar em um dos resultados, o jogador o levará até esse momento no vídeo. Você pode obter a exibição e a sincronização do Player/insights em seu aplicativo. Para obter mais informações, consulte [Inserir widgets de video indexer em seu aplicativo](video-indexer-embed-widgets.md). 
4. Pesquise a análise detalhada do vídeo.
    
    Se você quiser criar seu próprio clipe com base no vídeo encontrado, pressione o botão **Editar** . Esta página mostra o vídeo junto com suas ideias como filtros. Para obter mais informações, consulte [Exibir e editar informações de video indexer](video-indexer-view-edit.md). 

    Você pode pesquisar dentro do vídeo para mostrar apenas as linhas nas quais está interessado e usar as informações rápidas para filtrar as partes que você deseja ver. Quando terminar, você poderá visualizar o clipe e pressionar **publicar** para criar o novo clipe que aparecerá na galeria.
    
    No exemplo a seguir, pesquisamos o texto "realidade misturada". Também aplicamos filtros adicionais, conforme mostrado na tela abaixo.
    
    ![Pesquisa](./media/video-indexer-search/video-indexer-search03.png)

## <a name="next-steps"></a>Passos seguintes 

Quando encontrar o vídeo com o qual deseja trabalhar, você poderá continuar a processar o vídeo, conforme descrito em um destes tópicos: 

- [Use as ideias aprofundadas de seus vídeos](use-editor-create-project.md)
- [Processar conteúdo com a API REST do Video Indexer](video-indexer-use-apis.md)
- [Incorporar widgets visuais na sua aplicação](video-indexer-embed-widgets.md)

## <a name="see-also"></a>Consultar também

[Descrição geral do Video Indexer](video-indexer-overview.md)
