---
title: Adicionar entidades - LUIS
description: Criar entidades para extrair dados-chave das palavras dos utilizadores nas aplicações de Compreensão de Línguas (LUIS). Os dados de entidades extraídas são utilizados pela aplicação do cliente para pedidos de clientes fullfil.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 05/17/2020
ms.openlocfilehash: c5c6836c2d68036bf2b9c5abe191943537349b8d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91540970"
---
# <a name="add-entities-to-extract-data"></a>Adicionar entidades para extrair dados

Criar entidades para extrair dados-chave das palavras dos utilizadores nas aplicações de Compreensão de Línguas (LUIS). Os dados de entidades extraídas são utilizados pela aplicação do seu cliente para pedidos de clientes preenchidos.

A entidade representa uma palavra ou frase dentro da expressão que deseja extraída. As entidades descrevem informações relevantes para a intenção, e por vezes são essenciais para que a sua app execute a sua tarefa. Pode criar entidades quando adiciona um exemplo de expressão a uma intenção ou à parte de (antes ou depois) adicionando um exemplo de expressão a uma intenção.

## <a name="plan-entities-then-create-and-label"></a>Planear entidades, em seguida, criar e rotular

as entidades de aprendizagem automática podem ser criadas a partir das expressões de exemplo ou criadas a partir da página **Entidades.**

Em geral, uma boa prática é passar o tempo a planear as entidades antes de criar uma entidade de aprendizagem automática no portal. Em seguida, crie a entidade de aprendizagem automática a partir da expressão de exemplo com o máximo de detalhes nas subentências e funcionalidades que conhece na época. O [tutorial de entidade decompor-se](tutorial-machine-learned-entity.md) demonstra como usar este método.

Como parte do planeamento das entidades, poderá saber que precisa de entidades que correspondam a textos (tais como entidades pré-construídas, entidades de expressão regular ou entidades de lista). Pode criá-las a partir da página **Entidades** antes de serem rotuladas em palavras de exemplo.

Ao rotular, pode rotular entidades individuais e depois criar uma entidade de aprendizagem automática dos pais. Ou pode começar com uma entidade de aprendizagem automática dos pais e decompor-se em entidades infantis.

> [!TIP]
>Rotular todas as palavras que possam indicar uma entidade, mesmo que as palavras não sejam utilizadas quando extraídas na aplicação do cliente.

## <a name="when-to-create-an-entity"></a>Quando criar uma entidade

Depois de planear as suas entidades, deverá criar as suas entidades e subentidades de aprendizagem automática. Isto pode exigir a adição de entidades pré-construídas ou entidades que combinam texto para fornecer funcionalidades para as suas entidades de aprendizagem automática. Tudo isto deve ser feito antes da rotulagem.

Assim que começar a rotular palavras de exemplo, pode criar entidades aprendidas com máquinas ou alargar as entidades de lista.

Utilize a seguinte tabela para entender onde criar ou adicionar cada tipo de entidade à aplicação.

|Tipo de entidade|Onde criar entidade no portal LUIS|
|--|--|
|entidade de aprendizagem automática|Entidades ou Detalhes de Intenção|
|Entidade de lista|Entidades ou Detalhes de Intenção|
|Entidade de expressão regular|Entidades|
|Entidade Pattern.any|Entidades|
|Entidade pré-construída|Entidades|
|Entidade de domínio pré-construída|Entidades|

Pode criar todas as entidades a partir da página **Entidades,** ou pode criar algumas das entidades como parte da rotulagem da entidade no exemplo da página **de detalhes.** Só é _possível rotular_ uma entidade num exemplo da página de detalhes da **Intenção.**



## <a name="how-to-create-a-new-custom-entity"></a>Como criar uma nova entidade personalizada

Este processo funciona para entidades de aprendizagem automática, entidades de lista e entidades de expressão regular.

