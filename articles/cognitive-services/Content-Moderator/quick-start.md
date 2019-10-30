---
title: 'Início rápido: Experimente Content Moderator na Web-Content Moderator'
titleSuffix: Azure Cognitive Services
description: Neste guia de início rápido, você usará a ferramenta de revisão de Content Moderator online para testar a funcionalidade básica do Content Moderator sem precisar escrever nenhum código.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: quickstart
ms.date: 07/03/2019
ms.author: pafarley
ms.openlocfilehash: bb4fc076d01c1108278cea0cebba958b4ea94660
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73044058"
---
# <a name="quickstart-try-content-moderator-on-the-web"></a>Início rápido: Experimente o Content Moderator na Web

Neste guia de início rápido, você usará a ferramenta de revisão de Content Moderator online para testar a funcionalidade básica do Content Moderator sem precisar escrever nenhum código. Se você quiser integrar esse serviço em seu aplicativo mais rapidamente, consulte os outros guias de início rápido na seção [próximas etapas](#next-steps) .

## <a name="prerequisites"></a>Pré-requisitos

- Um navegador da Web

## <a name="set-up-the-review-tool"></a>Configurar a ferramenta de revisão
A ferramenta de análise de Content Moderator é uma ferramenta baseada na Web que permite que os revisores humanos auxiliem o serviço cognitiva na tomada de decisões. Neste guia, você passará pelo processo curto de configuração da ferramenta de revisão para que você possa ver como o serviço de Content Moderator funciona. Vá para o site da [ferramenta de revisão de Content moderator](https://contentmoderator.cognitive.microsoft.com/) e inscreva-se.

![Página inicial do Content Moderator](images/homepage.PNG)

## <a name="create-a-review-team"></a>Criar uma equipe de revisão

Em seguida, crie uma equipe de revisão. Em um cenário de trabalho, esse será o grupo de pessoas que examinará manualmente as decisões de moderação do serviço. Por enquanto, você só precisa criar um nome de equipe. Se você quiser convidar colegas para a equipe, poderá fazer isso inserindo seus endereços de email aqui.

![Convidar membro da equipe](images/QuickStart-2-small.png)

## <a name="upload-sample-content"></a>Carregar conteúdo de exemplo

Agora você está pronto para carregar o conteúdo de exemplo. Selecione **tentar > imagem**, **tente > texto**ou **tente > vídeo**.

![Experimente a moderação da imagem ou do texto](images/tryimagesortext.png)

Envie seu conteúdo para moderação. Internamente, a ferramenta de revisão chamará as APIs de moderação para verificar seu conteúdo. Quando a verificação for concluída, você verá uma mensagem informando que há resultados aguardando sua análise.

![Arquivos moderados](images/submitted.png)

## <a name="review-moderation-tags"></a>Examinar marcas de moderação

Examine as marcas de moderação aplicadas. Você pode ver quais marcas foram aplicadas ao seu conteúdo e qual foi a pontuação em cada categoria. Consulte os tópicos de moderação de [imagem](image-moderation-api.md), [texto](text-moderation-api.md)e [vídeo](video-moderation-api.md) para saber mais sobre o que as marcas de conteúdo diferentes indicam.

![Resultados da revisão](images/reviewresults_text.png)

Em um projeto, você ou sua equipe de análise podem alterar essas marcas ou adicionar mais marcas conforme necessário. Você enviará essas alterações com o botão **Avançar** . À medida que seu aplicativo de negócios chama as APIs do moderador, o conteúdo marcado entrará em fila aqui, pronto para ser revisado pelas equipes de análise humana. Você pode examinar rapidamente grandes volumes de conteúdo usando essa abordagem.

Neste ponto, você usou a ferramenta de revisão de Content Moderator para ver exemplos do que o serviço Content Moderator pode fazer. Em seguida, você pode saber mais sobre a ferramenta de revisão e como integrá-la a um projeto de software usando as APIs de revisão ou pode pular para a seção [próximas etapas](#next-steps) para saber como usar as APIs de moderação em seu aplicativo.

## <a name="learn-more-about-the-review-tool"></a>Saiba mais sobre a ferramenta de revisão

Para saber mais sobre como usar a ferramenta de revisão de Content Moderator, confira o guia de [ferramentas](Review-Tool-User-Guide/human-in-the-loop.md) de revisão e veja as APIs de ferramenta de revisão para saber como ajustar a experiência de revisão humana:
- A [API de trabalho](try-review-api-job.md) verifica seu conteúdo usando as APIs de moderação e gera revisões na ferramenta de revisão. 
- A [API de revisão](try-review-api-review.md) cria diretamente revisões de imagem, texto ou vídeo para moderadores humanos sem primeiro verificar o conteúdo. 
- A [API de fluxo de trabalho](try-review-api-workflow.md) cria, atualiza e obtém detalhes sobre os fluxos de trabalho personalizados que sua equipe cria.

Ou continue com as próximas etapas para começar a usar as APIs de moderação em seu código.

## <a name="next-steps"></a>Passos seguintes

Saiba como usar as APIs de moderação em seu aplicativo.
- Implemente a moderação da imagem. Use o [console de API](try-image-api.md) ou siga o [início rápido do SDK do .net](dotnet-sdk-quickstart.md) para digitalizar imagens e detectar possíveis conteúdos de adulto e erótico usando marcas, pontuações de confiança e outras informações extraídas.
- Implemente a moderação de texto. Use o [console de API](try-text-api.md) ou use o [início rápido do SDK do .net](dotnet-sdk-quickstart.md) para digitalizar o conteúdo de texto para possíveis obscenidades, classificação de texto indesejado assistido por computador (visualização) e dados pessoais.
- Implemente a moderação de vídeo. Siga o [Guia de instruções de moderação de C# vídeo para](video-moderation-api.md) para examinar vídeos e detectar possíveis conteúdos de adulto e erótico. 
