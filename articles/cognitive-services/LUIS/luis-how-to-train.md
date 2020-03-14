---
title: Aplicativo de comboio - LUIS
titleSuffix: Azure Cognitive Services
description: Treinamento é o processo de ensinar a sua versão de aplicação de compreensão de idiomas (LUIS) para aprimorar sua compreensão de linguagem natural. Treine a sua aplicação LUIS após as atualizações para o modelo de como adicionar, editar, etiquetagem ou a eliminação de entidades, intenções ou expressões com.
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
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79219865"
---
# <a name="train-your-active-version-of-the-luis-app"></a>Treine a sua versão ativa da app LUIS 

Treinamento é o processo de ensinar a sua aplicação de compreensão de idiomas (LUIS) para aprimorar sua compreensão de linguagem natural. Treine a sua aplicação LUIS após as atualizações para o modelo de como adicionar, editar, etiquetagem ou a eliminação de entidades, intenções ou expressões com. 

Treinar e [testar](luis-concept-test.md) uma aplicação é um processo iterativo. Depois de preparar a aplicação do LUIS, testá-la com expressões de exemplo para ver se as intenções e entidades são reconhecidas corretamente. Se não estiverem, fazer atualizações para a aplicação LUIS, formação e teste novamente. 

Treinamento é aplicado para a versão do Active Directory no portal do LUIS. 

## <a name="how-to-train-interactively"></a>Como dar formação interativamente

Para iniciar o processo iterativo no [portal LUIS,](https://www.luis.ai)primeiro precisa de treinar a sua app LUIS pelo menos uma vez. Certifique-se que cada intenção tem, pelo menos, uma expressão antes de treinamento.

1. Aceda à sua aplicação selecionando o seu nome na página **My Apps.** 

1. Na sua aplicação, selecione **Train** no painel superior. 

1. Quando o treino está concluído, uma notificação aparece no topo do navegador.

## <a name="training-date-and-time"></a>Data e hora de treino

A data e a hora do treino são GMT + 2. 

## <a name="train-with-all-data"></a>Treinar com todos os dados

Treinamento utiliza uma pequena porcentagem de amostragem negativa. Se pretender utilizar todos os dados em vez da pequena amostragem negativa, utilize a [API](#version-settings-api-use-of-usealltrainingdata).

### <a name="version-settings-api-use-of-usealltrainingdata"></a>Definições de versão API utilização do UseAllTrainingData

Utilize as definições de [Versão API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) com o `UseAllTrainingData` definido para desativar esta funcionalidade. 

## <a name="unnecessary-training"></a>Treinamento desnecessário

Não é necessário preparar após todas as alterações. Treinamento deve ser feito depois de um grupo de alterações são aplicadas ao modelo e a próxima etapa que deseja fazer é testar ou publicar. Se não é necessário testar ou publicar, treinamento não é necessário. 

## <a name="training-with-the-rest-apis"></a>Treinamento com as APIs REST

A formação no portal LUIS é um único passo para premir o botão **Comboio.** Treinamento com as APIs REST é um processo de dois passos. A primeira é [solicitar formação](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c45) com http POST. Em seguida, solicite o estado de [treino](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c46) com http Get. 

Para saber quando o treinamento estiver concluído, terá de consultar o estado até que todos os modelos são treinados com êxito. 

## <a name="next-steps"></a>Passos seguintes

* [Testes interativos](luis-interactive-test.md)
* [Ensaio de lote](luis-how-to-batch-test.md)
