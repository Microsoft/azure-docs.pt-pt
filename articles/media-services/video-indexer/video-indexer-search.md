---
title: Localizar momentos exatos nos vídeos - indexador de vídeo
titlesuffix: Azure Media Services
description: Este tópico demonstra como localizar momentos exatos nos vídeos, utilizar o Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.topic: article
ms.date: 02/25/2019
ms.author: juliako
ms.openlocfilehash: 07b3c806dc5df5f93bee3206cbca53485675e7dd
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56872408"
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
    
    Se quiser criar seu próprio clip com base no vídeo que encontrou, prima a **editar** botão. Esta página mostra-lhe vídeo juntamente com suas informações como filtros. Para obter mais informações, consulte [ver e editar informações do indexador de vídeo](video-indexer-view-edit.md). 

    Pode pesquisar no vídeo para mostrar apenas as linhas que está interessado e utiliza as informações de lado para filtrar as partes que pretende ver. Quando terminar, pode visualizar os seus clip e prima **publicar** para criar o novo clip que aparece na sua galeria.
    
    No exemplo a seguir, procuramos o texto "uma realidade mista,". Também Aplicamos filtros adicionais, conforme mostrado no ecrã abaixo.
    
    ![Pesquisa](./media/video-indexer-search/video-indexer-search03.png)

## <a name="next-steps"></a>Passos Seguintes 

Depois de localizar o vídeo que pretende trabalhar com, pode continuar a processar o vídeo, conforme descrito em um dos seguintes tópicos: 

- [Criar novas informações de vídeo com base no vídeo existente](video-indexer-create-new.md)
- [Processar conteúdo com a API REST do Video Indexer](video-indexer-use-apis.md)
- [Incorporar widgets visuais na sua aplicação](video-indexer-embed-widgets.md)

## <a name="see-also"></a>Consulte também

[Descrição geral do Video Indexer](video-indexer-overview.md)
