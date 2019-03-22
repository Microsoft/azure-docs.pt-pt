---
title: Expressão de ponto final de revisão
titleSuffix: Azure Cognitive Services
description: Melhore as predições da aplicação ao validar ou corrigir as expressões recebidas através do ponto final de HTTP que o LUIS não pode assegurar. Algumas expressões podem ser validadas para a intenção e outras podem ter de ser validadas para a entidade.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 02/19/2019
ms.author: diberry
ms.openlocfilehash: 118ac858103776e880e7304199279a7d50ad71b1
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58112284"
---
# <a name="tutorial-fix-unsure-predictions-by-reviewing-endpoint-utterances"></a>Tutorial: Corrigir previsões não sabe por rever as expressões de ponto final
Neste tutorial, melhore as predições da aplicação ao validar ou corrigir as expressões recebidas através do ponto final de HTTPS que o LUIS não pode assegurar. Algumas expressões podem ter de ser validadas para a intenção e outras podem ter de ser validadas para a entidade. Deve rever as expressões de ponto final como parte regular de uma manutenção agendada do LUIS. 

Este processo de revisão é outra maneira de o LUIS saber qual é o domínio da aplicação. O LUIS selecionou as expressões apresentadas na lista de revisão. Esta lista é:

* Específica da aplicação.
* Destina-se a melhorar a precisão de predição da aplicação. 
* Deve ser revista periodicamente. 

Ao rever as expressões de ponto final, está a validar ou corrigir a intenção prevista da expressão. Além disso, identifica as entidades personalizadas que não foram previstas ou foram previstas incorretamente. 

**Neste tutorial, vai aprender a:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Aplicação de exemplo de importação
> * Rever pronunciações de ponto final
> * Atualizar a lista de expressões
> * Preparar a aplicação
> * Publicar aplicação
> * Consultar o ponto final da aplicação para ver a resposta JSON de LUIS

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="import-example-app"></a>Aplicação de exemplo de importação

Continue com a aplicação criada no último tutorial, com o nome **RecursosHumanos**. 

Utilize os passos seguintes:

1.  Transfira e guarde o [ficheiro JSON da aplicação](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/custom-domain-sentiment-HumanResources.json).

1. Importe o JSON para uma nova aplicação.

1. Na secção **Gerir**, no separador **Versões**, clone a versão e dê-lhe o nome `review`. A clonagem é uma excelente forma de utilizar várias funcionalidades do LUIS sem afetar a versão original. Como o nome da versão é utilizado como parte da rota de URL, o nome não pode conter carateres que não sejam válidos num URL.

1. Formar e publicar a nova aplicação.

1. Utilize o ponto final para adicionar as seguintes expressões. Pode fazer isso com um [script](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/demo-upload-endpoint-utterances/endpoint.js) ou a partir do ponto final num browser. As expressões a adicionar são:

   [!code-nodejs[Node.js code showing endpoint utterances to add](~/samples-luis/examples/demo-upload-endpoint-utterances/endpoint.js?range=15-26)]

    Se tiver todas as versões da aplicação, através da série de tutoriais, pode ficar surpreendido ao constatar que a lista para **Rever expressões de ponto final** não é alterada, com base na versão. Existe um único conjunto de expressões para rever, independentemente da versão que está ativamente a editar ou da versão da aplicação que foi publicada no ponto final. 

## <a name="review-endpoint-utterances"></a>Rever pronunciações de ponto final

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

