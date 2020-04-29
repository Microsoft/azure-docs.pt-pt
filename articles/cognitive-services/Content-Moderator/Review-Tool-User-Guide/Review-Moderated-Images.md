---
title: Utilize avaliações de conteúdo através da ferramenta Review - Moderador de Conteúdo
titleSuffix: Azure Cognitive Services
description: Saiba como a ferramenta Review permite que moderadores humanos revejam imagens num portal web.
services: cognitive-services
author: PatrickFarley
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/15/2019
ms.author: pafarley
ms.openlocfilehash: b02324923e3f004395105b8e04165390cb950fe7
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "73044120"
---
# <a name="create-human-reviews"></a>Criar avaliações humanas

Neste guia, você aprenderá a configurar [comentários](../review-api.md#reviews) no site da ferramenta Review. Comentários armazenam e exibem conteúdo para moderadores humanos para avaliar. Os moderadores podem alterar as etiquetas aplicadas e aplicar as suas próprias etiquetas personalizadas conforme apropriado. Quando um utilizador completa uma revisão, os resultados são enviados para um ponto final de chamada especificado, e o conteúdo é removido do site.

## <a name="prerequisites"></a>Pré-requisitos

- Inscreva-se ou crie uma conta no site da ferramenta Content Moderator [Review.](https://contentmoderator.cognitive.microsoft.com/)

## <a name="image-reviews"></a>Revisões de imagens

1. Vá à [ferramenta Rever](https://contentmoderator.cognitive.microsoft.com/), selecione o separador **'Tentar'** e faça upload de algumas imagens para rever.
1. Uma vez que as imagens enviadas tenham terminado o processamento, vá ao separador **Rever** e selecione **Imagem**.

    ![Navegador Chrome mostrando a ferramenta de revisão com a opção Imagem de Revisão destacada](images/review-images-1.png)

    As imagens exibem com etiquetas que tenham sido atribuídas pelo processo de moderação automática. As imagens que submeteu através da ferramenta Review não são visíveis a outros revisores.

1. Opcionalmente, mova as **Opiniões para visualizar** o slider (1) para ajustar o número de imagens que são exibidas no ecrã. Clique nos botões **marcados** ou **sem etiqueta** (2) para classificar as imagens em conformidade. Clique num painel de etiquetas (3) para alternar ou desligar.

    ![Navegador Chrome mostrando a ferramenta Review com imagens marcadas para revisão](images/review-images-2.png)

1. Para ver mais informações sobre uma imagem, clique na elipse na miniatura e selecione **Ver detalhes**. Pode atribuir uma imagem a uma subequipa com a opção **Move to** option (consulte a secção de [equipas](./configure.md#manage-team-and-subteams) para saber mais sobre subequipas).

    ![Uma imagem com a opção de detalhes do Ver destacada](images/review-images-3.png)

1. Navegue nas informações de moderação da imagem na página de detalhes.

    ![Uma imagem com detalhes de moderação listados em um painel separado](images/review-images-4.png)

1. Depois de ter revisto e atualizado as atribuições de etiquetas conforme necessário, clique em **Next** para submeter as suas opiniões. Depois de submeter, tem cerca de cinco segundos para clicar no botão **Prev** para voltar ao ecrã anterior e rever as imagens novamente. Depois disso, as imagens já não estão na fila Enviar e o botão **Prev** já não está disponível.

## <a name="text-reviews"></a>Revisões de texto

As revisões de texto funcionam de forma semelhante às críticas de imagem. Em vez de carregar conteúdo, basta escrever ou colar em texto (até 1.024 caracteres). Em seguida, O Moderador de Conteúdo analisa o texto e aplica tags (além de outras informações de moderação, tais como profanação e dados pessoais). Em comentários de texto, pode alternar as etiquetas aplicadas e/ou aplicar etiquetas personalizadas antes de submeter o reexame.

![Screenshot da ferramenta de revisão mostrando texto sinalizado em uma janela do navegador Chrome](../images/reviewresults_text.png)

## <a name="next-steps"></a>Passos seguintes

Neste guia, aprendeu a configurar e a utilizar comentários da ferramenta Content Moderator [Review](https://contentmoderator.cognitive.microsoft.com). Em seguida, consulte o [guia REST API](../try-review-api-review.md) ou o [quickstart .NET SDK](../dotnet-sdk-quickstart.md) para aprender a criar comentários programáticamente.