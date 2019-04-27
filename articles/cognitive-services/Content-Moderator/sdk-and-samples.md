---
title: SDKs e exemplos - Content Moderator, Python, Java, Node.js e .NET
titlesuffix: Azure Cognitive Services
description: Obter SDKs e exemplos do Content Moderator
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: sample
ms.date: 02/27/2018
ms.author: sajagtap
ms.openlocfilehash: fd71a48372bcdb459bb3b7509e9a9c5dba529555
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60607111"
---
# <a name="content-moderator-sdks-and-samples"></a>SDKs e exemplos do Content Moderator

## <a name="sdks-for-python-java-nodejs-and-net"></a>SDKs de Python, Java, Node.js e .NET

- [SDK do Content Moderator para Python](https://pypi.python.org/pypi/azure-cognitiveservices-vision-contentmoderator)
- [SDK do Content Moderator para Java](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-cognitiveservices-contentmoderator%22)
- [SDK do Content Moderator para Node.js](https://www.npmjs.com/package/azure-cognitiveservices-contentmoderator)
- [SDK do Content Moderator para .NET](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/)

## <a name="net-sdk-samples"></a>Exemplos de SDK .NET

A lista seguinte inclui ligações para exemplos de código criados com o SDK do Azure Content Moderator para .NET.

- **Biblioteca auxiliar**: [Criar um cliente, o Content Moderator para utilização nos outros exemplos](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ModeratorHelper/Clients.cs). Veja o [guia de início rápido](content-moderator-helper-quickstart-dotnet.md).

### <a name="moderation"></a>Moderação 
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

### <a name="review"></a>Rever
- **Tarefas de imagem**: [Iniciar uma tarefa de moderação que analisa e cria as revisões](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageJobs/Program.cs). Veja o [guia de início rápido](moderation-jobs-quickstart-dotnet.md).
- **Revisões de imagem**: [Criar as revisões de humanos em loop](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageReviews/Program.cs). Veja o [guia de início rápido](moderation-reviews-quickstart-dotnet.md).
- **Revisões de vídeo**: [Criar as revisões de vídeo de humanos em loop](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoReviews/Program.cs). Ver o [guia de início rápido](video-reviews-quickstart-dotnet.md)
- **Revisões de transcrição de vídeo**: [Criar as revisões de transcrição de vídeo humanos no loop for](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoTranscriptReviews/Program.cs) consulte [início rápido](video-reviews-quickstart-dotnet.md)

Veja todos os exemplos de .NET nos [exemplos de .NET do Content Moderator no GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator).

## <a name="rest-api-samples-in-c"></a>Exemplos de API REST em C#

- [Moderação de imagens](https://github.com/MicrosoftContentModerator/ContentModerator-API-Samples/tree/master/ImageModeration)
- [Moderação de texto](https://github.com/MicrosoftContentModerator/ContentModerator-API-Samples/tree/master/TextModeration)
- [Moderação de vídeo](https://github.com/MicrosoftContentModerator/ContentModerator-API-Samples/tree/master/VideoModeration)
- [Revisões de imagem](https://github.com/MicrosoftContentModerator/ContentModerator-API-Samples/tree/master/ImageReviews)
- [Tarefas de imagem](https://github.com/MicrosoftContentModerator/ContentModerator-API-Samples/tree/master/ImageJob)

Para instruções destes exemplos, consulte o [webinar a pedido](https://info.microsoft.com/cognitive-services-content-moderator-ondemand.html).

## <a name="tutorials"></a>Tutoriais
- [Moderação do catálogo de eCommerce](https://github.com/MicrosoftContentModerator/samples-eCommerceCatalogModeration). Veja o [tutorial](ecommerce-retail-catalog-moderation.md).
- [Moderação de conteúdos do Facebook](https://github.com/MicrosoftContentModerator/samples-fbPageModeration). Veja o [tutorial](facebook-post-moderation.md).
- [Solução de moderação e revisão de conversão e vídeo ](https://github.com/MicrosoftContentModerator/VideoReviewConsoleApp) Ver [tutorial](video-transcript-moderation-review-tutorial-dotnet.md)

## <a name="on-demand-webinars"></a>Webinars a pedido
- [Moderação de conteúdos assistida por computador à escala com o Content Moderator](https://info.microsoft.com/cognitive-services-content-moderator-ondemand.html)