1. Inscreva-se no [portal LUIS](https://www.luis.ai)e selecione o seu recurso **de Subscrição** e **Autoria** para ver as aplicações atribuídas a esse recurso de autoria.
1. Abra a sua aplicação selecionando o seu nome na página **My Apps.**
1. Selecione a página **Entidades.**
1. Selecione **+ Criar**e, em seguida, selecione o tipo de entidade.
1. Continue a configurar a entidade e, em seguida, **selecione Criar** quando terminar.

## <a name="create-a-machine-learned-entity"></a>Criar uma entidade aprendida com máquinas

1. Inscreva-se no [portal LUIS](https://www.luis.ai)e selecione o seu recurso **de Subscrição** e **Autoria** para ver as aplicações atribuídas a esse recurso de autoria.
1. Abra a sua aplicação selecionando o seu nome na página **My Apps.**
1. A partir da secção **Construir,** selecione **Entidades** no painel esquerdo e, em seguida, selecione **+ Criar**.
1. Na caixa de diálogo **tipo entidade,** insira o nome da entidade e selecione **Máquina aprendida,** selecione. Para adicionar subentidades, **selecione Estrutura de adicionar**. Selecione **Criar**.

    > [!div class="mx-imgBorder"]
    > ![Screenshot de criar uma entidade aprendida máquina.](media/add-entities/machine-learned-entity-with-structure.png)

1. In **Add subentities**, adicione uma subinidade selecionando **+** a linha da entidade-mãe na linha.

    > [!div class="mx-imgBorder"]
    > ![Screenshot de adicionar subentidades.](media/add-entities/machine-learned-entity-with-subentities.png)

1. Selecione **Criar** para terminar o processo de criação.

## <a name="add-a-feature-to-a-machine-learned-entity"></a>Adicione uma funcionalidade a uma entidade aprendida com máquinas

1. Inscreva-se no [portal LUIS](https://www.luis.ai)e selecione o seu recurso **de Subscrição** e **Autoria** para ver as aplicações atribuídas a esse recurso de autoria.
1. Abra a sua aplicação selecionando o seu nome na página **My Apps.**
1. A partir da secção **Build,** selecione **Entidades** no painel esquerdo e, em seguida, selecione a entidade aprendida com a máquina.
1. Adicione uma funcionalidade selecionando **+ Adicionar função** na linha de entidade ou sub-entidade.
1. Selecione das entidades existentes e listas de frases.
1. Se a entidade só for extraída se a funcionalidade for encontrada, selecione o asterisco, `*` para essa funcionalidade.

    > [!div class="mx-imgBorder"]
    > ![Screenshot de adicionar funcionalidade à entidade.](media/add-entities/machine-learned-entity-schema-with-features.png)

## <a name="create-a-regular-expression-entity"></a>Criar uma entidade de expressão regular

1. Inscreva-se no [portal LUIS](https://www.luis.ai)e selecione o seu recurso **de Subscrição** e **Autoria** para ver as aplicações atribuídas a esse recurso de autoria.
1. Abra a sua aplicação selecionando o seu nome na página **My Apps.**
1. A partir da secção **Construir,** selecione **Entidades** no painel esquerdo e, em seguida, selecione **+ Criar**.

1. Na caixa de diálogo **tipo entidade,** insira o nome da entidade e selecione **RegEx,** introduza a expressão regular no campo **Regex** e selecione **Criar**.

    > [!div class="mx-imgBorder"]
    > ![Screenshot de criar uma entidade de expressão regular.](media/add-entities/add-regular-expression-entity.png)


<a name="add-list-entities"></a>

## <a name="create-a-list-entity"></a>Criar uma entidade de lista

As entidades da lista representam um conjunto fixo e fechado de palavras relacionadas. Enquanto você, como autor, pode mudar a lista, LUIS não vai crescer ou encolher a lista. Também pode importar para uma entidade de lista existente utilizando uma [entidade de lista .json.](reference-entity-list.md#example-json-to-import-into-list-entity)

A lista que se segue demonstra o nome canónico e os sinónimos.

|Cor - nome do item da lista|Cor - sinónimos|
|--|--|
|Red|carmesim, sangue, maçã, motor de incêndio|
|Blue|céu, cobalto|
|Green|kelly, lima|

Utilize o procedimento para criar uma entidade de lista. Uma vez criada a entidade da lista, não é necessário rotular palavras de exemplo numa intenção. Os itens e sinónimos da lista são combinados com texto exato.
1. Inscreva-se no [portal LUIS](https://www.luis.ai)e selecione o seu recurso **de Subscrição** e **Autoria** para ver as aplicações atribuídas a esse recurso de autoria.
1. Abra a sua aplicação selecionando o seu nome na página **My Apps.**
1. A partir da secção **Construir,** selecione **Entidades** no painel esquerdo e, em seguida, selecione **+ Criar**.

1. Na caixa de diálogo **tipo entidade,** insira o nome da entidade, tal como `Colors` e selecione **Lista.**
1. Na caixa de diálogo de **entidade de lista,** na **nova sublistista Add....**, introduza o nome do item da lista, `Green` como, em seguida, adicione sinónimos.

    > [!div class="mx-imgBorder"]
    > ![Crie uma lista de cores como entidade de lista na página de detalhes da Entidade.](media/how-to-add-entities/create-list-entity-of-colors.png)

1. Quando terminar de adicionar itens de lista e sinónimos, selecione **Criar**.

    Quando terminar com um grupo de alterações à app, lembre-se **de treinar** a aplicação. Não treine a aplicação depois de uma única alteração.

    > [!NOTE]
    > Este procedimento demonstra a criação e rotulagem de uma entidade de lista a partir de uma expressão de exemplo na página de detalhes da **Intenção.** Pode ainda criar a mesma entidade a partir da página **Entidades.**

## <a name="add-a-role-for-an-entity"></a>Adicionar um papel para uma entidade

Um papel é um subtipo nomeado de uma entidade, baseado no contexto.

### <a name="add-a-role-to-distinguish-different-contexts"></a>Adicione um papel para distinguir diferentes contextos

Na seguinte expressão, existem dois locais, e cada um é especificado semântica pelas palavras que o rodeiam, tais `to` `from` como:

`Pick up the package from Seattle and deliver to New York City.`

Neste procedimento, adicione `origin` e `destination` funções a uma entidade de geografia pré-construídaV2.
1. Inscreva-se no [portal LUIS](https://www.luis.ai)e selecione o seu recurso **de Subscrição** e **Autoria** para ver as aplicações atribuídas a esse recurso de autoria.
1. Abra a sua aplicação selecionando o seu nome na página **My Apps.**
1. A partir da secção **Construir,** **selecione Entidades** no painel esquerdo.

1. **Selecione + Adicionar entidade pré-construída.** Selecione **geografiaV2** e, em seguida, selecione **Feito**. Isto adiciona uma entidade pré-construída à aplicação.

    Se chegar à conclusão que o seu padrão, ao incluir uma entidade Pattern.any, extrai as entidades incorretamente, utilize uma [lista explícita](reference-pattern-syntax.md#explicit-lists) para corrigir este problema.

1. Selecione a entidade de geografia novamente adicionadaV2 da lista de **entidades** da página entidades.
1. Para adicionar um novo papel, selecione **+** ao lado de Não **adicionando funções adicionadas**.
1. Na **função Tipo...** caixa de texto, insira o nome da função `Origin` e, em seguida, introduza. Adicione um nome de segunda função `Destination` de então insira.

    > [!div class="mx-imgBorder"]
    > ![Screenshot de adicionar função de Origem à entidade de Localização](media/how-to-add-entities//add-role-to-prebuilt-geographyv2-entity.png)

    O papel é adicionado à entidade pré-construída, mas não é adicionado a quaisquer expressões usando essa entidade.

### <a name="label-text-with-a-role-in-an-example-utterance"></a>Rotular texto com um papel em uma expressão de exemplo

> [!TIP]
> As funções podem ser substituídas pela rotulagem com subentidades de uma entidade de aprendizagem automática.

1. Inscreva-se no [portal LUIS](https://www.luis.ai)e selecione o seu recurso **de Subscrição** e **Autoria** para ver as aplicações atribuídas a esse recurso de autoria.
1. Abra a sua aplicação selecionando o seu nome na página **My Apps.**
1. Aceda à página de detalhes da Intenção, que tem palavras de exemplo que usam o papel.
1. Para rotular com a função, selecione a etiqueta de entidade (linha sólida em texto) na expressão de exemplo e, em seguida, selecione Ver no painel de **entidades** da lista de suspensos.

    > [!div class="mx-imgBorder"]
    > ![A screenshot mostra o artigo do menu do painel de cores da entidade selecionado.](media/add-entities/view-in-entity-pane.png)

    A paleta de entidades abre-se para a direita.

1. Selecione a entidade e, em seguida, vá para a parte inferior da paleta e selecione a função.

    > [!div class="mx-imgBorder"]
    > ![A screenshot mostra onde selecionar o papel.](media/add-entities/select-role-in-entity-palette.png)

<a name="add-pattern-any-entities"></a>
<a name="add-a-patternany-entity"></a>
<a name="create-a-pattern-from-an-utterance"></a>

## <a name="create-a-patternany-entity"></a>Criar um padrão.qualquer entidade

O **Padrão.qualquer** entidade só está disponível com [Padrões](luis-how-to-model-intent-pattern.md).


## <a name="do-not-change-entity-type"></a>Não altere o tipo de entidade

A LUIS não lhe permite alterar o tipo de entidade porque não sabe o que adicionar ou remover para construir essa entidade. Para alterar o tipo, é melhor criar uma nova entidade do tipo correto com um nome ligeiramente diferente. Uma vez criada a entidade, em cada expressão, remova o nome da antiga entidade com rótulo e adicione o novo nome da entidade. Uma vez que todas as declarações tenham sido remarcadas, apague a antiga entidade.


## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Utilizar modelos pré-concebidos](howto-add-prebuilt-models.md)

Saiba mais sobre:
* Como [treinar](luis-how-to-train.md)
* Como [testar](luis-interactive-test.md)
* Como [publicar](luis-how-to-publish-app.md)
* Padrões:
    * [Conceitos](luis-concept-patterns.md)
    * [Syntax](reference-pattern-syntax.md)
* [Entidades pré-construídas GitHub repositório](https://github.com/Microsoft/Recognizers-Text)
* [Conceitos de extração de dados](luis-concept-data-extraction.md)



