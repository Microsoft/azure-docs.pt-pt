---
title: 'Quickstart: Experimente moderador de conteúdo na web - Moderador de Conteúdo'
titleSuffix: Azure Cognitive Services
description: Neste arranque rápido, utilizará a ferramenta online de revisão de moderadores de conteúdo para testar a funcionalidade básica do Moderador de Conteúdo sem ter de escrever qualquer código.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: quickstart
ms.date: 03/13/2020
ms.author: pafarley
ms.openlocfilehash: 666b70ba8b632cb2cadf20de384e3e615acb2b3d
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79203583"
---
# <a name="quickstart-try-content-moderator-on-the-web"></a>Quickstart: Experimente o Moderador de Conteúdo na web

Neste arranque rápido, utilizará a ferramenta online de revisão de moderadores de conteúdo para testar a funcionalidade básica do Moderador de Conteúdo sem ter de escrever qualquer código. Se desejar integrar este serviço na sua app mais rapidamente, consulte as outras acelerações na secção [Passos Seguintes.](#next-steps)

## <a name="prerequisites"></a>Pré-requisitos

- Um navegador web

## <a name="set-up-the-review-tool"></a>Configurar a ferramenta de revisão
A ferramenta Content Moderator Review é uma ferramenta baseada na web que permite aos revisores humanos ajudar o serviço cognitivo na tomada de decisões. Neste guia, você irá passar pelo curto processo de configuração da ferramenta de revisão para que você possa ver como funciona o serviço Moderador de Conteúdo. Vá ao site da [ferramenta Content Moderator Review](https://contentmoderator.cognitive.microsoft.com/) e inscreva-se.

![Página inicial do moderador de conteúdo](images/homepage.PNG)

## <a name="create-a-review-team"></a>Criar uma equipa de revisão

Em seguida, criar uma equipa de revisão. Num cenário de trabalho, este será o grupo de pessoas que analisarão manualmente as decisões de moderação do serviço. Para criar uma equipa, terá de selecionar uma **Região**e fornecer um **Nome de Equipa** e um ID de **equipa.** Se desejar convidar colegas para a equipa, pode fazê-lo inserindo aqui os seus endereços de e-mail.

> [!NOTE]
> **Nome da equipa** é um nome amigável para a sua equipa de revisão. Este é o nome exibido no portal Azure. O **ID da equipa** é o que é usado para identificar a sua equipa de revisão programaticamente.

> [!div class="mx-imgBorder"]
> ![Convidar membro da equipa](images/create-team.png)

Se optar por encriptar dados utilizando uma chave gerida pelo cliente (CMK), será solicitado o ID de **recurso** para o seu recurso Moderador de Conteúdo no nível de preços E0. O recurso que fornece deve ser novo. 

> [!div class="mx-imgBorder"]
> ![Convidar membro da equipa com cmk](images/create-team-cmk.png)

Se tentar reutilizar um recurso moderador de conteúdo, verá este aviso: 

> [!div class="mx-imgBorder"]
> ![Falha cmk](images/create-team-cmk-fail.png)

## <a name="upload-sample-content"></a>Carregar conteúdo da amostra

Agora está pronto para fazer o upload do conteúdo da amostra. Selecione **Tente > Imagem,** **tente > texto,** ou **tente > vídeo**.

![Experimente a moderação de imagem ou texto](images/tryimagesortext.png)

Submeta o seu conteúdo para moderação. Internamente, a ferramenta de revisão chamará as APIs de moderação para digitalizar o seu conteúdo. Uma vez concluída a digitalização, verá uma mensagem informando-o de que há resultados à espera da sua revisão.

![Ficheiros moderados](images/submitted.png)

## <a name="review-moderation-tags"></a>Rever etiquetas de moderação

Reveja as etiquetas de moderação aplicadas. Pode ver quais as etiquetas aplicadas ao seu conteúdo e qual a pontuação em cada categoria. Consulte os tópicos de moderação [de imagem,](image-moderation-api.md) [texto](text-moderation-api.md)e [vídeo](video-moderation-api.md) para saber mais sobre o que as diferentes etiquetas de conteúdo indicam.

![Resultados da revisão](images/reviewresults_text.png)

Num projeto, você ou a sua equipa de revisão podem alterar estas tags ou adicionar mais tags conforme necessário. Submeterá estas alterações com o botão **Seguinte.** Como a sua aplicação de negócio chama as APIs moderadoras, o conteúdo marcado irá fazer fila aqui, pronto para ser revisto pelas equipas de revisão humana. Pode rever rapidamente grandes volumes de conteúdo utilizando esta abordagem.

Neste ponto, utilizou a ferramenta Content Moderator Review para ver exemplos do que o serviço moderador de conteúdo pode fazer. Em seguida, você pode saber mais sobre a ferramenta de revisão e como integrá-la em um projeto de software usando as APIs de revisão, ou você pode saltar para a secção [De passos Seguintes](#next-steps) para aprender a usar as APIs de moderação na sua aplicação.

## <a name="learn-more-about-the-review-tool"></a>Saiba mais sobre a ferramenta de revisão

Para saber mais sobre como usar a ferramenta De revisão de moderadores de conteúdo, dê uma olhada no guia de [ferramentas Review](Review-Tool-User-Guide/human-in-the-loop.md) e consulte a ferramenta Review APIs para aprender a afinar a experiência de revisão humana:
- A [API job](try-review-api-job.md) analisa o seu conteúdo utilizando as APIs de moderação e gera comentários na ferramenta de revisão. 
- A [API review](try-review-api-review.md) cria diretamente críticas de imagem, texto ou vídeo para moderadores humanos sem primeiro digitalizar o conteúdo. 
- A [Workflow API](try-review-api-workflow.md) cria, atualiza e obtém detalhes sobre os fluxos de trabalho personalizados que a sua equipa cria.

Ou, continue com os próximos passos para começar a usar as APIs de moderação no seu código.

## <a name="next-steps"></a>Passos seguintes

Aprenda a usar as APIs de moderação na sua aplicação.
- Implementar a moderação da imagem. Utilize a [consola API](try-image-api.md) ou siga o [quickstart .NET SDK](dotnet-sdk-quickstart.md) para digitalizar imagens e detetar potenciais conteúdos adultos e picantes utilizando tags, pontuações de confiança e outras informações extraídas.
- Implementar moderação de texto. Utilize a [consola API](try-text-api.md) ou utilize o [quickstart .NET SDK](dotnet-sdk-quickstart.md) para digitalizar conteúdo de texto para potencial profanação, classificação de texto indesejado assistido por máquina (pré-visualização) e dados pessoais.
- Implementar a moderação do vídeo. Siga o [vídeo de moderação como guiar para C#](video-moderation-api.md) digitalizar vídeos e detetar potenciais conteúdos adultos e picantes. 
