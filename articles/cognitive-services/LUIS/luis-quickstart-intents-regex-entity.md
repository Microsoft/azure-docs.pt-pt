---
title: Entidade de expressão regular
titleSuffix: Azure Cognitive Services
description: Extraia dados formatados de forma consistente a partir de uma expressão com a entidade Expressão Regular.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: diberry
ms.openlocfilehash: 012d77227e7c0283b565361dda32cb77e12fc62b
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56867274"
---
# <a name="tutorial-get-well-formatted-data-from-the-utterance"></a>Tutorial: Obter dados bem formatados a partir da expressão
Neste tutorial, crie uma aplicação para extrair dados formatados de forma consistente de uma expressão a utilizar o **expressão Regular** entidade.

**Neste tutorial, vai aprender a:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Criar uma nova aplicação 
> * Adicionar intenções
> * Adicionar entidade de expressão regular 
> * Preparar
> * Publicar
> * Obter as intenções e as entidades do ponto final

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="regular-expression-entities"></a>Entidades de expressão regular

Utilize esta aplicação da entidade de expressão regular é extrair números de formulário de recursos humanos (RH) bem formatado de uma expressão. Embora a intenção da expressão seja sempre determinada por machine learning, este tipo de entidade específica não é de machine-learned. 

**As expressões de exemplo incluem:**

|Expressões de exemplo|
|--|
|Onde posso encontrar HRF 123456?|
|Quem criados HRF 123234?|
|HRF 456098 está publicada em francês?|
|HRF-456098|
|Data de HRF 456098?|
 
Uma expressão regular é uma boa opção para este tipo de dados quando:

* Os dados estão bem formatados.


## <a name="create-a-new-app"></a>Criar uma nova aplicação

[!INCLUDE [Follow these steps to create a new LUIS app](../../../includes/cognitive-services-luis-create-new-app-steps.md)]

## <a name="create-intent-for-finding-form"></a>Criar intenção para encontrar o formulário

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

1. Selecione **Create new intent** (Criar nova intenção). 

1. Introduza `FindForm` na caixa de diálogo de pop-up e, em seguida, selecione **Concluído**. 

    ![Captura de ecrã da caixa de diálogo Criar nova intenção com Utilitários na caixa de pesquisa](./media/luis-quickstart-intents-regex-entity/create-new-intent-ddl.png)

