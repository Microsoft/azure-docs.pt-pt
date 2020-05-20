---
title: Adicionar entidades - LUIS
description: Criar entidades para extrair dados-chave de declarações de utilizadores em aplicações de Compreensão de Idiomas (LUIS). Os dados da entidade extraída são utilizados pela aplicação do cliente para pedidos de clientes fullfil.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/17/2020
ms.author: diberry
ms.openlocfilehash: fa7e2321d61721e370ef6b5924dc6f820dd1f724
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83685296"
---
# <a name="add-entities-to-extract-data"></a>Adicionar entidades para extrair dados

Criar entidades para extrair dados-chave de declarações de utilizadores em aplicações de Compreensão de Idiomas (LUIS). Os dados da entidade extraída são utilizados pela sua aplicação de cliente para pedidos de clientes fullfil.

A entidade representa uma palavra ou frase dentro da expressão que quer extraída. As entidades descrevem informações relevantes para a intenção, e por vezes são essenciais para que a sua app execute a sua tarefa. Pode criar entidades quando adiciona um exemplo de expressão a uma intenção ou para além de (antes ou depois) adicionar um exemplo de expressão a uma intenção.

## <a name="plan-entities-then-create-and-label"></a>Planear entidades, em seguida, criar e rotular

As entidades de machine-learning podem ser criadas a partir das declarações de exemplo ou criadas a partir da página **Entidades.**

Em geral, a melhor prática é passar o tempo a planear as entidades antes de criar uma entidade de machine-learning no portal. Em seguida, crie a entidade de aprendizagem automática a partir da expressão do exemplo com tanto detalhe nas subentidades e funcionalidades que conhece na época. O [tutorial de entidade descomponsável](tutorial-machine-learned-entity.md) demonstra como usar este método.

Como parte do planeamento das entidades, pode saber que precisa de entidades que correspondam a texto (como entidades pré-construídas, entidades de expressão regular ou entidades de lista). Pode criá-las a partir da página **Entidades** antes de serem rotuladas como pronunciações.

Ao rotular, pode rotular entidades individuais e depois construir até uma entidade de aprendizagem automática dos pais. Ou pode começar com uma entidade de aprendizagem automática-mãe e decompor-se em entidades infantis.

> [!TIP]
>Rotular todas as palavras que possam indicar uma entidade, mesmo que as palavras não sejam utilizadas quando extraídas na aplicação do cliente.

## <a name="when-to-create-an-entity"></a>Quando criar uma entidade

Depois de planear as suas entidades, deverá criar as suas entidades e subentidades de aprendizagem automática. Isto pode exigir a adição de entidades pré-construídas ou entidades que correspondam a textos para fornecer funcionalidades às suas entidades de aprendizagem automática. Tudo isto deve ser feito antes da rotulagem.

Assim que começar a rotular as palavras de exemplo, pode criar entidades aprendidas com máquinas ou alargar entidades de lista.

Utilize a tabela seguinte para entender onde criar ou adicionar cada tipo de entidade à aplicação.

|Tipo de entidade|Onde criar entidade no portal LUIS|
|--|--|
|entidade de machine-learning|Entidades ou detalhes de intenções|
|Entidade de lista|Entidades ou detalhes de intenções|
|Entidade de expressão regular|Entidades|
|Entidade Pattern.any|Entidades|
|Entidade pré-construída|Entidades|
|Entidade de domínio pré-construído|Entidades|

Pode criar todas as entidades a partir da página **Entidades,** ou pode criar algumas entidades como parte da rotulagem da entidade na página de detalhes de **Intenção.** Só é possível _rotular_ uma entidade num exemplo da página de **detalhes intenta.**



## <a name="how-to-create-a-new-custom-entity"></a>Como criar uma nova entidade personalizada

Este processo funciona para entidades de aprendizagem automática, entidades de lista e entidades de expressão regular.

