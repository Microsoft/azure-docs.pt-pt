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
ms.date: 05/07/2020
ms.author: diberry
ms.openlocfilehash: c976d3b74badc4eeb5978af352fe425089f2fbfb
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83584975"
---
# <a name="how-to-improve-the-luis-app-by-reviewing-endpoint-utterances"></a>Como melhorar a app LUIS através da revisão das declarações de endpoint

O processo de revisão das expressões de pontofinal para previsões corretas chama-se [aprendizagem ativa](luis-concept-review-endpoint-utterances.md). A aprendizagem ativa captura consultas de ponto final e seleciona as declarações finais do utilizador de que não tem a certeza. Você revê estas expressões para selecionar a intenção e marcar entidades para estas expressões do mundo da leitura. Aceite estas alterações nas suas declarações de exemplo e depois treine e publique. Luis então identifica as expressões com mais precisão.

## <a name="enable-active-learning"></a>Ativar a aprendizagem ativa

Para permitir a aprendizagem ativa, deve registar as consultas dos utilizadores. Isto é conseguido chamando a [consulta de ponto final](luis-get-started-create-app.md#query-the-v3-api-prediction-endpoint) com o parâmetro de corda de consulta e o `log=true` valor.

Utilize o portal LUIS para construir a consulta de ponto final correto.

1. Inscreva-se no [portal LUIS](https://www.luis.ai)e selecione o seu recurso **De Subscrição** e **Autoria** para ver as aplicações atribuídas a esse recurso de autoria.
1. Abra a sua aplicação selecionando o seu nome na página **My Apps.**
1. Vá à secção **Gerir** e, em seguida, selecione **os recursos Azure**.
1. Para o recurso de previsão atribuído, selecione **parâmetros**de consulta de alteração .

    > [!div class="mx-imgBorder"]
    > ![Utilize o portal LUIS para guardar registos, que é necessário para a aprendizagem ativa.](./media/luis-tutorial-review-endpoint-utterances/azure-portal-change-query-url-settings.png)

1. Alternar **Guarde os registos** e, em seguida, guarde selecionando **Done**.

    > [!div class="mx-imgBorder"]
    > ![Utilize o portal LUIS para guardar registos, que é necessário para a aprendizagem ativa.](./media/luis-tutorial-review-endpoint-utterances/luis-portal-manage-azure-resource-save-logs.png)

     Esta ação altera o URL de exemplo adicionando o parâmetro de corda de `log=true` consulta. Copie e use o URL de consulta de exemplo alterado ao fazer consultas de previsão para o ponto final do tempo de execução.

## <a name="correct-intent-predictions-to-align-utterances"></a>Previsões de intenções corretas para alinhar expressões

Cada expressão tem uma intenção sugerida exibida na coluna de **intenções alinhada.**

> [!div class="mx-imgBorder"]
> [![Comentários finais de que luis não tem certeza](./media/label-suggested-utterances/review-endpoint-utterances.png)](./media/label-suggested-utterances/review-endpoint-utterances.png#lightbox)

Se concordar com essa intenção, selecione a marca de verificação. Se não concordar com a sugestão, selecione a intenção correta a partir da lista de intenção alinhada e, em seguida, selecione na marca de verificação à direita da intenção alinhada. Depois de selecionar na marca de verificação, a expressão é movida para a intenção e removida da lista de **comentários finais** de revisão.

> [!TIP]
> É importante ir à página de detalhes da Intent para rever e corrigir as previsões da entidade de todas as declarações de exemplo da lista de comentários finais de **revisão.**

## <a name="delete-utterance"></a>Excluir a expressão

Cada expressão pode ser eliminada da lista de revisão. Uma vez eliminado, não voltará a aparecer na lista. Isto é verdade mesmo que o utilizador introduza a mesma expressão a partir do ponto final.

Se não tiver a certeza se deve apagar a expressão, mova-a para a intenção De None, ou crie uma nova intenção, como `miscellaneous` e mova a expressão para essa intenção.

## <a name="disable-active-learning"></a>Desativar a aprendizagem ativa

Para desativar a aprendizagem ativa, não faça logi nas consultas dos utilizadores. Isto é realizado definindo a consulta de [ponto final](luis-get-started-create-app.md#query-the-v2-api-prediction-endpoint) com o parâmetro de corda de consulta e o valor ou não usando o valor da corda de consulta porque o valor padrão `log=false` é falso.

## <a name="next-steps"></a>Passos seguintes

Para testar como o desempenho melhora após a etiquetagem de expressões sugeridas, pode aceder à consola de teste selecionando **o Teste** no painel superior. Para obter instruções sobre como testar a sua aplicação utilizando a consola de teste, consulte [O Comboio e teste a sua aplicação](luis-interactive-test.md).
