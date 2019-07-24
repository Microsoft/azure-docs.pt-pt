---
title: Dados contextuais com funções-Reconhecimento vocal
titleSuffix: Azure Cognitive Services
description: Localizar dados relacionados com base no contexto. Por exemplo, as localizações de origem e destino para uma mudança física de um edifício e escritório para outros estão relacionadas.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: 061bd94a839d83f75566412ac546ab3208543780
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/24/2019
ms.locfileid: "68467638"
---
# <a name="tutorial-extract-contextually-related-data-from-an-utterance"></a>Tutorial: Extrair dados relacionados contextualmente de um expressão

Neste tutorial, localize fragmentos de dados relacionados com base no contexto. Por exemplo, uma origem e locais de destino para uma transferência de uma cidade para outra. Ambas as partes de dados podem ser necessárias e estão relacionadas entre si.  

Uma função pode ser usada com qualquer tipo de entidade predefinida ou personalizada e usada nos dois exemplos de declarações e padrões. 

**Neste tutorial, vai aprender a:**

> [!div class="checklist"]
> * Criar nova aplicação
> * Adicionar intenções 
> * Obter informações de origem e destino usando funções
> * Preparar
> * Publicar
> * Obter intenções e funções de entidade do ponto de extremidade

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="related-data"></a>Dados relacionados

Este aplicativo determina onde um funcionário deve ser movido da cidade de origem para a cidade de destino. Ele usa uma entidade predefinida GeographyV2 para identificar os nomes de cidades e usa funções para determinar os tipos de local (origem e destino) dentro do expressão.

Uma função deve ser usada quando os dados da entidade devem ser extraídos:

* Está relacionado entre si no contexto do expressão.
* Usa uma opção específica do Word para indicar cada função. Exemplos destas palavras: de/para, deixar/em direção a, sair de/para.
* Ambas as funções estão frequentemente no mesmo expressão, permitindo que o LUIS Aprenda com esse uso contextual frequente.
* Têm de ser agrupadas e processadas pela aplicação cliente como uma unidade de informações.

## <a name="create-a-new-app"></a>Criar uma nova aplicação

[!INCLUDE [Follow these steps to create a new LUIS app](../../../includes/cognitive-services-luis-create-new-app-steps.md)]

## <a name="create-an-intent-to-move-employees-between-cities"></a>Criar uma intenção de mover os funcionários entre as cidades

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

1. Selecione **Create new intent** (Criar nova intenção). 

1. Introduza `MoveEmployeeToCity` na caixa de diálogo de pop-up e, em seguida, selecione **Concluído**. 

    ![Captura de ecrã da caixa de diálogo Criar nova de intenção com](./media/tutorial-entity-roles/create-new-intent-move-employee-to-city.png)

1. Adicione expressões de exemplo à intenção.

    |Expressões de exemplo|
    |--|
    |mover John W. Smith deixando Seattle para Orlando|
    |transferir Jill Jones de Seattle para Cairo|
    |Coloque John Jackson longe de tampa, chegando a Atlanta |
    |mover Débora Doughtery para Tulsa de Chicago|
    |MV Jill Jones saindo de Cairo para tampa|
    |Mude de Alice Anderson para Oakland de Redmond|
    |Carl Chamerlin, da San Francisco a Redmond|
    |Transferir Steve Standish de San Diego para Bellevue |
    |Levante Tanner Thompson da cidade de Kansas e mude para Chicago|

    [![Captura de tela de LUIS com nova declarações no MoveEmployee intenção](./media/tutorial-entity-roles/hr-enter-utterances.png)](./media/tutorial-entity-roles/hr-enter-utterances.png#lightbox)

## <a name="add-prebuilt-entity-geographyv2"></a>Adicionar geographyV2 de entidade predefinida

A entidade predefinida, geographyV2, extrai informações de localização, incluindo nomes de cidades. Como os declarações têm dois nomes de cidades, relacionados entre si no contexto, use funções para extrair esse contexto.

1. Selecione **entidades** na navegação do lado esquerdo.

1. Selecione **Adicionar entidade predefinida**e, em `geo` seguida, selecione na barra de pesquisa para filtrar as entidades predefinidas. 

    ![Adicionar entidade predefinida geographyV2 ao aplicativo](media/tutorial-entity-roles/add-geographyV2-prebuilt-entity.png)
1. Marque a caixa de seleção e selecione **concluído**.
1. Na lista **entidades** , selecione **geographyV2** para abrir a nova entidade. 
1. Adicione duas funções, `Origin`e. `Destination` 

    ![Adicionar funções à entidade predefinida](media/tutorial-entity-roles/add-roles-to-prebuilt-entity.png)
1. Selecione **tentativas** na navegação do lado esquerdo e, em seguida, selecione a intenção **MoveEmployeeToCity** . Observe que os nomes de cidades são rotulados com a entidade predefinida **geographyV2**.
1. Na primeira expressão da lista, selecione o local de origem. Um menu suspenso é exibido. Selecione **geographyV2** na lista e, em seguida, siga o menu para selecionar **origem**.
1. Use o método da etapa anterior para marcar todas as funções de locais em todos os declarações. 


## <a name="add-example-utterances-to-the-none-intent"></a>Adicionar declarações de exemplo à intenção None 

[!INCLUDE [Follow these steps to add the None intent to the app](../../../includes/cognitive-services-luis-create-the-none-intent.md)]

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>Treinar o aplicativo para que as alterações na intenção possam ser testadas 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-so-the-trained-model-is-queryable-from-the-endpoint"></a>Publicar o aplicativo para que o modelo treinado seja passível de consulta do ponto de extremidade

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Obter previsão de intenção e entidade do ponto de extremidade

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]


1. Vá para o final do URL na barra de endereço e introduza `Please move Carl Chamerlin from Tampa to Portland`. O último parâmetro querystring é `q`, a expressão **query**. Esse expressão não é o mesmo que qualquer um dos declarações rotulados, portanto, é um bom teste e deve retornar `MoveEmployee` a intenção com a entidade extraída.

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
    
    A tentativa correta é prevista e a matriz de entidades tem as funções de origem e de destino na propriedade **entidades** correspondentes.
    
## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>Informações relacionadas

* [Conceitos de entidades](luis-concept-entity-types.md)
* [Conceitos de funções](luis-concept-roles.md)
* [Lista de entidades predefinidas](luis-reference-prebuilt-entities.md)
* [Como treinar](luis-how-to-train.md)
* [Como publicar](luis-how-to-publish-app.md)
* [Como testar no portal do LUIS](luis-interactive-test.md)
* [Funções](luis-concept-roles.md)

## <a name="next-steps"></a>Passos Seguintes

Este tutorial criou uma nova tentativa e adicionou declarações de exemplo para os dados de origem e locais de destino contextualmente aprendidos. Depois de a aplicação ser preparada e publicada, uma aplicação cliente pode utilizar essas informações para criar um pedido de movimentação com as informações relevantes.

> [!div class="nextstepaction"] 
> [Saiba como adicionar uma entidade composta](luis-tutorial-composite-entity.md) 
