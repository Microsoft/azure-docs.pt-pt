---
title: 'Tutorial: análise de sentimentos-LUIS'
titleSuffix: Azure Cognitive Services
description: Neste tutorial, crie um aplicativo que demonstra como obter um sentimentos positivo, negativo e neutro do declarações. O sentimento é determinado a partir da expressão inteira.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: 07afd197e514adb0f2fc65c11e9fec552aa05b99
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73492663"
---
# <a name="tutorial--get-sentiment-of-utterance"></a>Tutorial: obter sentimentos de expressão

Neste tutorial, crie um aplicativo que demonstra como determinar as opiniões positivas, negativas e neutras do declarações. O sentimento é determinado a partir da expressão inteira.

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

**Neste tutorial, ficará a saber como:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Criar uma nova aplicação
> * Adicionar a análise de sentimentos como definição de publicação
> * Preparar a aplicação
> * Publicar aplicação
> * Obter o sentimento da expressão a partir do ponto final

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="sentiment-analysis-is-a-publish-setting"></a>A análise de sentimentos é uma configuração de publicação

As expressões seguintes mostram exemplos de sentimentos:

|Sentimento|Classificação|Expressão|
|:--|:--|:--|
|positiva|0,91 |John W. Smith fez um excelente trabalho na apresentação em Paris.|
|positiva|0,84 |Os engenheiros de Seattle faziam fabulosos a trabalhar com o Parker de vendas.|

A análise de sentimentos é uma definição de publicação que se aplica a cada expressão. Você não precisa localizar as palavras que indicam sentimentos no expressão e marcá-las. 

