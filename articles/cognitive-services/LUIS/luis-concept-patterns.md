---
title: Padrões de ajudar a predição
titleSuffix: Language Understanding - Azure Cognitive Services
description: Um padrão permite-lhe obter maior exatidão para um objetivo sem fornecer expressões de com muitos mais.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 02/22/2019
ms.author: diberry
ms.openlocfilehash: 1c408a77f0bdd097db1fb462fa06245133773b71
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/01/2019
ms.locfileid: "57195392"
---
# <a name="patterns-improve-prediction-accuracy"></a>Padrões de melhorar a exatidão da previsão
Padrões foram criados para melhorar a precisão quando várias expressões são muito semelhantes.  Um padrão permite-lhe obter maior exatidão para um objetivo sem fornecer expressões de com muitos mais. 

## <a name="patterns-solve-low-intent-confidence"></a>Padrões de resolver a confiança de intenção baixa
Considere uma aplicação de recursos humanos que os relatórios no organograma em relação a um funcionário. Dado o nome e a relação de um funcionário, LUIS retorne os funcionários envolvidos. Considere um funcionário, Tom, com um Gestor de Alice de nome e uma equipe de subordinados com o nome: Michael, Rebecca e Carl.

![Imagem de gráfico de organização](./media/luis-concept-patterns/org-chart.png)

|Expressões|Intenção prevista|Pontuação de intenção|
|--|--|--|
|Quem é subordinada de José?|GetOrgChart|.30|
|Quem é o subordinado de Tom?|GetOrgChart|.30|

Se tiver uma aplicação entre 10 e 20 expressões com diferentes comprimentos de sentença, ordem das palavras diferente e até mesmo palavras (sinónimos de "subordinado", "Gerir", "relatório"), o LUIS pode devolver uma pontuação de confiança baixa. Crie um padrão para o ajudar a entender a importância da ordem das palavras do LUIS. 

Padrões de resolver as seguintes situações: 

* A pontuação de intenção é baixa
* A intenção correta não é a classificação superior mas próximos demais para a classificação superior. 

## <a name="patterns-are-not-a-guarantee-of-intent"></a>Padrões não são uma garantia de intenção
Padrões de utilizam uma combinação de tecnologias de predição. A definição de um objetivo de uma expressão de modelo num padrão não é uma garantia de predição intenção, mas é um sinal forte. 

## <a name="patterns-do-not-improve-entity-detection"></a>Padrões não aperfeiçoar a detecção de entidade
Embora padrões precisam entidades, um padrão não ajuda a detetar a entidade. Um padrão destina-se apenas para ajudar a predição com intenções e funções.  

Não espere ver a predição de entidade melhorado se fechar várias expressões de com num padrão único. Para obter entidades simples acionar, terá de adicionar expressões ou utilize entidades de lista mais que seu padrão de não serão acionados.

## <a name="patterns-use-entity-roles"></a>Padrões de utilizam as funções de entidade
Se dois ou mais entidades num padrão contextualmente estejam relacionadas, padrões de utilizam entidade [funções](luis-concept-roles.md) para extrair informações contextuais sobre entidades. Isso é equivalente a crianças de entidades hierárquicas, mas é **apenas** disponíveis em padrões. 

## <a name="prediction-scores-with-and-without-patterns"></a>Pontuações de previsão com e sem padrões
Tendo em conta suficiente expressões de exemplo, LUIS seria capaz de aumentar a confiança da previsão sem padrões. Padrões de aumentam a pontuação de confiança, sem ter de fornecer expressões com tantos.  

## <a name="pattern-matching"></a>Correspondência de padrões
Um padrão é correspondido com base em detetar as entidades dentro o padrão em primeiro lugar, em seguida, validar o resto das palavras e a ordem das palavras do padrão. Entidades são necessárias no padrão para um padrão corresponder. O padrão é aplicado ao nível do token, não no nível de caractere. 

## <a name="pattern-syntax"></a>Sintaxe de padrão
Sintaxe de padrão é um modelo para uma expressão. O modelo deve conter palavras e entidades que deseja correspondência com, bem como palavras e pontuação que pretende ignorar. É **não** uma expressão regular. 

