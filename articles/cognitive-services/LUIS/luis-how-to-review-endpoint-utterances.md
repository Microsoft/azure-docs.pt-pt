---
title: Expressões com de utilizador de revisão
titleSuffix: Language Understanding - Azure Cognitive Services
description: Aprendizagem ativa captura consultas de ponto final e seleciona as expressões de ponto final do utilizador que é verificá-lo. Reveja estas expressões para selecionar a intenção e marcar entidades para essas expressões de com do mundo de leitura. Aceitar estas alterações em expressões de com seu exemplo, em seguida, formar e publicar. LUIS identifica, em seguida, expressões com mais precisão.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 03/25/2019
ms.author: diberry
ms.openlocfilehash: 8fac360682ef11c438cdec333fac21d6f8cfc117
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2019
ms.locfileid: "58895912"
---
# <a name="how-to-review-endpoint-utterances-in-luis-portal-for-active-learning"></a>Como rever os discursos de ponto final no portal de LUIS para aprendizagem ativa

[Aprendizagem ativa](luis-concept-review-endpoint-utterances.md) captura consultas de ponto final e seleciona as expressões de ponto final do utilizador que é verificá-lo. Reveja estas expressões para selecionar a intenção e marcar entidades para essas expressões de com do mundo de leitura. Aceitar estas alterações em expressões de com seu exemplo, em seguida, formar e publicar. LUIS identifica, em seguida, expressões com mais precisão.


## <a name="enable-active-learning"></a>Ativar a aprendizagem ativa

Para ativar a aprendizagem ativa, inicie sessão consultas do utilizador. Isso é feito definindo a [consulta de ponto final](luis-get-started-create-app.md#query-the-endpoint-with-a-different-utterance) com o `log=true` querystring parâmetro e valor.

## <a name="disable-active-learning"></a>Desativar a aprendizagem ativa

Para desativar a aprendizagem ativa, não registar as consultas de utilizador. Isso é feito definindo a [consulta de ponto final](luis-get-started-create-app.md#query-the-endpoint-with-a-different-utterance) com o `log=false` querystring parâmetro e valor.

## <a name="filter-utterances"></a>Expressões de filtro

1. Abra a sua aplicação (por exemplo, TravelAgent) ao selecionar o respetivo nome na **as minhas aplicações** página, em seguida, selecione **criar** na barra superior.

1. Sob o **melhorar o desempenho da aplicação**, selecione **rever expressões de ponto final**.

1. Na **rever expressões de ponto final** página, selecione no **lista de filtros de intenção ou entidade** caixa de texto. Esta lista pendente inclui todas as intenções sob **INTENÇÕES** e todas as entidades sob **entidades**.

    ![Filtro de expressões](./media/label-suggested-utterances/filter.png)

1. Na lista pendente, selecione uma categoria (intenções ou entidades) e reveja as expressões.

    ![Expressões com intenção](./media/label-suggested-utterances/intent-utterances.png)

## <a name="label-entities"></a>Entidades de etiqueta
LUIS substitui tokens de entidade (palavras) com os nomes de entidades realçados em azul. Se uma expressão tem sem etiqueta entidades, da etiqueta-los na expressão. 

1. Selecione o word(s) na expressão. 

1. Selecione uma entidade a partir da lista.

    ![Etiqueta de entidade](./media/label-suggested-utterances/label-entity.png)

## <a name="align-single-utterance"></a>Alinhar expressão única

Cada ocorrência de pronunciação tem uma intenção sugerida zobrazené a **alinhadas com a intenção** coluna. 

1. Se concordar com essa sugestão, selecione a marca de verificação.

    ![Manter a intenção alinhada](./media/label-suggested-utterances/align-intent-check.png)

1. Se discordar com a sugestão, selecione a intenção correta da lista pendente intenção alinhada, em seguida, selecione na marca de verificação para a direita da intenção alinhada. 

    ![Alinhar intenção](./media/label-suggested-utterances/align-intent.png)

1. Depois de selecionar a marca de verificação, a expressão é removido da lista. 

## <a name="align-several-utterances"></a>Alinhar expressões com vários

Alinhar expressões com vários, marque a caixa à esquerda das expressões, em seguida, selecione sobre o **adicionar selecionado** botão. 

![Alinhar vários](./media/label-suggested-utterances/add-selected.png)

## <a name="verify-aligned-intent"></a>Certifique-se a intenção alinhada

Pode verificar a expressão foi alinhado com a intenção correta ao aceder a **intenções** página, selecione o nome de intenção e rever as expressões. A expressão da **rever expressões de ponto final** está na lista.

## <a name="delete-utterance"></a>Eliminar expressão

Cada expressão pode ser eliminado da lista de revisão. Depois de eliminado, não aparecerá na lista novamente. Isso vale mesmo que o usuário insere a expressão mesmo do ponto final. 

Se tiver a certeza se deve excluir a expressão, mova-o para a intenção, nenhuma ou criar uma intenção de novo, como "diversos" e mova a expressão para esse propósito. 

## <a name="delete-several-utterances"></a>Eliminar várias expressões

Para eliminar várias expressões, selecione cada item e selecione do contentor de caixote do lixo à direita dos **adicionar selecionado** botão.

![Eliminar vários](./media/label-suggested-utterances/delete-several.png)


## <a name="next-steps"></a>Passos Seguintes

Para testar como o desempenho aumenta depois coloca expressões sugeridas com uma etiqueta, pode aceder à consola de teste, selecionando **testar** no painel superior. Para obter instruções sobre como testar a sua aplicação utilizando a consola de teste, consulte [treinar e testar a aplicação](luis-interactive-test.md).
