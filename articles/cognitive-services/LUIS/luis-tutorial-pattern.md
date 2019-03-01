---
title: Padrões
titleSuffix: Azure Cognitive Services
description: Utilize padrões para aumentar a predição de intenções e entidades ao mesmo tempo que fornece menos expressões de exemplo. O padrão é fornecido por meio de um exemplo de expressão de modelo, que inclui a sintaxe para identificar entidades e texto ignorável.
services: cognitive-services
author: diberry
ms.custom: seodec18
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 02/22/2019
ms.author: diberry
ms.openlocfilehash: 33541d2a61c52476f6e314f6981a623390de8fa9
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/01/2019
ms.locfileid: "57193743"
---
# <a name="tutorial-add-common-pattern-template-utterance-formats"></a>Tutorial: Adicionar os formatos de expressão de modelo padrão comuns

Neste tutorial, vai utilizar padrões para aumentar a predição de intenções e entidades ao mesmo tempo que fornece menos expressões de exemplo. O padrão é fornecido por meio de um exemplo de expressão de modelo, que inclui a sintaxe para identificar entidades e texto ignorável. Um padrão é uma combinação de correspondência de expressões e aprendizagem automática.  O exemplo de expressão de modelo, juntamente com as expressões de intenção, dá ao LUIS uma melhor compreensão de quais as expressões que se ajustam à intenção. 

**Neste tutorial, vai aprender a:**

> [!div class="checklist"]
> * Aplicação de exemplo de importação 
> * Criar uma intenção
> * Preparar
> * Publicar
> * Obter as intenções e as entidades do ponto final
> * Criar um padrão
> * Verificar melhorias de predição do padrão
> * Marcar texto como ignorável e aninhar no padrão
> * Utilizar o painel de teste para verificar o êxito do padrão

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="import-example-app"></a>Aplicação de exemplo de importação

Continue com a aplicação criada no último tutorial, com o nome **RecursosHumanos**. 

Utilize os passos seguintes:

1.  Transfira e guarde o [ficheiro JSON da aplicação](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/custom-domain-batchtest-HumanResources.json).

2. Importe o JSON para uma nova aplicação.

3. Na secção **Gerir**, no separador **Versões**, clone a versão e dê-lhe o nome `patterns`. A clonagem é uma excelente forma de utilizar várias funcionalidades do LUIS sem afetar a versão original. Como o nome da versão é utilizado como parte da rota de URL, o nome não pode conter carateres que não sejam válidos num URL.

## <a name="create-new-intents-and-their-utterances"></a>Criar novas intenções e as respetivas expressões

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. Na página **Intents** (Intenções), selecione **Create new intent** (Criar nova intenção). 

3. Introduza `OrgChart-Manager` na caixa de diálogo de pop-up e, em seguida, selecione **Concluído**.

    ![Janela de pop-up para criar nova mensagem](media/luis-tutorial-pattern/hr-create-new-intent-popup.png)

