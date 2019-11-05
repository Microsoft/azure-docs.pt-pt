---
title: Modelos predefinidos para Reconhecimento vocal
titleSuffix: Azure Cognitive Services
description: O LUIS inclui um conjunto de modelos predefinidos para adicionar rapidamente cenários de usuário de conversação comuns.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: 2e90a5f3f7cd2cfde2a2ead26674d2348a24ae6f
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73507783"
---
# <a name="add-prebuilt-models-for-common-usage-scenarios"></a>Adicionar modelos predefinidos para cenários de uso comuns 

O LUIS inclui um conjunto de intenções predefinidas dos domínios predefinidos para adicionar rapidamente tentativas e declarações comuns. Essa é uma maneira rápida e fácil de adicionar habilidades ao seu aplicativo cliente de conversa sem ter que projetar os modelos para essas habilidades. 

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

## <a name="add-a-prebuilt-domain"></a>Adicionar um domínio pré-concebido

1. Na página **meus aplicativos** , selecione seu aplicativo. Isso abre seu aplicativo na seção de **Build** do aplicativo. 

1. Na página **tentativas** , selecione **adicionar domínios predefinidos** na barra de ferramentas inferior, esquerda. 

1. Selecione a intenção de **calendário** e, em seguida, selecione botão **Adicionar domínio** .

    ![Adicionar domínio predefinido do calendário](./media/luis-prebuilt-domains/add-prebuilt-domain.png)

1. Selecione **tentativas** no painel de navegação esquerdo para exibir as intenções do calendário. Cada tentativa desse domínio é prefixada com `Calendar.`. Junto com declarações, duas entidades para esse domínio são adicionadas ao aplicativo: `Calendar.Location` e `Calendar.Subject`. 

### <a name="train-and-publish"></a>Preparar e publicar

1. Depois que o domínio for adicionado, treine o aplicativo selecionando **treinar** na parte superior, barra de ferramentas direita. 

1. Na barra de ferramentas superior, selecione **publicar**. Publicar na **produção**. 

1. Quando a notificação de êxito verde for exibida, selecione o link **consultar a lista de pontos de extremidade** para ver os pontos de extremidade.

1. Selecione um ponto de extremidade. Uma nova guia do navegador é aberta para esse ponto de extremidade. Mantenha a guia do navegador aberta e continue na seção de **teste** .

### <a name="test"></a>Teste

Teste a nova tentativa no ponto de extremidade ao adicionar um valor para o parâmetro **q** : `Schedule a meeting with John Smith in Seattle next week`.

LUIS retorna a intenção correta e a entidade de reunião:

```json
{
  "query": "Schedule a meeting with John Smith in Seattle next week",
  "topScoringIntent": {
    "intent": "Calendar.Add",
    "score": 0.824783146
  },
  "entities": [
    {
      "entity": "a meeting with john smith",
      "type": "Calendar.Subject",
      "startIndex": 9,
      "endIndex": 33,
      "score": 0.484055847
    }
  ]
}
```

## <a name="add-a-prebuilt-intent"></a>Adicionar uma intenção predefinida

1. Na página **meus aplicativos** , selecione seu aplicativo. Isso abre seu aplicativo na seção de **Build** do aplicativo. 

1. Na página **tentativas** , selecione **Adicionar intenção predefinida** na barra de ferramentas acima da lista de intenções. 

1. Selecione a intenção **Utilities. Cancel** na caixa de diálogo pop-up. 

    ![Adicionar intenção predefinida](./media/luis-prebuilt-intents/prebuilt-intents-ddl.png)

1. Selecione o botão **concluído** .

### <a name="train-and-test"></a>Treinar e testar

1. Depois que a intenção for adicionada, treine o aplicativo selecionando **treinar** na parte superior, barra de ferramentas direita. 

1. Teste a nova intenção selecionando **testar** na barra de ferramentas à direita. 

1. Na caixa de texto, digite declarações para cancelar:

    |Testar expressão|Classificação da predição|
    |--|:--|
    |Quero cancelar meu vôo.|0,67|
    |Cancele a compra.|0,52|
    |Cancele a reunião.|0,56|

    ![Testar intenção predefinida](./media/luis-prebuilt-intents/test.png)

## <a name="add-a-prebuilt-entity"></a>Adicionar uma entidade predefinida

1. Abra seu aplicativo clicando em seu nome na página **meus aplicativos** e, em seguida, clique em **entidades** no lado esquerdo. 

1. Na página **entidades** , clique em **Adicionar entidade predefinida**.

1. Na caixa de diálogo **adicionar entidades predefinidas** , selecione a entidade predefinida datetimeV2. 

    ![Caixa de diálogo Adicionar entidade predefinida](./media/luis-use-prebuilt-entity/add-prebuilt-entity-dialog.png)

1. Selecione **Done** (Concluído). Depois que a entidade for adicionada, você não precisará treinar o aplicativo. 

## <a name="publish-to-view-prebuilt-model-from-prediction-endpoint"></a>Publicar para exibir o modelo predefinido do ponto de extremidade de previsão

A maneira mais fácil de exibir o valor de um modelo predefinido é consultar o ponto de extremidade publicado. 

## <a name="marking-entities-containing-a-prebuilt-entity-token"></a>Marcando entidades contendo um token de entidade predefinido
 Se você tiver texto, como `HH-1234`, que deseja marcar como uma entidade personalizada _e_ tiver um [número predefinido](luis-reference-prebuilt-number.md) adicionado ao modelo, não será possível marcar a entidade personalizada no portal do Luis. Você pode marcá-lo com a API. 

 Para marcar esse tipo de token, em que parte dele já está marcada com uma entidade predefinida, remova a entidade predefinida do aplicativo LUIS. Você não precisa treinar o aplicativo. Em seguida, marque o token com sua própria entidade personalizada. Em seguida, adicione a entidade predefinida de volta ao aplicativo LUIS.

 Para outro exemplo, considere o expressão como uma lista de preferências de classe: `I want first year spanish, second year calculus, and fourth year english lit.` se o aplicativo LUIS tiver o ordinal de compilação adicionado, `first`, `second`e `fourth` já serão marcados com ordinais. Se você quiser capturar o ordinal e a classe, poderá criar uma entidade composta e encapsulá-la em todo o ordinal predefinido e a entidade personalizada para o nome da classe.

## <a name="next-steps"></a>Passos seguintes
> [!div class="nextstepaction"]
> [Compilar modelo a partir de. csv com APIs REST](./luis-tutorial-node-import-utterances-csv.md)
