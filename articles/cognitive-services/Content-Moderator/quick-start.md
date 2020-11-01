---
title: 'Quickstart: Experimente o Moderador de Conteúdo na web'
titleSuffix: Azure Cognitive Services
description: Neste arranque rápido, utilizará a ferramenta online de Análise de Moderadores de Conteúdo para testar a funcionalidade básica do Moderador de Conteúdo sem ter de escrever qualquer código.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: quickstart
ms.date: 09/29/2020
ms.author: pafarley
ms.custom: cog-serv-seo-aug-2020
keywords: moderador de conteúdo, moderação de conteúdo
ms.openlocfilehash: d1d9315986f7a6c57c1da012b9034e4f1a3730bc
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2020
ms.locfileid: "93143743"
---
# <a name="quickstart-try-content-moderator-on-the-web"></a>Quickstart: Experimente o Moderador de Conteúdo na web

Neste arranque rápido, utilizará a ferramenta online de Análise de Moderadores de Conteúdo para testar a funcionalidade básica do Moderador de Conteúdo sem ter de escrever qualquer código. Se pretender integrar este serviço na sua aplicação de moderação de conteúdo mais rapidamente, consulte os outros quickstarts na secção [Etapas Seguintes.](#next-steps)

## <a name="prerequisites"></a>Pré-requisitos

- Um navegador web

## <a name="set-up-the-review-tool"></a>Configurar a ferramenta de revisão
A ferramenta content Moderator Review é uma ferramenta baseada na web que permite aos revisores humanos ajudar o serviço cognitivo na tomada de decisões. Neste guia, irá passar pelo curto processo de configuração da ferramenta de revisão para que possa ver como funciona o serviço Desemaçamento de Conteúdos. Vá ao site da [ferramenta De Revisão de Moderadores](https://contentmoderator.cognitive.microsoft.com/) de Conteúdo e inscreva-se.

![Página inicial do moderador de conteúdo](images/homepage.PNG)

## <a name="create-a-review-team"></a>Criar uma equipa de revisão

Em seguida, criar uma equipa de revisão. Num cenário de trabalho, esta equipa será o grupo de pessoas que reverão manualmente as decisões de moderação do serviço. Para criar uma equipa, terá de selecionar uma **Região** e fornecer um **Nome de Equipa** e um **ID de equipa.** Se desejar convidar colegas para a equipa, pode fazê-lo inserindo os seus endereços de e-mail aqui.

> [!NOTE]
> **Team Name** é um nome amigável para a sua equipa de revisão. Este é o nome apresentado no portal Azure. O **Team ID** é o que é usado para identificar a sua equipa de revisão de forma programatical.

> [!div class="mx-imgBorder"]
> ![Convidar membro da equipa](images/create-team.png)

Se optar por encriptar dados utilizando uma chave gerida pelo cliente (CMK), será solicitado o **ID de recursos** para o seu recurso de Moderador de Conteúdo no nível de preços E0. O recurso que fornece deve ser exclusivo desta equipa. 

> [!div class="mx-imgBorder"]
> ![Convidar membro da equipa com CMK](images/create-team-cmk.png)

## <a name="upload-sample-content"></a>Carregar o conteúdo da amostra

Agora está pronto para carregar o conteúdo da amostra. Selecione **Tente > imagem,** **tente > texto** ou tente > **vídeo** .

> [!div class="mx-imgBorder"]
> ![Experimente moderação de imagem ou texto](images/tryimagesortext.png)

Envie o seu conteúdo para moderação. Pode utilizar o seguinte conteúdo de texto de amostra:

```
Is this a grabage email abcdef@abcd.com, phone: 4255550111, IP: 255.255.255.255, 1234 Main Boulevard, Panapolis WA 96555.
Crap is the profanity here. Is this information PII? phone 4255550111
```

Internamente, a ferramenta de revisão irá chamar as APIs de moderação para digitalizar o seu conteúdo. Assim que a digitalização estiver concluída, verá uma mensagem informando-o de que há resultados à espera da sua revisão.

> [!div class="mx-imgBorder"]
> ![Ficheiros moderados](images/submitted.png)

## <a name="review-moderation-tags"></a>Rever etiquetas de moderação

Reveja as etiquetas de moderação aplicadas. Pode ver quais as etiquetas que foram aplicadas ao seu conteúdo e qual era a pontuação em cada categoria. Consulte os artigos de moderação [de Imagem,](image-moderation-api.md) [Texto](text-moderation-api.md)e [Vídeo](video-moderation-api.md) para saber mais sobre o que as diferentes etiquetas de conteúdo indicam.

<!-- ![Review results](images/reviewresults_text.png) -->

Num projeto, você ou a sua equipa de revisão podem alterar estas etiquetas ou adicionar mais tags conforme necessário. Submeterá estas alterações com o botão **Seguinte.** Como a sua aplicação de negócio chama de APIs moderador, o conteúdo marcado irá fazer fila aqui, pronto para ser revisto pelas equipas de revisão humana. Pode rever rapidamente grandes volumes de conteúdo utilizando esta abordagem.

Neste momento, utilizou a ferramenta de Análise de Moderadores de Conteúdo para ver exemplos do que o serviço de Moderador de Conteúdo pode fazer. Em seguida, pode aprender mais sobre a ferramenta de revisão e como integrá-la num projeto de software usando as APIs de revisão, ou pode saltar para a secção [etapas seguintes](#next-steps) para aprender a usar as próprias APIs de Moderação na sua aplicação.

## <a name="learn-more-about-the-review-tool"></a>Saiba mais sobre a ferramenta de revisão

Para saber mais sobre como usar a ferramenta de Análise de Moderador de Conteúdo, consulte o guia da [ferramenta Review](Review-Tool-User-Guide/human-in-the-loop.md) e consulte as APIs da ferramenta Review para aprender a afinar a experiência de revisão humana:
- A [API job](try-review-api-job.md) digitaliza o seu conteúdo utilizando as APIs de moderação e gera avaliações na ferramenta de revisão. 
- A [API de revisão](try-review-api-review.md) cria diretamente análises de imagem, texto ou vídeo para moderadores humanos sem primeiro digitalizar o conteúdo. 
- A [API workflow](try-review-api-workflow.md) cria, atualiza e obtém detalhes sobre os fluxos de trabalho personalizados que a sua equipa cria.

Ou, continue com os próximos passos para começar a usar as APIs de Moderação no seu código.

## <a name="next-steps"></a>Passos seguintes

Aprenda a usar as apis de moderação na sua aplicação.
- Implementar moderação de imagem. Utilize a [consola API](try-image-api.md) ou siga o [quickstart](client-libraries.md) da biblioteca do cliente para digitalizar imagens e detetar potenciais conteúdos adultos e picantes utilizando tags, pontuações de confiança e outras informações extraídas.
- Implementar moderação de texto. Utilize a [consola API](try-text-api.md) ou utilize o [quickstart](client-libraries.md) da biblioteca do cliente para digitalizar o conteúdo de texto para potencial profanação, classificação de texto indesejada assistida por máquina (pré-visualização) e dados pessoais.
- Implementar moderação de vídeo. Siga o guia de como pesquisar vídeos para adultos e picantes para a moderação do [vídeo.](video-moderation-api.md) 
