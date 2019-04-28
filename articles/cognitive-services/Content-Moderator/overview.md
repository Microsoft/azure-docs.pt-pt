---
title: O que é o Content Moderator do Azure?
titlesuffix: Azure Cognitive Services
description: Saiba como utilizar o Content Moderator para controlar, sinalizar, avaliar e filtrar informação inadequada em conteúdos gerados pelo utilizador.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: overview
ms.date: 02/20/2019
ms.author: pafarley
ms.openlocfilehash: 440471acb6e122bf25ba21b0ab3b5a2f7d9b021d
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62129439"
---
# <a name="what-is-azure-content-moderator"></a>O que é o Content Moderator do Azure?

A API do Content Moderator do Azure é um serviço cognitivo que verifica a existência de material potencialmente ofensivo, duvidoso ou indesejável em conteúdo de texto, imagem e vídeo. Quando este tipo de material é encontrado, o serviço aplica etiquetas adequadas (sinalizadores) ao conteúdo. Em seguida, a aplicação pode processar o conteúdo sinalizado para cumprir os regulamentos ou manter o ambiente desejado para os utilizadores. Veja a secção [APIs do Content Moderator](#content-moderator-apis) para saber mais sobre o que indicam os diferentes sinalizadores de conteúdo.

## <a name="where-it-is-used"></a>Onde é utilizado

Seguem-se alguns cenários onde um programador ou uma equipa de software pode utilizar o Content Moderator:

- Mercados online que moderam catálogos de produtos e outros conteúdos gerados pelo utilizador
- Empresas de jogos que moderam artefactos de jogo e salas de conversa gerados pelo utilizador
- Plataformas de redes sociais que moderam as imagens, o texto e os vídeos adicionados pelos utilizadores
- Empresas multimédia empresariais que implementam a moderação centralizada dos respetivos conteúdos
- Fornecedores de soluções de educação primária e secundária que filtram conteúdo inadequado para estudantes e educadores

## <a name="what-it-includes"></a>O que inclui

O serviço Content Moderator é composto por várias APIs de serviço Web disponíveis através de chamadas REST e de um SDK .NET. Também inclui a ferramenta de revisão humana, que permite que revisores ajudem o serviço e melhorem ou ajustem sua função de moderação.

![Diagrama de bloco do Content Moderator, que mostra as APIs de Moderação, as APIs de Revisão e a ferramenta de revisão humana](images/content-moderator-block-diagram.png)

### <a name="content-moderator-apis"></a>APIs do Content Moderator

O serviço Content Moderator inclui APIs para os seguintes cenários.

| Grupo de API | Descrição |
| ------ | ----------- |
|[**Moderação de texto**](text-moderation-api.md)| Analisa o texto para conteúdo ofensivo, conteúdo sexualmente explícito ou suggestive, linguagem inapropriada e dados pessoais.|
|[**Listas personalizadas de termos**](try-terms-list-api.md)| Analisa o texto, comparando-o com uma lista personalizada de termos, além dos termos incorporados. Utilize listas personalizadas para bloquear ou permitir conteúdo de acordo com as suas próprias políticas de conteúdo.|  
|[**Moderação de imagens**](image-moderation-api.md)| Procura imagens com conteúdo para adultos ou indecoroso, deteta texto em imagens com a função de OCR (reconhecimento ótico de carateres) e deteta rostos.|
|[**Listas personalizadas de imagens**](try-image-list-api.md)| Analisa as imagens, comparando-as com uma lista personalizada de imagens. Utilize listas personalizadas de imagens para filtrar instâncias de conteúdo normalmente recorrente, que não pretende classificar novamente.|
|[**Moderação de vídeo**](video-moderation-api.md)| Analisa vídeos relativamente a conteúdo para adultos ou indecoroso e devolve marcadores de hora para o dito conteúdo.|
|[**APIs de revisão**](try-review-api-job.md)| Utilize as operações [Tarefas](try-review-api-job.md), [Revisões](try-review-api-review.md) e [Fluxo de Trabalho](try-review-api-workflow.md) para criar e automatizar fluxos de trabalho com interação humana com a ferramenta de revisão humana. A API de fluxo de trabalho ainda não está disponível no SDK do .NET.|

### <a name="review-tool"></a>Ferramenta de revisão

O serviço do Content Moderator também inclui baseada na web [ferramenta de revisão](Review-Tool-User-Guide/human-in-the-loop.md), que aloja o conteúdo revisões para moderadores humanos processar. A contribuição humana não prepara o serviço, mas o trabalho combinado do serviço e das equipas de revisão humana permite que os programadores encontrem o equilíbrio certo entre eficiência e precisão. A ferramenta de revisão também fornece um amigável front-end para uma variedade de recursos do Content Moderator.

![Home page da ferramenta de revisão humana do Content Moderator](images/homepage.PNG)

## <a name="data-privacy-and-security"></a>Segurança e privacidade dos dados

Como com todos os serviços cognitivos, os desenvolvedores que usam o serviço do Content Moderator devem estar ciente das políticas da Microsoft em dados do cliente. Consulte a [página dos serviços cognitivos](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) no Microsoft Trust Center para obter mais informações.

## <a name="next-steps"></a>Passos Seguintes

Começar a utilizar o serviço do Content Moderator, seguindo as instruções em [Experimente o Content Moderator na web](quick-start.md).