Como é uma definição de publicação, não o vê nas páginas de intenções ou entidades. Pode vê-lo no painel [teste interativo](luis-interactive-test.md#view-sentiment-results) ou quando testar o URL de ponto final. 


## <a name="create-a-new-app"></a>Criar uma nova aplicação

[!INCLUDE [Follow these steps to create a new LUIS app](../../../includes/cognitive-services-luis-create-new-app-steps.md)]

## <a name="add-personname-prebuilt-entity"></a>Adicionar entidade predefinida PersonName 

1. Selecione **Compilar** no menu de navegação.

1. Selecione **Entidades** no menu de navegação esquerdo.

1. Selecione o botão **Adicionar entidade pré-compilada**.

1. Selecione a seguinte entidade na lista de entidades predefinidas e selecione **concluído**:

   * **[PersonName](luis-reference-prebuilt-person.md)** 

     ![Captura de ecrã do número selecionado na caixa de diálogo de entidades pré-concebidas](./media/luis-quickstart-intent-and-sentiment-analysis/add-personname-prebuilt-entity.png)

## <a name="create-an-intent-to-determine-employee-feedback"></a>Criar uma intenção para determinar os comentários dos funcionários

Adicione uma nova intenção para capturar os comentários dos colaboradores membros da empresa. 

1. Selecione **Intents** (Intenções) no painel esquerdo.

1. Selecione **Criar nova intenção**.

1. Nomeie a nova intenção `EmployeeFeedback`.

    ![Criar nova caixa de diálogo de intenção com EmployeeFeedback como nome](./media/luis-quickstart-intent-and-sentiment-analysis/hr-create-new-intent-ddl.png)

4. Adicione várias expressões que indiquem que um colaborador está a fazer algo bem ou uma área que precisa de ser melhorada:

    |Expressões|
    |--|
    |John Smith fez um bom trabalho de fazer boas-vindas de um colega de trabalho da Maternity Leave|
    |Jill Jones fez um ótimo trabalho de conforto de um colega de trabalho em seu tempo de desgosto.|
    |Bob Barnes não tinha todas as faturas necessárias para a papelada.|
    |Todd Thomas ativou os formulários necessários um mês tarde sem assinaturas|
    |Katherine Kelly não fez isso na reunião importante de marketing fora do local.|
    |Denise Dillard perdeu a reunião de análises de junho.|
    |Mark Mathews rocku o tom de vendas na Harvard|
    |Walter lindo fez um ótimo trabalho na apresentação da Stanford|

    Selecione as **Opções de exibição**, selecione **Mostrar valores de entidade** para ver os nomes.

    [![captura de tela do aplicativo LUIS com o declarações de exemplo na intenção de EmployeeFeedback](./media/luis-quickstart-intent-and-sentiment-analysis/hr-utterance-examples.png)](./media/luis-quickstart-intent-and-sentiment-analysis/hr-utterance-examples.png#lightbox)

## <a name="add-example-utterances-to-the-none-intent"></a>Adicionar declarações de exemplo à intenção None 

[!INCLUDE [Follow these steps to add the None intent to the app](../../../includes/cognitive-services-luis-create-the-none-intent.md)]

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>Treinar o aplicativo para que as alterações na intenção possam ser testadas 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="configure-app-to-include-sentiment-analysis"></a>Configurar a aplicação para incluir análise de sentimentos

1. Selecione **Gerir** no painel de navegação superior direito e, em seguida, selecione **Definições de publicação** no menu à esquerda.

1. Selecione **usar análise de sentimentos para determinar se o expressão de um usuário é positivo, negativo ou neutro.** para habilitar essa configuração. 

    ![Ativar Análise de Sentimento como configuração de publicação](./media/luis-quickstart-intent-and-sentiment-analysis/turn-on-sentiment-analysis-as-publish-setting.png)

## <a name="publish-the-app-so-the-trained-model-is-queryable-from-the-endpoint"></a>Publicar o aplicativo para que o modelo treinado seja passível de consulta do ponto de extremidade

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-the-sentiment-of-an-utterance-from-the-endpoint"></a>Obter a opinião de um expressão do ponto de extremidade

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

1. Vá para o final da URL no endereço e insira o seguinte expressão:

    `Jill Jones work with the media team on the public portal was amazing` 

    O último parâmetro querystring é `q`, a expressão **query**. Esta expressão não é igual a qualquer uma das expressões etiquetadas, pelo que é um bom teste e deve devolver a intenção `EmployeeFeedback` com a análise de sentimentos extraída.
    
    ```json
    {
      "query": "Jill Jones work with the media team on the public portal was amazing",
      "topScoringIntent": {
        "intent": "EmployeeFeedback",
        "score": 0.9616192
      },
      "intents": [
        {
          "intent": "EmployeeFeedback",
          "score": 0.9616192
        },
        {
          "intent": "None",
          "score": 0.09347677
        }
      ],
      "entities": [
        {
          "entity": "jill jones",
          "type": "builtin.personName",
          "startIndex": 0,
          "endIndex": 9
        }
      ],
      "sentimentAnalysis": {
        "label": "positive",
        "score": 0.8694164
      }
    }
    ```

    O sentimentAnalysis é positivo com uma pontuação de 86%. 

    Tente outro expressão removendo o valor de `q` na barra de endereços do navegador: `William Jones did a terrible job presenting his ideas.` a pontuação de sentimentos indica um incômodo negativo retornando uma pontuação baixa `0.18597582`.

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>Informações relacionadas

* A análise de sentimentos é fornecida pelo [análise de texto](../Text-Analytics/index.yml)de serviço cognitiva. O recurso é restrito a Análise de Texto [idiomas com suporte](luis-language-support.md##languages-supported).
* [Como treinar](luis-how-to-train.md)
* [Como publicar](luis-how-to-publish-app.md)
* [Como testar no portal do LUIS](luis-interactive-test.md)


## <a name="next-steps"></a>Passos seguintes
Neste tutorial, adicione a análise de sentimentos como uma definição de publicação para extrair valores de sentimentos da expressão como um todo.

> [!div class="nextstepaction"] 
> [Rever expressões de ponto final na aplicação de RH](luis-tutorial-review-endpoint-utterances.md) 

