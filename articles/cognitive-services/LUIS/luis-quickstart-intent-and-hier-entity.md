---
title: Entidade hierárquica
titleSuffix: Azure Cognitive Services
description: Localize fragmentos de dados relacionados com base no contexto. Por exemplo, as localizações de origem e destino para uma mudança física de um edifício e escritório para outros estão relacionadas.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 02/19/2019
ms.author: diberry
ms.openlocfilehash: 14d1b5b7113e8a4bd71aa89a246fa375adb03a14
ms.sourcegitcommit: a8948ddcbaaa22bccbb6f187b20720eba7a17edc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56594003"
---
# <a name="tutorial-extract-contextually-related-data-from-an-utterance"></a>Tutorial: Extrair dados contextualmente relacionados de uma expressão

Neste tutorial, localize fragmentos de dados relacionados com base no contexto. Por exemplo, uma origem e destino locais para uma transferência de uma cidade para outro. Ambas as partes de dados podem ser necessárias e se relacionam entre si.  

**Neste tutorial, vai aprender a:**

> [!div class="checklist"]
> * Criar nova aplicação
> * Adicionar intenções 
> * Adicionar a entidade hierárquica de localização com elementos subordinados de origem e de destino
> * Preparar
> * Publicar
> * Obter as intenções e as entidades do ponto final

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="hierarchical-data"></a>Dados hierárquicos

Esta aplicação determina em que um funcionário deve ser movido da cidade de origem para a cidade de destino. Utiliza a entidade hierárquica para determinar as localizações na expressão. 

A entidade hierárquica é uma boa opção para este tipo de dados, porque os dois conjuntos de dados, localizações de subordinados:

* São entidades simples.
* Estão relacionados entre si no contexto da expressão.
* Utilize a opção de palavra específica para indicar cada entidade. Exemplos destas palavras: de/para, deixar/em direção a, sair de/para.
* Ambas as crianças com frequência estão a ser a mesma expressão. 
* Têm de ser agrupadas e processadas pela aplicação cliente como uma unidade de informações.

## <a name="create-a-new-app"></a>Criar uma nova aplicação

[!INCLUDE [Follow these steps to create a new LUIS app](../../../includes/cognitive-services-luis-create-new-app-steps.md)]

## <a name="create-an-intent-to-move-employees-between-cities"></a>Criar uma intenção de mover os funcionários entre cidades

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

1. Selecione **Create new intent** (Criar nova intenção). 

1. Introduza `MoveEmployeeToCity` na caixa de diálogo de pop-up e, em seguida, selecione **Concluído**. 

    ![Captura de ecrã da caixa de diálogo Criar nova de intenção com](./media/luis-quickstart-intent-and-hier-entity/create-new-intent-move-employee-to-city.png)

