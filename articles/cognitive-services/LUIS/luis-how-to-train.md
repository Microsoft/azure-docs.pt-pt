---
title: Preparar a aplicação
titleSuffix: Language Understanding - Azure Cognitive Services
description: Treinamento é o processo de ensinar a sua versão de aplicação de compreensão de idiomas (LUIS) para aprimorar sua compreensão de linguagem natural. Treine a sua aplicação LUIS após as atualizações para o modelo de como adicionar, editar, etiquetagem ou a eliminação de entidades, intenções ou expressões com.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 01/23/2019
ms.author: diberry
ms.openlocfilehash: 1c3c7e913f2f096918efaebb84384df98bd68be8
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2019
ms.locfileid: "58895837"
---
# <a name="train-your-active-version-of-the-luis-app"></a>Preparar a sua versão do Active Directory da aplicação LUIS 

Treinamento é o processo de ensinar a sua aplicação de compreensão de idiomas (LUIS) para aprimorar sua compreensão de linguagem natural. Treine a sua aplicação LUIS após as atualizações para o modelo de como adicionar, editar, etiquetagem ou a eliminação de entidades, intenções ou expressões com. 

<!--
When you train a LUIS app by example, LUIS generalizes from the examples you have labeled, and it learns to recognize the relevant intents and entities. This teaches LUIS to improve classification accuracy in the future. -->

Treinamento e [teste](luis-concept-test.md) uma aplicação é um processo iterativo. Depois de preparar a aplicação do LUIS, testá-la com expressões de exemplo para ver se as intenções e entidades são reconhecidas corretamente. Se não estiverem, fazer atualizações para a aplicação LUIS, formação e teste novamente. 

Treinamento é aplicado para a versão do Active Directory no portal do LUIS. 

## <a name="how-to-train-interactively"></a>Como dar formação interativamente

Para iniciar o processo interativo no [portal de LUIS](https://www.luis.ai), primeiro tem de preparar a sua aplicação LUIS, pelo menos, uma vez. Certifique-se que cada intenção tem, pelo menos, uma expressão antes de treinamento.

1. Aceder à sua aplicação, selecionando o respetivo nome na **as minhas aplicações** página. 

2. Na sua aplicação, selecione **Train** no painel superior. 

3. Quando o treinamento estiver concluído, uma barra de notificação de verde é apresentada na parte superior do navegador.

<!-- The following note refers to what might cause the error message "Training failed: FewLabels for model: <ModelName>" -->

>[!NOTE]
>Se tiver uma ou mais objetivos na sua aplicação que não contêm as expressões de exemplo, não é possível preparar a sua aplicação. Adicione expressões para todos os seus objetivos. Para obter mais informações, consulte [adicionar expressões de exemplo](luis-how-to-add-example-utterances.md).

## <a name="train-with-all-data"></a>Treinar com todos os dados

Treinamento utiliza uma pequena porcentagem de amostragem negativa. Se pretender utilizar todos os dados em vez da amostragem negativa pequena, utilize o [versão configurações API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) com o `UseAllTrainingData` defina como verdadeiro para Desative esta funcionalidade. 

## <a name="unnecessary-training"></a>Treinamento desnecessário

Não é necessário preparar após todas as alterações. Treinamento deve ser feito depois de um grupo de alterações são aplicadas ao modelo e a próxima etapa que deseja fazer é testar ou publicar. Se não é necessário testar ou publicar, treinamento não é necessário. 

## <a name="training-with-the-rest-apis"></a>Treinamento com as APIs REST

Treinamento no portal do LUIS é um passo único de prima a **Train** botão. Treinamento com as APIs REST é um processo de dois passos. A primeira é [pedir treinamento](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c45) com HTTP POST. Em seguida, solicitar a [estado de treinamento](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c46) com HTTP Get. 

Para saber quando o treinamento estiver concluído, terá de consultar o estado até que todos os modelos são treinados com êxito. 

## <a name="next-steps"></a>Passos Seguintes

* [Identifique expressões sugeridas com os LUIS](luis-how-to-review-endpoint-utterances.md) 
* [Utilizar as funcionalidades para melhorar o desempenho da sua aplicação LUIS](luis-how-to-add-features.md) 
