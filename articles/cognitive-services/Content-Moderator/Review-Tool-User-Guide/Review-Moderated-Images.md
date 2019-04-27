---
title: Utilizar as revisões de conteúdo por meio da ferramenta de revisão - Content Moderator
titlesuffix: Azure Cognitive Services
description: Saiba como a ferramenta de revisão permite moderadores humanos rever as imagens num portal web.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: article
ms.date: 03/15/2019
ms.author: sajagtap
ms.openlocfilehash: a482ecf4a0d321525ab7e392695d2c4c0eebeadc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60629042"
---
# <a name="create-human-reviews"></a>Criar revisões realizadas por pessoas

Neste guia, aprenderá como configurar [revisões](../review-api.md#reviews) no site da ferramenta de revisão. As revisões de armazenarem e exibem o conteúdo de moderadores humanos avaliar. Os moderadores podem alterar as etiquetas aplicadas e aplique suas próprias etiquetas personalizadas conforme apropriado. Quando um utilizador concluir uma revisão, os resultados são enviados para um ponto de extremidade do retorno de chamada especificado e o conteúdo é removido do site.

## <a name="prerequisites"></a>Pré-requisitos

- Inicie sessão ou crie uma conta no Content Moderator [ferramenta de revisão](https://contentmoderator.cognitive.microsoft.com/) site.

## <a name="image-reviews"></a>Revisões de imagens

1. Vá para o [ferramenta de revisão](https://contentmoderator.cognitive.microsoft.com/), selecione a **tente** separador e carregar algumas imagens para rever.
1. Depois das imagens carregadas tem concluído o processamento, vá para o **revisão** separador e selecione **imagem**.

    ![Browser do Chrome que mostra a ferramenta de revisão com a opção de imagem rever realçada](images/review-images-1.png)

    As imagens são apresentadas com quaisquer etiquetas que foram atribuídas pelo processo de moderação automática. As imagens que ter enviado por meio da ferramenta de revisão não estão visíveis para outros revisores.

1. Opcionalmente, mover o **revisões para apresentar** controlo de deslize (1) para ajustar o número de imagens que são apresentados no ecrã. Clique nas **marcadas** ou **não marcada** botões (2) para classificar as imagens de acordo. Clique num painel de etiqueta (3) para alternar entre ativada ou desativada.

    ![Browser do Chrome que mostra a ferramenta de revisão com imagens marcadas para revisão](images/review-images-2.png)

1. Para obter mais informações sobre uma imagem, clique nas reticências na miniatura e selecione **ver detalhes**. Pode atribuir uma imagem a um subteam com o **mover para o** opção (consulte a [equipes](./configure.md#manage-team-and-subteams) secção para saber mais sobre subequipas).

    ![Uma imagem com a opção de detalhes da vista realçada](images/review-images-3.png)

1. Procure as informações de moderação de imagem na página de detalhes.

    ![Uma imagem com detalhes de moderação listados num painel separado](images/review-images-4.png)

1. Depois de rever e atualizar as atribuições de etiqueta, conforme necessário, clique em **seguinte** para enviar suas revisões. Depois de submeter, tem cerca de cinco segundos clicar o **anterior** botão para regressar ao ecrã anterior e reveja as imagens novamente. Depois disso, as imagens já não estão na fila de envio e o **anterior** botão já não está disponível.

## <a name="text-reviews"></a>Revisões de texto

Função da mesma forma para revisões de imagem de revisões de texto. Em vez de carregar o conteúdo, simplesmente escreva ou cole o texto (até 1024 carateres). Em seguida, o Content Moderator analisa o texto e aplica-se as etiquetas (além de outras informações de moderação, como linguagem inapropriada e dados pessoais). Nas revisões de texto, pode alternar entre as etiquetas aplicadas e/ou aplicar etiquetas personalizadas antes de submeter a revisão.

![Captura de ecrã da apresentação da ferramenta de revisão sinalizados texto numa janela do browser Chrome](../images/reviewresults_text.png)

## <a name="next-steps"></a>Passos Seguintes

Neste guia, aprendeu a configurar e utilizar as revisões do Content Moderator [ferramenta de revisão](https://contentmoderator.cognitive.microsoft.com). Em seguida, veja a [guia de REST API](../try-review-api-review.md) ou o [Guia do SDK do .NET](../moderation-reviews-quickstart-dotnet.md) para aprender a criar as revisões de forma programática.