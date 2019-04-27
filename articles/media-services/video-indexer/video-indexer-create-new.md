---
title: Criar informações de vídeo a partir de vídeos existentes - Azure
titlesuffix: Azure Media Services
description: Este tópico mostra como pode criar e publicar informações de vídeo com base em ficheiros de vídeo existentes.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.topic: article
ms.date: 02/10/2019
ms.author: juliako
ms.openlocfilehash: 4a65e88e3f94f64a56bde882b535030968ae354d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60560232"
---
# <a name="create-highlights-from-existing-videos"></a>Crie destaques de vídeos existentes

Este tópico mostra como pode criar e publicar informações de vídeo com base noutro vídeo.

1. Aceda ao site do [Video Indexer](https://www.videoindexer.ai/) e inicie sessão.
2. Localize um vídeo a partir do qual quer criar as suas informações de vídeo.
3. Prima **Play** (Reproduzir).

    A página mostra as informações de vídeo resumidas. 

    ![Informações](./media/video-indexer-create-new/video-indexer-summarized-insights.png)
3. Clique no botão **Edit** (Editar).

    Esta página mostra a estrutura completa de um vídeo. A estrutura encontra-se dividida em blocos. Estes blocos fazem com que seja mais fácil percorrer os dados. Por exemplo, o bloco pode estar dividido com base nos momentos em que ocorre uma mudança de orador ou uma longa pausa. Pode criar a sua própria lista de reprodução que contenha apenas as linhas que pretende. Para mostrar apenas partes específicas do vídeo de origem, pode filtrar por tópicos/palavras-chave, sentimentos, pessoas, oradores. Pode optar por ver apenas a transcrição do vídeo ou o OCR.    

    ![Informações](./media/video-indexer-create-new/video-indexer-create-new-playlist.png)
4. Crie a sua lista de reprodução.

    Para adicionar ou remover linhas da sua lista de reprodução, prima **+**/**-**.
5. Pré-visualize a sua lista de reprodução.

    Quando acabar de criar a lista de reprodução, prima **Preview** (Pré-visualizar).
6. Publique a lista de reprodução.

    Depois de pré-visualizar a lista de reprodução, pode publicá-la.

    Quando publicar a lista de reprodução, esta é adicionada à lista das suas informações de vídeo.


## <a name="next-steps"></a>Passos Seguintes 

Depois de criar a lista de reprodução nova, pode continuar a processá-la conforme descrito num dos seguintes tópicos: 

- [Processar conteúdo com a API REST do Video Indexer](video-indexer-use-apis.md)
- [Incorporar widgets visuais na sua aplicação](video-indexer-embed-widgets.md)

## <a name="see-also"></a>Consulte também

[Descrição geral do Video Indexer](video-indexer-overview.md) 
