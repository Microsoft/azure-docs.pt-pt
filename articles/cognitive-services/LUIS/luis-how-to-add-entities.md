---
title: Adicionar entidades
titleSuffix: Language Understanding - Azure Cognitive Services
description: Crie entidades para extrair dados de chave de expressões de utilizador nas aplicações de compreensão de idiomas (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 03/11/2019
ms.author: diberry
ms.openlocfilehash: 784fe19d1ae40a7cdff3cc853726d4c62265e0f1
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58106938"
---
# <a name="create-entities-without-utterances"></a>Criar entidades sem expressões com

A entidade representa uma palavra ou frase dentro da expressão que pretende que sejam extraídos. Uma entidade representa uma classe, incluindo uma coleção de objetos semelhantes (lugares, coisas, pessoas, eventos ou conceitos). Entidades descrevem informações relevantes para a intenção e, às vezes, são essenciais para a sua aplicação realizar suas tarefas. Pode criar entidades quando adicionar uma expressão para uma intenção ou diferença de (antes ou depois) a adição de uma expressão a um objetivo.

Pode adicionar, editar ou eliminar entidades na sua aplicação LUIS através da **lista de entidades** no **entidades** página. LUIS oferece dois tipos principais de entidades: [entidades pré-concebidas](luis-reference-prebuilt-entities.md)e a sua própria [entidades personalizadas](luis-concept-entity-types.md#types-of-entities).

Depois de criar uma entidade aprendidas por máquina, tem de marcar essa entidade em todos os a expressão de exemplo de todas as intenções está a ser.

<a name="add-prebuilt-entity"></a>

## <a name="add-a-prebuilt-entity-to-your-app"></a>Adicionar uma entidade pré-criados à sua aplicação

São entidades pré-concebidas comuns adicionadas a um aplicativo *número* e *datetimeV2*. 

1. Na sua aplicação, do **crie** secção, selecione **entidades** no painel esquerdo.
 
1. Sobre o **entidades** página, selecione **adicionar entidades pré-concebidas**.

1. Na **adicionar entidades pré-concebidas** caixa de diálogo, selecione a **número** e **datetimeV2** entidades pré-concebidas. Em seguida, selecione **Done** (Concluído).

    ![Captura de ecrã de adicionar caixa de diálogo de entidade predefinidos](./media/add-entities/list-of-prebuilt-entities.png)

<a name="add-simple-entities"></a>

## <a name="add-simple-entities-for-single-concepts"></a>Adicionar entidades simples para conceitos únicos

Uma entidade descreve um único conceito. Utilize o procedimento seguinte para criar uma entidade que extrai os nomes de departamento da empresa, tal como *recursos humanos* ou *operações*.   

1. Na sua aplicação, selecione o **crie** secção, em seguida, selecione **entidades** no painel esquerdo, em seguida, selecione **criar nova entidade**.

1. Na caixa de diálogo pop-up, escreva `Location` no **nome da entidade** caixa, selecione **simples** partir a **tipo de entidade** lista e, em seguida, selecione **feito**.

    Depois de criar esta entidade, vá para todos os objetivos que tenham expressões de exemplo que contêm a entidade. Selecione o texto da expressão de exemplo e marcar o texto como a entidade. 

    R [lista de frase](luis-concept-feature.md) é frequentemente utilizada para impulsionar o sinal de uma entidade.

<a name="add-regular-expression-entities"></a>

## <a name="add-regular-expression-entities-for-highly-structured-concepts"></a>Adicionar entidades de expressão regular para conceitos extremamente estruturada

Uma entidade de expressão regular é utilizada para extrair dados de expressão com base numa expressão regular que fornecer. 

1. Na sua aplicação, selecione **entidades** na navegação à esquerda e, em seguida, selecione **criar nova entidade**.

1. Na caixa de diálogo pop-up, introduza `Human resources form name` no **nome da entidade** caixa, selecione **expressão Regular** partir a **tipo de entidade** lista, introduza a expressão regular `hrf-[0-9]{6}`e, em seguida, selecione **feito**. 

    Essa expressão regular corresponde a caracteres literais `hrf-`, em seguida, de 6 dígitos para representar um formulário número para um formulário de recursos humanos.

## <a name="add-hierarchical-entities"></a>Adicionar entidades hierárquicas

Uma entidade hierárquica é uma categoria de entidades contextualmente aprendidas e conceitualmente relacionadas. No exemplo seguinte, a entidade contém localizações de origem e de destino. 

Na expressão `Move John Smith from Seattle to Cairo`, Seattle é a localização de origem e Cairo é a localização de destino. Cada localização é diferente e aprendidas contextualmente de ordem das palavras e a escolha do word na expressão.

Para adicionar entidades hierárquicas, conclua os seguintes passos: 

1. Na sua aplicação, selecione **entidades** na navegação à esquerda e, em seguida, selecione **criar nova entidade**.

1. Na caixa de diálogo pop-up, escreva `Location` no **nome da entidade** caixa e, em seguida, selecione **Hierarchical** partir a **tipo de entidade** lista.

    ![Adicionar entidade hierárquica](./media/add-entities/hier-location-entity-creation.png)

1. Selecione **adicionar secundário**e, em seguida, introduza `Origin` no **filho n. º 1** caixa. 

1. Selecione **adicionar secundário**e, em seguida, introduza `Destination` no **filho n. º 2** caixa. Selecione **Done** (Concluído).

    >[!CAUTION]
    >Os nomes de entidades de subordinado tem de ser exclusivos em todas as entidades numa única aplicação. Duas entidades hierárquicas diferentes não podem conter entidades subordinadas com o mesmo nome. 

    Depois de criar esta entidade, vá para todos os objetivos que tenham expressões de exemplo que contêm a entidade. Selecione o texto da expressão de exemplo e marcar o texto como a entidade. 

<a name="add-composite-entities"></a>

## <a name="add-composite-entities-to-group-into-a-parent-child-relationship"></a>Adicionar entidades de compostos para agrupar numa relação principal-subordinado

Pode definir relações entre entidades de diferentes tipos através da criação de uma entidade composta. No exemplo seguinte, a entidade contém uma expressão regular e uma entidade pré-criados do nome.  

Na expressão `Send hrf-123456 to John Smith`, o texto `hrf-123456` é correspondido a um human resources [expressão regular](#add-regular-expression-entities) e `John Smith` é extraída com o personName entidade pré-criados. Cada entidade faz parte de uma entidade principal maior. 

1. Na sua aplicação, selecione **entidades** no painel de navegação à esquerda da **crie** secção e, em seguida, selecione **adicionar entidade pré-criados**.

1. Adicionar a entidade pré-criados **PersonName**. Para obter instruções, consulte [adicionar entidades pré-concebidas](#add-prebuilt-entity). 

1. Selecione **entidades** na navegação à esquerda e, em seguida, selecione **criar nova entidade**.

1. Na caixa de diálogo pop-up, introduza `SendHrForm` no **nome da entidade** caixa, em seguida, selecione **compostos** partir a **tipo de entidade** lista.

1. Selecione **adicionar secundário** para adicionar um novo item subordinado.

1. Na **filho n. º 1**, selecione a entidade **número** da lista.

1. Na **filho n. º 2**, selecione a entidade **nome de formulário de recursos humanos** da lista. 

1. Selecione **Done** (Concluído).

<a name="add-pattern-any-entities"></a>

## <a name="add-patternany-entities-to-capture-free-form-entities"></a>Adicionar entidades de Pattern.any para capturar as entidades de forma livre

[Pattern.ANY](luis-concept-entity-types.md) apenas são válidas em entidades [padrões](luis-how-to-model-intent-pattern.md), não intenções. Este tipo de entidade ajuda LUIS encontrar o fim de entidades de comprimento variável e a escolha do word. Como esta entidade é utilizada num padrão, o LUIS sabe onde o fim da entidade é o modelo de expressão.

Se um aplicativo tiver um `FindHumanResourcesForm` intenção, o título do formulário extraídos pode interferir com a intenção de predição. Para esclarecer que palavras são no título do formulário, use um Pattern.any dentro de um padrão. A predição de LUIS começa com a expressão. Em primeiro lugar, a expressão é verificada e correspondentes para entidades, quando as entidades são encontradas, em seguida, o padrão é verificado e correspondentes. 

Na expressão `Where is Request relocation from employee new to the company on the server?`, o título do formulário é difícil, porque não é óbvio contextualmente onde termina o título e onde começa o restante da expressão. Títulos podem ser qualquer ordem das palavras incluindo uma única palavra, expressões complexas com pontuação e não fizer sentido a ordem das palavras. Um padrão permite-lhe criar uma entidade em que a entidade completa e exata pode ser extraída. Assim que o título é encontrado, o `FindHumanResourcesForm` intenção é prevista uma vez que é a intenção para o padrão.

1. Partir do **crie** secção, selecione **entidades** no painel esquerdo e, em seguida, selecione **criar nova entidade**.

1. Na **adicionar entidade** caixa de diálogo, introduza `HumanResourcesFormTitle` no **nome da entidade** caixa e selecione **Pattern.any** como o **tipo de entidade**.

    Para utilizar a entidade de pattern.any, adicione um padrão no **padrões** na página a **melhorar o desempenho de aplicações** secção com a sintaxe de chave de abertura correto, tais como `Where is **{HumanResourcesFormTitle}** on the server?`.

    Se chegar à conclusão que o seu padrão, ao incluir uma entidade Pattern.any, extrai as entidades incorretamente, utilize uma [lista explícita](luis-concept-patterns.md#explicit-lists) para corrigir este problema. 

<a name="add-a-role-to-pattern-based-entity"></a>

## <a name="add-a-role-to-distinguish-different-contexts"></a>Adicionar uma função para distinguir os diferentes contextos

Uma função é um subtipo com nome de uma entidade com base no contexto. É comparável para um [hierárquica](#add-hierarchical-entities) entidade, funções, mas só são utilizadas na [padrões](luis-how-to-model-intent-pattern.md). 

Usando o mesmo exemplo como a entidade hierárquica de origem e as cidades de destino, a diferença é que uma função com o nome origem em vez de um filho hierárquica. 

A sintaxe para uma função é **{Entityname:Rolename}** onde o nome da entidade é seguido por dois-pontos, em seguida, o nome da função. Por exemplo, `Move {personName} from {LocationUsingRoles:Origin} to {LocationUsingRoles:Destination}`.

1. Partir do **crie** secção, selecione **entidades** no painel esquerdo.

1. Selecione **Criar nova entidade**. Introduza o nome do `LocationUsingRoles`. Selecione o tipo **simples** e selecione **feito**. 

1. Selecione **entidades** do painel esquerdo, em seguida, selecione a nova entidade **LocationUsingRoles** criado no passo anterior.

1. Na **nome da função** caixa de texto, introduza o nome da função de `Origin` e introduza. Adicionar um segundo nome da função de `Destination`. 

    ![Captura de ecrã de adicionar a função de origem para a entidade de localização](./media/add-entities/roles-enter-role-name-text.png)

<a name="add-list-entities"></a>

## <a name="add-list-entities-for-exact-matches"></a>Adicionar entidades de lista para correspondências exatas

Lista de entidades representam um conjunto de palavras relacionadas fixo e fechado. 

Para uma aplicação de recursos humanos, pode ter uma lista de todos os departamentos, juntamente com quaisquer sinónimos para os departamentos. Não precisa saber todos os valores quando criar a entidade. Pode adicionar mais depois de rever as expressões de utilizador real com sinónimos.

1. Partir do **crie** secção, selecione **entidades** no painel esquerdo e, em seguida, selecione **criar nova entidade**.

1. Na **adicionar entidade** caixa de diálogo, escreva `Department` no **nome da entidade** caixa e selecione **lista** como o **tipo de entidade**. Selecione **Done** (Concluído).
  
1. A página de lista de entidades permite-lhe adicionar nomes normalizados. Na **valores** caixa de texto, introduza um nome de departamento para obter a lista, tal como `HumanResources` , em seguida, prima Enter no teclado. 

1. À direita do valor normalizado, introduza sinónimos, premindo Enter no teclado após cada item.

1. Se quiser que mais normalizados itens de lista, selecione **Recomendamos** para ver as opções da [dicionário semântico](luis-glossary.md#semantic-dictionary).

    ![Captura de ecrã da seleção de recursos recomendados para ver as opções](./media/add-entities/hr-list-2.png)


1. Selecione um item da lista de recomendados para adicioná-la como um valor normalizado ou selecione **adicionar todos** para adicionar todos os itens. 
    Pode importar valores para uma entidade de lista existente com o seguinte formato JSON:

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

## <a name="do-not-change-entity-type"></a>Não altere o tipo de entidade

LUIS não permite-lhe alterar o tipo da entidade, porque ele não sabe o que adicionar ou remover para construir essa entidade. Para alterar o tipo, é melhor criar uma nova entidade do tipo correto com um nome de um pouco diferente. Assim que a entidade é criada, em cada ocorrência de pronunciação, remova o nome da entidade com nome antigo e adicionar o novo nome de entidade. Depois de tem sido relabeled todas as expressões, elimine a entidade antiga. 

<a name="create-a-pattern-from-an-utterance"></a>

## <a name="create-a-pattern-from-an-example-utterance"></a>Criar um padrão a partir de uma expressão de exemplo

Ver [adicionar padrão de expressão existente na página de intenção ou a entidade](luis-how-to-model-intent-pattern.md#add-pattern-from-existing-utterance-on-intent-or-entity-page).

## <a name="train-your-app-after-changing-model-with-entities"></a>Preparar a sua aplicação depois de alterar o modelo de entidades

Depois de adicionar, editar ou remover entidades, [treinar](luis-how-to-train.md) e [publicar](luis-how-to-publish-app.md) as suas alterações afetar a consultas de ponto final na sua aplicação. 

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre entidades previamente concebidas, consulte a [reconhecedores texto](https://github.com/Microsoft/Recognizers-Text) projeto. 

Para obter informações sobre a forma como a entidade é apresentado na resposta da consulta de ponto final JSON, veja [extração de dados](luis-concept-data-extraction.md)

Agora que adicionou intenções, expressões e entidades, terá uma aplicação básica do LUIS. Saiba como [treinar](luis-how-to-train.md), [testar](luis-interactive-test.md), e [publicar](luis-how-to-publish-app.md) seu aplicativo.
 
