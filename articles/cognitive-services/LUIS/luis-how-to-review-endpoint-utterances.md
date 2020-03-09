---
title: Rever as declarações dos utilizadores - LUIS
titleSuffix: Azure Cognitive Services
description: Rever as declarações capturadas pela aprendizagem ativa para selecionar as intenções e marcar entidades para as expressões do mundo de leitura; aceitar mudanças, treinar e publicar.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 01/27/2020
ms.author: diberry
ms.openlocfilehash: 95b7c7446a47fafd26d00b0da4d880786340fcd0
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78393748"
---
# <a name="how-to-improve-the-luis-app-by-reviewing-endpoint-utterances"></a>Como melhorar a app LUIS através da revisão das declarações de endpoint

O processo de revisão das expressões de pontofinal para previsões corretas chama-se [aprendizagem ativa](luis-concept-review-endpoint-utterances.md). A aprendizagem ativa captura consultas de ponto final e seleciona as declarações finais do utilizador de que não tem a certeza. Você revê estas expressões para selecionar a intenção e marcar entidades para estas expressões do mundo da leitura. Aceite estas alterações nas suas declarações de exemplo e depois treine e publique. Luis então identifica as expressões com mais precisão.

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="enable-active-learning"></a>Ativar a aprendizagem ativa

Para permitir a aprendizagem ativa, deve registar as consultas dos utilizadores. Isto é conseguido chamando a [consulta de ponto final](luis-get-started-create-app.md#query-the-v3-api-prediction-endpoint) com o parâmetro de corda de `log=true` e valor.

Utilize o portal LUIS para construir a consulta de ponto final correto.

1. No [portal DE pré-visualização LUIS,](https://preview.luis.ai/)selecione a sua aplicação na lista de aplicações.
1. Vá à secção **Gerir** e, em seguida, selecione **os recursos Azure**.
1. Para o recurso de previsão atribuído, selecione **parâmetros**de consulta de alteração .

    > [!div class="mx-imgBorder"]
    > ![Utilize o portal LUIS para guardar registos, que é necessário para a aprendizagem ativa.](./media/luis-tutorial-review-endpoint-utterances/azure-portal-change-query-url-settings.png)

1. Alternar **Guarde os registos** e, em seguida, guarde selecionando **Done**.

    > [!div class="mx-imgBorder"]
    > ![Utilize o portal LUIS para guardar registos, que é necessário para a aprendizagem ativa.](./media/luis-tutorial-review-endpoint-utterances/luis-portal-manage-azure-resource-save-logs.png)

     Esta ação altera o URL de exemplo adicionando o parâmetro de `log=true` consulta. Copie e use o URL de consulta de exemplo alterado ao fazer consultas de previsão para o ponto final do tempo de execução.

## <a name="correct-intent-predictions-to-align-utterances"></a>Previsões de intenções corretas para alinhar expressões

Cada expressão tem uma intenção sugerida exibida na coluna de **intenções alinhada.**

> [!div class="mx-imgBorder"]
> [![Rever declarações finais que LUIS não tem a certeza](./media/label-suggested-utterances/review-endpoint-utterances.png)](./media/label-suggested-utterances/review-endpoint-utterances.png#lightbox)

Se concordar com essa intenção, selecione a marca de verificação. Se discordar com a sugestão, selecione a intenção correta da lista pendente intenção alinhada, em seguida, selecione na marca de verificação para a direita da intenção alinhada. Depois de selecionar na marca de verificação, a expressão é movida para a intenção e removida da lista de **comentários finais** de revisão.

> [!TIP]
> É importante ir à página de detalhes da Intent para rever e corrigir as previsões da entidade de todas as declarações de exemplo da lista de comentários finais de **revisão.**

## <a name="delete-utterance"></a>Eliminar expressão

Cada expressão pode ser eliminado da lista de revisão. Depois de eliminado, não aparecerá na lista novamente. Isso vale mesmo que o usuário insere a expressão mesmo do ponto final.

Se não tem a certeza se deve apagar a expressão, ou movê-la para a intenção De Sem Acordo, ou criar uma nova intenção, como `miscellaneous` e mover a expressão para essa intenção.

## <a name="disable-active-learning"></a>Desativar a aprendizagem ativa

Para desativar a aprendizagem ativa, não faça logi nas consultas dos utilizadores. Isto é realizado definindo a consulta de [ponto final](luis-get-started-create-app.md#query-the-v2-api-prediction-endpoint) com o parâmetro de `log=false` consulta string e valor ou não usando o valor de corda de consulta porque o valor padrão é falso.

## <a name="next-steps"></a>Passos seguintes

Para testar como o desempenho melhora após a etiquetagem de expressões sugeridas, pode aceder à consola de teste selecionando **o Teste** no painel superior. Para obter instruções sobre como testar a sua aplicação utilizando a consola de teste, consulte [O Comboio e teste a sua aplicação](luis-interactive-test.md).
