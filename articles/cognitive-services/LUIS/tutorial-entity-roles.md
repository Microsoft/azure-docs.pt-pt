---
title: 'Tutorial: Dados contextuais com funções - LUIS'
description: Encontre dados relacionados com base no contexto. Por exemplo, as localizações de origem e destino para uma mudança física de um edifício e escritório para outros estão relacionadas.
ms.topic: tutorial
ms.date: 03/30/2020
ms.openlocfilehash: fdb463896e531619ea7ebe7c384729763dc84138
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80475817"
---
# <a name="tutorial-extract-contextually-related-data-from-an-utterance"></a>Tutorial: Extrair dados contexicamente relacionados com uma expressão

Neste tutorial, localize fragmentos de dados relacionados com base no contexto. Por exemplo, locais de origem e destino para um transfer de uma cidade para outra. Ambos os dados podem ser necessários e estão relacionados uns com os outros.

Uma função pode ser usada com qualquer tipo de entidade pré-construída ou personalizada, e usada em ambos os exemplos e padrões.

**Neste tutorial, ficará a saber como:**

> [!div class="checklist"]
> * Criar nova aplicação
> * Adicionar intenções
> * Obtenha informações de origem e destino usando papéis
> * Preparar
> * Publicar
> * Obtenha intenções e funções de entidade a partir do ponto final

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="related-data"></a>Dados relacionados

Esta aplicação determina onde um funcionário deve ser transferido da cidade de origem para a cidade de destino. Utiliza uma entidade pré-construída de GeografiaV2 para identificar os nomes da cidade e utiliza funções para determinar os tipos de localização (origem e destino) dentro da expressão.

Deve ser utilizada uma função quando os dados da entidade para extrair:

* Está relacionado uns com os outros no contexto da expressão.
* Usa escolha de palavra específica para indicar cada papel. Exemplos destas palavras: de/para, deixar/em direção a, sair de/para.
* Ambos os papéis estão frequentemente na mesma expressão, permitindo que luis aprenda com este uso contextual frequente.
* Têm de ser agrupadas e processadas pela aplicação cliente como uma unidade de informações.

## <a name="create-a-new-app"></a>Criar uma nova aplicação

1. Inscreva-se no portal de [ **pré-visualização** ](https://preview.luis.ai)luis.

1. Selecione **+ Nova aplicação para conversação,** introduza o nome `HumanResources` e mantenha a cultura padrão, **inglês**. Deixe a descrição e o recurso de previsão vazios. Selecione **Done** (Concluído).

## <a name="create-an-intent-to-move-employees-between-cities"></a>Criar uma intenção de mover funcionários entre cidades

Uma intenção é usada para classificar as palavras do utilizador com base na intenção do utilizador, determinada a partir do texto de linguagem natural.

Para classificar uma expressão, a intenção necessita de exemplos de declarações de utilizadores que devem ser classificadas com esta intenção.

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

1. Selecione **+ Criar**.

1. Introduza `MoveEmployeeToCity` na caixa de diálogo de pop-up e, em seguida, selecione **Concluído**.

    > [!div class="mx-imgBorder"]
    > ![Captura de ecrã da caixa de diálogo Criar nova de intenção com](./media/tutorial-entity-roles/create-new-intent-move-employee-to-city.png)

1. Adicione várias declarações de exemplo a esta intenção que espera que um utilizador pergunte.

    |Expressões de exemplo|
    |--|
    |mover John W. Smith deixando Seattle rumo a Orlando|
    |transferir Jill Jones de Seattle para o Cairo|
    |Coloque John Jackson longe de Tampa, vindo para Atlanta |
    |mover Debra Doughtery para Tulsa de Chicago|
    |mv Jill Jones deixando Cairo rumo a Tampa|
    |Mude Alice Anderson para Oakland de Redmond|
    |Carl Chamerlin de São Francisco para Redmond|
    |Transferir Steve Standish de San Diego para Bellevue |
    |levantar Tanner Thompson da cidade de Kansas e mudar-se para Chicago|

    > [!div class="mx-imgBorder"]
    > ![Captura de ecrã do LUIS com as novas expressões na intenção MoveEmployee](./media/tutorial-entity-roles/hr-enter-utterances.png)

## <a name="add-prebuilt-entity-geographyv2"></a>Adicionar geografia de entidade pré-construídaV2

A entidade pré-construída, **geografiaV2,** extrai informações de localização, incluindo nomes da cidade. Uma vez que as proclamações têm dois nomes da cidade, relativos uns aos outros em contexto, usam papéis para extrair esse contexto.

1. Selecione **Entidades** da navegação do lado esquerdo.

1. Selecione + Adicione a `geo` **entidade pré-construída**e, em seguida, introduza na barra de pesquisa para filtrar as entidades pré-construídas.

    > [!div class="mx-imgBorder"]
    > ![Adicione geografiaV2 entidade pré-construída à app](media/tutorial-entity-roles/add-geographyV2-prebuilt-entity.png)

1. Selecione a caixa de verificação e selecione **Done**.

## <a name="add-roles-to-prebuilt-entity"></a>Adicionar funções a entidade pré-construída

1. Na lista **de Entidades,** selecione o **geografiaV2** para abrir a nova entidade.
1. Para adicionar um **+** papel, selecione `Origin`e `Destination`adicione as seguintes duas funções: . e .

    > [!div class="mx-imgBorder"]
    > ![Adicionar funções a entidade pré-construída](media/tutorial-entity-roles/add-roles-to-prebuilt-entity.png)

## <a name="label-entity-roles-in-example-utterances"></a>Papel de entidade de etiquetaem em declarações por exemplo

1. Selecione **Intenções** a partir da navegação do lado esquerdo e, em seguida, selecione a intenção **MoveEmployeeToCity.** Note que os nomes da cidade estão rotulados com a entidade pré-construída **geografiaV2**.
1. Na barra de ferramentas de contexto, selecione a **paleta Entidade** com o ícone do _lápis_.

    > [!div class="mx-imgBorder"]
    > ![Selecione Paleta de Entidades a partir da barra de ferramentas de conteúdo](media/tutorial-entity-roles/intent-detail-context-toolbar-select-entity-palette.png)

1. Selecione a entidade pré-construída, **geografiaV2,** em seguida, selecione o **inspetor da Entidade**.
1. No **inspetor da Entidade,** selecione uma função, **Destino**. Isto muda o cursor do rato. Utilize o cursor para rotular o texto em todas as expressões que são a localização do destino.

    > [!div class="mx-imgBorder"]
    > ![Selecione Função na Paleta de Entidades](media/tutorial-entity-roles/entity-palette-select-entity-role.png)


1. Regresso ao **inspetor da Entidade,** alteração ao papel à **Origem.** Utilize o cursor para rotular o texto em todas as frases que são a localização de origem.

## <a name="add-example-utterances-to-the-none-intent"></a>Adicione declarações exemplo à intenção de Nenhum

[!INCLUDE [Follow these steps to add the None intent to the app](../../../includes/cognitive-services-luis-create-the-none-intent.md)]

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>Treine a app para que as alterações à intenção possam ser testadas

Para treinar a aplicação, selecione **Train**. A formação aplica as alterações, como as novas entidades e as expressões rotuladas, ao modelo ativo.

## <a name="publish-the-app-to-access-it-from-the-http-endpoint"></a>Publique a app para aceder a ela a partir do ponto final http

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]


## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Obtenha intenção e previsão de entidade a partir do ponto final

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]


1. Vá até ao fim do URL na _YOUR_QUERY_HERE_ barra `Please move Carl Chamerlin from Tampa to Portland`de endereços e substitua YOUR_QUERY_HERE por .

Esta expressão não é a mesma que qualquer uma das declarações rotuladas, `MoveEmployee` pelo que é um bom teste e deve devolver a intenção com a entidade extraída.

    ```json
    {
      "query": "Please move Carl Chamerlin from Tampa to Portland",
      "topScoringIntent": {
        "intent": "MoveEmployeeToCity",
        "score": 0.9706451
      },
      "intents": [
        {
          "intent": "MoveEmployeeToCity",
          "score": 0.9706451
        },
        {
          "intent": "None",
          "score": 0.0307451729
        }
      ],
      "entities": [
        {
          "entity": "tampa",
          "type": "builtin.geographyV2.city",
          "startIndex": 32,
          "endIndex": 36,
          "role": "Origin"
        },
        {
          "entity": "portland",
          "type": "builtin.geographyV2.city",
          "startIndex": 41,
          "endIndex": 48,
          "role": "Destination"
        }
      ]
    }
    ```

    The correct intent is predicted and the entities array has both the origin and destination roles in the corresponding **entities** property.

[!INCLUDE [LUIS How to clean up resources](includes/quickstart-tutorial-cleanup-resources.md)]

## <a name="related-information"></a>Informações relacionadas

* [Conceitos de entidades](luis-concept-entity-types.md)
* [Conceitos de papéis](luis-concept-roles.md)
* [Lista de entidades pré-construídas](luis-reference-prebuilt-entities.md)
* [Como treinar](luis-how-to-train.md)
* [Como publicar](luis-how-to-publish-app.md)
* [Como testar no portal LUIS](luis-interactive-test.md)
* [Funções](luis-concept-roles.md)

## <a name="next-steps"></a>Passos seguintes

Este tutorial criou uma nova intenção e acrescentou declarações de exemplo para os dados de origem e destino aprendidos contexinalmente. Depois de a aplicação ser preparada e publicada, uma aplicação cliente pode utilizar essas informações para criar um pedido de movimentação com as informações relevantes.

> [!div class="nextstepaction"]
> [Saiba como adicionar uma entidade composta](luis-tutorial-composite-entity.md)