4. Adicione expressões de exemplo à intenção.

    |Expressões de exemplo|
    |--|
    |De quem é subordinado John W. Smith?|
    |A quem reporta John W. Smith?|
    |Quem é o gestor de John W. Smith?|
    |A quem reporta diretamente Jill Jones?|
    |Quem é o supervisor de Jill Jones?|

    [![Captura de ecrã do LUIS a adicionar expressões novas à intenção](media/luis-tutorial-pattern/hr-orgchart-manager-intent.png "Captura de ecrã do LUIS a adicionar expressões novas à intenção")](media/luis-tutorial-pattern/hr-orgchart-manager-intent.png#lightbox)

    Não se preocupe se a entidade keyPhrase tiver o nome nas expressões da intenção em vez de na entidade Employee. Ambas são previstas corretamente no painel de Teste e no ponto final. 

5. Selecione **Intenções** no painel de navegação esquerdo.

6. Selecione **Create new intent** (Criar nova intenção). 

7. Introduza `OrgChart-Reports` na caixa de diálogo de pop-up e, em seguida, selecione **Concluído**.

8. Adicione expressões de exemplo à intenção.

    |Expressões de exemplo|
    |--|
    |Quem são os subordinados de John W. Smith?|
    |Quem reporta a John W. Smith?|
    |Quem é que John W. Smith gere?|
    |Quem são os subordinados diretos de Jill Jones?|
    |Quem é que Jill Jones supervisiona?|

## <a name="caution-about-example-utterance-quantity"></a>Atenção em relação à quantidade de expressões de exemplo

[!INCLUDE [Too few examples](../../../includes/cognitive-services-luis-too-few-example-utterances.md)]

## <a name="train"></a>Preparar

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish"></a>Publicar

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entities-from-endpoint"></a>Obter as intenções e as entidades do ponto final

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Vá para o final do URL no endereço e introduza `Who is the boss of Jill Jones?`. O último parâmetro querystring é `q`, a expressão **query**. 

    ```json
    {
        "query": "who is the boss of jill jones?",
        "topScoringIntent": {
            "intent": "OrgChart-Manager",
            "score": 0.353984952
        },
        "intents": [
            {
                "intent": "OrgChart-Manager",
                "score": 0.353984952
            },
            {
                "intent": "OrgChart-Reports",
                "score": 0.214128986
            },
            {
                "intent": "EmployeeFeedback",
                "score": 0.08434003
            },
            {
                "intent": "MoveEmployee",
                "score": 0.019131
            },
            {
                "intent": "GetJobInformation",
                "score": 0.004819009
            },
            {
                "intent": "Utilities.Confirm",
                "score": 0.0043958663
            },
            {
                "intent": "Utilities.StartOver",
                "score": 0.00312064588
            },
            {
                "intent": "Utilities.Cancel",
                "score": 0.002265454
            },
            {
                "intent": "Utilities.Help",
                "score": 0.00133465114
            },
            {
                "intent": "None",
                "score": 0.0011388344
            },
            {
                "intent": "Utilities.Stop",
                "score": 0.00111166481
            },
            {
                "intent": "FindForm",
                "score": 0.0008900076
            },
            {
                "intent": "ApplyForJob",
                "score": 0.0007836131
            }
        ],
        "entities": [
            {
                "entity": "jill jones",
                "type": "Employee",
                "startIndex": 19,
                "endIndex": 28,
                "resolution": {
                    "values": [
                        "Employee-45612"
                    ]
                }
            },
            {
                "entity": "boss of jill jones",
                "type": "builtin.keyPhrase",
                "startIndex": 11,
                "endIndex": 28
            }
        ]
    }
    ```

Esta consulta foi bem-sucedida? Para este ciclo de preparação, foi bem-sucedida. As classificações das duas principais intenções estão próximas. Uma vez que a preparação do LUIS não é sempre exatamente a mesma, existe alguma variação, estas duas classificações podem inverter-se no próximo ciclo de preparação. O resultado é a possibilidade de ser devolvida a intenção errada. 

Utilize padrões para tornar a classificação da intenção correta significativamente mais alta em percentagem e mais distante da classificação mais alta seguinte. 

Deixe esta segunda janela do browser aberta. Vai utilizá-la mais à frente no tutorial. 

## <a name="template-utterances"></a>Expressões de modelo
Devido à natureza do domínio de Recursos Humanos, existem algumas formas comuns de perguntar sobre as relações entre colaboradores nas organizações. Por exemplo:

|Expressões|
|--|
|A quem reporta Jill Jones?|
|Quem reporta a Jill Jones?|

Estas expressões são demasiado parecidas para determinar a exclusividade contextual de cada sem fornecer muitos exemplos de expressão. Ao adicionar um padrão a uma intenção, o LUIS aprende padrões de expressões comuns para uma intenção sem fornecer muitos exemplos de expressões. 

Alguns exemplos de expressões de modelo para esta intenção incluem:

|Exemplos de expressões de modelo|significado da sintaxe|
|--|--|
|A quem reporta {Employee}[?]|intercambiável {Employee}, ignorar [?]}|
|Quem reporta a {Employee}[?]|intercambiável {Employee}, ignorar [?]}|

A sintaxe `{Employee}` marca a localização da entidade na expressão de modelo, bem como de que entidade se trata. A sintaxe opcional, `[?]`, marca palavras ou pontuação que são opcionais. O LUIS faz corresponder a expressão, ignorando o texto opcional dentro dos parênteses retos.

Embora a sintaxe se pareça com as expressões regulares, não é uma expressão regular. Apenas é suportada a sintaxe das chavetas, `{}`, e dos parênteses retos, `[]`. Podem ser aninhadas até dois níveis.

