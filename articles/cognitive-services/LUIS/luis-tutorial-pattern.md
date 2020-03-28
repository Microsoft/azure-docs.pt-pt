---
title: 'Tutorial: Padrões - LUIS'
titleSuffix: Azure Cognitive Services
description: Use padrões para aumentar a intenção e a previsão da entidade, ao mesmo tempo que fornece menos declarações de exemplo neste tutorial. O padrão é fornecido como um exemplo de expressão de modelo, que inclui sintaxe para identificar entidades e texto ignorável.
services: cognitive-services
author: diberry
ms.custom: seodec18
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 12/17/2019
ms.author: diberry
ms.openlocfilehash: 69894dfc6bcbe9eb56451524c78e82da2745aa52
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75979772"
---
# <a name="tutorial-add-common-pattern-template-utterance-formats-to-improve-predictions"></a>Tutorial: Adicione formatos de expressão de modelo comum para melhorar as previsões

Neste tutorial, use padrões para aumentar a intenção e a previsão da entidade, o que lhe permite fornecer menos declarações de exemplo. O padrão é a expressão do modelo atribuída a uma intenção, contendo sintaxe para identificar entidades e texto ignorável.

**Neste tutorial, vai aprender a:**

> [!div class="checklist"]
> * Criar um padrão
> * Verificar melhorias de predição do padrão
> * Marcar texto como ignorável e aninhar no padrão
> * Utilizar o painel de teste para verificar o êxito do padrão

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="utterances-in-intent-and-pattern"></a>Expressões em intenção e padrão

Existem dois tipos de expressões armazenadas na app LUIS:

* Declarações exemplo na Intenção
* Declarações de modelo no Padrão

Adicionar expressões de modelo como padrão permite-lhe fornecer menos declarações de exemplo em geral a uma intenção.

Um padrão é aplicado como uma combinação de correspondência de expressão e aprendizagem automática.  A expressão do modelo, juntamente com as declarações de exemplo, dão a LUIS uma melhor compreensão do que as expressões se encaixam na intenção.

## <a name="import-example-app-and-clone-to-new-version"></a>App de exemplo de importação e clone para nova versão

Utilize os passos seguintes:

1.  Descarregue e guarde o [ficheiro JSON](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/custom-domain-batchtest-HumanResources.json?raw=true)da aplicação .

