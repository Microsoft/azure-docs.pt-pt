---
title: Adicionar entidades - LUIS
titleSuffix: Azure Cognitive Services
description: Criar entidades para extrair dados-chave de declarações de utilizadores em aplicações de Compreensão de Idiomas (LUIS). Os dados da entidade extraída são utilizados pela aplicação do cliente para pedidos de clientes fullfil.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/15/2019
ms.author: diberry
ms.openlocfilehash: 1f2b293acdc77e25e6b932c47d466cc28a04a2b6
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79220910"
---
# <a name="add-entities-to-extract-data"></a>Adicionar entidades para extrair dados 

Criar entidades para extrair dados-chave de declarações de utilizadores em aplicações de Compreensão de Idiomas (LUIS). Os dados da entidade extraída são utilizados pela sua aplicação de cliente para pedidos de clientes fullfil.

A entidade representa uma palavra ou frase dentro da expressão que pretende que sejam extraídos. Entidades descrevem informações relevantes para a intenção e, às vezes, são essenciais para a sua aplicação realizar suas tarefas. Pode criar entidades quando adiciona um exemplo de expressão a uma intenção ou para além de (antes ou depois) adicionar um exemplo de expressão a uma intenção.

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="plan-entities-then-create-and-label"></a>Planear entidades, em seguida, criar e rotular

As entidades aprendidas com máquinas podem ser criadas a partir das declarações de exemplo ou criadas a partir da página **Entidades.** 

Em geral, a melhor prática é passar o tempo a planear as entidades antes de criar uma entidade aprendida em máquinas no portal. Em seguida, crie a entidade aprendida pela máquina a partir da expressão do exemplo com tanto detalhe nos subcomponentes e descritores e constrangimentos como sabe na época. O [tutorial de entidade descomponsável](tutorial-machine-learned-entity.md) demonstra como usar este método. 

Como parte do planeamento das entidades, pode saber que precisa de entidades que correspondam a texto (como entidades pré-construídas, entidades de expressão regular ou entidades de lista). Pode criá-las a partir da página **Entidades** antes de serem rotuladas como pronunciações. 

Ao rotular, pode rotular entidades individuais e depois construir até uma entidade-mãe aprendida com máquinas. Ou pode começar com uma entidade mãe aprendida com máquinas e decompor-se em entidades infantis. 

> [!TIP] 
>Rotular todas as palavras que possam indicar uma entidade, mesmo que as palavras não sejam utilizadas quando extraídas na aplicação do cliente. 

## <a name="creating-an-entity-before-or-with-labeling"></a>Criação de uma entidade antes ou com rotulagem

Utilize a tabela seguinte para entender quais as entidades onde criar ou adicionar cada entidade à app. 

|Tipo de entidade|Onde criar entidade no portal LUIS|
|--|--|
|Entidade de aprendizagem automática|Entidades ou detalhes de intenções|
|Entidade de lista|Entidades ou detalhes de intenções|
|Entidade de expressão regular|Entidades|
|Entidade Pattern.any|Entidades|
|Entidade pré-criados|Entidades|
|Entidade de domínio pré-construído|Entidades|

Pode criar todas as entidades a partir da página **Entidades,** ou pode criar algumas entidades como parte da rotulagem da entidade na página de detalhes de **Intenção.** Só é possível _rotular_ uma entidade num exemplo da página de **detalhes intenta.** 

## <a name="create-a-machine-learned-entity"></a>Criar uma entidade aprendida por máquinas

[!INCLUDE [Create and label entities in machine-learned tutorial](includes/decomposable-tutorial-links.md)]

## <a name="create-a-text-matching-entity"></a>Criar uma entidade que colhe texto

Utilize entidades que correspondam a textos fornecem várias formas de extrair dados:

