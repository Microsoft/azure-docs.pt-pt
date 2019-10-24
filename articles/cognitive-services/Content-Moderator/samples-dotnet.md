---
title: Exemplos de código – Content Moderator, .NET
titleSuffix: Azure Cognitive Services
description: Use Content Moderator em seus aplicativos .NET por meio do SDK.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: sample
ms.date: 01/10/2019
ms.author: pafarley
ms.openlocfilehash: 23ef7b515f5391a30f6d1fa9fce1dcb856643c69
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2019
ms.locfileid: "72757271"
---
# <a name="content-moderator-net-sdk-samples"></a>Exemplos do SDK do .NET Content Moderator

A lista seguinte inclui ligações para exemplos de código criados com o SDK do Azure Content Moderator para .NET.

## <a name="moderation"></a>Moderação

- **Moderação de imagens**: [avalie uma imagem relativamente a conteúdo para adultos e indecoroso, texto e rostos](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageModeration/Program.cs). Veja o [guia de início rápido](image-moderation-quickstart-dotnet.md).
- **Imagens personalizadas**: [moderar com listas personalizadas de imagens](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageListManagement/Program.cs). Veja o [guia de início rápido](image-lists-quickstart-dotnet.md).

> [!NOTE]
> Existe um limite máximo de **5 listas de imagens**, sendo que cada lista **não pode exceder 10 000 imagens**.
>

- **Moderação de texto**: [texto de tela para dados pessoais e obscenas](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/TextModeration/Program.cs). Veja o [guia de início rápido](text-moderation-quickstart-dotnet.md).
- **Termos personalizados**: [modere com listas personalizadas de termos](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/TermListManagement/Program.cs). Veja o [guia de início rápido](term-lists-quickstart-dotnet.md).

> [!NOTE]
> Existe um limite máximo de **5 listas de termos**, sendo que cada lista **não pode exceder 10 000 termos**.
>

- **Moderação de vídeos**: [analise um vídeo relativamente a conteúdo para adultos e indecoroso, e obtenha resultados](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoModeration/Program.cs). Veja o [guia de início rápido](video-moderation-api.md).

## <a name="review"></a>Rever

- **Tarefas de imagem**: [inicie uma tarefa de moderação que analisa e cria revisões](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageJobs/Program.cs). Veja o [guia de início rápido](moderation-jobs-quickstart-dotnet.md).
- **Revisões de imagem**: [crie as revisões para interação humana](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageReviews/Program.cs). Veja o [guia de início rápido](moderation-reviews-quickstart-dotnet.md).
- **Revisões de vídeo**: [crie revisões de vídeos para interação humana](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoReviews/Program.cs). Ver o [guia de início rápido](video-reviews-quickstart-dotnet.md)
- **Revisões de conversão de vídeo**: [crie revisões de conversão de vídeo para interação humana](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoTranscriptReviews/Program.cs) Ver o [guia de início rápido](video-reviews-quickstart-dotnet.md)

Veja todos os exemplos de .NET nos [exemplos de .NET do Content Moderator no GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator).