1. Importe o JSON numa nova aplicação para o [portal DE PRÉ-visualização LUIS.](https://preview.luis.ai)

1. Na secção **Gerir**, no separador **Versões**, clone a versão e dê-lhe o nome `patterns`. A clonagem é uma excelente forma de utilizar várias funcionalidades do LUIS sem afetar a versão original. Como o nome da versão é utilizado como parte da rota de URL, o nome não pode conter carateres que não sejam válidos num URL.

## <a name="create-new-intents-and-their-utterances"></a>Criar novas intenções e as respetivas expressões

1. Selecione **Construir** a partir da barra de navegação.

1. Na página **Intenções,** selecione **+ Crie** para criar uma nova intenção.

1. Introduza `OrgChart-Manager` na caixa de diálogo de pop-up e, em seguida, selecione **Concluído**.

    ![Janela de pop-up para criar nova mensagem](media/luis-tutorial-pattern/hr-create-new-intent-popup.png)

1. Adicione expressões de exemplo à intenção. Estas declarações não são _exatamente_ iguais, mas têm um padrão que pode ser extraído.

    |Expressões de exemplo|
    |--|
    |`Who is John W. Smith the subordinate of?`|
    |`Who does John W. Smith report to?`|
    |`Who is John W. Smith's manager?`|
    |`Who does Jill Jones directly report to?`|
    |`Who is Jill Jones supervisor?`|

    Não se preocupe se a entidade keyPhrase tiver o nome nas expressões da intenção em vez de na entidade Employee. Ambas são previstas corretamente no painel de Teste e no ponto final.

1. Selecione **Intenções** no painel de navegação esquerdo.

1. Selecione **+ Criar** para criar uma nova intenção. Introduza `OrgChart-Reports` na caixa de diálogo de pop-up e, em seguida, selecione **Concluído**.

1. Adicione expressões de exemplo à intenção.

    |Expressões de exemplo|
    |--|
    |`Who are John W. Smith's subordinates?`|
    |`Who reports to John W. Smith?`|
    |`Who does John W. Smith manage?`|
    |`Who are Jill Jones direct reports?`|
    |`Who does Jill Jones supervise?`|

### <a name="caution-about-example-utterance-quantity"></a>Atenção em relação à quantidade de expressões de exemplo

[!INCLUDE [Too few examples](../../../includes/cognitive-services-luis-too-few-example-utterances.md)]

### <a name="train-the-app-before-testing-or-publishing"></a>Treine a app antes de testar ou publicar

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

### <a name="publish-the-app-to-query-from-the-endpoint"></a>Publique a app para consultar a partir do ponto final

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

### <a name="get-intent-and-entities-from-endpoint"></a>Obter as intenções e as entidades do ponto final

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. Vá para o final do URL no endereço e introduza `Who is the boss of Jill Jones?`. O último parâmetro de corda de `query`consulta é a expressão.

    ```json
    {
        "query": "Who is the boss of Jill Jones?",
        "prediction": {
            "topIntent": "OrgChart-Manager",
            "intents": {
                "OrgChart-Manager": {
                    "score": 0.313054234
                },
                "OrgChart-Reports": {
                    "score": 0.2462688
                },
                "EmployeeFeedback": {
                    "score": 0.0488328524
                },
                "GetJobInformation": {
                    "score": 0.0156933
                },
                "MoveEmployee": {
                    "score": 0.011265873
                },
                "Utilities.StartOver": {
                    "score": 0.003065792
                },
                "Utilities.Stop": {
                    "score": 0.00300148362
                },
                "Utilities.Cancel": {
                    "score": 0.00271081156
                },
                "None": {
                    "score": 0.00212835032
                },
                "ApplyForJob": {
                    "score": 0.0020669254
                },
                "Utilities.Confirm": {
                    "score": 0.00200891262
                },
                "FindForm": {
                    "score": 0.00194145238
                },
                "Utilities.Help": {
                    "score": 0.00182301877
                }
            },
            "entities": {
                "keyPhrase": [
                    "boss of Jill Jones"
                ],
                "Employee": [
                    [
                        "Employee-45612"
                    ]
                ],
                "$instance": {
                    "keyPhrase": [
                        {
                            "type": "builtin.keyPhrase",
                            "text": "boss of Jill Jones",
                            "startIndex": 11,
                            "length": 18,
                            "modelTypeId": 2,
                            "modelType": "Prebuilt Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ],
                    "Employee": [
                        {
                            "type": "Employee",
                            "text": "Jill Jones",
                            "startIndex": 19,
                            "length": 10,
                            "modelTypeId": 5,
                            "modelType": "List Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ]
                }
            }
        }
    }
    ```

Esta consulta foi bem-sucedida? Para este ciclo de preparação, foi bem-sucedida. As pontuações das duas principais intenções são próximas, mas a maior intenção não é significativamente alta (mais de 60%) e não está longe o suficiente acima da pontuação da próxima intenção.

Uma vez que a preparação do LUIS não é sempre exatamente a mesma, existe alguma variação, estas duas classificações podem inverter-se no próximo ciclo de preparação. O resultado é a possibilidade de ser devolvida a intenção errada.

Utilize padrões para tornar a classificação da intenção correta significativamente mais alta em percentagem e mais distante da classificação mais alta seguinte.

Deixe esta segunda janela do browser aberta. Vai utilizá-la mais à frente no tutorial.

## <a name="template-utterances"></a>Expressões de modelo
Devido à natureza do domínio de Recursos Humanos, existem algumas formas comuns de perguntar sobre as relações entre colaboradores nas organizações. Por exemplo:

|Expressões|
|--|
|`Who does Jill Jones report to?`|
|`Who reports to Jill Jones?`|

Estas expressões são demasiado parecidas para determinar a exclusividade contextual de cada sem fornecer muitos exemplos de expressão. Ao adicionar um padrão a uma intenção, o LUIS aprende padrões de expressões comuns para uma intenção sem fornecer muitos exemplos de expressões.

Alguns exemplos de expressões de modelo para esta intenção incluem:

|Exemplos de expressões de modelo|significado da sintaxe|
|--|--|
|`Who does {Employee} report to[?]`|intercambiável`{Employee}`<br>ignorar`[?]`|
|`Who reports to {Employee}[?]`|intercambiável`{Employee}`<br>ignorar`[?]`|

A sintaxe `{Employee}` marca a localização da entidade na expressão de modelo, bem como de que entidade se trata. A sintaxe `[?]`opcional, marca palavras ou pontuação que é opcional. O LUIS faz corresponder a expressão, ignorando o texto opcional dentro dos parênteses retos.

Embora a sintaxe pareça uma expressão regular, não é uma expressão regular. Apenas é suportada a sintaxe das chavetas, `{}`, e dos parênteses retos, `[]`. Podem ser aninhadas até dois níveis.

Para que seja feita a correspondência de um padrão com uma expressão, as entidades na expressão têm de corresponder primeiro às entidades da expressão do modelo. Isto significa que as entidades têm de ter exemplos suficientes, por exemplo, expressões com um elevado grau de previsão antes de os padrões com entidades serem bem sucedidos. No entanto, o modelo não ajuda a prever as entidades, apenas as intenções.

**Embora os padrões permitam que forneça menos expressões de exemplo, se as entidades não forem detetadas, o padrão não corresponde.**

### <a name="add-the-patterns-for-the-orgchart-manager-intent"></a>Adicione os padrões para a intenção orgchart-manager

1. Selecione **Build** (Criar) no menu superior.

1. No painel de navegação à esquerda, em **Improve app performance** (Melhorar o desempenho da aplicação), selecione **Patterns** (Padrões) no painel de navegação à esquerda.

1. Selecione a intenção **OrgChart-Manager** e introduza as seguintes expressões de modelo:

    |Expressões de modelo|
    |:--|
    |`Who is {Employee} the subordinate of[?]`|
    |`Who does {Employee} report to[?]`|
    |`Who is {Employee}['s] manager[?]`|
    |`Who does {Employee} directly report to[?]`|
    |`Who is {Employee}['s] supervisor[?]`|
    |`Who is the boss of {Employee}[?]`|

1. Enquanto ainda está na página Padrões, selecione a intenção **OrgChart-Reports** e, em seguida, introduza as seguintes expressões do modelo:

    |Expressões de modelo|
    |:--|
    |`Who are {Employee}['s] subordinates[?]`|
    |`Who reports to {Employee}[?]`|
    |`Who does {Employee} manage[?]`|
    |`Who are {Employee} direct reports[?]`|
    |`Who does {Employee} supervise[?]`|
    |`Who does {Employee} boss[?]`|

### <a name="query-endpoint-when-patterns-are-used"></a>Ponto final de consulta quando são utilizados padrões

Agora que os padrões são adicionados à app, treinam, publicam e consultam a app no ponto final da previsão.

1. Selecione **Comboio**. Depois de concluído o treino, **selecione Publicar** e selecione a ranhura **de Produção** e, em seguida, selecione **Done**.

1. Depois de a publicação estar concluída, mude os separadores do navegador de volta para o separador URL do ponto final.

1. Vá para o fim do URL no endereço e introduza `Who is the boss of Jill Jones?` como a expressão. O último parâmetro de corda `query`de consulta é o .

    ```json
    {
        "query": "Who is the boss of Jill Jones?",
        "prediction": {
            "topIntent": "OrgChart-Manager",
            "intents": {
                "OrgChart-Manager": {
                    "score": 0.999997854
                },
                "OrgChart-Reports": {
                    "score": 6.13748343E-05
                },
                "EmployeeFeedback": {
                    "score": 8.052567E-06
                },
                "GetJobInformation": {
                    "score": 1.18197136E-06
                },
                "MoveEmployee": {
                    "score": 7.65549657E-07
                },
                "None": {
                    "score": 3.975E-09
                },
                "Utilities.StartOver": {
                    "score": 1.53E-09
                },
                "Utilities.Confirm": {
                    "score": 1.38181822E-09
                },
                "Utilities.Help": {
                    "score": 1.38181822E-09
                },
                "Utilities.Stop": {
                    "score": 1.38181822E-09
                },
                "Utilities.Cancel": {
                    "score": 1.25833333E-09
                },
                "FindForm": {
                    "score": 1.15384613E-09
                },
                "ApplyForJob": {
                    "score": 5.26923061E-10
                }
            },
            "entities": {
                "keyPhrase": [
                    "boss of Jill Jones"
                ],
                "Employee": [
                    [
                        "Employee-45612"
                    ]
                ],
                "$instance": {
                    "keyPhrase": [
                        {
                            "type": "builtin.keyPhrase",
                            "text": "boss of Jill Jones",
                            "startIndex": 11,
                            "length": 18,
                            "modelTypeId": 2,
                            "modelType": "Prebuilt Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ],
                    "Employee": [
                        {
                            "type": "Employee",
                            "text": "Jill Jones",
                            "startIndex": 19,
                            "length": 10,
                            "modelTypeId": 5,
                            "modelType": "List Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ]
                }
            }
        }
    }
    ```

A previsão das intenções é agora significativamente mais confiante e a pontuação da próxima maior intenção é significativamente menor. Estas duas intenções não vão virar-se quando treinam.

### <a name="working-with-optional-text-and-prebuilt-entities"></a>Trabalhar com texto opcional e entidades pré-criadas

As expressões de modelo de padrão anteriores neste tutorial tinham alguns exemplos de texto opcional, como a utilização da letra s, `'s` e do ponto de interrogação, `?`. Suponha que precisa permitir datas atuais e futuras no texto de expressão.

As expressões de exemplo são:

|Intenção|Expressões de exemplo com texto opcional e entidades pré-criadas|
|:--|:--|
|OrgChart-Manager|`Who was Jill Jones manager on March 3?`|
|OrgChart-Manager|`Who is Jill Jones manager now?`|
|OrgChart-Manager|`Who will be Jill Jones manager in a month?`|
|OrgChart-Manager|`Who will be Jill Jones manager on March 3?`|

Cada um destes exemplos utiliza um tempo verbal, `was`, `is`, `will be`, bem como uma data, `March 3`, `now` e `in a month`, que o LUIS precisa de prever corretamente. Note que os dois últimos exemplos da `in` tabela `on`utilizam quase o mesmo texto, exceto para e .

Declarações de modelo de exemplo que permitem esta informação opcional:

|Intenção|Expressões de exemplo com texto opcional e entidades pré-criadas|
|:--|:--|
|OrgChart-Manager|`who was {Employee}['s] manager [[on]{datetimeV2}?`]|
|OrgChart-Manager|`who is {Employee}['s] manager [[on]{datetimeV2}?]`|


