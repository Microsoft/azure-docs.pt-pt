---
title: O que é o Content Moderator do Azure?
titleSuffix: Azure Cognitive Services
description: Saiba como utilizar o Content Moderator para controlar, sinalizar, avaliar e filtrar informação inadequada em conteúdos gerados pelo utilizador.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: overview
ms.date: 12/15/2020
ms.author: pafarley
ms.custom: cog-serv-seo-aug-2020
keywords: moderador de conteúdo, moderador de conteúdo azul, moderador on-line, software de filtragem de conteúdos, serviço de moderação de conteúdos, moderação de conteúdos
ms.openlocfilehash: a53611fdad84f06661f3b8928296b6a45851cea4
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/23/2021
ms.locfileid: "104867275"
---
# <a name="what-is-azure-content-moderator"></a>O que é o Content Moderator do Azure?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

O Azure Content Moderador é um serviço de IA que permite lidar com conteúdos potencialmente ofensivos, arriscados ou indesejáveis. Inclui o serviço de moderação de conteúdo movido a IA que digitaliza texto, imagem e vídeos e aplica automaticamente bandeiras de conteúdo, bem como a ferramenta Review, um ambiente moderador online para uma equipa de revisores humanos.

Pode querer criar software de filtragem de conteúdos na sua app para cumprir os regulamentos ou manter o ambiente pretendido para os seus utilizadores.

Esta documentação contém os seguintes tipos de artigos:  

* [**Os quickstarts**](client-libraries.md) estão a iniciar instruções para guiá-lo através da realização de pedidos ao serviço.  
* [**Os guias de como fazer**](try-text-api.md) contêm instruções para a utilização do serviço de formas mais específicas ou personalizadas.  
* [**Os conceitos**](text-moderation-api.md) fornecem explicações aprofundadas sobre a funcionalidade e funcionalidades do serviço.  
* [**Os tutoriais**](ecommerce-retail-catalog-moderation.md) são guias mais longos que mostram como usar o serviço como componente em soluções de negócio mais amplas.  

## <a name="where-its-used"></a>Onde é usado

Seguem-se alguns cenários em que um desenvolvedor de software ou equipa exigiria um serviço de moderação de conteúdo:

- Mercados online que moderam catálogos de produtos e outros conteúdos gerados pelo utilizador.
- Empresas de jogos que moderam artefactos de jogos gerados pelo utilizador e salas de chat.
- Plataformas de mensagens sociais que moderam imagens, textos e vídeos adicionados pelos seus utilizadores.
- Empresas de media empresariais que implementam moderação centralizada para o seu conteúdo.
- Os fornecedores de soluções de educação K-12 filtram conteúdos inadequados para estudantes e educadores.

> [!IMPORTANT]
> Não é possível utilizar o Moderador de Conteúdo para detetar imagens ilegais de exploração infantil. No entanto, as organizações qualificadas podem usar o [Serviço de Nuvem PhotoDNA](https://www.microsoft.com/photodna "Microsoft PhotoDNA Cloud Service") para rastrear este tipo de conteúdo.

## <a name="what-it-includes"></a>O que inclui

O serviço Content Moderator é composto por várias APIs de serviço Web disponíveis através de chamadas REST e de um SDK .NET. Inclui também a ferramenta Review, que permite aos revisores humanos ajudar o serviço e melhorar ou afinar a sua função de moderação.

## <a name="moderation-apis"></a>APIs de Moderação

O serviço Content Moderator inclui APIs de Moderação, que verificam o conteúdo de material potencialmente inadequado ou censurável.

![diagrama de bloco para APIs de moderação de moderador de conteúdo](images/content-moderator-mod-api.png)

A tabela a seguir descreve os diferentes tipos de APIs de moderação.

| Grupo API | Descrição |
| ------ | ----------- |
|[**Moderação de texto**](text-moderation-api.md)| Verifica texto para conteúdo ofensivo, conteúdo sexualmente explícito ou sugestivo, profanação e dados pessoais.|
|[**Listas personalizadas de termos**](try-terms-list-api.md)| Digitaliza o texto contra uma lista personalizada de termos, juntamente com os termos incorporados. Utilize listas personalizadas para bloquear ou permitir conteúdo de acordo com as suas próprias políticas de conteúdo.|  
|[**Moderação de imagens**](image-moderation-api.md)| Procura imagens com conteúdo para adultos ou indecoroso, deteta texto em imagens com a função de OCR (reconhecimento ótico de carateres) e deteta rostos.|
|[**Listas personalizadas de imagens**](try-image-list-api.md)| Analisa as imagens, comparando-as com uma lista personalizada de imagens. Utilize listas personalizadas de imagens para filtrar instâncias de conteúdo normalmente recorrente, que não pretende classificar novamente.|
|[**Moderação de vídeos**](video-moderation-api.md)| Analisa vídeos relativamente a conteúdo para adultos ou indecoroso e devolve marcadores de hora para o dito conteúdo.|

## <a name="review-apis"></a>APIs de revisão

As APIs de revisão permitem-lhe integrar o seu pipeline de moderação com revisores humanos. Utilize as operações [Jobs,](review-api.md#jobs) [Reviews](review-api.md#reviews)e [Workflow](review-api.md#workflows) para criar e automatizar fluxos de trabalho humanos em loop com a [ferramenta Review](#review-tool) (abaixo).

> [!NOTE]
> A API do Fluxo de Trabalho ainda não está disponível no .NET SDK, mas pode ser utilizada com o ponto final REST.

![diagrama de bloco para revisão de moderador de conteúdo APIs](images/content-moderator-rev-api.png)

## <a name="review-tool"></a>Ferramenta de revisão

O serviço Desemisco de Conteúdo também inclui a [ferramenta Review](Review-Tool-User-Guide/human-in-the-loop.md)baseada na Web, que acolhe as avaliações de conteúdo para moderadores humanos processarem. A entrada humana não treina o serviço, mas o trabalho combinado do serviço e das equipas de revisão humana permite que os desenvolvedores atinjam o equilíbrio certo entre eficiência e precisão. A ferramenta Review também fornece uma extremidade frontal fácil de usar para vários recursos de Moderador de Conteúdo.

![Página inicial da ferramenta de análise de moderador de conteúdo](images/homepage.PNG)

## <a name="data-privacy-and-security"></a>Privacidade e segurança dos dados

Tal como acontece com todos os Serviços Cognitivos, os desenvolvedores que usam o serviço Desemaco de Conteúdo devem estar cientes das políticas da Microsoft sobre os dados dos clientes. Consulte a [página de Serviços Cognitivos](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) no Microsoft Trust Center para saber mais.

## <a name="next-steps"></a>Passos seguintes

Para começar a utilizar o Moderador de Conteúdo no portal da web, siga [o Moderador de Conteúdo na web](quick-start.md). Ou, completar uma [biblioteca de clientes ou REST API iniciar](client-libraries.md) para implementar os cenários básicos em código.