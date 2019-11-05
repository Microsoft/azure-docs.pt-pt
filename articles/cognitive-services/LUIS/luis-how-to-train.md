---
title: Treinar aplicativo-LUIS
titleSuffix: Azure Cognitive Services
description: O treinamento é o processo de ensinar sua versão do aplicativo Reconhecimento vocal (LUIS) para melhorar seu entendimento em linguagem natural. Treine seu aplicativo LUIS após as atualizações para o modelo, como adicionar, editar, rotular ou excluir entidades, intenções ou declarações.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: 259ea23c05f0c0a138ad54b6efd11aad2061cf7a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73500221"
---
# <a name="train-your-active-version-of-the-luis-app"></a>Treinar sua versão ativa do aplicativo LUIS 

O treinamento é o processo de ensinar seu aplicativo de Reconhecimento vocal (LUIS) para melhorar seu conhecimento em linguagem natural. Treine seu aplicativo LUIS após as atualizações para o modelo, como adicionar, editar, rotular ou excluir entidades, intenções ou declarações. 

O treinamento e o [teste](luis-concept-test.md) de um aplicativo é um processo iterativo. Depois de treinar seu aplicativo LUIS, você o testará com o declarações de exemplo para ver se as intenções e as entidades são reconhecidas corretamente. Se não estiverem, faça atualizações no aplicativo LUIS, treine e teste novamente. 

O treinamento é aplicado à versão ativa no portal do LUIS. 

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

## <a name="how-to-train-interactively"></a>Como treinar de forma interativa

Para iniciar o processo iterativo no [portal do Luis](https://www.luis.ai), primeiro você precisa treinar seu aplicativo Luis pelo menos uma vez. Certifique-se de que todas as intenções tenham pelo menos um expressão antes do treinamento.

1. Acesse seu aplicativo selecionando seu nome na página **meus aplicativos** . 

2. Em seu aplicativo, selecione **treinar** no painel superior. 

3. Quando o treinamento for concluído, uma barra de notificação verde aparecerá na parte superior do navegador.

<!-- The following note refers to what might cause the error message "Training failed: FewLabels for model: <ModelName>" -->

>[!NOTE]
>Se você tiver uma ou mais intenções em seu aplicativo que não contêm o exemplo declarações, não será possível treinar seu aplicativo. Adicione declarações para todas as suas intenções. Para obter mais informações, consulte [Adicionar exemplo declarações](luis-how-to-add-example-utterances.md).

## <a name="training-date-and-time"></a>Data e hora de treinamento

A data e a hora de treinamento são GMT + 2. 

## <a name="train-with-all-data"></a>Treinar com todos os dados

O treinamento usa uma pequena porcentagem de amostragem negativa. 

Se você quiser usar todos os dados em vez da pequena amostragem negativa, use a [API](#version-settings-api-use-of-usealltrainingdata).

<!--

 or the [LUIS portal setting](#luis-portal-setting-to-use-all-training-data)

### LUIS portal setting to use all training data

!!!IGNITE


-->

### <a name="version-settings-api-use-of-usealltrainingdata"></a>Configurações de versão uso da API de UseAllTrainingData

Use a [API de configurações de versão](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) com o `UseAllTrainingData` definido como true para desativar esse recurso. 

## <a name="unnecessary-training"></a>Treinamento desnecessário

Você não precisa treinar após cada alteração única. O treinamento deve ser feito depois que um grupo de alterações é aplicado ao modelo e a próxima etapa que você deseja fazer é testar ou publicar. Se você não precisar testar ou publicar, o treinamento não será necessário. 

## <a name="training-with-the-rest-apis"></a>Treinamento com as APIs REST

O treinamento no portal do LUIS é uma única etapa de pressionar o botão **treinar** . O treinamento com as APIs REST é um processo de duas etapas. A primeira é [solicitar treinamento](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c45) com http post. Em seguida, solicite o [status de treinamento](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c46) com http Get. 

Para saber quando o treinamento está concluído, você precisa sondar o status até que todos os modelos sejam treinados com êxito. 

## <a name="next-steps"></a>Passos seguintes

* [Rótulo sugerido declarações com LUIS](luis-how-to-review-endpoint-utterances.md) 
* [Use os recursos para melhorar o desempenho do aplicativo LUIS](luis-how-to-add-features.md) 
