---
title: Aplicativo de comboio - LUIS
titleSuffix: Azure Cognitive Services
description: A formação é o processo de ensino da sua versão de app Deinscêndio (LUIS) para melhorar a sua compreensão natural da linguagem. Treine a sua aplicação LUIS após atualizações ao modelo como adicionar, editar, rotular ou eliminar entidades, intenções ou expressões.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 11/15/2019
ms.openlocfilehash: 47b006932aace3149dd94e136e334c1b6e5bfcef
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "98762704"
---
# <a name="train-your-active-version-of-the-luis-app"></a>Treine a sua versão ativa da app LUIS

A formação é o processo de ensino da sua app de Compreensão de Línguas (LUIS) para melhorar a sua compreensão de linguagem natural. Treine a sua aplicação LUIS após atualizações ao modelo como adicionar, editar, rotular ou eliminar entidades, intenções ou expressões.

Treinar e [testar](luis-concept-test.md) uma aplicação é um processo iterativo. Depois de preparar a aplicação LUIS, deverá testá-la com expressões de exemplo para ver se as intenções e as entidades são reconhecidas corretamente. Se não estiverem, faça atualizações para a app LUIS, treine e teste novamente.

A formação é aplicada à versão ativa no portal LUIS.

## <a name="how-to-train-interactively"></a>Como treinar interativamente

Para iniciar o processo iterativo no [portal LUIS,](https://www.luis.ai)primeiro tem de treinar a sua app LUIS pelo menos uma vez. Certifique-se de que todas as intenções têm pelo menos uma expressão antes do treino.

1. Aceda à sua aplicação selecionando o seu nome na página **My Apps.**

1. Na sua aplicação, selecione **Train** no painel superior.

1. Quando o treino estiver concluído, uma notificação aparece no topo do navegador.

## <a name="training-date-and-time"></a>Data e hora do treino

A data e a hora do treino são GMT + 2.

## <a name="train-with-all-data"></a>Treine com todos os dados

O treino usa uma pequena percentagem de amostragem negativa. Pode utilizar todos os dados disponíveis em vez de utilizar o portal ou a API. 

### <a name="using-the-luis-portal"></a>Usando o portal LUIS

Inicie sessão no [portal LUIS](https://www.luis.ai/) e clique na sua aplicação. **Selecione Gerir** na parte superior do ecrã e, em seguida, selecionar **Definições** e ativar ou desativar a opção **de treino de utilização determinística.** Quando desativado, o treino utilizará todos os dados disponíveis.

![Um botão para permitir ou desativar o treino não determinístico](./media/non-determinstic-training.png)

### <a name="using-the-version-settings-api"></a>Utilização das definições de versão API

Utilize a [API de definições de versão](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) com o `UseAllTrainingData` conjunto para ser verdadeiro para desligar esta função.

## <a name="unnecessary-training"></a>Treino desnecessário

Não precisas de treinar depois de cada mudança. A formação deve ser feita depois de um grupo de alterações serem aplicadas ao modelo, e o próximo passo que pretende fazer é testar ou publicar. Se não precisa de testar ou publicar, o treino não é necessário.

## <a name="training-with-the-rest-apis"></a>Formação com as APIs REST

A formação no portal LUIS é um único passo para pressionar o botão **Train.** Treinar com as APIs rest é um processo em duas etapas. A primeira é [solicitar formação](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c45) com HTTP POST. Em seguida, solicite o [estado de formação](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c46) com HTTP Get.

Para saber quando o treino está completo, tem de fazer uma sondagem até que todos os modelos sejam treinados com sucesso.

## <a name="next-steps"></a>Passos seguintes

* [Testes interativos](luis-interactive-test.md)
* [Testes em lote](luis-how-to-batch-test.md)
