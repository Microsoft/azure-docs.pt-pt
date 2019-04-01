---
title: Exemplos de código – Content Moderator, .NET
description: Utilize o Content Moderator em seus aplicativos do .NET através do SDK.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: sample
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: f2b5fb5c44cbb2e0d766c49ed6b715484c22f62e
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2019
ms.locfileid: "58756733"
---
# <a name="content-moderator-net-sdk-samples"></a>Exemplos de SDK de .NET de moderador de conteúdo

A lista seguinte inclui ligações para exemplos de código criados com o SDK do Azure Content Moderator para .NET.

- **Biblioteca auxiliar**: [Criar um cliente, o Content Moderator para utilização nos outros exemplos](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ModeratorHelper/Clients.cs). Veja o [guia de início rápido](content-moderator-helper-quickstart-dotnet.md).

## <a name="moderation"></a>Moderação

- **Moderação de imagens**: [Avaliar uma imagem de conteúdo para adultos, texto e rostos](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageModeration/Program.cs). Veja o [guia de início rápido](image-moderation-quickstart-dotnet.md).
- **Imagens personalizadas**: [Moderada com listas de imagens personalizadas](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageListManagement/Program.cs). Veja o [guia de início rápido](image-lists-quickstart-dotnet.md).

> [!NOTE]
> Existe um limite máximo de **5 listas de imagens**, sendo que cada lista **não pode exceder 10 000 imagens**.
>

- **Moderação de texto**: [Ecrã de texto para os dados pessoais e de linguagem inapropriada](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/TextModeration/Program.cs). Veja o [guia de início rápido](text-moderation-quickstart-dotnet.md).
- **Termos personalizados**: [Moderada com listas de termos personalizado](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/TermListManagement/Program.cs). Veja o [guia de início rápido](term-lists-quickstart-dotnet.md).

> [!NOTE]
> Existe um limite máximo de **5 listas de termos**, sendo que cada lista **não pode exceder 10 000 termos**.
>

- **Moderação de vídeos**: [Analisar um vídeo para o conteúdo para adultos e obtenha resultados](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoModeration/Program.cs). Veja o [guia de início rápido](video-moderation-api.md).

## <a name="review"></a>Rever

- **Tarefas de imagem**: [Iniciar uma tarefa de moderação que analisa e cria as revisões](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageJobs/Program.cs). Veja o [guia de início rápido](moderation-jobs-quickstart-dotnet.md).
- **Revisões de imagem**: [Criar as revisões de humanos em loop](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageReviews/Program.cs). Veja o [guia de início rápido](moderation-reviews-quickstart-dotnet.md).
- **Revisões de vídeo**: [Criar as revisões de vídeo de humanos em loop](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoReviews/Program.cs). Ver o [guia de início rápido](video-reviews-quickstart-dotnet.md)
- **Revisões de transcrição de vídeo**: [Criar as revisões de transcrição de vídeo humanos no loop for](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoTranscriptReviews/Program.cs) consulte [início rápido](video-reviews-quickstart-dotnet.md)

Veja todos os exemplos de .NET nos [exemplos de .NET do Content Moderator no GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator).