1. Selecione **Rever expressões de ponto final** na navegação à esquerda. A lista está filtrada para a intenção **ApplyForJob**. 

    [![Botão de expressões de ponto final de captura de ecrã de revisão na navegação esquerda](./media/luis-tutorial-review-endpoint-utterances/review-endpoint-utterances-with-entity-view.png)](./media/luis-tutorial-review-endpoint-utterances/review-endpoint-utterances-with-entity-view.png#lightbox)

1. Alterne a **Vista de entidades** para ver as entidades identificadas. 
    
    [![Expressões com ponto final de captura de ecrã de revisão com entidades ver o botão de alternar realçado](./media/luis-tutorial-review-endpoint-utterances/review-endpoint-utterances-with-token-view.png)](./media/luis-tutorial-review-endpoint-utterances/review-endpoint-utterances-with-token-view.png#lightbox)

    |Expressão|Intenção correta|Entidades em falta|
    |:--|:--|:--|
    |Estou à procura de uma tarefa com o Processamento de Linguagem Natural|GetJobInfo|Tarefa - "Processo de Linguagem Natural"|

    Esta expressão não está na intenção correta e tem uma classificação inferior a 50%. A intenção **ApplyForJob** tem 21 expressões, em comparação com as sete expressões na **GetJobInformation**. Juntamente com o alinhamento correto das expressões de ponto final, devem ser adicionadas mais expressões à intenção **GetJobInformation**. Fica como um exercício para ser concluído por conta própria. Cada intenção, exceto para a intenção**None** (Nenhuma), deverá ter aproximadamente o mesmo número de expressões de exemplo. A intenção **None** (Nenhuma) deve ter 10% do total de expressões na aplicação. 

1. Para a intenção `I'm looking for a job with Natual Language Processing`, selecione a intenção correta, **GetJobInformation** na coluna **Intenção alinhada**. 

    [![Expressões com ponto final de captura de ecrã de revisão alinhando expressão em intenção](./media/luis-tutorial-review-endpoint-utterances/align-intent-1.png)](./media/luis-tutorial-review-endpoint-utterances/align-intent-1.png#lightbox)

1. Na mesma expressão, a entidade de `Natural Language Processing` é keyPhrase. Deve ser uma entidade **Tarefa** em vez disso. Selecione `Natural Language Processing` e, em seguida, selecione a entidade **Tarefa** na lista.

    [![Expressões com ponto final de captura de ecrã de revisão entidade numa expressão de etiquetagem](./media/luis-tutorial-review-endpoint-utterances/label-entity.png)](./media/luis-tutorial-review-endpoint-utterances/label-entity.png#lightbox)

1. Na mesma linha, selecione a marca de verificação com um círculo à volta na coluna **Adicionar a intenção alinhada**. 

    [![Captura de ecrã de finalização alinhamento de expressão na intenção](./media/luis-tutorial-review-endpoint-utterances/align-utterance.png)](./media/luis-tutorial-review-endpoint-utterances/align-utterance.png#lightbox)

    Esta ação move a expressão de **Rever expressões de ponto final** para a intenção **GetJobInformation**. A expressão de ponto final é agora uma expressão de exemplo dessa intenção. 

1. Reveja as restantes expressões nesta intenção, identificando as expressões e corrigindo a **Intenção alinhada**, se as expressões estiverem incorretas.

1. Quando todas as expressões estiverem corretas, selecione a caixa de verificação em cada linha e, em seguida, selecione **Adicionar seleção** para alinhar as expressões corretamente. 

    [![Captura de ecrã de finalização restantes expressões com alinhados intenção](./media/luis-tutorial-review-endpoint-utterances/finalize-utterance-alignment.png)](./media/luis-tutorial-review-endpoint-utterances/finalize-utterance-alignment.png#lightbox)

1. A lista já não deve ter essas expressões. Se surgirem mais expressões, continue a trabalhar na lista ao corrigir as intenções e ao identificar quaisquer entidades em falta, até que a lista esteja vazia. 

1. Selecione a intenção seguinte na lista Filtro e, em seguida, continue a corrigir as expressões e a identificar as entidades. Lembre-se de que o último passo de cada intenção é selecionar **Adicionar a intenção alinhada** na linha da expressão ou marcar a caixa ao lado de cada intenção e selecionar **Adicionar seleção**, acima da tabela.

    Continue até que todas as intenções e entidades na lista de filtro tenham uma lista vazia. Esta aplicação é muito pequena. O processo de revisão demora apenas alguns minutos. 

## <a name="update-phrase-list"></a>Atualizar a lista de expressões
Mantenha a lista de expressões atualizada com quaisquer nomes de tarefas recentemente detetadas. 

1. Selecione **Listas de expressões** na navegação à esquerda.

2. Selecione a lista de expressões **Tarefas**.

3. Adicione `Natural Language Processing` como um valor e selecione **Guardar**. 

## <a name="train"></a>Preparar

O LUIS desconhece as alterações até estar preparado. 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish"></a>Publicar

Se tiver importado esta aplicação, tem de selecionar a **Análise de sentimentos**.

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entities-from-endpoint"></a>Obter as intenções e as entidades do ponto final

Experimente uma expressão próxima da expressão corrigida. 

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Vá para o final do URL no endereço e introduza `Are there any natural language processing jobs in my department right now?`. O último parâmetro querystring é `q`, a expressão **query**. 

   ```json
   {
    "query": "are there any natural language processing jobs in my department right now?",
    "topScoringIntent": {
      "intent": "GetJobInformation",
      "score": 0.9247605
    },
    "intents": [
      {
        "intent": "GetJobInformation",
        "score": 0.9247605
      },
      {
        "intent": "ApplyForJob",
        "score": 0.129989788
      },
      {
        "intent": "FindForm",
        "score": 0.006438211
      },
      {
        "intent": "EmployeeFeedback",
        "score": 0.00408575451
      },
      {
        "intent": "Utilities.StartOver",
        "score": 0.00194211153
      },
      {
        "intent": "None",
        "score": 0.00166400627
      },
      {
        "intent": "Utilities.Help",
        "score": 0.00118593348
      },
      {
        "intent": "MoveEmployee",
        "score": 0.0007885918
      },
      {
        "intent": "Utilities.Cancel",
        "score": 0.0006373631
      },
      {
        "intent": "Utilities.Stop",
        "score": 0.0005980781
      },
      {
        "intent": "Utilities.Confirm",
        "score": 3.719905E-05
      }
    ],
    "entities": [
      {
        "entity": "right now",
        "type": "builtin.datetimeV2.datetime",
        "startIndex": 64,
        "endIndex": 72,
        "resolution": {
          "values": [
            {
              "timex": "PRESENT_REF",
              "type": "datetime",
              "value": "2018-07-05 15:23:18"
            }
          ]
        }
      },
      {
        "entity": "natural language processing",
        "type": "Job",
        "startIndex": 14,
        "endIndex": 40,
        "score": 0.9869922
      },
      {
        "entity": "natural language processing jobs",
        "type": "builtin.keyPhrase",
        "startIndex": 14,
        "endIndex": 45
      },
      {
        "entity": "department",
        "type": "builtin.keyPhrase",
        "startIndex": 53,
        "endIndex": 62
      }
    ],
    "sentimentAnalysis": {
      "label": "positive",
      "score": 0.8251864
    }
   }
   }
   ```

   A intenção correta foi prevista com uma classificação alta, e a entidade **Tarefa** é detetada como `natural language processing`. 

## <a name="can-reviewing-be-replaced-by-adding-more-utterances"></a>A revisão pode ser substituída ao adicionar mais expressões? 
Pode questionar-se sobre o motivo pelo qual não deve adicionar mais expressões de exemplo. Qual é o objetivo da revisão de expressões de ponto final? Numa aplicação LUIS do mundo real, as expressões de ponto final são provenientes de utilizadores com uma escolha e disposição de palavras que ainda não utilizou. Se tivesse utilizado a mesma escolha e disposição de palavras, a predição original teria uma percentagem mais elevada. 

## <a name="why-is-the-top-intent-on-the-utterance-list"></a>Por que motivo a intenção principal está na lista de expressões? 
Algumas expressões de ponto final terão uma classificação de predição elevada na lista de revisão. Ainda tem de rever e validar essas expressões. Estão na lista porque a intenção mais alta seguinte tinha uma classificação demasiado próxima da classificação da intenção principal. Pretende ter cerca de 15% de diferença entre as duas intenções principais.

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Passos Seguintes
Neste tutorial, reviu as expressões submetidas ao ponto final, que o LUIS não conseguiu assegurar. Depois destas expressões terem sido validadas e movidas para as intenções corretas como expressões de exemplo, o LUIS irá melhorar a exatidão da predição.

> [!div class="nextstepaction"]
> [Saiba como utilizar padrões](luis-tutorial-pattern.md)
