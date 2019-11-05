---
title: Adicionar entidades-LUIS
titleSuffix: Azure Cognitive Services
description: Crie entidades para extrair dados de chave de declarações de usuário em aplicativos de Reconhecimento vocal (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: 54c9d79c62052daeee76de5dffb1099dc7d75180
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73467713"
---
# <a name="create-entities-without-utterances"></a>Criar entidades sem declarações

A entidade representa uma palavra ou frase dentro do expressão que você deseja extrair. Uma entidade representa uma classe que inclui uma coleção de objetos semelhantes (locais, coisas, pessoas, eventos ou conceitos). As entidades descrevem as informações relevantes para a intenção e, às vezes, são essenciais para que seu aplicativo execute sua tarefa. Você pode criar entidades ao adicionar um expressão a uma intenção ou à parte (antes ou depois) de adicionar um expressão a uma intenção.

Você pode adicionar, editar ou excluir entidades em seu aplicativo LUIS por meio da **lista entidades** na página **entidades** . O LUIS oferece dois tipos principais de entidades: [entidades predefinidas](luis-reference-prebuilt-entities.md)e suas próprias [entidades personalizadas](luis-concept-entity-types.md#types-of-entities).

Depois que uma entidade aprendida por máquina é criada, você precisa marcar essa entidade em todos os expressão de exemplo de todas as intenções em que ela se encontra.

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

<a name="add-prebuilt-entity"></a>

## <a name="add-a-prebuilt-entity-to-your-app"></a>Adicionar uma entidade predefinida ao seu aplicativo

Entidades predefinidas comuns adicionadas a um aplicativo são *Number* e *datetimeV2*. 

1. Em seu aplicativo, na seção **Compilar** , selecione **entidades** no painel esquerdo.
 
1. Na página **entidades** , selecione **adicionar entidades predefinidas**.

1. Na caixa de diálogo **adicionar entidades predefinidas** , selecione o **número** e **datetimeV2** entidades predefinidas. Em seguida, selecione **Done** (Concluído).

    ![Captura de tela da caixa de diálogo Adicionar entidade predefinida](./media/add-entities/list-of-prebuilt-entities.png)

<a name="add-simple-entities"></a>

## <a name="add-simple-entities-for-single-concepts"></a>Adicionar entidades simples para conceitos únicos

Uma entidade simples descreve um único conceito. Use o procedimento a seguir para criar uma entidade que extrai nomes de departamento da empresa, como *recursos humanos* ou *operações*.   

1. Em seu aplicativo, selecione a seção **Compilar** e, em seguida, selecione **entidades** no painel esquerdo e, em seguida, selecione **criar nova entidade**.

1. Na caixa de diálogo pop-up, digite `Location` na caixa **nome da entidade** , selecione **simples** na lista **tipo de entidade** e, em seguida, selecione **concluído**.

    Depois que essa entidade for criada, vá para todas as tentativas que têm declarações de exemplo que contêm a entidade. Selecione o texto no exemplo expressão e marque o texto como a entidade. 

    Uma [lista de frases](luis-concept-feature.md) é normalmente usada para impulsionar o sinal de uma entidade simples.

<a name="add-regular-expression-entities"></a>

## <a name="add-regular-expression-entities-for-highly-structured-concepts"></a>Adicionar entidades de expressão regular para conceitos altamente estruturados

Uma entidade de expressão regular é usada para extrair dados do expressão com base em uma expressão regular que você fornece. 

1. Em seu aplicativo, selecione **entidades** na navegação à esquerda e, em seguida, selecione **criar nova entidade**.

1. Na caixa de diálogo pop-up, digite `Human resources form name` na caixa **nome da entidade** , selecione **expressão regular** na lista **tipo de entidade** , insira a expressão regular `hrf-[0-9]{6}`e, em seguida, selecione **concluído**. 

    Essa expressão regular corresponde a caracteres literais `hrf-`e, em seguida, seis dígitos para representar um número de formulário para um formulário de recursos humanos.

<a name="add-composite-entities"></a>

## <a name="add-composite-entities-to-group-into-a-parent-child-relationship"></a>Adicionar entidades compostas ao grupo em uma relação pai-filho

Você pode definir relações entre entidades de tipos diferentes criando uma entidade composta. No exemplo a seguir, a entidade contém uma expressão regular e uma entidade de nome predefinida.  

No `Send hrf-123456 to John Smith`expressão, o texto `hrf-123456` é correspondido a uma [expressão regular](#add-regular-expression-entities) de recursos humanos e `John Smith` é extraído com a entidade predefinida. Cada entidade faz parte de uma entidade pai maior. 

1. Em seu aplicativo, selecione **entidades** no painel de navegação esquerdo da seção de **compilação** e, em seguida, selecione **Adicionar entidade predefinida**.

1. Adicione a entidade predefinida **PersonName**. Para obter instruções, consulte [adicionar entidades predefinidas](#add-prebuilt-entity). 

1. Selecione **entidades** na navegação à esquerda e, em seguida, selecione **criar nova entidade**.

1. Na caixa de diálogo pop-up, digite `SendHrForm` na caixa **nome da entidade** e, em seguida, selecione **composto** na lista **tipo de entidade** .

1. Selecione **Adicionar filho** para adicionar um novo filho.

1. Em **#1 filho**, selecione o **número** da entidade na lista.

1. Em **#2 filho**, selecione o nome do formulário da entidade **recursos humanos** na lista. 

1. Selecione **Done** (Concluído).

<a name="add-pattern-any-entities"></a>

## <a name="add-patternany-entities-to-capture-free-form-entities"></a>Adicionar padrão. qualquer entidade para capturar entidades de forma livre

[Padrão. todas as](luis-concept-entity-types.md) entidades são válidas apenas em [padrões](luis-how-to-model-intent-pattern.md), não em intenções. Esse tipo de entidade ajuda a LUIS a localizar o fim das entidades de comprimento variável e de uma opção de palavra. Como essa entidade é usada em um padrão, LUIS sabe onde o fim da entidade está no modelo expressão.

Se um aplicativo tiver uma intenção `FindHumanResourcesForm`, o título do formulário extraído poderá interferir na previsão da intenção. Para esclarecer quais palavras estão no título do formulário, use um padrão. any dentro de um padrão. A previsão LUIS começa com o expressão. Primeiro, o expressão é verificado e correspondido para entidades, quando as entidades são encontradas, o padrão é verificado e correspondido. 

No `Where is Request relocation from employee new to the company on the server?`expressão, o título do formulário é complicado porque não é contextual claramente onde o título termina e onde o restante do expressão começa. Os títulos podem ser qualquer ordem de palavras, incluindo uma única palavra, frases complexas com pontuação e ordem sem detecção de palavras. Um padrão permite que você crie uma entidade na qual a entidade completa e exata pode ser extraída. Depois que o título é encontrado, a intenção de `FindHumanResourcesForm` é prevista porque essa é a intenção do padrão.

1. Na seção **Compilar** , selecione **entidades** no painel esquerdo e, em seguida, selecione **criar nova entidade**.

1. Na caixa de diálogo **Adicionar entidade** , digite `HumanResourcesFormTitle` na caixa **nome da entidade** e selecione **padrão. qualquer** como o **tipo de entidade**.

    Para usar o padrão. qualquer entidade, adicione um padrão na página **padrões** , na seção **melhorar desempenho do aplicativo** , com a sintaxe de chave correta, como `Where is **{HumanResourcesFormTitle}** on the server?`.

    Se chegar à conclusão que o seu padrão, ao incluir uma entidade Pattern.any, extrai as entidades incorretamente, utilize uma [lista explícita](luis-concept-patterns.md#explicit-lists) para corrigir este problema. 

<a name="add-a-role-to-pattern-based-entity"></a>

## <a name="add-a-role-to-distinguish-different-contexts"></a>Adicionar uma função para distinguir contextos diferentes

Uma função é um subtipo nomeado com base no contexto. Ele está disponível em todas as entidades, incluindo entidades predefinidas e não aprendidas por computador. 

A sintaxe para uma função é **`{Entityname:Rolename}`** em que o nome da entidade é seguido por dois-pontos e, em seguida, o nome da função. Por exemplo, `Move {personName} from {Location:Origin} to {Location:Destination}`.

1. Na seção **Compilar** , selecione **entidades** no painel esquerdo.

1. Selecione **Criar nova entidade**. Insira o nome do `Location`. Selecione o tipo **simples** e selecione **concluído**. 

1. Selecione **entidades** no painel esquerdo e, em seguida, selecione o novo **local** de entidade criado na etapa anterior.

1. Na caixa de texto **nome da função** , insira o nome da função `Origin` e digite. Adicione um segundo nome de função de `Destination`. 

    ![Captura de tela da adição da função de origem à entidade de localização](./media/add-entities/roles-enter-role-name-text.png)

<a name="add-list-entities"></a>

## <a name="add-list-entities-for-exact-matches"></a>Adicionar entidades de lista para correspondências exatas

As entidades de lista representam um conjunto fixo e fechado de palavras relacionadas. 

Para um aplicativo de recursos humanos, você pode ter uma lista de todos os departamentos junto com qualquer sinônimo para os departamentos. Você não precisa saber todos os valores ao criar a entidade. Você pode adicionar mais depois de revisar declarações de usuário reais com sinônimos.

1. Na seção **Compilar** , selecione **entidades** no painel esquerdo e, em seguida, selecione **criar nova entidade**.

1. Na caixa de diálogo **Adicionar entidade** , digite `Department` na caixa **nome da entidade** e selecione **lista** como o **tipo de entidade**. Selecione **Done** (Concluído).
  
1. A página de entidade lista permite que você adicione nomes normalizados. Na caixa de texto **valores** , insira um nome de departamento para a lista, como `HumanResources` pressione Enter no teclado. 

1. À direita do valor normalizado, insira sinônimos, pressionando Enter no teclado após cada item.

1. Se você quiser mais itens normalizados para a lista, selecione **recomendado** para ver as opções do [dicionário semântico](luis-glossary.md#semantic-dictionary).

    ![Captura de tela da seleção do recurso recomendado para ver as opções](./media/add-entities/hr-list-2.png)


1. Selecione um item na lista recomendada para adicioná-lo como um valor normalizado ou selecione **Adicionar tudo** para adicionar todos os itens. 
    Você pode importar valores para uma entidade de lista existente usando o seguinte formato JSON:

    ```JSON
    [
        {
            "canonicalForm": "Blue",
            "list": [
                "navy",
                "royal",
                "baby"
            ]
        },
        {
            "canonicalForm": "Green",
            "list": [
                "kelly",
                "forest",
                "avacado"
            ]
        }
    ]  
    ```

<a name="change-entity-type"></a>

## <a name="do-not-change-entity-type"></a>Não alterar tipo de entidade

LUIS não permite que você altere o tipo da entidade porque ela não sabe o que adicionar ou remover para construir essa entidade. Para alterar o tipo, é melhor criar uma nova entidade do tipo correto com um nome ligeiramente diferente. Depois que a entidade for criada, em cada expressão, remova o nome de entidade rotulado antigo e adicione o nome da nova entidade. Depois que todos os declarações tiverem sido rerotulados, exclua a entidade antiga. 

<a name="create-a-pattern-from-an-utterance"></a>

## <a name="create-a-pattern-from-an-example-utterance"></a>Criar um padrão a partir de um exemplo de expressão

Consulte [Adicionar padrão de expressão existentes na página de intenção ou entidade](luis-how-to-model-intent-pattern.md#add-pattern-from-existing-utterance-on-intent-or-entity-page).

## <a name="train-your-app-after-changing-model-with-entities"></a>Treinar seu aplicativo depois de alterar o modelo com entidades

Depois de adicionar, editar ou remover entidades, [treine](luis-how-to-train.md) e [publique](luis-how-to-publish-app.md) seu aplicativo para que suas alterações afetem as consultas de ponto de extremidade. 

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre entidades predefinidas, consulte o projeto de [texto Recognizers](https://github.com/Microsoft/Recognizers-Text) . 

Para obter informações sobre como a entidade aparece na resposta de consulta de ponto de extremidade JSON, consulte [extração de dados](luis-concept-data-extraction.md)

Agora que você adicionou intenções, declarações e entidades, você tem um aplicativo LUIS básico. Saiba como [treinar](luis-how-to-train.md), [testar](luis-interactive-test.md)e [publicar](luis-how-to-publish-app.md) seu aplicativo.
 