1. Inscreva-se no [portal LUIS](https://www.luis.ai)e selecione o seu recurso **De Subscrição** e **Autoria** para ver as aplicações atribuídas a esse recurso de autoria.
1. Abra a sua aplicação selecionando o seu nome na página **My Apps.**
1. Selecione a página **Entidades.**
1. Selecione **+ Criar**e, em seguida, selecione o tipo de entidade.
1. Continue a configurar a entidade e selecione **Criar** quando terminar.

## <a name="create-a-machine-learned-entity"></a>Criar uma entidade aprendida por máquinas

1. Inscreva-se no [portal LUIS](https://www.luis.ai)e selecione o seu recurso **De Subscrição** e **Autoria** para ver as aplicações atribuídas a esse recurso de autoria.
1. Abra a sua aplicação selecionando o seu nome na página **My Apps.**
1. A partir da secção **Construir,** selecione **Entidades** no painel esquerdo e, em seguida, selecione **+ Criar**.
1. Na caixa de diálogo **criar um tipo de entidade,** introduza o nome da entidade e selecione Máquina **aprendida**, selecione. Para adicionar subentidades, **selecione Adicionar estrutura**. Selecione **Criar**.

    > [!div class="mx-imgBorder"]
    > ![Screenshot de criar uma entidade aprendida por máquinas.](media/add-entities/machine-learned-entity-with-structure.png)

1. Em **Adicionar subentidades,** adicione uma subentidade selecionando a **+** linha da entidade-mãe.

    > [!div class="mx-imgBorder"]
    > ![Screenshot de adicionar subentidades.](media/add-entities/machine-learned-entity-with-subentities.png)

1. Selecione **Criar** para terminar o processo de criação.

## <a name="add-a-feature-to-a-machine-learned-entity"></a>Adicione uma funcionalidade a uma entidade aprendida por máquinas

1. Inscreva-se no [portal LUIS](https://www.luis.ai)e selecione o seu recurso **De Subscrição** e **Autoria** para ver as aplicações atribuídas a esse recurso de autoria.
1. Abra a sua aplicação selecionando o seu nome na página **My Apps.**
1. A partir da secção **Build,** selecione **Entidades** no painel esquerdo e, em seguida, selecione a entidade aprendida pela máquina.
1. Adicione uma funcionalidade selecionando **+ Adicione a funcionalidade** na linha entidade ou subentidade.
1. Selecione entre as entidades existentes e listas de frases.
1. Se a entidade só for extraída se a funcionalidade for encontrada, selecione o asterisco, `*` para essa função.

    > [!div class="mx-imgBorder"]
    > ![Screenshot de adicionar funcionalidade à entidade.](media/add-entities/machine-learned-entity-schema-with-features.png)

## <a name="create-a-regular-expression-entity"></a>Criar uma entidade de expressão regular

1. Inscreva-se no [portal LUIS](https://www.luis.ai)e selecione o seu recurso **De Subscrição** e **Autoria** para ver as aplicações atribuídas a esse recurso de autoria.
1. Abra a sua aplicação selecionando o seu nome na página **My Apps.**
1. A partir da secção **Construir,** selecione **Entidades** no painel esquerdo e, em seguida, selecione **+ Criar**.

1. Na caixa de diálogo **Criar uma entidade,** introduza o nome da entidade e selecione **RegEx,** introduza a expressão regular no campo **Regex** e selecione **Criar**.

    > [!div class="mx-imgBorder"]
    > ![Screenshot de criar uma entidade de expressão regular.](media/add-entities/add-regular-expression-entity.png)


<a name="add-list-entities"></a>

## <a name="create-a-list-entity"></a>Criar uma entidade de lista

As entidades da lista representam um conjunto fixo e fechado de palavras relacionadas. Enquanto você, como autor, pode mudar a lista, LUIS não vai crescer ou encolher a lista. Também pode importar para uma entidade de lista existente usando um formato de [entidade de lista .json](reference-entity-list.md#example-json-to-import-into-list-entity).

A lista que se segue demonstra o nome canónico e os sinónimos.

|Cor - nome do item da lista|Cor - sinónimos|
|--|--|
|Vermelho|carmesim, sangue, maçã, fogo-de-incêndio|
|Azul|céu, cobalto|
|Verde|kelly, lima|

Utilize o procedimento para criar uma entidade de lista. Uma vez criada a entidade da lista, não é necessário rotular as palavras de exemplo numa intenção. Os itens de lista e os sinónimos são combinados usando texto exato.
1. Inscreva-se no [portal LUIS](https://www.luis.ai)e selecione o seu recurso **De Subscrição** e **Autoria** para ver as aplicações atribuídas a esse recurso de autoria.
1. Abra a sua aplicação selecionando o seu nome na página **My Apps.**
1. A partir da secção **Construir,** selecione **Entidades** no painel esquerdo e, em seguida, selecione **+ Criar**.

1. Na caixa de diálogo **criar uma entidade,** introduza o nome da entidade, por exemplo `Colors` e selecione **Lista**.
1. Na caixa de diálogo criar **uma entidade de lista,** na **nova sublista Adicionar....** introduza o nome do item da lista, `Green` como, em seguida, adicione sinónimos.

    > [!div class="mx-imgBorder"]
    > ![Crie uma lista de cores como entidade de lista na página de detalhes da Entidade.](media/how-to-add-entities/create-list-entity-of-colors.png)

1. Quando terminar de adicionar itens de lista e sinónimos, selecione **Criar**.

    Quando terminar com um grupo de alterações na aplicação, lembre-se de **treinar** a app. Não treine a aplicação depois de uma única alteração.

    > [!NOTE]
    > Este procedimento demonstra a criação e rotulagem de uma entidade de lista a partir de um exemplo na página **de detalhes intent.** Pode ainda criar a mesma entidade a partir da página **Entidades.**

## <a name="add-a-role-for-an-entity"></a>Adicione um papel para uma entidade

Um papel é um subtipo nomeado de uma entidade, baseada no contexto.

### <a name="add-a-role-to-distinguish-different-contexts"></a>Adicione um papel para distinguir diferentes contextos

Na seguinte expressão, existem dois locais, e cada um é especificado semânticamente pelas palavras à sua volta tais `to` como: `from`

`Pick up the package from Seattle and deliver to New York City.`

Neste procedimento, adicione `origin` e `destination` funções a uma entidade de geografia pré-construídaV2.
1. Inscreva-se no [portal LUIS](https://www.luis.ai)e selecione o seu recurso **De Subscrição** e **Autoria** para ver as aplicações atribuídas a esse recurso de autoria.
1. Abra a sua aplicação selecionando o seu nome na página **My Apps.**
1. A partir da secção **Build,** selecione **Entidades** no painel esquerdo.

1. Selecione **+ Adicionar entidade pré-construída**. **Selecione geografiaV2** e, em seguida, selecione **Done**. Isto adiciona uma entidade pré-construída à app.

    Se chegar à conclusão que o seu padrão, ao incluir uma entidade Pattern.any, extrai as entidades incorretamente, utilize uma [lista explícita](reference-pattern-syntax.md#explicit-lists) para corrigir este problema.

1. Selecione a entidade de geografia pré-construída recém-construída V2 da lista de entidades da **Página entidades.**
1. Para adicionar um novo papel, selecione **+** ao lado de nenhuma **função adicionada**.
1. Na **função Tipo...** caixa de texto, introduza o nome da função `Origin` e depois entre. Adicione um segundo nome de papel de `Destination` então insira.

    > [!div class="mx-imgBorder"]
    > ![Screenshot de adicionar papel de Origem à entidade de localização](media/how-to-add-entities//add-role-to-prebuilt-geographyv2-entity.png)

    O papel é adicionado à entidade pré-construída, mas não é adicionado a nenhuma expressão usando essa entidade.

### <a name="label-text-with-a-role-in-an-example-utterance"></a>Rotular texto com um papel na expressão de exemplo

> [!TIP]
> As funções podem ser substituídas pela rotulagem por subentidades de uma entidade de aprendizagem automática.

1. Inscreva-se no [portal LUIS](https://www.luis.ai)e selecione o seu recurso **De Subscrição** e **Autoria** para ver as aplicações atribuídas a esse recurso de autoria.
1. Abra a sua aplicação selecionando o seu nome na página **My Apps.**
1. Vá à página de detalhes da Intenção, que tem declarações exemplo que usam o papel.
1. Para rotular com a função, selecione o rótulo da entidade (linha sólida em texto) na expressão do exemplo e, em seguida, selecione **View in entity pane** a partir da lista de abandono.

    > [!div class="mx-imgBorder"]
    > ![Screenshot de selecionar Vista na paleta de entidade](media/add-entities/view-in-entity-pane.png)

    A paleta de entidades abre-se para a direita.

1. Selecione a entidade, depois vá para a parte inferior da paleta e selecione o papel.

    > [!div class="mx-imgBorder"]
    > ![Screenshot de selecionar Vista na paleta de entidade](media/add-entities/select-role-in-entity-palette.png)

<a name="add-pattern-any-entities"></a>
<a name="add-a-patternany-entity"></a>
<a name="create-a-pattern-from-an-utterance"></a>

## <a name="create-a-patternany-entity"></a>Criar um padrão.qualquer entidade

O **Padrão.qualquer** entidade só está disponível com [Padrões.](luis-how-to-model-intent-pattern.md)


## <a name="do-not-change-entity-type"></a>Não alterar o tipo de entidade

A LUIS não lhe permite alterar o tipo de entidade porque não sabe o que adicionar ou remover para construir essa entidade. Para alterar o tipo, é melhor criar uma nova entidade do tipo correto com um nome ligeiramente diferente. Uma vez criada a entidade, em cada expressão, remova o nome da antiga entidade rotulada e adicione o nome da nova entidade. Uma vez remarcadas todas as declarações, apague a antiga entidade.


## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Utilizar modelos pré-concebidos](howto-add-prebuilt-models.md)

Saiba mais sobre:
* Como [treinar](luis-how-to-train.md)
* Como [testar](luis-interactive-test.md)
* Como [publicar](luis-how-to-publish-app.md)
* Padrões:
    * [Conceitos](luis-concept-patterns.md)
    * [Sintaxe](reference-pattern-syntax.md)
* [Repositório de entidades pré-construídas GitHub](https://github.com/Microsoft/Recognizers-Text)
* [Conceitos de extração de dados](luis-concept-data-extraction.md)



