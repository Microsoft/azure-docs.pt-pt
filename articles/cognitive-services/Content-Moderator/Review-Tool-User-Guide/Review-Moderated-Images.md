---
title: Utilize avaliações de conteúdo através da ferramenta Review - Content Moderador
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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "73044120"
---
# <a name="create-human-reviews"></a>Criar críticas humanas

Neste guia, você vai aprender a configurar [comentários](../review-api.md#reviews) no site da ferramenta Review. As avaliações armazenam e exibem conteúdo para moderadores humanos avaliarem. Os moderadores podem alterar as etiquetas aplicadas e aplicar as suas próprias etiquetas personalizadas conforme apropriado. Quando um utilizador completa uma revisão, os resultados são enviados para um ponto final de retorno especificado e o conteúdo é removido do site.

## <a name="prerequisites"></a>Pré-requisitos

- Iniciar sessão ou criar uma conta no site da [ferramenta De Revisão](https://contentmoderator.cognitive.microsoft.com/) de Moderadores de Conteúdo.

## <a name="image-reviews"></a>Revisões de imagens

1. Vá à [ferramenta 'Rever',](https://contentmoderator.cognitive.microsoft.com/)selecione o separador **'Tentar'** e faça upload de algumas imagens para rever.
1. Assim que as imagens carregadas terminarem o processamento, vá ao **separador 'Rever'** e selecione **Imagem**.

    ![Navegador Chrome mostrando a ferramenta de revisão com a opção De Imagem de Revisão em destaque](images/review-images-1.png)

    As imagens exibem quaisquer etiquetas que tenham sido atribuídas pelo processo de moderação automática. As imagens que submeteu através da ferramenta 'Revisão' não são visíveis para outros revisores.

1. Opcionalmente, mova as **Avaliações para visualizar** o slider (1) para ajustar o número de imagens que são apresentadas no ecrã. Clique nos botões **marcados** ou **não marcados** (2) para classificar as imagens em conformidade. Clique num painel de identificação (3) para o alternar dentro ou fora.

    ![Navegador Chrome mostrando a ferramenta Review com imagens marcadas para revisão](images/review-images-2.png)

1. Para ver mais informações sobre uma imagem, clique na elipse na miniatura e selecione **Ver detalhes**. Pode atribuir uma imagem a uma subeconada com a opção **Move** (consulte a secção [de equipas](./configure.md#manage-team-and-subteams) para saber mais sobre subeconsãos).

    ![Uma imagem com a opção ver detalhes realçada](images/review-images-3.png)

1. Consulte as informações de moderação de imagem na página de detalhes.

    ![Uma imagem com detalhes de moderação listados num painel separado](images/review-images-4.png)

1. Uma vez revisto e atualizado as atribuições de tags conforme necessário, clique em **Next** para submeter as suas avaliações. Depois de submeter, tem cerca de cinco segundos para clicar no botão **Prev** para voltar ao ecrã anterior e rever novamente as imagens. Depois disso, as imagens deixaram de estar na fila 'Enviar' e o botão **Prev** já não está disponível.

## <a name="text-reviews"></a>Revisões de texto

As análises de texto funcionam da mesma forma com as revisões de imagem. Em vez de carregar conteúdo, basta escrever ou colar em texto (até 1.024 caracteres). Em seguida, o Moderador de Conteúdo analisa o texto e aplica tags (além de outras informações de moderação, tais como palavrões e dados pessoais). Nas análises de texto, pode alternar as etiquetas aplicadas e/ou aplicar tags personalizadas antes de submeter a revisão.

![Screenshot da ferramenta de revisão mostrando texto sinalizado em uma janela do navegador Chrome](../images/reviewresults_text.png)

## <a name="next-steps"></a>Passos seguintes

Neste guia, aprendeu a configurar e utilizar comentários da ferramenta De [Revisão](https://contentmoderator.cognitive.microsoft.com)do Moderador de Conteúdo. Em seguida, consulte o [guia REST API](../try-review-api-review.md) ou o [quickstart .NET SDK](../dotnet-sdk-quickstart.md) para aprender a criar avaliações programáticamente.