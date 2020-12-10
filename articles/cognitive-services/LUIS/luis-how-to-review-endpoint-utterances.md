---
title: Rever declarações de utilizador - LUIS
titleSuffix: Azure Cognitive Services
description: Rever as expressões capturadas pela aprendizagem ativa para selecionar as intenções e marcar entidades para declarações de mundo de leitura; aceitar mudanças, treinar e publicar.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 12/08/2020
ms.openlocfilehash: ea2b44d05d25756a16b6b84f0734966b1f579848
ms.sourcegitcommit: 273c04022b0145aeab68eb6695b99944ac923465
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2020
ms.locfileid: "97007607"
---
# <a name="how-to-improve-the-luis-app-by-reviewing-endpoint-utterances"></a>Como melhorar a app LUIS através da revisão das expressões de ponto final

O processo de revisão das proclamações de ponto final para previsões corretas chama-se [Aprendizagem Ativa](luis-concept-review-endpoint-utterances.md). A aprendizagem ativa captura consultas de ponto final e seleciona as expressões de ponto final do utilizador de que não tem a certeza. Você revê estas expressões para selecionar as intenções e marcar entidades para estas expressões do mundo real. Aceite estas alterações nas expressões de exemplo e, em seguida, prepare e publique. Luis identifica então as expressões com mais precisão.

## <a name="log-user-queries-to-enable-active-learning"></a>Registar consultas de utilizador para permitir a aprendizagem ativa

Para ativar a aprendizagem ativa, tem de registar consultas de utilizador. Isto é conseguido chamando a consulta de [ponto final](luis-get-started-create-app.md#query-the-v3-api-prediction-endpoint) com o parâmetro de consulta `log=true` e valor.

Utilize o portal LUIS para construir a consulta correta do ponto final.

1. Inscreva-se no [portal LUIS](https://www.luis.ai)e selecione o seu recurso **de Subscrição** e **Autoria** para ver as aplicações atribuídas a esse recurso de autoria.
1. Abra a sua aplicação selecionando o seu nome na página **My Apps.**
1. Vá à secção **Gerir** e, em seguida, selecione **recursos Azure**.
1. Para o recurso de previsão atribuído, selecione **Alterar parâmetros de consulta**.

    > [!div class="mx-imgBorder"]
    > ![A screenshot mostra a ligação de parâmetros de consulta De alteração.](./media/luis-tutorial-review-endpoint-utterances/azure-portal-change-query-url-settings.png)

1. Guardar **registos** e guardar selecionando **Feito**.

    > [!div class="mx-imgBorder"]
    > ![Utilize o portal LUIS para guardar registos, que são necessários para a aprendizagem ativa.](./media/luis-tutorial-review-endpoint-utterances/luis-portal-manage-azure-resource-save-logs.png)

     Esta ação altera o URL de exemplo adicionando o `log=true` parâmetro de consulta. Copie e use o URL de consulta de exemplo alterado ao fazer consultas de previsão para o ponto final de tempo de execução.

## <a name="correct-intent-predictions-to-align-utterances"></a>Previsões de intenções corretas para alinhar expressões

Cada expressão tem uma intenção sugerida exibida na coluna **de intenções alinhadas.**

> [!div class="mx-imgBorder"]
> [![Rever declarações de ponto final que LUIS não tem certeza de](./media/label-suggested-utterances/review-endpoint-utterances.png)](./media/label-suggested-utterances/review-endpoint-utterances.png#lightbox)

Se concordar com essa intenção, selecione a marca de verificação. Se não concordar com a sugestão, selecione a intenção correta da lista de recuos de intenções alinhadas e, em seguida, selecione na marca de verificação à direita da intenção alinhada. Depois de selecionar na marca de verificação, a expressão é transferida para a intenção e removida da lista **de Comentários de Comentários.**

> [!TIP]
> É importante ir à página de detalhes da Intenção para rever e corrigir as previsões da entidade a partir de todas as declarações de exemplo da lista **de comentários de Endpoint.**

## <a name="delete-utterance"></a>Apagar a expressão

Cada expressão pode ser eliminada da lista de revisão. Uma vez eliminado, não voltará a aparecer na lista. Isto é verdade mesmo que o utilizador introduza a mesma expressão a partir do ponto final.

Se não tiver a certeza se deve apagar a expressão, ou movê-la para a intenção de Zero, ou criar uma nova intenção, como `miscellaneous` e mover a expressão para essa intenção.

## <a name="disable-active-learning"></a>Desativar a aprendizagem ativa

Para desativar a aprendizagem ativa, não faça login nas consultas do utilizador. Isto é conseguido definindo a consulta de [ponto final](luis-get-started-create-app.md#query-the-v2-api-prediction-endpoint) com o parâmetro de consulta e valor ou `log=false` não usando o valor de consulta porque o valor padrão é falso.

## <a name="next-steps"></a>Passos seguintes

Para testar como o desempenho melhora depois de rotular as expressões sugeridas, pode aceder à consola de teste selecionando **Test** no painel superior. Para obter instruções sobre como testar a sua aplicação utilizando a consola de teste, consulte [Train e teste a sua aplicação.](luis-interactive-test.md)