1. Adicione expressões de exemplo à intenção.

    |Expressões de exemplo|
    |--|
    |Qual é o URL para hrf-123456?|
    |Onde está hrf-345678?|
    |Quando foi atualizado hrf-456098?|
    |João Silva atualizou hrf-234639 na semana passada?|
    |Como todas as versões das hrf 345123 existem?|
    |Quem tem de autorizar o formulário hrf-123456?|
    |Quantas pessoas têm de terminar sessão em hrf-345678?|
    |Data de hrf-234123?|
    |Autor de hrf-546234?|
    |Título de hrf-456234?|

    [![Página de captura de ecrã da intenção com expressões com novo realçado](./media/luis-quickstart-intents-regex-entity/findform-intent.png)](./media/luis-quickstart-intents-regex-entity/findform-intent.png#lightbox)

    [!INCLUDE [Do not use too few utterances](../../../includes/cognitive-services-luis-too-few-example-utterances.md)]  

## <a name="use-the-regular-expression-entity-for-well-formatted-data"></a>Utilizar a entidade de expressão regular para dados de formatados correto
A entidade de expressão regular para corresponder ao número de formulário é `hrf-[0-9]{6}`. Esta expressão regular corresponde aos carateres literais `hrf-`, mas ignora as maiúsculas e minúsculas, e as variantes culturais. Corresponde aos dígitos 0 a 9, para exatamente 6 dígitos.

HRF significa `human resources form`.

O LUIS divide a expressão em tokens quando esta é adicionada a uma intenção. A atomização para estas expressões adiciona espaços antes e depois do hífen, `Where is HRF - 123456?`. A expressão regular é aplicada à expressão no respetivo formato não processado, antes de ser atomizada. Uma vez que é aplicada ao formato _não processado_, a expressão regular não tem de lidar com limites de palavras. 

Crie uma entidade de expressão regular para indicar ao LUIS o que é um formato de número HRF nos passos seguintes:

1. Selecione **Entidades** no painel esquerdo.

1. Selecione o botão **Criar nova entidade** na página Entidades. 

1. Na caixa de diálogo de pop-up, introduza o novo nome de entidade `HRF-number`, selecione **RegEx** como tipo de entidade, introduza `hrf-[0-9]{6}` como o valor **Regex** e, em seguida, selecione **Concluído**.

    ![Captura de ecrã da definição Propriedades da nova entidade da caixa de diálogo de pop-up](./media/luis-quickstart-intents-regex-entity/create-regex-entity.png)

1. Selecione **Intenções** no menu à esquerda e, em seguida, a intenção **FindForm** para ver a expressão regular etiquetada nas expressões. 

    [![Captura de ecrã da expressão Etiqueta com o padrão de entidade e regex existente](./media/luis-quickstart-intents-regex-entity/labeled-utterances-for-entity.png)](./media/luis-quickstart-intents-regex-entity/labeled-utterances-for-entity.png#lightbox)

    Como a entidade não é uma entidade aprendidas por máquina, a entidade é aplicada as expressões e apresentada no Web site do LUIS, assim que for criado.

## <a name="add-example-utterances-to-the-none-intent"></a>Adicionar expressões de exemplo para a intenção None 

[!INCLUDE [Follow these steps to add the None intent to the app](../../../includes/cognitive-services-luis-create-the-none-intent.md)]

## <a name="train-the-app-before-testing-or-publishing"></a>Preparar a aplicação antes de teste ou de publicação

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-to-query-from-the-endpoint"></a>Publicar a aplicação para consultar a partir do ponto final

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Obter previsão intenções e entidades do ponto final

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Vá para o final do URL no endereço e introduza `When were HRF-123456 and hrf-234567 published in the last year?`. O último parâmetro querystring é `q`, a expressão **query**. Esta expressão não é igual a qualquer uma das expressões etiquetadas, pelo que é um bom teste e deve devolver a intenção `FindForm` com os dois números de formulário de `HRF-123456` e `hrf-234567`.

    ```json
    {
      "query": "When were HRF-123456 and hrf-234567 published in the last year?",
      "topScoringIntent": {
        "intent": "FindForm",
        "score": 0.9988884
      },
      "intents": [
        {
          "intent": "FindForm",
          "score": 0.9988884
        },
        {
          "intent": "None",
          "score": 0.00204812363
        }
      ],
      "entities": [
        {
          "entity": "hrf-123456",
          "type": "HRF-number",
          "startIndex": 10,
          "endIndex": 19
        },
        {
          "entity": "hrf-234567",
          "type": "HRF-number",
          "startIndex": 25,
          "endIndex": 34
        }
      ]
    }
    ```

    Ao utilizar uma entidade de expressão regular, o LUIS extrai dados com nome, o que é mais útil programaticamente para a aplicação cliente receber a resposta JSON.


## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>Informações relacionadas

* [Expressão regular](luis-concept-entity-types.md#regular-expression-entity) conceitos de entidade
* [Como dar formação](luis-how-to-train.md)
* [Como publicar](luis-how-to-publish-app.md)
* [Como testar no portal do LUIS](luis-interactive-test.md)

## <a name="next-steps"></a>Passos Seguintes
Este tutorial criou uma nova intenção, adicionou expressões de exemplo e criou uma entidade de expressão regular para extrair dados bem formatados das expressões. Depois de preparar e publicar a aplicação, uma consulta ao ponto final identifica a intenção e devolve os dados extraídos.

> [!div class="nextstepaction"]
> [Saiba mais sobre a entidade de lista](luis-quickstart-intent-and-list-entity.md)

