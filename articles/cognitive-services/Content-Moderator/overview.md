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
ms.date: 07/03/2019
ms.author: pafarley
ms.openlocfilehash: a78a92a33075a97ddadb2e1fe677b7ded541d12c
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68565578"
---
# <a name="what-is-azure-content-moderator"></a>O que é o Content Moderator do Azure?

A API do Content Moderator do Azure é um serviço cognitivo que verifica a existência de material potencialmente ofensivo, duvidoso ou indesejável em conteúdo de texto, imagem e vídeo. Quando este tipo de material é encontrado, o serviço aplica etiquetas adequadas (sinalizadores) ao conteúdo. Em seguida, a aplicação pode processar o conteúdo sinalizado para cumprir os regulamentos ou manter o ambiente desejado para os utilizadores. Consulte a seção [APIs](#moderation-apis) de moderação para saber mais sobre o que os diferentes sinalizadores de conteúdo indicam.

## <a name="where-it-is-used"></a>Onde é utilizado

Seguem-se alguns cenários onde um programador ou uma equipa de software pode utilizar o Content Moderator:

- Marketplaces online que moderam catálogos de produtos e outros conteúdos gerados pelo usuário.
- Empresas de jogos que continham artefatos de jogos e salas de bate-papo gerados pelo usuário.
- Plataformas de mensagens sociais que imagens, textos e vídeos moderados são adicionados por seus usuários.
- Empresas de mídia empresarial que implementam moderação centralizada para seu conteúdo.
- Os provedores de soluções de educação K-12 filtram o conteúdo que é inadequado para estudantes e educadores.

> [!NOTE]
> Você não pode usar Content Moderator para detectar imagens de exploração de filhos ilegais. No entanto, as organizações qualificadas podem usar o serviço de [nuvem do PhotoDNA](https://www.microsoft.com/photodna "Microsoft PhotoDNA Cloud Service") para fazer a tela para esse tipo de conteúdo.

## <a name="what-it-includes"></a>O que inclui

O serviço Content Moderator é composto por várias APIs de serviço Web disponíveis através de chamadas REST e de um SDK .NET. Também inclui a ferramenta de revisão humana, que permite que revisores ajudem o serviço e melhorem ou ajustem sua função de moderação.

## <a name="moderation-apis"></a>APIs de Moderação

O serviço de Content Moderator inclui APIs de moderação, que verificam o conteúdo de materiais que são potencialmente inadequados ou censuráveis.

![diagrama de bloco para APIs de moderação de Content Moderator](images/content-moderator-mod-api.png)

A tabela a seguir descreve os diferentes tipos de APIs de moderação.

| Grupo de API | Descrição |
| ------ | ----------- |
|[**Moderação de texto**](text-moderation-api.md)| Verifica o texto em busca de conteúdo ofensivo, de sexo explícito ou de conteúdo sugerido, de profanação e de dados pessoais.|
|[**Listas personalizadas de termos**](try-terms-list-api.md)| Analisa o texto, comparando-o com uma lista personalizada de termos, além dos termos incorporados. Utilize listas personalizadas para bloquear ou permitir conteúdo de acordo com as suas próprias políticas de conteúdo.|  
|[**Moderação de imagens**](image-moderation-api.md)| Procura imagens com conteúdo para adultos ou indecoroso, deteta texto em imagens com a função de OCR (reconhecimento ótico de carateres) e deteta rostos.|
|[**Listas personalizadas de imagens**](try-image-list-api.md)| Analisa as imagens, comparando-as com uma lista personalizada de imagens. Utilize listas personalizadas de imagens para filtrar instâncias de conteúdo normalmente recorrente, que não pretende classificar novamente.|
|[**Moderação de vídeo**](video-moderation-api.md)| Analisa vídeos relativamente a conteúdo para adultos ou indecoroso e devolve marcadores de hora para o dito conteúdo.|

## <a name="review-apis"></a>APIs de revisão

As APIs de revisão permitem que você integre seu pipeline de moderação com revisores humanos. Use [tarefas](review-api.md#jobs), [análises](review-api.md#reviews)e operações de [fluxo](review-api.md#workflows) de trabalho para criar e automatizar fluxos de trabalho humanos no loop com a ferramenta de [revisão](#the-review-tool) (abaixo).

> [!NOTE]
> A API de fluxo de trabalho ainda não está disponível no SDK do .NET, mas pode ser usada com o ponto de extremidade REST.

![diagrama de bloco para APIs de revisão de Content Moderator](images/content-moderator-rev-api.png)

## <a name="the-review-tool"></a>A ferramenta de revisão

O serviço de Content Moderator também inclui a ferramenta de [revisão](Review-Tool-User-Guide/human-in-the-loop.md)baseada na Web, que hospeda as revisões de conteúdo para que os moderadores humanos processem. A contribuição humana não prepara o serviço, mas o trabalho combinado do serviço e das equipas de revisão humana permite que os programadores encontrem o equilíbrio certo entre eficiência e precisão. A ferramenta de revisão também fornece um front-end amigável para uma variedade de recursos de Content Moderator.

![Home page da ferramenta de revisão humana do Content Moderator](images/homepage.PNG)

## <a name="data-privacy-and-security"></a>Privacidade e segurança de dados

Assim como acontece com todos os serviços cognitivas, os desenvolvedores que usam o serviço de Content Moderator devem estar cientes das políticas da Microsoft nos dados do cliente. Consulte a [página serviços cognitivas](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) na central de confiabilidade da Microsoft para saber mais.

## <a name="next-steps"></a>Passos Seguintes

Comece a usar o serviço de Content Moderator seguindo as instruções em [Content moderator de teste na Web](quick-start.md).