Entidades em padrões são rodeadas por chavetas, `{}`. Padrões podem incluir entidades e entidades com funções. [Pattern.ANY](luis-concept-entity-types.md#patternany-entity) é uma entidade só é utilizada em padrões. 

Sintaxe do padrão dá suporte a seguinte sintaxe:

|Função|Sintaxe|[Nível de aninhamento](#nesting-syntax)|Exemplo|
|--|--|--|--|
|entidade| {} -chaves de saída|2|Onde está o formulário {nome da entidade}?|
|opcional|[] - Parênteses Retos<BR><BR>Existe um limite de 3 em níveis de aninhamento de qualquer combinação de opcional e agrupamentos |2|O ponto de interrogação é opcional [?]|
|Agrupamento|(a) - parênteses|2|is (a \| b)|
|ou| \| -barra (pipe) vertical<br><br>Existe um limite de 2 nas barras verticais (ou) num grupo |-|É onde o formulário ({formulário-nome-short} &#x7c; {formulário-nome-longa} &#x7c; {formulário-number})| 
|início e/ou de fim da expressão|^-acento circunflexo|-|^ começar a expressão<br>a expressão é feito ^<br>^ strict correspondência literal da expressão inteira com a entidade de {number} ^|

## <a name="nesting-syntax-in-patterns"></a>Sintaxe de aninhamento em padrões

O **opcional** sintaxe, com Parênteses Retos, pode ser aninhados dois níveis. Por exemplo: `[[this]is] a new form`. Este exemplo permite que as expressões seguintes: 

|Exemplo de expressão opcional aninhados|Explicação|
|--|--|
|Este é um novo formulário|corresponde a todas as palavras no padrão|
|é um novo formulário|corresponde à palavra opcional externa e de palavras não opcional no padrão|
|um novo formulário|apenas palavras de correspondências necessárias|

O **agrupamento** sintaxe, com parênteses, pode ser aninhadas dois níveis. Por exemplo: `(({Entity1.RoleName1} | {Entity1.RoleName2} ) | {Entity2} )`. Isso permite que qualquer um dos três entidades para corresponder. 

Se Entity1 é uma localização com funções, tais como a origem (Seattle) e de destino (Cairo) e 2 de entidade é um nome de conhecido de construção de uma entidade de lista (RedWest-C), as seguintes expressões seriam mapeados para este padrão:

|Exemplo de expressão de agrupamento aninhado|Explicação|
|--|--|
|RedWest-C|corresponde a entidade de agrupamento externo|
|Seattle|corresponde a uma das entidades agrupamento interno|
|Cairo|corresponde a uma das entidades agrupamento interno|

## <a name="nesting-limits-for-groups-with-optional-syntax"></a>Limites de aninhamento de grupos com a sintaxe opcional

Uma combinação de **agrupamento** com **opcional** sintaxe tem um limite de 3 níveis de aninhamento.

|Permitido|Exemplo|
|--|--|
|Sim|( [ ( test1 &#x7c; test2 ) ] &#x7c; test3 )|
|Não|( [ ( [ test1 ] &#x7c; test2 ) ] &#x7c; test3 )|

## <a name="nesting-limits-for-groups-with-or-ing-syntax"></a>Limites de aninhamento de grupos com a sintaxe ou gerúndio

Uma combinação de **agrupamento** com **ou gerúndio** sintaxe tem um limite de 2 barras verticais.

|Permitido|Exemplo|
|--|--|
|Sim|(test1 &#x7c; test2 &#x7c; (test3 &#x7c; test4))|
|Não|(test1 &#x7c; test2 &#x7c; test3 &#x7c; (test4 &#x7c; test5)) |

## <a name="syntax-to-add-an-entity-to-a-pattern-template"></a>Sintaxe para adicionar uma entidade a um modelo padrão
Para adicionar uma entidade no modelo padrão, coloque o nome da entidade com chavetas, como `Who does {Employee} manage?`. 

|Padrão de entidade|
|--|
|`Who does {Employee} manage?`|

## <a name="syntax-to-add-an-entity-and-role-to-a-pattern-template"></a>Sintaxe para adicionar uma entidade e a função a um modelo padrão
Uma função de entidade é denotada como `{entity:role}` com o nome de entidade seguido por dois-pontos, em seguida, o nome da função. Para adicionar uma entidade com uma função no modelo padrão, coloque o nome da entidade e o nome de função com chavetas, como `Book a ticket from {Location:Origin} to {Location:Destination}`. 

|Padrão com funções de entidade|
|--|
|`Book a ticket from {Location:Origin} to {Location:Destination}`|

## <a name="syntax-to-add-a-patternany-to-pattern-template"></a>Sintaxe para adicionar um pattern.any ao modelo de padrão
A entidade de Pattern.any permite-lhe adicionar uma entidade do comprimento diferentes para o padrão. O modelo padrão é seguido, desde que o pattern.any pode ser qualquer comprimento. 

Para adicionar um **Pattern.any** entidade no modelo padrão, coloque a entidade de Pattern.any com chavetas, como `How much does {Booktitle} cost and what format is it available in?`.  

|Padrão com Pattern.any entidade|
|--|
|`How much does {Booktitle} cost and what format is it available in?`|

|Títulos de livros no padrão|
|--|
|Quanto **roubar esse livro** custo e que formato está disponível em?|
|Quanto **perguntar** custo e que formato está disponível em?|
|Quanto **o incidente curioso de Dog no tempo de noite** custo e que formato está disponível em?| 

As palavras do título do livro não são confusas para LUIS porque LUIS sabe onde termina o título do livro, com base na entidade Pattern.any.

## <a name="explicit-lists"></a>Listas explícitas

criar uma [lista explícita](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5ade550bd5b81c209ce2e5a8) por meio da API de criação para permitir que a exceção quando:

* Seu padrão contém um [Pattern.any](luis-concept-entity-types.md#patternany-entity)
* E que a sintaxe padrão permite que a possibilidade de uma extração de entidades incorreto com base na expressão. 

Por exemplo, suponha que tenha um padrão que contém ambas as sintaxes opcional, `[]`e a sintaxe de entidade, `{}`, combinados numa forma de extrair dados incorretamente.

Considere o padrão '[find] e-mail sobre {subject} [de {pessoa}]'.

Em expressões de com os seguintes, o **assunto** e **pessoa** entidade são extraídos corretamente e incorretamente:

|Expressão|Entidade|Extração correta|
|--|--|:--:|
|enviar um e-mail sobre cães Chris|assunto = cães<br>pessoa = Chris|✔|
|enviar um e-mail sobre o homem de La Mancha|assunto = o homem<br>pessoa = La Mancha|X|

Na tabela anterior, o assunto deve estar `the man from La Mancha` (um título do livro), mas porque o requerente inclui a palavra opcional `from`, o título está previsto incorretamente. 

Para corrigir essa exceção para o padrão, adicione `the man from la mancha` como uma correspondência de lista explícita para a entidade de {subject} utilizando o [API de criação para lista explícita](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5ade550bd5b81c209ce2e5a8).

## <a name="syntax-to-mark-optional-text-in-a-template-utterance"></a>Sintaxe para marcar texto opcional numa expressão de modelo
Marcar o texto opcional na expressão usando a sintaxe de colchete de expressão regular, `[]`. O texto opcional pode aninhar Parênteses Retos até dois Retos apenas.

|Padrão com o texto opcional|Significado|
|--|--|
|`[find] email about {subject} [from {person}]`|`find` e `from {person}` são opcionais|
|' Pode me ajudar a [?] | A marca de pontuação é opcional|

Marcas de pontuação (`?`, `!`, `.`) devem ser ignorados e terá de ignorá-los usando a sintaxe de colchete em padrões. 

## <a name="pattern-only-apps"></a>Só de padrão de aplicações
Pode criar uma aplicação com objetivos que têm sem expressões de exemplo, desde que haja um padrão para cada intenção. Para uma aplicação só por padrão, o padrão não deve conter entidades aprendidas por máquina, porque estes requerem a expressões de exemplo. 

## <a name="best-practices"></a>Melhores práticas
Saiba mais [melhores práticas](luis-concept-best-practices.md).

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Saiba como implementar padrões neste tutorial](luis-tutorial-pattern.md)
