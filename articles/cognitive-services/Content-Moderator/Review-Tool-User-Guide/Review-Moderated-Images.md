---
title: Usar revisões de conteúdo por meio da ferramenta de revisão-Content Moderator
titleSuffix: Azure Cognitive Services
description: Saiba como a ferramenta de revisão permite que os moderadores humanos revisem imagens em um portal da Web.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/15/2019
ms.author: sajagtap
ms.openlocfilehash: 065d3cd80f93753eb91571d4ada4fe7151258ec0
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68882664"
---
# <a name="create-human-reviews"></a>Criar análises humanas

Neste guia, você aprenderá a configurar as [revisões](../review-api.md#reviews) no site da ferramenta de revisão. Revisa o armazenamento e exibe o conteúdo de moderadores humanos para avaliar. Os moderadores podem alterar as marcas aplicadas e aplicar suas próprias marcas personalizadas, conforme apropriado. Quando um usuário conclui uma revisão, os resultados são enviados para um ponto de extremidade de retorno de chamada especificado e o conteúdo é removido do site.

## <a name="prerequisites"></a>Pré-requisitos

- Entre ou crie uma conta no site da [ferramenta de revisão](https://contentmoderator.cognitive.microsoft.com/) de Content Moderator.

## <a name="image-reviews"></a>Revisões de imagens

1. Vá para a [ferramenta de revisão](https://contentmoderator.cognitive.microsoft.com/), selecione a guia **tentar** e carregue algumas imagens para revisar.
1. Depois que as imagens carregadas tiverem terminado o processamento, vá para a guia revisar e selecione **imagem**.

    ![Navegador Chrome mostrando a ferramenta de revisão com a opção revisar imagem realçada](images/review-images-1.png)

    As imagens são exibidas com os rótulos que foram atribuídos pelo processo de moderação automática. As imagens que você enviou por meio da ferramenta de revisão não são visíveis para outros revisores.

1. Opcionalmente, mova as **revisões para exibir** o controle deslizante (1) para ajustar o número de imagens exibidas na tela. Clique nos botões **marcados** ou não **marcados** (2) para classificar as imagens de acordo. Clique em um painel de marca (3) para ativá-lo ou desativá-lo.

    ![Navegador Chrome mostrando a ferramenta de revisão com imagens marcadas para revisão](images/review-images-2.png)

1. Para ver mais informações sobre uma imagem, clique nas reticências na miniatura e selecione **Exibir detalhes**. Você pode atribuir uma imagem a uma subequipe com a opção **mover para** (consulte a seção [equipes](./configure.md#manage-team-and-subteams) para saber mais sobre subequipes).

    ![Uma imagem com a opção Exibir detalhes realçada](images/review-images-3.png)

1. Procure as informações de moderação da imagem na página de detalhes.

    ![Uma imagem com detalhes de moderação listados em um painel separado](images/review-images-4.png)

1. Depois de examinar e atualizar as atribuições de marca conforme necessário, clique em **Avançar** para enviar suas revisões. Depois de enviar, você terá cerca de cinco segundos para clicar no botão **anterior** para retornar à tela anterior e examinar as imagens novamente. Depois disso, as imagens não estarão mais na fila de envio e o botão **anterior** não estará mais disponível.

## <a name="text-reviews"></a>Revisões de texto

As revisões de texto funcionam de forma semelhante às revisões de imagem. Em vez de carregar o conteúdo, basta escrever ou colar texto (até 1.024 caracteres). Em seguida, Content Moderator analisa o texto e aplica marcas (além de outras informações de moderação, como dados pessoais e profanação). Em revisões de texto, você pode alternar as marcas aplicadas e/ou aplicar marcas personalizadas antes de enviar a revisão.

![Captura de tela da ferramenta de revisão mostrando texto sinalizado em uma janela do navegador Chrome](../images/reviewresults_text.png)

## <a name="next-steps"></a>Passos seguintes

Neste guia, você aprendeu a configurar e usar as revisões da [ferramenta de análise](https://contentmoderator.cognitive.microsoft.com)de Content Moderator. Em seguida, consulte o [guia da API REST](../try-review-api-review.md) ou o [Guia do SDK do .net](../moderation-reviews-quickstart-dotnet.md) para saber como criar revisões programaticamente.