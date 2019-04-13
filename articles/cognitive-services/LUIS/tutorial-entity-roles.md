---
title: Dados contextuais com funções - compreensão de idiomas
titleSuffix: Azure Cognitive Services
description: Localize dados relacionados com base no contexto. Por exemplo, as localizações de origem e destino para uma mudança física de um edifício e escritório para outros estão relacionadas.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 03/08/2019
ms.author: diberry
ms.openlocfilehash: 98c2ad15d836162608affbfbc89908a15e274cee
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/12/2019
ms.locfileid: "59528719"
---
# <a name="tutorial-extract-contextually-related-data-from-an-utterance"></a>Tutorial: Extrair dados contextualmente relacionados de uma expressão

Neste tutorial, localize fragmentos de dados relacionados com base no contexto. Por exemplo, uma origem e destino locais para uma transferência de uma cidade para outro. Ambas as partes de dados podem ser necessárias e se relacionam entre si.  

Este tutorial anteriormente foi escrito usando entidades hierárquicas. Funções de entidade substituem a necessidade do tipo de entidade hierárquica. Uma função pode ser utilizada com qualquer tipo de entidade predefinidos ou personalizados e utilizada em expressões de exemplo e padrões. 

**Neste tutorial, vai aprender a:**

> [!div class="checklist"]
> * Criar nova aplicação
> * Adicionar intenções 
> * Obter as informações de origem e de destino através das funções
> * Preparar
> * Publicar
> * Obter as intenções e funções de entidade do ponto final

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="related-data"></a>Dados relacionados

Esta aplicação determina em que um funcionário deve ser movido da cidade de origem para a cidade de destino. Ele usa um GeographyV2 entidade criados previamente para identificar os nomes de cidade e utiliza funções para determinar os tipos de localização (de origem e de destino) dentro da expressão.

Uma função deve ser utilizado quando os dados da entidade para extrair:

* Está relacionado com entre si no contexto da expressão.
* Utiliza a opção de palavra específica para indicar cada função. Exemplos destas palavras: de/para, deixar/em direção a, sair de/para.
* Ambas as funções com frequência estão a ser a mesma expressão, permitindo que o LUIS aprender com esta utilização contextual frequente.
* Têm de ser agrupadas e processadas pela aplicação cliente como uma unidade de informações.

## <a name="create-a-new-app"></a>Criar uma nova aplicação

[!INCLUDE [Follow these steps to create a new LUIS app](../../../includes/cognitive-services-luis-create-new-app-steps.md)]

## <a name="create-an-intent-to-move-employees-between-cities"></a>Criar uma intenção de mover os funcionários entre cidades

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

1. Selecione **Create new intent** (Criar nova intenção). 

1. Introduza `MoveEmployeeToCity` na caixa de diálogo de pop-up e, em seguida, selecione **Concluído**. 

    ![Captura de ecrã da caixa de diálogo Criar nova de intenção com](./media/tutorial-entity-roles/create-new-intent-move-employee-to-city.png)

1. Adicione expressões de exemplo à intenção.

    |Expressões de exemplo|
    |--|
    |mover a Orlando de caminho de John W. Smith, deixando de Seattle|
    |Transferir Jill Jones de Seattle para Cairo|
    |Jackson de John lugar na direção oposta a Tampa, a chegar ao Atlanta |
    |Mover Debra Doughtery para Tulsa de Chicago|
    |mV Jill Jones deixando Cairo chegar a Tampa|
    |SHIFT Alice Anderson para Oakland de Redmond|
    |Carl Chamerlin de San Francisco até Redmond|
    |Steve Standish de transferência de San Diego no sentido de Bellevue |
    |comparação de precisão Tanner Thompson da cidade de Kansas- and -shift para Chicago|

    [![Captura de ecrã do LUIS com expressões com novo na intenção MoveEmployee](./media/tutorial-entity-roles/hr-enter-utterances.png)](./media/tutorial-entity-roles/hr-enter-utterances.png#lightbox)

## <a name="add-prebuilt-entity-geographyv2"></a>Adicionar entidade pré-criados geographyV2

A entidade pré-criados, geographyV2, extrai informações de localização, incluindo os nomes das cidades. Uma vez que as expressões têm dois nomes de cidade, relacionados entre si no contexto, utilize as funções para extrair nesse contexto.

1. Selecione **entidades** no painel de navegação esquerda.

1. Selecione **adicionar entidade pré-criados**, em seguida, selecione `geo` na barra de pesquisa para filtrar as entidades criados previamente. 

    ![Adicionar geographyV2 pré-criados entidade à aplicação](media/tutorial-entity-roles/add-geographyV2-prebuilt-entity.png)
1. Selecione a caixa de verificação e selecione **feito**.
1. Na **entidades** lista, selecione a **geographyV2** para abrir a nova entidade. 
1. Adicionar duas funções, `Origin`, e `Destination`. 

    ![Adicionar funções a entidade pré-criados](media/tutorial-entity-roles/add-roles-to-prebuilt-entity.png)
1. Selecione **intenções** no painel de navegação esquerda, em seguida, selecione a **MoveEmployeeToCity** intenção. Tenha em atenção os nomes de cidade são rotulados com a entidade pré-criados **geogrpahyV2**.
1. Na expressão primeiro da lista, selecione a localização de origem. É apresentado um menu de lista pendente. Selecione **geographyV2** na lista, em seguida, siga o menu para selecionar **origem**.

    [![Captura de ecrã da cidade como localização de origem a marcação](media/tutorial-entity-roles/tag-origin-city-with-role.png "captura de ecrã da marcação cidade como localização de origem")](media/tutorial-entity-roles/tag-origin-city-with-role.png#lightbox)

1. Utilize o método do passo anterior para marcar todas as funções de localizações em todas as expressões. 

    [![Em expressões com o nome de entidade de captura de ecrã de localizações](media/tutorial-entity-roles/all-locations-marked-with-roles.png "em expressões com o nome de entidade de captura de ecrã de localizações")](media/tutorial-entity-roles/all-locations-marked-with-roles.png#lightbox)

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
          "entity": "geographyV2",
          "role": "Destination",
          "startIndex": 41,
          "endIndex": 48,
          "score": 0.6044041
        },
        {
          "entity": "geographyV2",
          "role": "Origin",
          "startIndex": 32,
          "endIndex": 36,
          "score": 0.739491045
        }
      ]
    }
    ```
    
    A intenção correta é prevista e a matriz de entidades tem funções de origem e de destino nas correspondentes **entidades** propriedade.
    
## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>Informações relacionadas

* [Conceitos de entidades](luis-concept-entity-types.md)
* [Conceitos de funções](luis-concept-roles.md)
* [Lista de entidades pré-concebidas](luis-reference-prebuilt-entities.md)
* [Como dar formação](luis-how-to-train.md)
* [Como publicar](luis-how-to-publish-app.md)
* [Como testar no portal do LUIS](luis-interactive-test.md)
* [Funções](luis-concept-roles.md)

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial criou uma intenção de novo e adicionado expressões de exemplo para os dados contextualmente adquiridos das localizações de origem e de destino. Depois de a aplicação ser preparada e publicada, uma aplicação cliente pode utilizar essas informações para criar um pedido de movimentação com as informações relevantes.

> [!div class="nextstepaction"] 
> [Saiba como adicionar uma entidade composta](luis-tutorial-composite-entity.md) 
