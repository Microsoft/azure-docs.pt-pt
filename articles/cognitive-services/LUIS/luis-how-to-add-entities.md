---
title: Adicionar entidades-LUIS
titleSuffix: Azure Cognitive Services
description: Crie entidades para extrair dados de chave de declarações de usuário em aplicativos de Reconhecimento vocal (LUIS). Os dados de entidade extraídos são usados pelo aplicativo cliente para fullfil solicitações de clientes.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/11/2019
ms.author: diberry
ms.openlocfilehash: ed100c27d482065e244bb3dc2cca3b66dfc11986
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74013405"
---
# <a name="add-entities-to-extract-data"></a>Adicionar entidades para extrair dados 

Crie entidades para extrair dados de chave de declarações de usuário em aplicativos de Reconhecimento vocal (LUIS). Os dados de entidade extraídos são usados pelo aplicativo cliente para fullfil solicitações de clientes.

A entidade representa uma palavra ou frase dentro da expressão que pretende que sejam extraídos. Entidades descrevem informações relevantes para a intenção e, às vezes, são essenciais para a sua aplicação realizar suas tarefas. Você pode criar entidades ao adicionar um exemplo de expressão a uma intenção ou além de (antes ou depois) de adicionar um expressão de exemplo a uma intenção.

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="creating-an-entity-is-different-from-labeling-an-entity"></a>A criação de uma entidade é diferente de rotular uma entidade

Primeiro, você precisa criar uma entidade antes de poder rotular a entidade no exemplo expressão. 

Use a tabela a seguir para entender quais entidades onde criar ou adicionar cada entidade ao aplicativo. 

|Tipo de entidade|Onde criar a entidade no portal do LUIS|
|--|--|
|Entidade aprendida por máquina|Detalhes de entidades ou intenções|
|Entidade de lista|Detalhes de entidades ou intenções|
|Entidade de expressão regular|Entidades|
|Entidade Pattern.any|Entidades|
|Entidade pré-criados|Entidades|
|Entidade de domínio predefinida|Entidades|

Você pode criar todas as entidades na página **entidades** ou pode criar duas entidades como parte da rotulagem da entidade no exemplo expressão na página de **detalhes da intenção** . Você só pode _rotular_ uma entidade em um exemplo de expressão da página de **detalhes da intenção** . 

## <a name="create-a-machine-learned-entity"></a>Criar uma entidade aprendida por máquina

[!INCLUDE [Create and label entities in machine-learned tutorial](includes/decomposable-tutorial-links.md)]

## <a name="create-a-text-matching-entity"></a>Criar uma entidade de correspondência de texto

O uso de entidades de correspondência de texto fornece várias maneiras de extrair dados:

