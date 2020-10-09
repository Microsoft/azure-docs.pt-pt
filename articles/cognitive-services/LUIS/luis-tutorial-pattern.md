---
title: 'Tutorial: Padrões - LUIS'
description: Use padrões para aumentar a intenção e a previsão da entidade, ao mesmo tempo que fornece menos palavras de exemplo neste tutorial. O padrão é fornecido como um exemplo de expressão de modelo, que inclui sintaxe para identificar entidades e texto ignorável.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 07/06/2020
ms.openlocfilehash: 9814304aed4d7a5f307fb2179491b0fa9635fd68
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91324659"
---
# <a name="tutorial-add-common-pattern-template-utterance-formats-to-improve-predictions"></a>Tutorial: Adicione formatos comuns de expressão de modelo de padrão para melhorar as previsões

Neste tutorial, use padrões para aumentar a intenção e a previsão da entidade, o que lhe permite fornecer menos palavras de exemplo. O padrão é uma expressão de modelo atribuída a uma intenção, que contém sintaxe para identificar entidades e texto ignorável.

**Neste tutorial, ficará a saber como:**

> [!div class="checklist"]
> * Criar um padrão
> * Verificar melhorias de predição do padrão
> * Marcar texto como ignorável e aninhar no padrão
> * Utilizar o painel de teste para verificar o êxito do padrão

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="utterances-in-intent-and-pattern"></a>Expressões na intenção e padrão

Existem dois tipos de expressões armazenadas na app LUIS:

* Palavras de exemplo na Intenção
* Expressões de modelo no padrão

Adicionar expressões de modelo como um padrão permite-lhe fornecer menos palavras de exemplo em geral a uma intenção.

Um padrão é aplicado como uma combinação de correspondência de texto e aprendizagem automática.  A expressão de modelo no padrão, juntamente com as expressões de exemplo na intenção, dá ao LUIS uma melhor compreensão de quais as expressões que se ajustam à intenção.

## <a name="import-example-app-and-clone-to-new-version"></a>App de exemplo de importação e clone para nova versão

Utilize os passos seguintes:

1.  Faça o download e guarde o ficheiro JSON da [aplicação.](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/apps/tutorial-fix-unsure-predictions.json?raw=true)