1. Adicione expressões de exemplo à intenção.

    |Expressões de exemplo|
    |--|
    |mover a Dallas de caminho de John W. Smith, deixando de Seattle|
    |Transferir Jill Jones de Seattle para Cairo|
    |Jackson de John lugar na direção oposta a Tampa, a chegar ao Atlanta |
    |Mover Debra Doughtery para Tulsa de Dallas|
    |mV Jill Jones deixando Cairo chegar a Tampa|
    |SHIFT Alice Anderson para Oakland de Redmond|
    |Carl Chamerlin de San Francisco até Redmond|
    |Steve Standish de transferência de San Diego no sentido de Bellevue |
    |comparação de precisão Tanner Thompson da cidade de Kansas- and -shift para Chicago|

    [ ![Captura de ecrã do LUIS com as novas expressões na intenção MoveEmployee](./media/luis-quickstart-intent-and-hier-entity/hr-enter-utterances.png)](./media/luis-quickstart-intent-and-hier-entity/hr-enter-utterances.png#lightbox)

## <a name="create-a-location-entity"></a>Criar uma entidade de localização
O LUIS tem de compreender o que é uma localização ao etiquetar a origem e o destino nas expressões. Se precisar de ver a expressão na vista de tokens (não processados), selecione o botão de alternar na barra acima das expressões etiquetadas como **Vista de Entidades**. Depois de alternar o comutador, o controlo está etiquetado como **Vista de Tokens**.

Considere a seguinte expressão:

```json
move John W. Smith leaving Seattle headed to Dallas
```

A expressão tem duas localizações especificadas, `Seattle` e `Dallas`. Ambos são agrupadas como filhos de uma entidade hierárquica, `Location`, uma vez que ambas as partes de dados tem de ser extraídos da expressão para concluir a solicitação no aplicativo cliente e se relacionam entre si. 
 
Se estiver presente apenas um elemento subordinado (origem ou destino) de uma entidade hierárquica, ainda assim é extraído. Não é necessário que todos os elementos subordinados sejam encontrados para apenas um ou alguns serem extraídos. 

1. Na expressão `move John W. Smith leaving Seattle headed to Dallas`, selecione a palavra `Seattle`. É apresentado um menu pendente com uma caixa de texto na parte superior. Introduza o nome da entidade `Location` na caixa de texto e selecione **Create new entity** (Criar nova entidade) no menu pendente. 

    [![Captura de ecrã da criação de nova entidade na página intenção](media/luis-quickstart-intent-and-hier-entity/tutorial-hierarichical-entity-labeling-1.png "captura de ecrã da criação de nova entidade na página de intenção")](media/luis-quickstart-intent-and-hier-entity/tutorial-hierarichical-entity-labeling-1.png#lightbox)

1. Na janela de pop-up, selecione o tipo de entidade **Hierarchical** (Hierárquica) com `Origin` e `Destination` como as entidades subordinadas. Selecione **Done** (Concluído).

    ![Captura de ecrã da caixa de diálogo de pop-up de criação de entidade para a nova entidade de localização](media/luis-quickstart-intent-and-hier-entity/hr-create-new-entity-2.png "captura de ecrã da caixa de diálogo de pop-up de criação de entidade para a nova entidade de localização")

1. O identificador para `Seattle` está assinalado como `Location` porque o LUIS não sabe se o termo corresponde à origem ou ao destino, ou a nenhum dos dois. Selecione `Seattle`, em seguida, selecione **localização**, em seguida, siga o menu à direita e selecione `Origin`.

    [![Captura de ecrã da caixa de diálogo pop-up de etiquetagem de entidade para alterar o filho de entidade de localizações](media/luis-quickstart-intent-and-hier-entity/tutorial-hierarichical-entity-labeling-2.png "captura de ecrã da caixa de diálogo pop-up de etiquetagem de entidade para alterar o filho de entidade de localizações")](media/luis-quickstart-intent-and-hier-entity/tutorial-hierarichical-entity-labeling-2.png#lightbox)

1. As outras localizações em todas as outras expressões da etiqueta. Quando todos os locais são marcados, as expressões começam para se parecer com um padrão. 

    [![Em expressões com o nome de entidade de captura de ecrã de localizações](media/luis-quickstart-intent-and-hier-entity/all-intents-marked-with-origin-and-destination-location.png "em expressões com o nome de entidade de captura de ecrã de localizações")](media/luis-quickstart-intent-and-hier-entity/all-intents-marked-with-origin-and-destination-location.png#lightbox)

    O sublinhado em vermelho indica o que Luis não está confiante sobre a entidade. Treinamento resolve isso. 

## <a name="add-example-utterances-to-the-none-intent"></a>Adicionar expressões de exemplo para a intenção None 

[!INCLUDE [Follow these steps to add the None intent to the app](../../../includes/cognitive-services-luis-create-the-none-intent.md)]

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>Preparar a aplicação para que as alterações à intenção podem ser testadas 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-so-the-trained-model-is-queryable-from-the-endpoint"></a>Publicar a aplicação, para que o modelo preparado é consultável do ponto final

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Obter previsão intenções e entidades do ponto final

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]


1. Vá para o final do URL na barra de endereço e introduza `Please move Carl Chamerlin from Tampa to Portland`. O último parâmetro querystring é `q`, a expressão **query**. Esta expressão não é igual a qualquer uma das expressões identificadas, pelo que é um bom teste e deve devolver a intenção `MoveEmployee` com a entidade hierárquica extraída.

    ```json
    {
      "query": "Please move Carl Chamerlin from Tampa to Portland",
      "topScoringIntent": {
        "intent": "MoveEmployeeToCity",
        "score": 0.979823351
      },
      "intents": [
        {
          "intent": "MoveEmployeeToCity",
          "score": 0.979823351
        },
        {
          "intent": "None",
          "score": 0.0156363435
        }
      ],
      "entities": [
        {
          "entity": "portland",
          "type": "Location::Destination",
          "startIndex": 41,
          "endIndex": 48,
          "score": 0.6044041
        },
        {
          "entity": "tampa",
          "type": "Location::Origin",
          "startIndex": 32,
          "endIndex": 36,
          "score": 0.739491045
        }
      ]
    }
    ```
    
    A intenção correta é prevista e a matriz de entidades tem valores de origem e de destino nas correspondentes **entidades** propriedade.
    
## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>Informações relacionadas

* [Entidades hierárquicas](luis-concept-entity-types.md) informações concetuais
* [Como dar formação](luis-how-to-train.md)
* [Como publicar](luis-how-to-publish-app.md)
* [Como testar no portal do LUIS](luis-interactive-test.md)
* [Funções versus entidades hierárquicas](luis-concept-roles.md#roles-versus-hierarchical-entities)
* [Melhorar as previsões com padrões](luis-concept-patterns.md)

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial criou uma intenção de novo e adicionado expressões de exemplo para os dados contextualmente adquiridos das localizações de origem e de destino. Depois de a aplicação ser preparada e publicada, uma aplicação cliente pode utilizar essas informações para criar um pedido de movimentação com as informações relevantes.

> [!div class="nextstepaction"] 
> [Saiba como adicionar uma entidade composta](luis-tutorial-composite-entity.md) 