|Entidades de correspondência de texto|Objetivo|
|--|--|
|[Listar entidade](#add-list-entities-for-exact-matches)|lista de nomes canônicos, juntamente com sinônimos como formulários alternativos|
|Entidade de expressão regular|corresponder texto usando uma entidade de expressão regular|
|[Entidade predefinida](tutorial-machine-learned-entity.md#add-prebuilt-number-to-app-to-help-extract-data)|corresponder tipos de dados comuns, como número, email, data|
|Entidade de domínio predefinida|corresponder usando domínios de assunto selecionados|
|[Pattern.any](#add-a-patternany-entity)| para corresponder as entidades que podem ser facilmente confundidas com o texto ao redor|  

As entidades predefinidas funcionam sem fornecer dados de treinamento personalizados. As outras entidades precisam que você forneça dados de treinamento do cliente (como itens da entidade de lista) ou uma expressão (como uma expressão regular ou padrão. any).

<a name="add-list-entities"></a>

### <a name="how-to-create-a-new-custom-entity"></a>Como criar uma nova entidade personalizada

1. No portal do LUIS, vá para a seção **gerenciar** e, em seguida, a página **entidades** . 
1. Selecione **+ criar**e, em seguida, selecione o tipo de entidade. 
1. Continue Configurando a entidade e, em seguida, selecione **criar** quando terminar. 

### <a name="add-list-entities-for-exact-matches"></a>Adicionar entidades de lista para correspondências exatas

Lista de entidades representam um conjunto de palavras relacionadas fixo e fechado. Embora você, como autor, possa alterar a lista, o LUIS não aumentará nem reduzirá a lista. Você também pode importar para uma entidade de lista existente usando um formato [listar entidade. JSON (referência-entidade-lista. MD # example-JSON para Import-in-List-Entity). 

A lista a seguir demonstra o nome canônico e os sinônimos. 

|Nome do item da lista de cores|Sinônimos de cor|
|--|--|
|Vermelho|Crimson, sangue, Apple, mecanismo de incêndio|
|Azul|céu, Azure, Cobalt|
|Verde|Kelly, verde-limão|

Use o procedimento para criar uma entidade de lista. Depois que a entidade de lista for criada, você não precisará rotular declarações de exemplo em uma intenção. Itens de lista e sinônimos são correspondidos usando texto exato. 

1. Na seção **Compilar** , selecione **entidades** no painel esquerdo e, em seguida, selecione **+ criar**.

1. Na caixa de diálogo **criar um tipo de entidade** , digite o nome da entidade, como `Colors` e selecione **lista**.
1. Na caixa de diálogo **criar uma entidade de lista** , em **Adicionar nova sublista...** , insira o nome do item de lista, como `Green`e, em seguida, adicione sinônimos.

    > [!div class="mx-imgBorder"]
    > ![criar uma lista de cores como uma entidade de lista na página de detalhes da entidade.](media/how-to-add-entities/create-list-entity-of-colors.png) 

1. Quando terminar de adicionar itens de lista e sinônimos, selecione **criar**.

    Quando você terminar com um grupo de alterações no aplicativo, lembre-se de **treinar** o aplicativo. Não treine o aplicativo após uma única alteração. 

    > [!NOTE]
    > Este procedimento demonstra como criar e rotular uma entidade de lista a partir de um exemplo de expressão na página de **detalhes da intenção** . Você também pode criar a mesma entidade na página **entidades** .

## <a name="add-a-role-for-an-entity"></a>Adicionar uma função para uma entidade

Uma função é um subtipo nomeado de uma entidade, com base no contexto. 

### <a name="add-a-role-to-distinguish-different-contexts"></a>Adicionar uma função para distinguir contextos diferentes

No expressão a seguir, há dois locais, e cada um é especificado semanticamente pelas palavras, como `to` e `from`: 

`Pick up the package from Seattle and deliver to New York City.`

Neste procedimento, adicione `origin` e `destination` funções a uma entidade geographyV2 predefinida.

1. Partir do **crie** secção, selecione **entidades** no painel esquerdo.

1. Selecione **+ Adicionar entidade predefinida**. Selecione **geographyV2** e, em seguida, selecione **concluído**. Isso adiciona uma entidade predefinida ao aplicativo.
    
    Se chegar à conclusão que o seu padrão, ao incluir uma entidade Pattern.any, extrai as entidades incorretamente, utilize uma [lista explícita](reference-pattern-syntax.md#explicit-lists) para corrigir este problema. 

1. Selecione a entidade geographyV2 pré-criados recém-adicionada na lista de entidades da página **entidades** . 
1. Para adicionar uma nova função, selecione **+** ao lado de **não há funções adicionadas**.
1. Na caixa de texto **função de tipo...** , insira o nome da função `Origin`, em seguida, insira. Adicione um segundo nome de função de `Destination` em seguida, digite. 

    > [!div class="mx-imgBorder"]
    > ![captura de tela da adição da função de origem à entidade local](media/how-to-add-entities//add-role-to-prebuilt-geographyv2-entity.png)

    A função é adicionada à entidade predefinida, mas não é adicionada a nenhum declarações usando essa entidade. 

### <a name="label-text-with-a-role-in-an-example-utterance"></a>Texto do rótulo com uma função em um exemplo de expressão

1. Vá para a página de detalhes da intenção, que tem exemplo declarações que usam a função. 
1. Para rotular com a função, selecione o rótulo da entidade (linha sólida em texto) no exemplo expressão e, em seguida, selecione **Exibir na paleta de entidades** na lista suspensa. 

    > [!div class="mx-imgBorder"]
    > ![captura de tela da seleção de exibição na paleta de entidades](media/how-to-add-entities/select-text-label-with-entity-palette-for-role.png)   

    A paleta de entidades é aberta à direita. 

1. Selecione a entidade e, em seguida, vá para a parte inferior da paleta e selecione a função. 

    > [!div class="mx-imgBorder"]
    > ![captura de tela da seleção de exibição na paleta de entidades](media/how-to-add-entities/select-role-from-entity-palette-entity-inspector.png)

<a name="add-pattern-any-entities"></a>

## <a name="add-a-patternany-entity"></a>Adicionar um padrão. qualquer entidade

[Padrão. todas as](luis-concept-entity-types.md) entidades são válidas apenas em [padrões](luis-how-to-model-intent-pattern.md), não como declarações de exemplo. Este tipo de entidade ajuda LUIS encontrar o fim de entidades de comprimento variável e a escolha do word. Como esta entidade é utilizada num padrão, o LUIS sabe onde o fim da entidade é o modelo de expressão.

### <a name="steps-to-create-a-patternany-entity"></a>Etapas para criar um padrão. qualquer entidade

1. Na seção **Compilar** , selecione **entidades** no painel esquerdo e, em seguida, selecione **+ criar**.

1. Na caixa de diálogo **escolher um tipo de entidade** , insira o nome da entidade na caixa **nome** e selecione **padrão. qualquer** como o **tipo** e, em seguida, selecione **criar**.

    Depois de [criar um padrão expressão](luis-how-to-model-intent-pattern.md) usando essa entidade, a entidade é extraída com um algoritmo combinado de computador e de correspondência de texto. 

### <a name="create-a-pattern-template-utterance-to-use-patternany-entity"></a>Crie um modelo de padrão expressão para usar o padrão. qualquer entidade

Para utilizar a entidade de pattern.any, adicione um padrão no **padrões** na página a **melhorar o desempenho de aplicações** secção com a sintaxe de chave de abertura correto, tais como `Where is **{HumanResourcesFormTitle}** on the server?`.

Se chegar à conclusão que o seu padrão, ao incluir uma entidade Pattern.any, extrai as entidades incorretamente, utilize uma [lista explícita](reference-pattern-syntax.md#explicit-lists) para corrigir este problema. 

## <a name="do-not-change-entity-type"></a>Não alterar tipo de entidade

LUIS não permite-lhe alterar o tipo da entidade, porque ele não sabe o que adicionar ou remover para construir essa entidade. Para alterar o tipo, é melhor criar uma nova entidade do tipo correto com um nome de um pouco diferente. Assim que a entidade é criada, em cada ocorrência de pronunciação, remova o nome da entidade com nome antigo e adicionar o novo nome de entidade. Depois de tem sido relabeled todas as expressões, elimine a entidade antiga. 

<a name="create-a-pattern-from-an-utterance"></a>

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre padrões:

* [Conceitos de padrões](luis-concept-patterns.md)
* [Sintaxe de padrões](reference-pattern-syntax.md)

Para obter mais informações sobre entidades previamente concebidas, consulte a [reconhecedores texto](https://github.com/Microsoft/Recognizers-Text) projeto. 

Para obter informações sobre a forma como a entidade é apresentado na resposta da consulta de ponto final JSON, veja [extração de dados](luis-concept-data-extraction.md)

Agora que adicionou intenções, expressões e entidades, terá uma aplicação básica do LUIS. Saiba como [treinar](luis-how-to-train.md), [testar](luis-interactive-test.md), e [publicar](luis-how-to-publish-app.md) seu aplicativo.
 
