---
title: Inscreva-se no Indexer de Vídeo e faça upload do seu primeiro vídeo - Azure
titleSuffix: Azure Media Services
description: Saiba como inscrever-se e carregar o seu primeiro vídeo no portal do Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: quickstart
ms.date: 03/17/2020
ms.author: juliako
ms.openlocfilehash: 22b06ce99333750d48d1cb65d9f60779572723b9
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "79499639"
---
# <a name="quickstart-how-to-sign-up-and-upload-your-first-video"></a>Quickstart: Como se inscrever e fazer upload do seu primeiro vídeo

Este arranque rápido mostra como iniciar o site do Indexer de Vídeo e como fazer o upload do seu primeiro vídeo.

Quando criar uma conta do Video Indexer, pode optar por uma conta de avaliação gratuita (através da qual obtém um determinado número de minutos de indexação gratuitos) ou uma opção paga (não fica limitado pela quota). Com a avaliação gratuita, o Video Indexer fornece até 600 minutos de indexação gratuita a utilizadores de sites e até 2400 minutos de indexação gratuita a utilizadores de APIs. Com opção paga, cria uma conta De Indexer de Vídeo que está [ligada à sua subscrição Azure e a uma conta Azure Media Services.](connect-to-azure.md) Irá pagar pelos minutos de indexação e pelas cobranças relacionadas com a conta dos Serviços de Multimédia do Azure. 

## <a name="sign-up-for-video-indexer"></a>Inscrever-se no Video Indexer

Para começar a criar com o Video Indexer, aceda ao site do [Video Indexer](https://www.videoindexer.com) e inscreva-se.

> [!NOTE]
> Assim que começar a utilizar o Indexer de Vídeo, todos os seus dados armazenados e conteúdo carregado são encriptados em repouso com uma chave gerida pela Microsoft.

## <a name="upload-a-video-using-the-video-indexer-website"></a>Carregar um vídeo através do site do Video Indexer

### <a name="supported-file-formats-for-video-indexer"></a>Formatos de ficheirosuportados para Indexer de Vídeo

Consulte o artigo de [formatos de recipiente/ficheiro](../latest/media-encoder-standard-formats.md#input-containerfile-formats) de entrada para obter uma lista de formatos de ficheiros que pode utilizar com o Indexer de Vídeo.

### <a name="upload-a-video"></a>Carregar um vídeo

1. Inicie sessão no site do [Video Indexer](https://www.videoindexer.ai/).
2. Para carregar um vídeo, selecione o botão ou ligação **Upload** (Carregar)

    > [!NOTE]
    > O nome do vídeo não deve ser superior a 80 caracteres.

    ![Carregar](./media/video-indexer-get-started/video-indexer-upload.png)

    Após carregar o seu vídeo, o Video Indexer iniciará a indexação e análise do vídeo.

    ![Carregado](./media/video-indexer-get-started/video-indexer-uploaded.png) 

    Quando o Video Indexer concluir a análise, será apresentada uma notificação com uma ligação para o seu vídeo juntamente com uma breve descrição dos conteúdos encontrados no mesmo. Por exemplo: people (pessoas), topics (tópicos), OCRs.

## <a name="see-also"></a>Consulte também

Consulte [o Upload e indexe os vídeos](upload-index-videos.md) para obter mais detalhes.

Depois de fazer o upload e indexar um vídeo, pode começar a usar o site [do Indexer](video-indexer-view-edit.md) de Vídeo ou o Portal de Desenvolvedores de [Indexadores](video-indexer-use-apis.md) de Vídeo para ver as informações do vídeo. 

[Comece a usar APIs](video-indexer-use-apis.md)

## <a name="next-steps"></a>Passos seguintes

Para uma introdução detalhada visite o nosso laboratório de [introdução.](https://github.com/Azure-Samples/media-services-video-indexer/blob/master/IntroToVideoIndexer.md) 

No final do workshop terá uma boa compreensão do tipo de informação que pode ser extraída a partir de conteúdos de vídeo e áudio, estará mais preparado para identificar oportunidades relacionadas com a inteligência de conteúdo, pitch video AI no Azure e demo vários cenários no Indexer de Vídeo.

