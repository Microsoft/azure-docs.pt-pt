---
title: Treinar aplicativo-LUIS
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
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2019
ms.locfileid: "74143688"
---
# <a name="train-your-active-version-of-the-luis-app"></a>Treinar sua versão ativa do aplicativo LUIS 

Treinamento é o processo de ensinar a sua aplicação de compreensão de idiomas (LUIS) para aprimorar sua compreensão de linguagem natural. Treine a sua aplicação LUIS após as atualizações para o modelo de como adicionar, editar, etiquetagem ou a eliminação de entidades, intenções ou expressões com. 

Treinamento e [teste](luis-concept-test.md) uma aplicação é um processo iterativo. Depois de preparar a aplicação do LUIS, testá-la com expressões de exemplo para ver se as intenções e entidades são reconhecidas corretamente. Se não estiverem, fazer atualizações para a aplicação LUIS, formação e teste novamente. 

Treinamento é aplicado para a versão do Active Directory no portal do LUIS. 

## <a name="how-to-train-interactively"></a>Como dar formação interativamente

Para iniciar o processo interativo no [portal de LUIS](https://www.luis.ai), primeiro tem de preparar a sua aplicação LUIS, pelo menos, uma vez. Certifique-se que cada intenção tem, pelo menos, uma expressão antes de treinamento.

1. Aceder à sua aplicação, selecionando o respetivo nome na **as minhas aplicações** página. 

1. Na sua aplicação, selecione **Train** no painel superior. 

1. Quando o treinamento for concluído, uma notificação aparecerá na parte superior do navegador.

## <a name="training-date-and-time"></a>Data e hora de treinamento

A data e a hora de treinamento são GMT + 2. 

## <a name="train-with-all-data"></a>Treinar com todos os dados

Treinamento utiliza uma pequena porcentagem de amostragem negativa. Se você quiser usar todos os dados em vez da pequena amostragem negativa, use a [API](#version-settings-api-use-of-usealltrainingdata).

### <a name="version-settings-api-use-of-usealltrainingdata"></a>Configurações de versão uso da API de UseAllTrainingData

Use a [API de configurações de versão](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) com o `UseAllTrainingData` definido como true para desativar esse recurso. 

## <a name="unnecessary-training"></a>Treinamento desnecessário

Não é necessário preparar após todas as alterações. Treinamento deve ser feito depois de um grupo de alterações são aplicadas ao modelo e a próxima etapa que deseja fazer é testar ou publicar. Se não é necessário testar ou publicar, treinamento não é necessário. 

## <a name="training-with-the-rest-apis"></a>Treinamento com as APIs REST

Treinamento no portal do LUIS é um passo único de prima a **Train** botão. Treinamento com as APIs REST é um processo de dois passos. A primeira é [pedir treinamento](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c45) com HTTP POST. Em seguida, solicitar a [estado de treinamento](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c46) com HTTP Get. 

Para saber quando o treinamento estiver concluído, terá de consultar o estado até que todos os modelos são treinados com êxito. 

## <a name="next-steps"></a>Passos seguintes

* [Testes interativos](luis-interactive-test.md)
* [Teste em lote](luis-how-to-batch-test.md)
