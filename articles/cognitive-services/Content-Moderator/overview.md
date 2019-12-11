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
ms.date: 12/05/2019
ms.author: pafarley
ms.openlocfilehash: c1ff74bc358f167612b2bfe05ca47e21203d8702
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74973497"
---
# <a name="what-is-azure-content-moderator"></a>O que é o Content Moderator do Azure?

O Azure Content Moderator é um serviço cognitiva que verifica o conteúdo de texto, imagem e vídeo para materiais potencialmente ofensivos, arriscados ou de outra forma indesejáveis. Quando esse material é encontrado, o serviço aplica os rótulos (sinalizadores) apropriados ao conteúdo. Em seguida, a aplicação pode processar o conteúdo sinalizado para cumprir os regulamentos ou manter o ambiente desejado para os utilizadores. Consulte a seção [APIs de moderação](#moderation-apis) para saber mais sobre o que os diferentes sinalizadores de conteúdo indicam.

## <a name="where-its-used"></a>Onde ele é usado

Seguem-se alguns cenários onde um programador ou uma equipa de software pode utilizar o Content Moderator:

- Marketplaces online que moderam catálogos de produtos e outros conteúdos gerados pelo usuário.
- Empresas de jogos que continham artefatos de jogos e salas de bate-papo gerados pelo usuário.
- Plataformas de mensagens sociais que imagens, textos e vídeos moderados são adicionados por seus usuários.
- Empresas de mídia empresarial que implementam moderação centralizada para seu conteúdo.
- Os provedores de soluções de educação K-12 filtram o conteúdo que é inadequado para estudantes e educadores.

> [!NOTE]
> Você não pode usar Content Moderator para detectar imagens de exploração de filhos ilegais. No entanto, as organizações qualificadas podem usar o [serviço de nuvem PhotoDNA](https://www.microsoft.com/photodna "Serviço de nuvem do Microsoft PhotoDNA") para fazer a tela para esse tipo de conteúdo.

## <a name="what-it-includes"></a>O que inclui

O serviço Content Moderator é composto por várias APIs de serviço Web disponíveis através de chamadas REST e de um SDK .NET. Também inclui a ferramenta de revisão humana, que permite que revisores ajudem o serviço e melhorem ou ajustem sua função de moderação.

## <a name="moderation-apis"></a>APIs de Moderação

O serviço de Content Moderator inclui APIs de moderação, que verificam o conteúdo de materiais que são potencialmente inadequados ou censuráveis.

![diagrama de bloco para APIs de moderação de Content Moderator](images/content-moderator-mod-api.png)

A tabela a seguir descreve os diferentes tipos de APIs de moderação.

| Grupo de API | Descrição |
| ------ | ----------- |
|[**Moderação de texto**](text-moderation-api.md)| Verifica o texto em busca de conteúdo ofensivo, de sexo explícito ou de conteúdo sugerido, de profanação e de dados pessoais.|
|[**Listas personalizadas de termos**](try-terms-list-api.md)| Examina o texto em relação a uma lista personalizada de termos junto com os termos internos. Utilize listas personalizadas para bloquear ou permitir conteúdo de acordo com as suas próprias políticas de conteúdo.|  
|[**Moderação de imagens**](image-moderation-api.md)| Procura imagens com conteúdo para adultos ou indecoroso, deteta texto em imagens com a função de OCR (reconhecimento ótico de carateres) e deteta rostos.|
|[**Listas personalizadas de imagens**](try-image-list-api.md)| Analisa as imagens, comparando-as com uma lista personalizada de imagens. Utilize listas personalizadas de imagens para filtrar instâncias de conteúdo normalmente recorrente, que não pretende classificar novamente.|
|[**Moderação de vídeo**](video-moderation-api.md)| Analisa vídeos relativamente a conteúdo para adultos ou indecoroso e devolve marcadores de hora para o dito conteúdo.|

## <a name="review-apis"></a>APIs de revisão

As APIs de revisão permitem que você integre seu pipeline de moderação com revisores humanos. Use [tarefas](review-api.md#jobs), [análises](review-api.md#reviews)e operações de [fluxo](review-api.md#workflows) de trabalho para criar e automatizar fluxos de trabalho humanos no loop com a ferramenta de [revisão](#the-review-tool) (abaixo).

> [!NOTE]
> A API de fluxo de trabalho ainda não está disponível no SDK do .NET, mas pode ser usada com o ponto de extremidade REST.

![diagrama de bloco para APIs de revisão de Content Moderator](images/content-moderator-rev-api.png)

## <a name="the-review-tool"></a>A ferramenta de revisão

O serviço de Content Moderator também inclui a ferramenta de [revisão](Review-Tool-User-Guide/human-in-the-loop.md)baseada na Web, que hospeda as revisões de conteúdo para que os moderadores humanos processem. A entrada humana não treina o serviço, mas o trabalho combinado das equipes de serviço e de revisão humana permite que os desenvolvedores tenham o equilíbrio certo entre eficiência e precisão. A ferramenta de revisão também fornece um front-end amigável para vários Content Moderator recursos.

![Home page da ferramenta de revisão humana do Content Moderator](images/homepage.PNG)

## <a name="data-privacy-and-security"></a>Privacidade e segurança de dados

Assim como acontece com todos os serviços cognitivas, os desenvolvedores que usam o serviço de Content Moderator devem estar cientes das políticas da Microsoft nos dados do cliente. Consulte a [página serviços cognitivas](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) na central de confiabilidade da Microsoft para saber mais.

## <a name="next-steps"></a>Passos seguintes

Comece a usar o serviço de Content Moderator seguindo as instruções em [Content moderator de teste na Web](quick-start.md).