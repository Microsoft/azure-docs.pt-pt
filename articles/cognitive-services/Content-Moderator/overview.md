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
ms.openlocfilehash: 570d55c8523e1c1deca3242a8841b0cc34322786
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80053875"
---
# <a name="what-is-azure-content-moderator"></a>O que é o Content Moderator do Azure?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

O Azure Content Moderator é um serviço cognitivo que verifica o conteúdo de texto, imagem e vídeo para material potencialmente ofensivo, arriscado ou de outra forma indesejável. Quando este material é encontrado, o serviço aplica etiquetas (bandeiras) adequadas ao conteúdo. Em seguida, a aplicação pode processar o conteúdo sinalizado para cumprir os regulamentos ou manter o ambiente desejado para os utilizadores. Consulte a secção [APIs de moderação](#moderation-apis) para saber mais sobre o que as diferentes bandeiras de conteúdo indicam.

## <a name="where-its-used"></a>Onde é usado

Seguem-se alguns cenários onde um programador ou uma equipa de software pode utilizar o Content Moderator:

- Marketplaces online que moderam catálogos de produtos e outros conteúdos gerados pelo utilizador.
- Empresas de jogos que moderam artefactos de jogo gerados pelo utilizador e salas de chat.
- Plataformas de mensagens sociais que moderam imagens, textos e vídeos adicionados pelos seus utilizadores.
- Empresas de media empresariais que implementam moderação centralizada para o seu conteúdo.
- Os fornecedores de soluções educativas K-12 filtram conteúdos que são inadequados para estudantes e educadores.

> [!NOTE]
> Não é possível utilizar o Moderador de Conteúdo para detetar imagens ilegais de exploração infantil. No entanto, organizações qualificadas podem usar o [PhotoDNA Cloud Service](https://www.microsoft.com/photodna "Serviço de Nuvem de FotodNA da Microsoft") para rastrear este tipo de conteúdo.

## <a name="what-it-includes"></a>O que inclui

O serviço Content Moderator é composto por várias APIs de serviço Web disponíveis através de chamadas REST e de um SDK .NET. Também inclui a ferramenta de revisão humana, que permite que revisores ajudem o serviço e melhorem ou ajustem sua função de moderação.

## <a name="moderation-apis"></a>APIs de Moderação

O serviço moderador de conteúdo inclui APIs de moderação, que verificam o conteúdo de material potencialmente inadequado ou censurável.

![diagrama de bloco para APIs de moderação moderador de conteúdo](images/content-moderator-mod-api.png)

A tabela seguinte descreve os diferentes tipos de APIs de moderação.

| Grupo API | Descrição |
| ------ | ----------- |
|[**Moderação de texto**](text-moderation-api.md)| Digitaliza texto para conteúdo ofensivo, conteúdo sexualmente explícito ou sugestivo, profanação e dados pessoais.|
|[**Listas personalizadas de termos**](try-terms-list-api.md)| Digitaliza texto contra uma lista personalizada de termos, juntamente com os termos incorporados. Utilize listas personalizadas para bloquear ou permitir conteúdo de acordo com as suas próprias políticas de conteúdo.|  
|[**Moderação de imagem**](image-moderation-api.md)| Procura imagens com conteúdo para adultos ou indecoroso, deteta texto em imagens com a função de OCR (reconhecimento ótico de carateres) e deteta rostos.|
|[**Listas personalizadas de imagens**](try-image-list-api.md)| Analisa as imagens, comparando-as com uma lista personalizada de imagens. Utilize listas personalizadas de imagens para filtrar instâncias de conteúdo normalmente recorrente, que não pretende classificar novamente.|
|[**Moderação de vídeo**](video-moderation-api.md)| Analisa vídeos relativamente a conteúdo para adultos ou indecoroso e devolve marcadores de hora para o dito conteúdo.|

## <a name="review-apis"></a>APIs de revisão

As APIs de revisão permitem-lhe integrar o seu pipeline de moderação com revisores humanos. Utilize as operações [jobs,](review-api.md#jobs) [reviews](review-api.md#reviews)e [workflow](review-api.md#workflows) para criar e automatizar fluxos de trabalho humanos em loop com a [ferramenta Review](#the-review-tool) (abaixo).

> [!NOTE]
> O Workflow API ainda não está disponível no .NET SDK, mas pode ser utilizado com o ponto final REST.

![diagrama de bloco para apis de revisão de moderador de conteúdo](images/content-moderator-rev-api.png)

## <a name="the-review-tool"></a>A ferramenta de revisão

O serviço de Moderador de Conteúdo também inclui a [ferramenta de revisão](Review-Tool-User-Guide/human-in-the-loop.md)baseada na Web, que acolhe as avaliações de conteúdo para moderadores humanos para processar. A entrada humana não treina o serviço, mas o trabalho combinado das equipas de serviço e revisão humana permite que os desenvolvedores atinjam o equilíbrio certo entre eficiência e precisão. A ferramenta Review também fornece uma extremidade frontal amigável para vários recursos moderadores de conteúdo.

![Home page da ferramenta de revisão humana do Content Moderator](images/homepage.PNG)

## <a name="data-privacy-and-security"></a>Privacidade e segurança de dados

Tal como acontece com todos os Serviços Cognitivos, os desenvolvedores que utilizam o serviço Moderador de Conteúdo devem estar cientes das políticas da Microsoft em dados de clientes. Consulte a página de [Serviços Cognitivos](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) no Microsoft Trust Center para saber mais.

## <a name="next-steps"></a>Passos seguintes

Começar a utilizar o serviço Moderador de Conteúdo seguindo as instruções em ['Tentar Moderador de Conteúdo' na web](quick-start.md).