1. Inscreva-se no [portal LUIS](https://www.luis.ai)e selecione o seu recurso **de Subscrição** e **Autoria** para ver as aplicações atribuídas a esse recurso de autoria.
1. Importe o JSON numa nova app para o [portal LUIS.](https://www.luis.ai) Na página **My Apps,** selecione **+ novo aplicativo para conversação,** em seguida, selecione **Import as JSON**. Selecione o ficheiro que descarregou no passo anterior, nomeie a aplicação, `Patterns tutorial` .

## <a name="create-new-intents-and-their-utterances"></a>Criar novas intenções e as respetivas expressões

As duas intenções encontram os relatórios diretos do gestor ou do gestor, com base no texto de expressão. A dificuldade é que as duas intenções _significam_ coisas diferentes, mas a maioria das palavras são as mesmas. Só a ordem de palavras é diferente. Para que a intenção fosse corretamente prevista, teria de ter muitos exemplos.

1. **Selecione Construir** a partir da barra de navegação.

1. Na página **Intenções,** selecione **+ Criar** para criar uma nova intenção.

1. Introduza `OrgChart-Manager` na caixa de diálogo de pop-up e, em seguida, selecione **Concluído**.

    ![Janela de pop-up para criar nova mensagem](media/luis-tutorial-pattern/hr-create-new-intent-popup.png)

1. Adicione expressões de exemplo à intenção. Estas expressões não são _exatamente_ iguais, mas têm um padrão que pode ser extraído.

    |Expressões de exemplo|
    |--|
    |`Who is John W. Smith the subordinate of?`|
    |`Who does John W. Smith report to?`|
    |`Who is John W. Smith's manager?`|
    |`Who does Jill Jones directly report to?`|
    |`Who is Jill Jones supervisor?`|

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

1. Vá até ao final do URL na barra de endereços e substitua _YOUR_QUERY_HERE_ por: `Who is the boss of Jill Jones?` .

    ```json
    {
        "query": "Who is the boss of Jill Jones?",
        "prediction": {
            "topIntent": "OrgChart-Manager",
            "intents": {
                "OrgChart-Manager": {
                    "score": 0.326605469
                },
                "OrgChart-Reports": {
                    "score": 0.127583548
                },
                "EmployeeFeedback": {
                    "score": 0.0299124215
                },
                "MoveEmployee": {
                    "score": 0.01159851
                },
                "GetJobInformation": {
                    "score": 0.0104600191
                },
                "ApplyForJob": {
                    "score": 0.007508645
                },
                "Utilities.StartOver": {
                    "score": 0.00359402061
                },
                "Utilities.Stop": {
                    "score": 0.00336530479
                },
                "FindForm": {
                    "score": 0.002653719
                },
                "Utilities.Cancel": {
                    "score": 0.00263288687
                },
                "None": {
                    "score": 0.00238638581
                },
                "Utilities.Help": {
                    "score": 0.00226386427
                },
                "Utilities.Confirm": {
                    "score": 0.00211663754
                }
            },
            "entities": {
                "keyPhrase": [
                    "boss of Jill Jones"
                ],
                "EmployeeListEntity": [
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
                    "EmployeeListEntity": [
                        {
                            "type": "EmployeeListEntity",
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

A intenção correta estava prevista, `OrgChart-Manager` mas a pontuação não é superior a 70% e não está longe o suficiente acima da intenção mais alta. Utilize padrões para tornar a classificação da intenção correta significativamente mais alta em percentagem e mais distante da classificação mais alta seguinte.

Deixe esta segunda janela do browser aberta. Vai usá-lo de novo mais tarde no tutorial.

## <a name="template-utterances"></a>Expressões de modelo
Devido à natureza do domínio do tema dos Recursos Humanos, existem algumas formas comuns de perguntar sobre as relações dos colaboradores nas organizações. Por exemplo:

|Expressões|
|--|
|`Who does Jill Jones report to?`|
|`Who reports to Jill Jones?`|

Estas expressões estão muito próximas para determinar a singularidade contextual de cada um sem fornecer _muitos_ exemplos de expressão. Ao adicionar um padrão para uma intenção, LUIS aprende padrões de expressão comuns para uma intenção sem a necessidade de fornecer muitos mais exemplos de expressão.

Alguns exemplos de expressões de modelo para esta intenção incluem:

|Exemplos de expressões de modelo|significado da sintaxe|
|--|--|
|`Who does {EmployeeListEntity} report to[?]`|permutável `{EmployeeListEntity}`<br>ignorar `[?]`|
|`Who reports to {EmployeeListEntity}[?]`|permutável `{EmployeeListEntity}`<br>ignorar `[?]`|

A sintaxe `{EmployeeListEntity}` marca a localização da entidade na expressão de modelo, bem como de que entidade se trata. A sintaxe opcional, `[?]` marca palavras, ou [pontuação](luis-reference-application-settings.md#punctuation-normalization) que é opcional. O LUIS faz corresponder a expressão, ignorando o texto opcional dentro dos parênteses retos.

Embora a sintaxe pareça uma expressão regular, não é uma expressão regular. Apenas é suportada a sintaxe das chavetas, `{}`, e dos parênteses retos, `[]`. Podem ser aninhadas até dois níveis.

Para que um padrão seja igualado a uma expressão, _primeiro_ as entidades dentro da expressão têm de corresponder às entidades na expressão do modelo. Isto significa que as entidades têm de ter exemplos suficientes em declarações de exemplo com um elevado grau de previsão antes que os padrões com as entidades sejam bem sucedidos. No entanto, o modelo não ajuda a prever as entidades, apenas as intenções.

**Embora os padrões permitam que forneça menos expressões de exemplo, se as entidades não forem detetadas, o padrão não corresponde.**

### <a name="add-the-patterns-for-the-orgchart-manager-intent"></a>Adicione os padrões para a intenção OrgChart-Manager

1. Selecione **Build** (Criar) no menu superior.

1. No painel de navegação à esquerda, em **Improve app performance** (Melhorar o desempenho da aplicação), selecione **Patterns** (Padrões) no painel de navegação à esquerda.

1. Selecione a intenção **OrgChart-Manager** e introduza as seguintes expressões de modelo:

    |Expressões de modelo|
    |:--|
    |`Who is {EmployeeListEntity} the subordinate of[?]`|
    |`Who does {EmployeeListEntity} report to[?]`|
    |`Who is {EmployeeListEntity}['s] manager[?]`|
    |`Who does {EmployeeListEntity} directly report to[?]`|
    |`Who is {EmployeeListEntity}['s] supervisor[?]`|
    |`Who is the boss of {EmployeeListEntity}[?]`|

    Estas expressões de modelo incluem a entidade **EmployeeListEntity** com a notação de suporte encaracolado.

1. Enquanto ainda está na página Padrões, selecione a intenção **orgChart-Reports** e, em seguida, insira as seguintes expressões de modelo:

    |Expressões de modelo|
    |:--|
    |`Who are {EmployeeListEntity}['s] subordinates[?]`|
    |`Who reports to {EmployeeListEntity}[?]`|
    |`Who does {EmployeeListEntity} manage[?]`|
    |`Who are {EmployeeListEntity} direct reports[?]`|
    |`Who does {EmployeeListEntity} supervise[?]`|
    |`Who does {EmployeeListEntity} boss[?]`|

### <a name="query-endpoint-when-patterns-are-used"></a>Ponto final de consulta quando são utilizados padrões

Agora que os padrões são adicionados à app, treine, publique e questione a app no ponto final de tempo de previsão.

1. Selecione **Train** (Preparar). Após a conclusão do treino, **selecione Publicar** e selecione a ranhura **de Produção** e, em seguida, selecione **Fazer**.

1. Após a publicação estar concluída, altere os separadores do navegador de volta para o separador URL do ponto final.

1. Vá até ao final do URL na barra de endereços e verifique se a sua consulta ainda é `Who is the boss of Jill Jones?` submetida a URL para uma nova previsão.

    ```json
    {
        "query": "Who is the boss of Jill Jones?",
        "prediction": {
            "topIntent": "OrgChart-Manager",
            "intents": {
                "OrgChart-Manager": {
                    "score": 0.999999046
                },
                "OrgChart-Reports": {
                    "score": 3.237443E-05
                },
                "EmployeeFeedback": {
                    "score": 4.364242E-06
                },
                "GetJobInformation": {
                    "score": 1.616159E-06
                },
                "MoveEmployee": {
                    "score": 7.575752E-07
                },
                "ApplyForJob": {
                    "score": 5.234157E-07
                },
                "None": {
                    "score": 3.3E-09
                },
                "Utilities.StartOver": {
                    "score": 1.26E-09
                },
                "FindForm": {
                    "score": 1.13636367E-09
                },
                "Utilities.Cancel": {
                    "score": 1.13636367E-09
                },
                "Utilities.Confirm": {
                    "score": 1.13636367E-09
                },
                "Utilities.Help": {
                    "score": 1.13636367E-09
                },
                "Utilities.Stop": {
                    "score": 1.13636367E-09
                }
            },
            "entities": {
                "keyPhrase": [
                    "boss of Jill Jones"
                ],
                "EmployeeListEntity": [
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
                    "EmployeeListEntity": [
                        {
                            "type": "EmployeeListEntity",
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

A previsão de intenção é agora significativamente mais confiante e a próxima pontuação mais alta é muito baixa. Estas duas intenções não vão virar-se quando treinam.

### <a name="working-with-optional-text-and-prebuilt-entities"></a>Trabalhar com texto opcional e entidades pré-criadas

As expressões de modelo de padrão anteriores neste tutorial tinham alguns exemplos de texto opcional, como a utilização da letra s, `'s` e do ponto de interrogação, `?`. Suponha que você precisa permitir datas atuais e futuras no texto de expressão.

As expressões de exemplo são:

|Intenção|Expressões de exemplo com texto opcional e entidades pré-criadas|
|:--|:--|
|OrgChart-Manager|`Who was Jill Jones manager on March 3?`|
|OrgChart-Manager|`Who is Jill Jones manager now?`|
|OrgChart-Manager|`Who will be Jill Jones manager in a month?`|
|OrgChart-Manager|`Who will be Jill Jones manager on March 3?`|

Cada um destes exemplos utiliza um tempo verbal, `was`, `is`, `will be`, bem como uma data, `March 3`, `now` e `in a month`, que o LUIS precisa de prever corretamente. Note que os dois últimos exemplos da tabela utilizam quase o mesmo texto, exceto `in` e `on` .

Palavras de modelo de exemplo que permitem esta informação opcional:

|Intenção|Expressões de exemplo com texto opcional e entidades pré-criadas|
|:--|:--|
|OrgChart-Manager|`who was {EmployeeListEntity}['s] manager [[on]{datetimeV2}?]`|
|OrgChart-Manager|`who is {EmployeeListEntity}['s] manager [[on]{datetimeV2}?]`|


A utilização da sintaxe opcional dos parênteses retos, `[]`, faz com que este texto opcional seja fácil de adicionar à expressão de modelo e possa ser aninhado até ao segundo nível, `[[]]`, e incluir entidades ou texto.


**Pergunta: Por que todas as `w` letras, a primeira letra em cada modelo, minúscula? Não deveriam ser opcionalmente maiúsculas ou minúsculas?** A expressão submetida para o ponto final de consulta, pela aplicação cliente, é convertida em minúsculas. A expressão do modelo pode estar em maiúsculas ou minúsculas e a expressão do ponto final também. A comparação é feita sempre após a conversão em minúsculas.

**Pergunta: Porque é que o número pré-criado não faz parte da expressão do modelo se 3 de março é previsto como número `3` e como data `March 3`?** A expressão do modelo está a utilizar contextualmente uma data, quer literalmente como em `March 3` ou de forma abstrata como `in a month`. Uma data pode conter um número, mas um número pode não ser necessariamente visto como uma data. Utilize sempre a entidade que melhor representa o tipo que quer que seja devolvido nos resultados JSON da predição.

**Pergunta: E as expressões mal formuladas, como `Who will {EmployeeListEntity}['s] manager be on March 3?`.** Tempos verbais gramaticamente diferentes como estes, em que o `will` e o `be` estão separados, têm de ser uma nova expressão de modelo. A expressão de modelo existente não vai fazer a correspondência. Embora a intenção da expressão não tenha sido alterada, o posicionamento das palavras na expressão foi alterada. Esta alteração afeta a predição no LUIS. Você pode [agrupar e ou](#use-the-or-operator-and-groups) o verbo-tenses para combinar estas expressões.

> [!CAUTION]
> **Lembre-se: as entidades são encontradas em primeiro lugar e, em seguida, é feita a correspondência do padrão.**

### <a name="add-new-pattern-template-utterances"></a>Adicionar novas expressões de modelo do padrão

1. Ainda na secção **Patterns** (Padrões) de **Build** (Criar), adicione várias expressões novas de modelo do padrão. Selecione **OrgChart-Manager** no menu pendente de Intenção e introduza cada uma das seguintes expressões de modelo:

    |Intenção|Expressões de exemplo com texto opcional e entidades pré-criadas|
    |--|--|
    |OrgChart-Manager|`who was {EmployeeListEntity}['s] manager [[on]{datetimeV2}?]`|
    |OrgChart-Manager|`who will be {EmployeeListEntity}['s] manager [[in]{datetimeV2}?]`|
    |OrgChart-Manager|`who will be {EmployeeListEntity}['s] manager [[on]{datetimeV2}?]`|

2. Selecione **Train** na barra de navegação para treinar a aplicação.

3. Após o treino estar concluído, selecione **Teste** na parte superior do painel para abrir o painel de testes.

4. Introduza várias expressões de teste para verificar se o padrão é correspondido e a classificação da intenção é significativamente alta.

    Depois de introduzir a primeira expressão, selecione **Inspect** (Inspecionar) sob o resultado, para que possa ver todos os resultados da predição. Cada expressão deve ter a intenção **orgChart-Manager** e extrair os valores para as `EmployeeListEntity` entidades e `datetimeV2` entidades.

    |Expressão|
    |--|
    |`Who will be Jill Jones manager`|
    |`who will be jill jones's manager`|
    |`Who will be Jill Jones's manager?`|
    |`who will be Jill jones manager on March 3`|
    |`Who will be Jill Jones manager next Month`|
    |`Who will be Jill Jones manager in a month?`|

Todas estas expressões encontraram as entidades dentro. Por isso, correspondem ao mesmo padrão e têm uma classificação de predição alta. Adicionou alguns padrões que combinam com muitas variações de expressões. Não precisava de adicionar quaisquer palavras de exemplo na intenção de ter o modelo a funcionar no padrão.

Esta utilização de padrões fornecidos:
* Notas de previsão mais altas
* Com o mesmo exemplo declarações na intenção
* Com apenas algumas expressões de modelo bem construídas no padrão

### <a name="use-the-or-operator-and-groups"></a>Utilize o operador e grupos de OR

Várias das expressões anteriores do modelo são muito próximas. Utilize o **grupo** `()` e a sintaxe **OR** para reduzir `|` as expressões do modelo.

Os dois padrões seguintes podem combinar-se num único padrão usando o grupo `()` e `|` a sintaxe OR.

|Intenção|Expressões de exemplo com texto opcional e entidades pré-criadas|
|--|--|
|OrgChart-Manager|`who will be {EmployeeListEntity}['s] manager [[in]{datetimeV2}?]`|
|OrgChart-Manager|`who will be {EmployeeListEntity}['s] manager [[on]{datetimeV2}?]`|

A nova expressão do modelo será:

`who ( was | is | will be ) {EmployeeListEntity}['s] manager [([in]|[on]){datetimeV2}?]`.

Isto utiliza um **grupo** em torno do verbo necessário e do opcional `in` e com um `on` **ou** tubo entre eles.

> [!NOTE]
> Quando utilizar o símbolo _OR_ , `|` (tubo), certifique-se de separar o símbolo do tubo com um espaço antes e depois dele no modelo de exemplo.

1. Na página **Padrões,** selecione o filtro **OrgChart-Manager.** Reduza a lista `manager` procurando.

1. Mantenha uma versão da expressão do modelo (para editar no passo seguinte) e elimine as outras variações.

1. Altere a expressão de modelo para: 

    `who ( was | is | will be ) {EmployeeListEntity}['s] manager [([in]|[on]){datetimeV2}?]`

2. Selecione **Train** na barra de navegação para treinar a aplicação.

3. Após o treino estar concluído, selecione **Teste** na parte superior do painel para abrir o painel de testes.

    Utilize o painel de teste para testar versões da expressão:

    |Declarações para entrar no painel de teste|
    |--|
    |`Who is Jill Jones manager this month`|
    |`Who is Jill Jones manager on July 5th`|
    |`Who was Jill Jones manager last month`|
    |`Who was Jill Jones manager on July 5th`|
    |`Who will be Jill Jones manager in a month`|
    |`Who will be Jill Jones manager on July 5th`|

Ao utilizar mais sintaxe de padrão, reduz o número de palavras de modelo que tem de manter na sua aplicação, enquanto ainda tem uma pontuação de previsão elevada.

### <a name="use-the-utterance-beginning-and-ending-anchors"></a>Use as âncoras de início e fim da expressão

A sintaxe padrão proporciona sintaxe de âncora de início e fim de um cuidado, `^` . As âncoras de início e fim podem ser usadas em conjunto para atingir expressões muito específicas e possivelmente literais ou usadas separadamente para visar as intenções.

## <a name="using-patternany-entity"></a>Usando o Padrão.qualquer entidade

[!INCLUDE [Pattern.any entity - concepts](./includes/pattern-any-entity.md)]

### <a name="add-example-utterances-with-patternany"></a>Adicione palavras de exemplo com Padrão.qualquer

1. Selecione **Compilar** no painel de navegação superior e, em seguida, selecione **Intenções** no painel de navegação esquerdo.

1. Selecione **FindForm** na lista de intenções.

1. Adicione algumas palavras de exemplo. O texto que deve ser previsto como um Padrão.qualquer um está em **texto arrojado**. O nome da forma é difícil de determinar a partir das outras palavras que o rodeiam na expressão. O Padrão.qualquer um ajudará marcando os limites da entidade.

    |Expressão de exemplo|Nome do formulário|
    |--|--|
    |Onde está o formulário **O que fazer quando um incêndio deflagra no laboratório** e quem precisa de iniciar sessão depois de eu o ler?|O que fazer quando um incêndio deflagra no Laboratório
    |Onde está **Pedir transferência do colaborador que é novo na empresa** no servidor?|Pedido de deslocalização de funcionário novo para a empresa|
    |Quem é o autor de “**Pedidos relacionados com saúde e bem-estar no campus principal**” e qual é a versão mais atual?|Pedidos de saúde e bem-estar no campus principal|
    |Estou à procura do formulário com o nome “**Pedidos de mudança de escritório, incluindo os recursos físicos**”. |Pedido de mudança de escritório, incluindo bens físicos|

    Sem uma entidade Pattern.any, o LUIS teria dificuldade em compreender onde termina o título do formulário, devido a muitas variações de nomes do formulário.

### <a name="create-a-patternany-entity"></a>Criar uma entidade Pattern.any
A entidade Pattern.any extrai entidades de comprimento variável. Só funciona num padrão porque o padrão marca o início e o fim da entidade com sintaxe.

1. Selecione **Entidades** no painel de navegação esquerdo.

1. Selecione **+ Criar,** insira o nome `FormName` e selecione **Padrão.qualquer** como o tipo. Selecione **Criar**.

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

Este tutorial acrescentou padrões para ajudar LUIS a prever a intenção com uma pontuação significativamente maior sem ter que adicionar mais palavras de exemplo. A marcação de entidades e de texto ignorável permitiu que o LUIS aplicasse o padrão a uma maior variedade de expressões.

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Passos seguintes


> [!div class="nextstepaction"]
> [Saiba como utilizar funções com um padrão](luis-tutorial-pattern.md)