Para que seja feita a correspondência de um padrão com uma expressão, as entidades na expressão têm de corresponder primeiro às entidades da expressão do modelo. No entanto, o modelo não ajuda a prever as entidades, apenas as intenções. 

**Embora os padrões permitam que forneça menos expressões de exemplo, se as entidades não forem detetadas, o padrão não corresponde.**

## <a name="add-the-patterns-for-the-orgchart-manager-intent"></a>Adicionar os padrões para a intenção de OrgChart-Manager

1. Selecione **Build** (Criar) no menu superior.

2. No painel de navegação à esquerda, em **Improve app performance** (Melhorar o desempenho da aplicação), selecione **Patterns** (Padrões) no painel de navegação à esquerda.

3. Selecione a intenção **OrgChart-Manager** e introduza as seguintes expressões de modelo:

    |Expressões de modelo|
    |:--|
    |De quem é subordinado {Employee}[?]|
    |A quem reporta {Employee}[?]|
    |Quem é o gestor de {Employee}[?]|
    |A quem reporta diretamente {Employee}[?]|
    |Quem é o supervisor de {Employee}[?]|
    |Quem é o chefe de {Employee}[?]|

    As entidades com funções utilizam sintaxe que inclui o nome da função e são abordadas num [tutorial separado para funções](luis-tutorial-pattern-roles.md). 

    Se escrever a expressão de modelo, o LUIS ajuda a preencher a entidade quando introduzir a chaveta esquerda, `{`.

    [![Captura de ecrã da introdução de expressões de modelo para a intenção](./media/luis-tutorial-pattern/hr-pattern-missing-entity.png)](./media/luis-tutorial-pattern/hr-pattern-missing-entity.png#lightbox)

4. Ainda na página de padrões, selecione o **OrgChart relatórios** intenção, em seguida, introduza as seguintes expressões de modelo:

    |Expressões de modelo|
    |:--|
    |Quem são os subordinados de {Employee}[?]|
    |Quem reporta a {Employee}[?]|
    |Quem é que {Employee} gere[?]|
    |Quem são os subordinados diretos de {Employee}[?]|
    |Quem é que {Employee} supervisiona[?]|
    |Quem é que {Employee} chefia[?]|

## <a name="query-endpoint-when-patterns-are-used"></a>Ponto final de consulta quando são utilizados padrões

Agora que os padrões são adicionados à aplicação, preparar, publicar e consultar a aplicação no ponto de final de tempo de execução de predição.

1. Prepare e publique novamente a aplicação.

1. Volte a mudar de separador do browser, para o separador do URL do ponto final.

1. Vá para o fim do URL no endereço e introduza `Who is the boss of Jill Jones?` como a expressão. O último parâmetro querystring é `q`, a expressão **query**. 

    ```json
    {
        "query": "who is the boss of jill jones?",
        "topScoringIntent": {
            "intent": "OrgChart-Manager",
            "score": 0.9999989
        },
        "intents": [
            {
                "intent": "OrgChart-Manager",
                "score": 0.9999989
            },
            {
                "intent": "OrgChart-Reports",
                "score": 7.616303E-05
            },
            {
                "intent": "EmployeeFeedback",
                "score": 7.84204349E-06
            },
            {
                "intent": "GetJobInformation",
                "score": 1.20674213E-06
            },
            {
                "intent": "MoveEmployee",
                "score": 7.91245157E-07
            },
            {
                "intent": "None",
                "score": 3.875E-09
            },
            {
                "intent": "Utilities.StartOver",
                "score": 1.49E-09
            },
            {
                "intent": "Utilities.Confirm",
                "score": 1.34545453E-09
            },
            {
                "intent": "Utilities.Help",
                "score": 1.34545453E-09
            },
            {
                "intent": "Utilities.Stop",
                "score": 1.34545453E-09
            },
            {
                "intent": "Utilities.Cancel",
                "score": 1.225E-09
            },
            {
                "intent": "FindForm",
                "score": 1.123077E-09
            },
            {
                "intent": "ApplyForJob",
                "score": 5.625E-10
            }
        ],
        "entities": [
            {
                "entity": "jill jones",
                "type": "Employee",
                "startIndex": 19,
                "endIndex": 28,
                "resolution": {
                    "values": [
                        "Employee-45612"
                    ]
                },
                "role": ""
            },
            {
                "entity": "boss of jill jones",
                "type": "builtin.keyPhrase",
                "startIndex": 11,
                "endIndex": 28
            }
        ]
    }
    ```

A predição de intenção agora é significativamente mais confiança.

## <a name="working-with-optional-text-and-prebuilt-entities"></a>Trabalhar com texto opcional e entidades pré-criadas

As expressões de modelo de padrão anteriores neste tutorial tinham alguns exemplos de texto opcional, como a utilização da letra s, `'s` e do ponto de interrogação, `?`. Suponha que precisa permitir datas atuais e futuras no texto da expressão.

As expressões de exemplo são:

|Intenção|Expressões de exemplo com texto opcional e entidades pré-criadas|
|:--|:--|
|OrgChart-Manager|`Who was Jill Jones manager on March 3?`|
|OrgChart-Manager|`Who is Jill Jones manager now?`|
|OrgChart-Manager|`Who will be Jill Jones manager in a month?`|
|OrgChart-Manager|`Who will be Jill Jones manager on March 3?`|

Cada um destes exemplos utiliza um tempo verbal, `was`, `is`, `will be`, bem como uma data, `March 3`, `now` e `in a month`, que o LUIS precisa de prever corretamente. Repare que os dois últimos exemplos utilizam quase o mesmo texto, à exceção de `in` e `on`.

Expressão de modelo de exemplo que permitem a estas informações opcionais: 

|Intenção|Expressões de exemplo com texto opcional e entidades pré-criadas|
|:--|:--|
|OrgChart-Manager|`who was {Employee}['s] manager [[on]{datetimeV2}?`]|
|OrgChart-Manager|`who is {Employee}['s] manager [[on]{datetimeV2}?]`|


