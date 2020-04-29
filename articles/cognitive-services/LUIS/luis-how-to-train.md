---
title: Aplicativo de comboio - LUIS
titleSuffix: Azure Cognitive Services
description: A formação é o processo de ensino da sua versão de app Language Understanding (LUIS) para melhorar a sua compreensão da linguagem natural. Treine a sua aplicação LUIS após atualizações para o modelo, como adicionar, editar, rotular ou apagar entidades, intenções ou expressões.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/15/2019
ms.author: diberry
ms.openlocfilehash: 1da8ab3015730c6b3e1962301a34b1ad43b1aad6
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "79219865"
---
# <a name="train-your-active-version-of-the-luis-app"></a>Treine a sua versão ativa da app LUIS 

A formação é o processo de ensino da sua app Language Understanding (LUIS) para melhorar a sua compreensão da linguagem natural. Treine a sua aplicação LUIS após atualizações para o modelo, como adicionar, editar, rotular ou apagar entidades, intenções ou expressões. 

Treinar e [testar](luis-concept-test.md) uma aplicação é um processo iterativo. Depois de treinar a sua aplicação LUIS, testa-a com declarações de amostra para ver se as intenções e entidades são reconhecidas corretamente. Se não forem, faça atualizações para a app LUIS, treine e teste novamente. 

A formação é aplicada à versão ativa no portal LUIS. 

## <a name="how-to-train-interactively"></a>Como treinar interativamente

Para iniciar o processo iterativo no [portal LUIS,](https://www.luis.ai)primeiro precisa de treinar a sua app LUIS pelo menos uma vez. Certifique-se de que todas as intenções têm pelo menos uma expressão antes do treino.

1. Aceda à sua aplicação selecionando o seu nome na página **My Apps.** 

1. Na sua aplicação, selecione **Train** no painel superior. 

1. Quando o treino está concluído, uma notificação aparece no topo do navegador.

## <a name="training-date-and-time"></a>Data e hora de treino

A data e a hora do treino são GMT + 2. 

## <a name="train-with-all-data"></a>Treine com todos os dados

O treino utiliza uma pequena percentagem de amostragem negativa. Se pretender utilizar todos os dados em vez da pequena amostragem negativa, utilize a [API](#version-settings-api-use-of-usealltrainingdata).

### <a name="version-settings-api-use-of-usealltrainingdata"></a>Definições de versão API utilização do UseAllTrainingData

Utilize as [definições de Versão API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) com o `UseAllTrainingData` conjunto verdadeiro para desligar esta funcionalidade. 

## <a name="unnecessary-training"></a>Formação desnecessária

Não precisas de treinar depois de cada mudança. A formação deve ser feita depois de um grupo de alterações ser aplicada ao modelo, e o próximo passo que pretende fazer é testar ou publicar. Se não precisa de testar ou publicar, o treino não é necessário. 

## <a name="training-with-the-rest-apis"></a>Formação com as APIs rest

A formação no portal LUIS é um único passo para premir o botão **Comboio.** Treinar com as APIs rest é um processo em duas etapas. A primeira é [solicitar formação](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c45) com http POST. Em seguida, solicite o estado de [treino](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c46) com http Get. 

Para saber quando o treino está completo, você tem que sondar o estado até que todos os modelos sejam treinados com sucesso. 

## <a name="next-steps"></a>Passos seguintes

* [Testes interativos](luis-interactive-test.md)
* [Testes em lote](luis-how-to-batch-test.md)
