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
ms.openlocfilehash: a53857061110d5a77ac3d166277e7076f4f7f9c6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91541378"
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

O treino usa uma pequena percentagem de amostragem negativa. Se pretender utilizar todos os dados em vez da pequena amostragem negativa, utilize a [API](#version-settings-api-use-of-usealltrainingdata).

### <a name="version-settings-api-use-of-usealltrainingdata"></a>Definições de versão API utilização de UseAllTrainingData

Utilize a [API de definições de versão](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) com o `UseAllTrainingData` conjunto para ser verdadeiro para desligar esta função.

## <a name="unnecessary-training"></a>Treino desnecessário

Não precisas de treinar depois de cada mudança. A formação deve ser feita depois de um grupo de alterações serem aplicadas ao modelo, e o próximo passo que pretende fazer é testar ou publicar. Se não precisa de testar ou publicar, o treino não é necessário.

## <a name="training-with-the-rest-apis"></a>Formação com as APIs REST

A formação no portal LUIS é um único passo para pressionar o botão **Train.** Treinar com as APIs rest é um processo em duas etapas. A primeira é [solicitar formação](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c45) com HTTP POST. Em seguida, solicite o [estado de formação](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c46) com HTTP Get.

Para saber quando o treino está completo, tem de fazer uma sondagem até que todos os modelos sejam treinados com sucesso.

## <a name="next-steps"></a>Passos seguintes

* [Testes interativos](luis-interactive-test.md)
* [Testes em lote](luis-how-to-batch-test.md)
