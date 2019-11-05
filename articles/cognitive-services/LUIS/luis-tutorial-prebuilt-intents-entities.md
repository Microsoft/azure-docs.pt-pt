---
title: 'Tutorial: tentativas e entidades predefinidas-LUIS'
titleSuffix: Azure Cognitive Services
description: Neste tutorial, adicione as intenções e as entidades predefinidas a um aplicativo para obter rapidamente a previsão de intenção e a extração de dados. Não é necessário etiquetar expressões com entidades pré-concebidas. A entidade é detetada automaticamente.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 10/21/2019
ms.author: diberry
ms.openlocfilehash: 0a59d9783eac122f96b1671f2dba5d0d708e1d83
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73499400"
---
# <a name="tutorial-identify-common-intents-and-entities"></a>Tutorial: identificar tentativas e entidades comuns

Neste tutorial, adicione tentativas e entidades predefinidas a um aplicativo de tutorial de recursos humanos para obter rapidamente a previsão de intenções e a extração de dados. Você não precisa marcar nenhum declarações com entidades predefinidas porque a entidade é detectada automaticamente.

Modelos predefinidos (domínios, intenções e entidades) ajudam a criar seu modelo rapidamente.

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

**Neste tutorial, ficará a saber como:**

> [!div class="checklist"]
> * Criar nova aplicação
> * Adicionar intenções pré-concebidas 
> * Adicionar entidades pré-concebidas 
> * Preparar 
> * Publicar 
> * Obter as intenções e as entidades do ponto final

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="create-a-new-app"></a>Criar uma nova aplicação

[!INCLUDE [Follow these steps to create a new LUIS app](../../../includes/cognitive-services-luis-create-new-app-steps.md)]


## <a name="add-prebuilt-intents-to-help-with-common-user-intentions"></a>Adicionar tentativas predefinidas para ajudar com as intenções comuns do usuário

O LUIS oferece várias intenções pré-concebidas para ajudar com intenções comuns do utilizador.  

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

1. Selecione **Adicionar intenção de domínio pré-concebido**. 

1. Procurar `Utilities`. 

1. Selecione todas as tentativas e selecione **concluído**. Essas intenções são úteis para determinar onde, na conversa, o usuário é e o que eles estão pedindo. 

## <a name="add-prebuilt-entities-to-help-with-common-data-type-extraction"></a>Adicionar entidades predefinidas para ajudar com a extração de tipo de dados comum

O LUIS oferece várias entidades pré-concebidas para extração de dados comuns. 

1. Selecione **Entidades** no menu de navegação esquerdo.

1. Selecione o botão **Adicionar entidade pré-compilada**.

1. Selecione as seguintes entidades na lista de entidades predefinidas e selecione **concluído**:

   * **[GeographyV2](luis-reference-prebuilt-geographyV2.md)**

     Esta entidade ajudará você a adicionar o reconhecimento de local ao seu aplicativo cliente.

## <a name="add-example-utterances-to-the-none-intent"></a>Adicionar declarações de exemplo à intenção None 

[!INCLUDE [Follow these steps to add the None intent to the app](../../../includes/cognitive-services-luis-create-the-none-intent.md)]

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>Treinar o aplicativo para que as alterações na intenção possam ser testadas 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-so-the-trained-model-is-queryable-from-the-endpoint"></a>Publicar o aplicativo para que o modelo treinado seja passível de consulta do ponto de extremidade

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Obter previsão de intenção e entidade do ponto de extremidade

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

1. Vá para o final do URL na barra de endereço do browser e introduza `I want to cancel my trip to Seattle`. O último parâmetro de cadeia de consulta é `q`, a expressão **query**. 

    ```json
    {
      "query": "I want to cancel my trip to Seattle",
      "topScoringIntent": {
        "intent": "Utilities.Cancel",
        "score": 0.1055009
      },
      "intents": [
        {
          "intent": "Utilities.Cancel",
          "score": 0.1055009
        },
        {
          "intent": "Utilities.SelectItem",
          "score": 0.02659072
        },
        {
          "intent": "Utilities.Stop",
          "score": 0.0253379084
        },
        {
          "intent": "Utilities.ReadAloud",
          "score": 0.02528683
        },
        {
          "intent": "Utilities.SelectNone",
          "score": 0.02434013
        },
        {
          "intent": "Utilities.Escalate",
          "score": 0.009161292
        },
        {
          "intent": "Utilities.Help",
          "score": 0.006861785
        },
        {
          "intent": "Utilities.StartOver",
          "score": 0.00633448
        },
        {
          "intent": "Utilities.ShowNext",
          "score": 0.0053827134
        },
        {
          "intent": "None",
          "score": 0.002602003
        },
        {
          "intent": "Utilities.ShowPrevious",
          "score": 0.001797354
        },
        {
          "intent": "Utilities.SelectAny",
          "score": 0.000831930141
        },
        {
          "intent": "Utilities.Repeat",
          "score": 0.0006924066
        },
        {
          "intent": "Utilities.Confirm",
          "score": 0.000606057351
        },
        {
          "intent": "Utilities.GoBack",
          "score": 0.000276725681
        },
        {
          "intent": "Utilities.FinishTask",
          "score": 0.000267822179
        },
        {
          "intent": "Utilities.Reject",
          "score": 3.21784828E-05
        }
      ],
      "entities": [
        {
          "entity": "seattle",
          "type": "builtin.geographyV2.city",
          "startIndex": 28,
          "endIndex": 34
        }
      ]
    }
    ```

    O resultado prevê a intenção Utilities. Cancel com 80% de confiança e extraiu os dados da cidade. 


## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>Informações relacionadas

Saiba mais sobre modelos predefinidos:

* [Domínios predefinidos](luis-reference-prebuilt-domains.md): são domínios comuns que reduzem a criação geral de aplicativos Luis
* Tentativas predefinidas: são as intenções individuais dos domínios comuns. Você pode adicionar tentativas individualmente em vez de adicionar o domínio inteiro.
* [Entidades predefinidas](luis-prebuilt-entities.md): são tipos de dados comuns úteis para a maioria dos aplicativos Luis.

Saiba mais sobre como trabalhar com seu aplicativo LUIS:

* [Como treinar](luis-how-to-train.md)
* [Como publicar](luis-how-to-publish-app.md)
* [Como testar no portal do LUIS](luis-interactive-test.md)

## <a name="next-steps"></a>Passos seguintes

Ao adicionar intenções e entidades pré-concebidas, a aplicação cliente pode determinar as intenções de utilizador comuns e extrair tipos de dados comuns.  

> [!div class="nextstepaction"]
> [Adicionar uma entidade de expressão regular à aplicação](luis-quickstart-intents-regex-entity.md)