A utilização da sintaxe opcional dos parênteses retos, `[]`, faz com que este texto opcional seja fácil de adicionar à expressão de modelo e possa ser aninhado até ao segundo nível, `[[]]`, e incluir entidades ou texto.


**Pergunta: Por que motivo são todos o `w` letras, a primeira letra de cada expressão de modelo, em minúsculas? Não devia ser opcional utilizar maiúsculas ou minúsculas?** A expressão submetida para o ponto final de consulta, pela aplicação cliente, é convertida em minúsculas. A expressão do modelo pode estar em maiúsculas ou minúsculas e a expressão do ponto final também. A comparação é feita sempre após a conversão em minúsculas.

**Pergunta: Por que não é o número pré-criados parte do modelo de expressão se 3 de Março for previsto ambos como número `3` e a data `March 3`?** A expressão do modelo está a utilizar contextualmente uma data, quer literalmente como em `March 3` ou de forma abstrata como `in a month`. Uma data pode conter um número, mas um número pode não ser necessariamente visto como uma data. Utilize sempre a entidade que melhor representa o tipo que quer que seja devolvido nos resultados JSON da predição.  

**Pergunta: E quanto insatisfatoriamente dizer como expressões com `Who will {Employee}['s] manager be on March 3?`.** Tempos verbais gramaticamente diferentes como estes, em que o `will` e o `be` estão separados, têm de ser uma nova expressão de modelo. A expressão de modelo existente não vai fazer a correspondência. Embora a intenção da expressão não tenha sido alterada, o posicionamento das palavras na expressão foi alterada. Esta alteração afeta a predição no LUIS. Pode [grupo e ou](#use-the-or-operator-and-groups) verbais combinar essas expressões. 

**Lembre-se: as entidades são encontradas em primeiro lugar e, em seguida, é feita a correspondência do padrão.**

## <a name="edit-the-existing-pattern-template-utterance"></a>Editar a expressão de modelo do padrão existente

1. No site do LUIS, selecione **Build** (Criar) no menu superior e, em seguida, selecione **Padrões** no menu à esquerda. 

1. Pesquisa de expressão de modelo existente, `Who is {Employee}['s] manager[?]`e selecione as reticências (***...*** ) à direita, em seguida, selecione **editar** no menu de pop-up. 

1. Altere a expressão de modelo para: `who is {Employee}['s] manager [[on]{datetimeV2}?]`

## <a name="add-new-pattern-template-utterances"></a>Adicionar novas expressões de modelo do padrão

1. Ainda na secção **Patterns** (Padrões) de **Build** (Criar), adicione várias expressões novas de modelo do padrão. Selecione **OrgChart-Manager** no menu pendente de Intenção e introduza cada uma das seguintes expressões de modelo:

    |Intenção|Expressões de exemplo com texto opcional e entidades pré-criadas|
    |--|--|
    |OrgChart-Manager|`who was {Employee}['s] manager [[on]{datetimeV2}?]`|
    |OrgChart-Manager|`who will be {Employee}['s] manager [[in]{datetimeV2}?]`|
    |OrgChart-Manager|`who will be {Employee}['s] manager [[on]{datetimeV2}?]`|

2. Prepare a aplicação.

3. Selecione **Test** (Testar) na parte superior do painel para abrir o painel de teste. 

4. Introduza várias expressões de teste para verificar se o padrão é correspondido e a classificação da intenção é significativamente alta. 

    Depois de introduzir a primeira expressão, selecione **Inspect** (Inspecionar) sob o resultado, para que possa ver todos os resultados da predição. Cada ocorrência de pronunciação deve ter o **OrgChart Manager** intenção e deve extrair os valores para as entidades de funcionários e datetimeV2.

    |Expressão|
    |--|
    |Quem será o gestor de Jill Jones|
    |quem será o gestor de jill jones|
    |Quem será o gestor de Jill Jones?|
    |quem será o gestor de Jill jones a 3 de março|
    |Quem será o gestor de Jill Jones no próximo Mês|
    |Quem será o gestor de Jill Jones daqui a um mês?|

Todas estas expressões encontraram as entidades dentro. Por isso, correspondem ao mesmo padrão e têm uma classificação de predição alta.

## <a name="use-the-or-operator-and-groups"></a>Utilize o operador OR e grupos

Vários das expressões de modelo anteriores são muito parecidos. Utilize o **grupo** `()` e **ou** `|` sintaxe para reduzir as expressões de modelo. 

Os seguintes 2 padrões podem combinar num padrão único a utilizar o grupo `()` e ou `|` sintaxe.

|Intenção|Expressões de exemplo com texto opcional e entidades pré-criadas|
|--|--|
|OrgChart-Manager|`who will be {Employee}['s] manager [[in]{datetimeV2}?]`|
|OrgChart-Manager|`who will be {Employee}['s] manager [[on]{datetimeV2}?]`|

A nova expressão de modelo será: 

`who ( was | is | will be ) {Employee}['s] manager [([in]|[on]){datetimeV2}?]`. 

Esta opção utiliza um **grupo** em todo o tempo verbal verbo necessária e o opcional `in` e `on` com um **ou** pipe entre eles. 

1. Sobre o **padrões** página, selecione o **OrgChart Manager** filtro. Restringir a lista ao procurar `manager`. 

    ![Pesquisar em padrões de intenção OrgChart Manager para o termo "Gestor"](./media/luis-tutorial-pattern/search-patterns.png)

1. Mantenha uma versão da expressão de modelo (para editar na próxima etapa) e elimine as outras variações. 

1. Altere a expressão de modelo para: 

    `who ( was | is | will be ) {Employee}['s] manager [([in]|[on]){datetimeV2}?]`.

1. Prepare a aplicação.

1. Utilize o painel de teste para testar versões da expressão:

    |Expressões com introduza no painel de teste|
    |--|
    |`Who is Jill Jones manager this month`|
    |`Who is Jill Jones manager on July 5th`|
    |`Who was Jill Jones manager last month`|
    |`Who was Jill Jones manager on July 5th`|    
    |`Who will be Jill Jones manager in a month`|
    |`Who will be Jill Jones manager on July 5th`|


## <a name="use-the-utterance-beginning-and-ending-anchors"></a>Utilize a expressão início e fim âncoras

A sintaxe padrão fornece a sintaxe de âncora de expressão de um sinal de interpolação de iniciais e finais `^`. As âncoras da expressão de iniciais e podem ser utilizado em conjunto para expressão de muito específico e, possivelmente, literal de destino ou utilizado em separado para os objetivos de destino. 

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Passos Seguintes

Este tutorial adiciona duas intenções para expressões cuja predição era difícil de fazer com uma precisão elevada, sem ter muitas expressões de exemplo. A adição de padrões às mesmas permitiu que o LUIS fizesse uma predição mais eficaz da intenção, com uma classificação significativamente superior. A marcação de entidades e de texto ignorável permitiu que o LUIS aplicasse o padrão a uma maior variedade de expressões.

> [!div class="nextstepaction"]
> [Saiba como utilizar funções com um padrão](luis-tutorial-pattern-roles.md)