|Entidades que correspondem a texto|Objetivo|
|--|--|
|[Entidade da lista](#add-list-entities-for-exact-matches)|lista de nomes canónicos, juntamente com sinónimos como formas alternativas|
|Entidade de expressão regular|texto de jogo usando uma entidade de expressão regular|
|[Entidade pré-construída](tutorial-machine-learned-entity.md#add-prebuilt-number-to-help-extract-data)|combinar tipos de dados comuns, tais como número, e-mail, data|
|Entidade de domínio pré-construído|combinar usando domínios de assunto selecionados|
|[Padrão.qualquer](#add-a-patternany-entity)| para combinar entidades que podem ser facilmente confundidas com o texto circundante|  

As entidades pré-construídas trabalham sem fornecer quaisquer dados de formação personalizados. As outras entidades precisam que forneça dados de formação de clientes (como itens da entidade list) ou uma expressão (como expressão regular ou padrão.qualquer).

<a name="add-list-entities"></a>

### <a name="how-to-create-a-new-custom-entity"></a>Como criar uma nova entidade personalizada

1. No portal LUIS, vá à secção **Gerir,** depois à página **Entidades.** 
1. Selecione **+ Criar**e, em seguida, selecione o tipo de entidade. 
1. Continue a configurar a entidade e selecione **Criar** quando terminar. 

### <a name="add-list-entities-for-exact-matches"></a>Adicionar entidades da lista para jogos exatos

Lista de entidades representam um conjunto de palavras relacionadas fixo e fechado. Enquanto você, como autor, pode mudar a lista, LUIS não vai crescer ou encolher a lista. Também pode importar para uma entidade de lista existente utilizando um formato [entidade da lista .json (reference-entity-list.md#example-json-to-import-into-list-entity). 

A lista que se segue demonstra o nome canónico e os sinónimos. 

|Cor - nome do item da lista|Cor - sinónimos|
|--|--|
|Vermelho|carmesim, sangue, maçã, fogo-de-incêndio|
|Azul|céu, azul, cobalto|
|Verde|kelly, lima|

Utilize o procedimento para criar uma entidade de lista. Uma vez criada a entidade da lista, não é necessário rotular as palavras de exemplo numa intenção. Os itens de lista e os sinónimos são combinados usando texto exato. 

1. A partir da secção **Construir,** selecione **Entidades** no painel esquerdo e, em seguida, selecione **+ Criar**.

1. Na caixa de diálogo **criar uma entidade,** introduza o nome da entidade, como `Colors` e selecione **Lista**.
1. Na caixa de diálogo criar **uma entidade de lista,** na **nova sublista Adicionar....** introduza o nome do item da lista, como `Green`, em seguida, adicione sinónimos.

    > [!div class="mx-imgBorder"]
    > ![Criar uma lista de cores como entidade de lista na página de detalhes da Entidade.](media/how-to-add-entities/create-list-entity-of-colors.png) 

1. Quando terminar de adicionar itens de lista e sinónimos, selecione **Criar**.

    Quando terminar com um grupo de alterações na aplicação, lembre-se de **treinar** a app. Não treine a aplicação depois de uma única alteração. 

    > [!NOTE]
    > Este procedimento demonstra a criação e rotulagem de uma entidade de lista a partir de um exemplo na página **de detalhes intent.** Pode ainda criar a mesma entidade a partir da página **Entidades.**

## <a name="add-a-role-for-an-entity"></a>Adicione um papel para uma entidade

Um papel é um subtipo nomeado de uma entidade, baseada no contexto. 

### <a name="add-a-role-to-distinguish-different-contexts"></a>Adicione um papel para distinguir diferentes contextos

Na seguinte expressão, existem dois locais, e cada um é especificado semânticamente pelas palavras à sua volta, tais como `to` e `from`: 

`Pick up the package from Seattle and deliver to New York City.`

Neste procedimento, adicione `origin` e `destination` funções a uma entidade de geografia pré-construídaV2.

1. A partir da secção **Build,** selecione **Entidades** no painel esquerdo.

1. Selecione **+ Adicionar entidade pré-construída**. **Selecione geografiaV2** e, em seguida, selecione **Done**. Isto adiciona uma entidade pré-construída à app.
    
    Se chegar à conclusão que o seu padrão, ao incluir uma entidade Pattern.any, extrai as entidades incorretamente, utilize uma [lista explícita](reference-pattern-syntax.md#explicit-lists) para corrigir este problema. 

1. Selecione a entidade de geografia pré-construída recém-construída V2 da lista de entidades da **Página entidades.** 
1. Para adicionar um novo papel, selecione **+** ao lado de **nenhuma função adicionada**.
1. Na **função Tipo...** caixa de texto, introduza o nome do papel `Origin` depois entre. Adicione um segundo nome de `Destination`, em seguida, entre. 

    > [!div class="mx-imgBorder"]
    > ![Screenshot de adicionar papel de Origem à entidade de localização](media/how-to-add-entities//add-role-to-prebuilt-geographyv2-entity.png)

    O papel é adicionado à entidade pré-construída, mas não é adicionado a nenhuma expressão usando essa entidade. 

### <a name="label-text-with-a-role-in-an-example-utterance"></a>Rotular texto com um papel na expressão de exemplo

1. Vá à página de detalhes da Intenção, que tem declarações exemplo que usam o papel. 
1. Para rotular com a função, selecione o rótulo da entidade (linha sólida em texto) na expressão do exemplo e, em seguida, selecione **View in entity palette** from the drop-down list. 

    > [!div class="mx-imgBorder"]
    > ![Screenshot de selecionar Vista na entidade Paleta](media/how-to-add-entities/select-text-label-with-entity-palette-for-role.png)   

    A paleta de entidades abre-se para a direita. 

1. Selecione a entidade, depois vá para a parte inferior da paleta e selecione o papel. 

    > [!div class="mx-imgBorder"]
    > ![Screenshot de selecionar Vista na entidade Paleta](media/how-to-add-entities/select-role-from-entity-palette-entity-inspector.png)

<a name="add-pattern-any-entities"></a>

## <a name="add-a-patternany-entity"></a>Adicione um padrão.qualquer entidade

[Padrão.qualquer](luis-concept-entity-types.md) entidade só é válida em [padrões,](luis-how-to-model-intent-pattern.md)não em declarações exemplo de intenções. Este tipo de entidade ajuda LUIS encontrar o fim de entidades de comprimento variável e a escolha do word. Como esta entidade é utilizada num padrão, o LUIS sabe onde o fim da entidade é o modelo de expressão.

### <a name="steps-to-create-a-patternany-entity"></a>Passos para criar um padrão.qualquer entidade

1. A partir da secção **Construir,** selecione **Entidades** no painel esquerdo e, em seguida, selecione **+ Criar**.

1. Na caixa de diálogo **tipo entidade Escolha uma entidade,** introduza o nome da entidade na caixa nome **e** selecione **Pattern.Any** as the **Type** então selecione **Criar**.

    Uma vez [que você cria uma expressão](luis-how-to-model-intent-pattern.md) padrão usando esta entidade, a entidade é extraída com um algoritmo combinado aprendido com máquina e correspondência de texto. 

### <a name="create-a-pattern-template-utterance-to-use-patternany-entity"></a>Criar uma expressão de modelo de padrão para usar o padrão.qualquer entidade

Para utilizar o padrão.qualquer entidade, adicione um padrão na página **Patterns,** na secção de desempenho da **aplicação Improve,** com a sintaxe de cinta encaracolada correta, como `Where is **{HumanResourcesFormTitle}** on the server?`.

Se chegar à conclusão que o seu padrão, ao incluir uma entidade Pattern.any, extrai as entidades incorretamente, utilize uma [lista explícita](reference-pattern-syntax.md#explicit-lists) para corrigir este problema. 

## <a name="do-not-change-entity-type"></a>Não alterar o tipo de entidade

LUIS não permite-lhe alterar o tipo da entidade, porque ele não sabe o que adicionar ou remover para construir essa entidade. Para alterar o tipo, é melhor criar uma nova entidade do tipo correto com um nome de um pouco diferente. Assim que a entidade é criada, em cada ocorrência de pronunciação, remova o nome da entidade com nome antigo e adicionar o novo nome de entidade. Depois de tem sido relabeled todas as expressões, elimine a entidade antiga. 

<a name="create-a-pattern-from-an-utterance"></a>

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"] 
> [Utilizar modelos pré-construídos](howto-add-prebuilt-models.md) 

Saiba mais sobre:
* Como [treinar](luis-how-to-train.md)
* Como [testar](luis-interactive-test.md)
* Como [publicar](luis-how-to-publish-app.md)
* Padrões:
    * [Conceitos](luis-concept-patterns.md)
    * [Sintaxe](reference-pattern-syntax.md)
* [Repositório de entidades pré-construídas GitHub](https://github.com/Microsoft/Recognizers-Text)
* [Conceitos de extração de dados](luis-concept-data-extraction.md)


 
