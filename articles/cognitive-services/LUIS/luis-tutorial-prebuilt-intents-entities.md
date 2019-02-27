---
title: Pré-criados intenções e entidades
titleSuffix: Azure Cognitive Services
description: Neste tutorial, adicione pré-criados intenções e entidades a uma aplicação para obter rapidamente intenção extração de dados e de predição. Não é necessário etiquetar expressões com entidades pré-concebidas. A entidade é detetada automaticamente.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: diberry
ms.openlocfilehash: cf16f27a8d39871491b7cf46a509b9714a669667
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56873819"
---
# <a name="tutorial-identify-common-intents-and-entities"></a>Tutorial: Identificar intenções e entidades comuns

Neste tutorial, adicione pré-criados intenções e entidades a uma aplicação de tutorial de recursos humanos para obter rapidamente intenção extração de dados e de predição. Não é necessário marcar qualquer expressões com entidades previamente concebidas porque a entidade é detetada automaticamente.

Modelos pré-construídos (domínios, objetivos e entidades) ajudam a criar rapidamente o seu modelo.

**Neste tutorial, vai aprender a:**

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


## <a name="add-prebuilt-intents-to-help-with-common-user-intentions"></a>Adicionar intenções criados previamente para ajudar com intenções comuns do utilizador

O LUIS oferece várias intenções pré-concebidas para ajudar com intenções comuns do utilizador.  

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

1. Selecione **Adicionar intenção de domínio pré-concebido**. 

1. Procurar `Utilities`. 

    [![Captura de ecrã da caixa de diálogo de objetivos criados previamente com utilitários na caixa de pesquisa](./media/luis-tutorial-prebuilt-intents-and-entities/prebuilt-intent-utilities.png)](./media/luis-tutorial-prebuilt-intents-and-entities/prebuilt-intent-utilities.png#lightbox)

1. Selecione as intenções seguintes e selecione **Concluído**: 

    * Utilities.Cancel
    * Utilities.Confirm
    * Utilities.Help
    * Utilities.StartOver
    * Utilities.Stop

    Esses objetivos são úteis para determinar em que lugar, a conversa, o utilizador é e o que eles estão fazendo para o fazer. 


## <a name="add-prebuilt-entities-to-help-with-common-data-type-extraction"></a>Adicionar entidades previamente concebidas para ajudar a extração do tipo de dados comuns

O LUIS oferece várias entidades pré-concebidas para extração de dados comuns. 

1. Selecione **Entidades** no menu de navegação esquerdo.

1. Selecione o botão **Adicionar entidade pré-compilada**.

1. Selecione as entidades a seguir na lista de entidades previamente concebidas, em seguida, selecione **feito**:

    * **[PersonName](luis-reference-prebuilt-person.md)** 
    * **[GeographyV2](luis-reference-prebuilt-geographyV2.md)**

    ![Captura de ecrã do número selecionado na caixa de diálogo de entidades pré-concebidas](./media/luis-tutorial-prebuilt-intents-and-entities/select-prebuilt-entities.png)

    Estas entidades lhe ajudarão a adicionar o reconhecimento de nome e local para a aplicação cliente.

## <a name="add-example-utterances-to-the-none-intent"></a>Adicionar expressões de exemplo para a intenção None 

[!INCLUDE [Follow these steps to add the None intent to the app](../../../includes/cognitive-services-luis-create-the-none-intent.md)]

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>Preparar a aplicação para que as alterações à intenção podem ser testadas 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-so-the-trained-model-is-queryable-from-the-endpoint"></a>Publicar a aplicação, para que o modelo preparado é consultável do ponto final

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Obter previsão intenções e entidades do ponto final

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

1. Vá para o final do URL na barra de endereço do browser e introduza `I want to cancel my trip to Seattle to see Bob Smith`. O último parâmetro de cadeia de consulta é `q`, a expressão **query**. 

    ```json
    {
      "query": "I want to cancel my trip to Seattle to see Bob Smith",
      "topScoringIntent": {
        "intent": "Utilities.Cancel",
        "score": 0.807676256
      },
      "intents": [
        {
          "intent": "Utilities.Cancel",
          "score": 0.807676256
        },
        {
          "intent": "Utilities.StartOver",
          "score": 0.0487322025
        },
        {
          "intent": "Utilities.Help",
          "score": 0.0208660364
        },
        {
          "intent": "None",
          "score": 0.008789532
        },
        {
          "intent": "Utilities.Stop",
          "score": 0.006929268
        },
        {
          "intent": "Utilities.Confirm",
          "score": 0.00136293867
        }
      ],
      "entities": [
        {
          "entity": "seattle",
          "type": "builtin.geographyV2.city",
          "startIndex": 28,
          "endIndex": 34
        },
        {
          "entity": "bob smith",
          "type": "builtin.personName",
          "startIndex": 43,
          "endIndex": 51
        }
      ]
    }
    ```

    O resultado prever a intenção de Utilities.Cancel com confiança de 80% e os dados de nome de cidade e de pessoa extraídos. 


## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>Informações relacionadas

Saiba mais sobre modelos pré-construídos:

* [Domínios pré-concebidos](luis-reference-prebuilt-domains.md): estes são domínios comuns que reduzem a criação da aplicação de LUIS geral
* Objetivos pré-criados: estes são os objetivos individuais dos domínios comuns. Pode adicionar intenções individualmente em vez de adicionar todo o domínio.
* [Entidades pré-concebidas](luis-prebuilt-entities.md): estes são tipos de dados comuns, útil para a maioria das aplicações de LUIS.

Saiba mais sobre como trabalhar com a sua aplicação LUIS:

* [Como dar formação](luis-how-to-train.md)
* [Como publicar](luis-how-to-publish-app.md)
* [Como testar no portal do LUIS](luis-interactive-test.md)

## <a name="next-steps"></a>Passos Seguintes

Ao adicionar intenções e entidades pré-concebidas, a aplicação cliente pode determinar as intenções de utilizador comuns e extrair tipos de dados comuns.  

> [!div class="nextstepaction"]
> [Adicionar uma entidade de expressão regular à aplicação](luis-quickstart-intents-regex-entity.md)

