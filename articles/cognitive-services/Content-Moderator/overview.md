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
ms.date: 07/03/2019
ms.author: pafarley
ms.openlocfilehash: d814a943bc8dc789abe84b33583714beb998c0ef
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67607007"
---
# <a name="what-is-azure-content-moderator"></a>O que é o Content Moderator do Azure?

A API do Content Moderator do Azure é um serviço cognitivo que verifica a existência de material potencialmente ofensivo, duvidoso ou indesejável em conteúdo de texto, imagem e vídeo. Quando este tipo de material é encontrado, o serviço aplica etiquetas adequadas (sinalizadores) ao conteúdo. Em seguida, a aplicação pode processar o conteúdo sinalizado para cumprir os regulamentos ou manter o ambiente desejado para os utilizadores. Consulte a [APIs de moderação](#moderation-apis) indicar secção para saber mais sobre o que sinaliza o conteúdo diferente.

## <a name="where-it-is-used"></a>Onde é utilizado

Seguem-se alguns cenários onde um programador ou uma equipa de software pode utilizar o Content Moderator:

- Mercados online que moderada de catálogos de produtos e outros conteúdos gerados pelo utilizador.
- Empresas de jogos que moderado gerados pelo utilizador artefactos de jogos e salas de chat.
- Plataformas de mensagem sociais que moderar as imagens, texto e vídeos adicionados pelos seus utilizadores.
- Empresas de multimédia de Enterprise que implementam a moderação centralizada para o seu conteúdo.
- Fornecedores de soluções de educação K-12 filtragem de conteúdo que não é apropriado para estudantes e educadores.

> [!NOTE]
> Não é possível utilizar o Content Moderator de detetar imagens de exploração de subordinados ilegal. No entanto, as organizações qualificadas podem utilizar o [serviço de Cloud PhotoDNA](https://www.microsoft.com/photodna "serviço Cloud da Microsoft PhotoDNA") ao ecrã para este tipo de conteúdo.

## <a name="what-it-includes"></a>O que inclui

O serviço Content Moderator é composto por várias APIs de serviço Web disponíveis através de chamadas REST e de um SDK .NET. Também inclui a ferramenta de revisão humana, que permite que revisores ajudem o serviço e melhorem ou ajustem sua função de moderação.

## <a name="moderation-apis"></a>APIs de Moderação

O serviço do Content Moderator inclui APIs de moderação, o que verificar conteúdo de material é potencialmente inadequado ou objetáveis.

![Diagrama de bloco para APIs de moderação do Content Moderator](images/content-moderator-mod-api.png)

A tabela seguinte descreve os diferentes tipos de APIs de moderação.

| Grupo de API | Descrição |
| ------ | ----------- |
|[**Moderação de texto**](text-moderation-api.md)| Analisa o texto para conteúdo ofensivo, conteúdo sexualmente explícito ou suggestive, linguagem inapropriada e dados pessoais.|
|[**Listas personalizadas de termos**](try-terms-list-api.md)| Analisa o texto, comparando-o com uma lista personalizada de termos, além dos termos incorporados. Utilize listas personalizadas para bloquear ou permitir conteúdo de acordo com as suas próprias políticas de conteúdo.|  
|[**Moderação de imagens**](image-moderation-api.md)| Procura imagens com conteúdo para adultos ou indecoroso, deteta texto em imagens com a função de OCR (reconhecimento ótico de carateres) e deteta rostos.|
|[**Listas personalizadas de imagens**](try-image-list-api.md)| Analisa as imagens, comparando-as com uma lista personalizada de imagens. Utilize listas personalizadas de imagens para filtrar instâncias de conteúdo normalmente recorrente, que não pretende classificar novamente.|
|[**Moderação de vídeo**](video-moderation-api.md)| Analisa vídeos relativamente a conteúdo para adultos ou indecoroso e devolve marcadores de hora para o dito conteúdo.|

## <a name="review-apis"></a>APIs de revisão

As APIs de revisão permitem-lhe integrar o seu pipeline de moderação com os revisores humanos. Utilize o [trabalhos](review-api.md#jobs), [revisões](review-api.md#reviews), e [fluxo de trabalho](review-api.md#workflows) operações para criar e automatizar fluxos de trabalho humanos no loop com o [ferramenta de revisão](#the-review-tool) ( abaixo).

> [!NOTE]
> A API de fluxo de trabalho ainda não está disponível no SDK do .NET, mas pode ser utilizada com o ponto final REST.

![Diagrama de bloco para Content Moderator reveja APIs](images/content-moderator-rev-api.png)

## <a name="the-review-tool"></a>A ferramenta de revisão

O serviço do Content Moderator também inclui baseada na web [ferramenta de revisão](Review-Tool-User-Guide/human-in-the-loop.md), que aloja o conteúdo revisões para moderadores humanos processar. A contribuição humana não prepara o serviço, mas o trabalho combinado do serviço e das equipas de revisão humana permite que os programadores encontrem o equilíbrio certo entre eficiência e precisão. A ferramenta de revisão também fornece um front-end amigável de utilizador para uma variedade de recursos do Content Moderator.

![Home page da ferramenta de revisão humana do Content Moderator](images/homepage.PNG)

## <a name="data-privacy-and-security"></a>Segurança e privacidade dos dados

Como com todos os serviços cognitivos, os desenvolvedores que usam o serviço do Content Moderator devem estar ciente das políticas da Microsoft em dados do cliente. Consulte a [página dos serviços cognitivos](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) no Microsoft Trust Center para obter mais informações.

## <a name="next-steps"></a>Passos Seguintes

Começar a utilizar o serviço do Content Moderator, seguindo as instruções em [Experimente o Content Moderator na web](quick-start.md).