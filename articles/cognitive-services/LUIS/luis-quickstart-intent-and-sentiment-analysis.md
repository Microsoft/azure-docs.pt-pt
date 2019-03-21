---
title: Análise de sentimentos
titleSuffix: Azure Cognitive Services
description: Neste tutorial, crie uma aplicação que demonstra como obter o sentimento positivo, negativo e neutro de expressões. O sentimento é determinado a partir da expressão inteira.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: diberry
ms.openlocfilehash: 0e61f6a914c33842f4f42b2e1e4206b370a11dd4
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58099040"
---
# <a name="tutorial--get-sentiment-of-utterance"></a>Tutorial:  Obter o sentimento de expressão

Neste tutorial, crie uma aplicação que demonstra como determinar o sentimento positivo, negativo e neutro de expressões. O sentimento é determinado a partir da expressão inteira.

**Neste tutorial, vai aprender a:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Criar uma nova aplicação
> * Adicionar a análise de sentimentos como definição de publicação
> * Preparar a aplicação
> * Publicar aplicação
> * Obter o sentimento da expressão a partir do ponto final

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="sentiment-analysis-is-a-publish-setting"></a>Análise de sentimentos é uma configuração de publicação

As expressões seguintes mostram exemplos de sentimentos:

|Sentimento|Classificação|Expressão|
|:--|:--|:--|
|positiva|0,91 |John W. Smith fez um excelente trabalho na apresentação em Paris.|
|positiva|0,84 |Os engenheiros de Seattle fizeram o argumento de vendas Parker fabuloso de trabalho.|

A análise de sentimentos é uma definição de publicação que se aplica a cada expressão. Não é necessário que localizar as palavras que indica o sentimento na expressão e marcá-los. 

Como é uma definição de publicação, não o vê nas páginas de intenções ou entidades. Pode vê-lo no painel [teste interativo](luis-interactive-test.md#view-sentiment-results) ou quando testar o URL de ponto final. 


## <a name="create-a-new-app"></a>Criar uma nova aplicação

[!INCLUDE [Follow these steps to create a new LUIS app](../../../includes/cognitive-services-luis-create-new-app-steps.md)]

## <a name="add-personname-prebuilt-entity"></a>Adicionar PersonName pré-criados de entidade 


1. Selecione **Entidades** no menu de navegação esquerdo.

1. Selecione o botão **Adicionar entidade pré-compilada**.

1. Selecione a entidade seguinte da lista de entidades previamente concebidas, em seguida, selecione **feito**:

   * **[PersonName](luis-reference-prebuilt-person.md)** 

     ![Captura de ecrã do número selecionado na caixa de diálogo de entidades pré-concebidas](./media/luis-quickstart-intent-and-sentiment-analysis/add-personname-prebuilt-entity.png)

## <a name="create-an-intent-to-determine-employee-feedback"></a>Criar uma intenção para determinar os comentários de funcionário

Adicione uma nova intenção para capturar os comentários dos colaboradores membros da empresa. 

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. Selecione **Create new intent** (Criar nova intenção).

3. Nomeie a nova intenção `EmployeeFeedback`.

    ![Criar nova caixa de diálogo de intenção com EmployeeFeedback como nome](./media/luis-quickstart-intent-and-sentiment-analysis/hr-create-new-intent-ddl.png)

4. Adicione várias expressões que indiquem que um colaborador está a fazer algo bem ou uma área que precisa de ser melhorada:

    |Expressões|
    |--|
    |João Silva fez um ótimo trabalho de cópia fechado de um colega de trabalho de deixe maternity|
    |Jill Jones fez um grande trabalho comforting um colega de trabalho em seu tempo muita dor de cabeça.|
    |BOB Barnes não tinha a todas as notas fiscais necessárias para a papelada.|
    |Todd Thomas ativados dos formulários, um mês tardia com nenhuma assinaturas|
    |Katherine Kelly não torná-lo para a reunião de fora do local de marketing importante.|
    |Denise Dillard omitido da reunião para análises de Junho.|
    |Mark Mathews rocked o argumento de vendas na Harvard|
    |Walter Williams fez um grande trabalho na apresentação em Stanford|

    [![Aplicação de captura de ecrã do LUIS com expressões de exemplo em intenção, EmployeeFeedback](./media/luis-quickstart-intent-and-sentiment-analysis/hr-utterance-examples.png)](./media/luis-quickstart-intent-and-sentiment-analysis/hr-utterance-examples.png#lightbox)

## <a name="add-example-utterances-to-the-none-intent"></a>Adicionar expressões de exemplo para a intenção None 

[!INCLUDE [Follow these steps to add the None intent to the app](../../../includes/cognitive-services-luis-create-the-none-intent.md)]

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>Preparar a aplicação para que as alterações à intenção podem ser testadas 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="configure-app-to-include-sentiment-analysis"></a>Configurar a aplicação para incluir análise de sentimentos

1. Selecione **Gerir** no painel de navegação superior direito e, em seguida, selecione **Definições de publicação** no menu à esquerda.

1. Selecione **a análise de sentimentos** para ativar esta definição. 

    ![Ativar análise de sentimentos como a configuração de publicação](./media/luis-quickstart-intent-and-sentiment-analysis/turn-on-sentiment-analysis-as-publish-setting.png)

## <a name="publish-the-app-so-the-trained-model-is-queryable-from-the-endpoint"></a>Publicar a aplicação, para que o modelo preparado é consultável do ponto final

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-the-sentiment-of-an-utterance-from-the-endpoint"></a>Obter o sentimento de uma expressão a partir do ponto final

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

1. Vá para o final do URL no endereço e introduza `Jill Jones work with the media team on the public portal was amazing`. O último parâmetro querystring é `q`, a expressão **query**. Esta expressão não é igual a qualquer uma das expressões etiquetadas, pelo que é um bom teste e deve devolver a intenção `EmployeeFeedback` com a análise de sentimentos extraída.
    
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

    O sentimentAnalysis é positivo com uma classificação de 86%. 

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>Informações relacionadas

* Análise de sentimentos é fornecida pelo serviço cognitivo [análise de texto](../Text-Analytics/index.yml). O recurso está limitado a análise de texto [idiomas suportados](luis-language-support.md##languages-supported).
* [Como dar formação](luis-how-to-train.md)
* [Como publicar](luis-how-to-publish-app.md)
* [Como testar no portal do LUIS](luis-interactive-test.md)


## <a name="next-steps"></a>Passos Seguintes
Neste tutorial, adicione a análise de sentimentos como uma definição de publicação para extrair valores de sentimentos da expressão como um todo.

> [!div class="nextstepaction"] 
> [Rever expressões de ponto final na aplicação de RH](luis-tutorial-review-endpoint-utterances.md) 