A utilização da sintaxe opcional dos parênteses retos, `[]`, faz com que este texto opcional seja fácil de adicionar à expressão de modelo e possa ser aninhado até ao segundo nível, `[[]]`, e incluir entidades ou texto.


**Pergunta: Por que `w` todas as letras, a primeira letra em cada expressão de modelo, minúscula? Não deveriam ser opcionalmente superiores ou inferiores?** A expressão submetida para o ponto final de consulta, pela aplicação cliente, é convertida em minúsculas. A expressão do modelo pode estar em maiúsculas ou minúsculas e a expressão do ponto final também. A comparação é feita sempre após a conversão em minúsculas.

**Pergunta: Porque é que o número pré-criado não faz parte da expressão do modelo se 3 de março é previsto como número `3` e como data `March 3`?** A expressão do modelo está a utilizar contextualmente uma data, quer literalmente como em `March 3` ou de forma abstrata como `in a month`. Uma data pode conter um número, mas um número pode não ser necessariamente visto como uma data. Utilize sempre a entidade que melhor representa o tipo que quer que seja devolvido nos resultados JSON da predição.

**Pergunta: E as expressões mal formuladas, como `Who will {Employee}['s] manager be on March 3?`.** Tempos verbais gramaticamente diferentes como estes, em que o `will` e o `be` estão separados, têm de ser uma nova expressão de modelo. A expressão de modelo existente não vai fazer a correspondência. Embora a intenção da expressão não tenha sido alterada, o posicionamento das palavras na expressão foi alterada. Esta alteração afeta a predição no LUIS. Você pode [agrupar e ou](#use-the-or-operator-and-groups) os verbo-tenses para combinar estas expressões.

**Lembre-se: as entidades são encontradas em primeiro lugar e, em seguida, é feita a correspondência do padrão.**

### <a name="edit-the-existing-pattern-template-utterance"></a>Editar a expressão de modelo do padrão existente

1. No portal DE pré-visualização LUIS, **selecione Construir** no menu superior e, em seguida, selecione **Padrões** no menu esquerdo.

1. Procure a expressão do modelo `Who is {Employee}['s] manager[?]`existente, e selecione a elipse ***(...)*** à direita e, em seguida, selecione **Editar** a partir do menu pop-up.

1. Altere a expressão de modelo para: `who is {Employee}['s] manager [[on]{datetimeV2}?]`

### <a name="add-new-pattern-template-utterances"></a>Adicionar novas expressões de modelo do padrão

1. Ainda na secção **Patterns** (Padrões) de **Build** (Criar), adicione várias expressões novas de modelo do padrão. Selecione **OrgChart-Manager** no menu pendente de Intenção e introduza cada uma das seguintes expressões de modelo:

    |Intenção|Expressões de exemplo com texto opcional e entidades pré-criadas|
    |--|--|
    |OrgChart-Manager|`who was {Employee}['s] manager [[on]{datetimeV2}?]`|
    |OrgChart-Manager|`who will be {Employee}['s] manager [[in]{datetimeV2}?]`|
    |OrgChart-Manager|`who will be {Employee}['s] manager [[on]{datetimeV2}?]`|

2. Selecione **Treinar** na barra de navegação para treinar a aplicação.

3. Depois de concluído o treino, selecione **Teste** na parte superior do painel para abrir o painel de testes.

4. Introduza várias expressões de teste para verificar se o padrão é correspondido e a classificação da intenção é significativamente alta.

    Depois de introduzir a primeira expressão, selecione **Inspect** (Inspecionar) sob o resultado, para que possa ver todos os resultados da predição. Cada expressão deve ter a intenção **orgChart-Manager** e extrair os valores para as entidades de Empregado e dataV2.

    |Expressão|
    |--|
    |`Who will be Jill Jones manager`|
    |`who will be jill jones's manager`|
    |`Who will be Jill Jones's manager?`|
    |`who will be Jill jones manager on March 3`|
    |`Who will be Jill Jones manager next Month`|
    |`Who will be Jill Jones manager in a month?`|

Todas estas expressões encontraram as entidades dentro. Por isso, correspondem ao mesmo padrão e têm uma classificação de predição alta. Adicionou alguns padrões que combinarão com muitas variações de expressões. Não precisava de adicionar nenhum exemplo na intenção de ter o modelo a funcionar no padrão.

Esta utilização de padrões fornecidos:
* pontuações de previsão mais altas
* com as mesmas declarações exemplo na intenção
* com apenas algumas proclamações de modelo bem-construído no padrão

### <a name="use-the-or-operator-and-groups"></a>Utilize o operador e grupos de OR

Várias das proclamações anteriores do modelo são muito próximas. Utilize a sintaxe do **grupo** `()` e **do OR** `|` para reduzir as expressões do modelo.

Os seguintes 2 padrões podem combinar-se num único padrão usando o grupo `()` e a sintaxe DE. `|`

|Intenção|Expressões de exemplo com texto opcional e entidades pré-criadas|
|--|--|
|OrgChart-Manager|`who will be {Employee}['s] manager [[in]{datetimeV2}?]`|
|OrgChart-Manager|`who will be {Employee}['s] manager [[on]{datetimeV2}?]`|

A nova expressão do modelo será:

`who ( was | is | will be ) {Employee}['s] manager [([in]|[on]){datetimeV2}?]`.

Isto usa um **grupo** em torno do `in` verbo necessário e opcional e `on` com um **ou** tubo entre eles.

1. Na página **Padrões,** selecione o filtro **OrgChart-Manager.** Reduza a lista `manager`procurando por.

1. Mantenha uma versão da expressão do modelo (para editar no próximo passo) e elimine as outras variações.

1. Altere a expressão de modelo para: 

    `who ( was | is | will be ) {Employee}['s] manager [([in]|[on]){datetimeV2}?]`

2. Selecione **Treinar** na barra de navegação para treinar a aplicação.

3. Depois de concluído o treino, selecione **Teste** na parte superior do painel para abrir o painel de testes.

    Utilize o painel de teste para testar versões da expressão:

    |Expressões para entrar no painel de teste|
    |--|
    |`Who is Jill Jones manager this month`|
    |`Who is Jill Jones manager on July 5th`|
    |`Who was Jill Jones manager last month`|
    |`Who was Jill Jones manager on July 5th`|
    |`Who will be Jill Jones manager in a month`|
    |`Who will be Jill Jones manager on July 5th`|

Ao utilizar mais sintaxe de padrão, pode reduzir o número de expressões de modelo que tem de manter na sua aplicação, enquanto ainda tem uma pontuação de previsão elevada.

### <a name="use-the-utterance-beginning-and-ending-anchors"></a>Use as âncoras de início e final de expressão

A sintaxe padrão fornece sintaxe de âncora de `^`início e fim de um cuidador, . As âncoras de expressão inicial e final podem ser usadas em conjunto para visar uma expressão muito específica e possivelmente literal ou usadas separadamente para visar as intenções.

## <a name="using-patternany-entity"></a>Usando O Padrão.qualquer entidade

A entidade pattern.any permite localizar dados de forma livre em que o texto da entidade dificulta distinguir o final da entidade do resto da expressão.

Esta aplicação de Recursos Humanos ajuda os colaboradores a encontrar formulários de empresa.

|Expressão|
|--|
|Onde está **HRF-123456**?|
|Quem é o autor de **HRF-123234**?|
|**HRF-456098** está publicado em francês?|

No entanto, cada formulário tem um nome formatado, utilizado na tabela anterior, bem como um nome amigável, como `Request relocation from employee new to the company 2018 version 5`.

As expressões na forma amigável têm o seguinte aspeto:

|Expressão|
|--|
|Onde está **Pedir transferência do colaborador que é novo na empresa 2018 versão 5**?|
|Quem é o autor de **Pedir transferência do colaborador que é novo na empresa 2018 versão 5**?|
|**Pedir transferência do colaborador que é novo na empresa 2018 versão 5** está publicado em francês?|

O comprimento variado inclui palavras que podem confundir o LUIS sobre onde termina a entidade. Utilizar uma entidade Pattern.any num padrão permite especificar o início e o final do nome do formulário, para que o LUIS extraia corretamente o nome do formulário.

|Exemplo de expressão de modelo|
|--|
|Onde está {FormName}[?]|
|Quem é o autor de {FormName}[?]|
|{FormName} está publicado em francês[?]|

### <a name="add-example-utterances-with-patternany"></a>Adicione pronunciações de exemplo com Padrão.qualquer

1. Selecione **Compilar** no painel de navegação superior e, em seguida, selecione **Intenções** no painel de navegação esquerdo.

1. Selecione **FindForm** na lista de intenções.

1. Adicione algumas expressões de exemplo:

    |Expressão de exemplo|Nome de formulário|
    |--|--|
    |Onde está o formulário **O que fazer quando um incêndio deflagra no laboratório** e quem precisa de iniciar sessão depois de eu o ler?|O que fazer quando um incêndio deflagra no Laboratório
    |Onde está **Pedir transferência do colaborador que é novo na empresa** no servidor?|Pedido de deslocalização de empregado novo para a empresa|
    |Quem é o autor de “**Pedidos relacionados com saúde e bem-estar no campus principal**” e qual é a versão mais atual?|Pedidos de saúde e bem-estar no campus principal|
    |Estou à procura do formulário com o nome “**Pedidos de mudança de escritório, incluindo os recursos físicos**”. |Pedido de movimentação de escritório, incluindo bens físicos|

    Sem uma entidade Pattern.any, o LUIS teria dificuldade em compreender onde termina o título do formulário, devido a muitas variações de nomes do formulário.

### <a name="create-a-patternany-entity"></a>Criar uma entidade Pattern.any
A entidade Pattern.any extrai entidades de comprimento variável. Só funciona num padrão porque o padrão marca o início e o fim da entidade com sintaxe.

1. Selecione **Entidades** no painel de navegação esquerdo.

1. Selecione **+ Criar,** introduza o nome, `FormName`e selecione **Pattern.qualquer como** o tipo. Selecione **Criar**.

### <a name="add-a-pattern-that-uses-the-patternany"></a>Adicionar um padrão que utiliza o Pattern.any

1. Selecione **Padrões** no menu de navegação esquerdo.

1. Selecione a intenção **FindForm**.

1. Introduza as seguintes expressões de modelo, que utilizam a nova entidade:

    |Expressões de modelo|
    |--|
    |`Where is the form ["]{FormName}["] and who needs to sign it after I read it[?]`|
    |`Where is ["]{FormName}["] on the server[?]`|
    |`Who authored ["]{FormName}["] and what is the most current version[?]`|
    |`I'm looking for the form named ["]{FormName}["][.]`|

1. Prepare a aplicação.

### <a name="test-the-new-pattern-for-free-form-data-extraction"></a>Testar o novo padrão para extração de dados de forma livre
1. Selecione **Testar** na barra superior para abrir o painel de teste.

1. Introduza a seguinte expressão:

    `Where is the form Understand your responsibilities as a member of the community and who needs to sign it after I read it?`

1. Selecione **Inspecionar** no resultado para ver os resultados da entidade e intenção.

    Em primeiro lugar, é encontrada a entidade `FormName` e depois o padrão, que determina a intenção. Se tiver um resultado de teste em que as entidades não são detetadas e, por conseguinte, o padrão não é encontrado, terá de adicionar mais expressões de exemplo na intenção (não no padrão).

1. Feche o painel de teste, ao selecionar o botão **Testar** no painel de navegação superior.

### <a name="using-an-explicit-list"></a>Usando uma lista explícita

Se chegar à conclusão que o seu padrão, ao incluir uma entidade Pattern.any, extrai as entidades incorretamente, utilize uma [lista explícita](reference-pattern-syntax.md#explicit-lists) para corrigir este problema.

## <a name="what-did-this-tutorial-accomplish"></a>O que este tutorial conseguiu?

Este tutorial acrescentou padrões para ajudar luis a prever a intenção com uma pontuação significativamente mais alta sem ter que adicionar mais declarações de exemplo. A marcação de entidades e de texto ignorável permitiu que o LUIS aplicasse o padrão a uma maior variedade de expressões.

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Passos seguintes


> [!div class="nextstepaction"]
> [Saiba como utilizar funções com um padrão](luis-tutorial-pattern.md)
