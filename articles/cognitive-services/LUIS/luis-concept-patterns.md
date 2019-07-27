---
title: Previsão de ajuda de padrões-LUIS
titleSuffix: Azure Cognitive Services
description: Um padrão permite-lhe obter maior exatidão para um objetivo sem fornecer expressões de com muitos mais.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: diberry
ms.openlocfilehash: acbcaa7e5588c0fecf2c20751e69442e1373cbb5
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68563986"
---
# <a name="patterns-improve-prediction-accuracy"></a>Padrões de melhorar a exatidão da previsão
Padrões foram criados para melhorar a precisão quando várias expressões são muito semelhantes.  Um padrão permite-lhe obter maior exatidão para um objetivo sem fornecer expressões de com muitos mais. 

## <a name="patterns-solve-low-intent-confidence"></a>Padrões de resolver a confiança de intenção baixa
Considere uma aplicação de recursos humanos que os relatórios no organograma em relação a um funcionário. Dado o nome e a relação de um funcionário, LUIS retorne os funcionários envolvidos. Considere um funcionário, Tom, com um nome de gerente Alice e uma equipe de subordinados denominada: Michael, Rebecca e Carl.

![Imagem de gráfico de organização](./media/luis-concept-patterns/org-chart.png)

|Expressões|Intenção prevista|Pontuação de intenção|
|--|--|--|
|Quem é subordinada de José?|GetOrgChart|.30|
|Quem é o subordinado de Tom?|GetOrgChart|.30|

Se tiver uma aplicação entre 10 e 20 expressões com diferentes comprimentos de sentença, ordem das palavras diferente e até mesmo palavras (sinónimos de "subordinado", "Gerir", "relatório"), o LUIS pode devolver uma pontuação de confiança baixa. Crie um padrão para ajudar a LUIS a entender a importância da ordem das palavras. 

Padrões de resolver as seguintes situações: 

* A pontuação da intenção é baixa
* A intenção correta não é a pontuação superior, mas muito próximo da pontuação superior. 

## <a name="patterns-are-not-a-guarantee-of-intent"></a>Padrões não são uma garantia de intenção
Padrões de utilizam uma combinação de tecnologias de predição. A definição de um objetivo de uma expressão de modelo num padrão não é uma garantia de predição intenção, mas é um sinal forte. 

<a name="patterns-do-not-improve-entity-detection"/></a>

## <a name="patterns-do-not-improve-machine-learned-entity-detection"></a>Padrões não melhoram a detecção de entidade aprendida por máquina

Um padrão destina-se principalmente a ajudar a previsão de intenções e funções. O padrão. qualquer entidade é usada para extrair entidades de forma livre. Embora os padrões usem entidades, um padrão não ajuda a detectar uma entidade aprendida por computador.  

Não espere ver uma previsão de entidade aprimorada se você recolher várias declarações em um único padrão. Para que as entidades simples sejam acionadas, você precisa adicionar declarações ou usar entidades de lista caso contrário, o padrão não será acionado.

## <a name="patterns-use-entity-roles"></a>Padrões de utilizam as funções de entidade
Se dois ou mais entidades num padrão contextualmente estejam relacionadas, padrões de utilizam entidade [funções](luis-concept-roles.md) para extrair informações contextuais sobre entidades.  

## <a name="prediction-scores-with-and-without-patterns"></a>Pontuações de previsão com e sem padrões
Tendo em conta suficiente expressões de exemplo, LUIS seria capaz de aumentar a confiança da previsão sem padrões. Padrões de aumentam a pontuação de confiança, sem ter de fornecer expressões com tantos.  

## <a name="pattern-matching"></a>Correspondência de padrões
Um padrão é correspondido com base em detetar as entidades dentro o padrão em primeiro lugar, em seguida, validar o resto das palavras e a ordem das palavras do padrão. Entidades são necessárias no padrão para um padrão corresponder. O padrão é aplicado ao nível do token, não no nível de caractere. 

## <a name="pattern-syntax"></a>Sintaxe de padrão
Sintaxe de padrão é um modelo para uma expressão. O modelo deve conter palavras e entidades que deseja correspondência com, bem como palavras e pontuação que pretende ignorar. É **não** uma expressão regular. 

Entidades em padrões são rodeadas por chavetas, `{}`. Padrões podem incluir entidades e entidades com funções. [Padrão. any](luis-concept-entity-types.md#patternany-entity) é uma entidade usada apenas em padrões. 

A sintaxe de padrão oferece suporte à seguinte sintaxe:

|Função|Sintaxe|Nível de aninhamento|Exemplo|
|--|--|--|--|
|entidade| {}-chaves|2|Onde é o formato {Entity-Name}?|
|opcional|[]-colchetes<BR><BR>Há um limite de 3 em níveis de aninhamento de qualquer combinação de opcional e agrupamento |2|O ponto de interrogação é opcional [?]|
|Agrupamento|()-parênteses|2|é (a \| b)|
|ou| \|-barra vertical (pipe)<br><br>Há um limite de 2 nas barras verticais (ou) em um grupo |-|Onde é formulário ({Form-Name-Short} &#x7c; {formulário-nome-longo} &#x7c; {formulário-número})| 
|início e/ou fim de expressão|^-cursor|-|^ iniciar o expressão<br>o expressão é concluído ^<br>^ correspondência literal estrita de expressão inteira com {Number} entidade ^|

## <a name="nesting-syntax-in-patterns"></a>Sintaxe de aninhamento em padrões

A sintaxe **opcional** , com colchetes, pode ser aninhada em dois níveis. Por exemplo: `[[this]is] a new form`. Este exemplo permite o seguinte declarações: 

|Exemplo de expressão aninhado opcional|Explicação|
|--|--|
|Este é um novo formulário|corresponde a todas as palavras no padrão|
|é um novo formulário|corresponde à palavra opcional externa e às palavras não opcionais no padrão|
|um novo formulário|corresponde apenas às palavras necessárias|

A sintaxe de **agrupamento** , com parênteses, pode ser aninhada em dois níveis. Por exemplo: `(({Entity1.RoleName1} | {Entity1.RoleName2} ) | {Entity2} )`. Esse recurso permite que qualquer uma das três entidades seja correspondida. 

Se Entity1 for um local com funções como Origin (Seattle) e Destination (Cairo) e a entidade 2 for um nome de compilação conhecido de uma entidade de lista (RedWest-C), o seguinte declarações mapearia para esse padrão:

|Exemplo de expressão de agrupamento aninhado|Explicação|
|--|--|
|RedWest-C|corresponde à entidade de agrupamento externo|
|Seattle|corresponde a uma das entidades de agrupamento internas|
|Cairo|corresponde a uma das entidades de agrupamento internas|

## <a name="nesting-limits-for-groups-with-optional-syntax"></a>Limites de aninhamento de grupos com sintaxe opcional

Uma combinação de **agrupamento** com sintaxe **opcional** tem um limite de 3 níveis de aninhamento.

|Permitido|Exemplo|
|--|--|
|Sim|( [ ( test1 &#x7c; test2 ) ] &#x7c; test3 )|
|Não|( [ ( [ test1 ] &#x7c; test2 ) ] &#x7c; test3 )|

## <a name="nesting-limits-for-groups-with-or-ing-syntax"></a>Limites de aninhamento para grupos com sintaxe or-ing

Uma combinação de **agrupamento** com a sintaxe **ou-ing** tem um limite de duas barras verticais.

|Permitido|Exemplo|
|--|--|
|Sim|(Test1 &#x7c; test2 &#x7c; (test3 &#x7c; TEST4))|
|Não|(Test1 &#x7c; test2 &#x7c; test3 &#x7c; (TEST4 &#x7c; TEST5)) |

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

As palavras do título do livro não são confusas para LUIS porque o LUIS sabe onde o título do livro termina, com base no padrão. qualquer entidade.

## <a name="explicit-lists"></a>Listas explícitas

Crie uma [lista explícita](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5ade550bd5b81c209ce2e5a8) por meio da API de criação para permitir a exceção quando:

* Seu padrão contém um [padrão. any](luis-concept-entity-types.md#patternany-entity)
* E essa sintaxe de padrão permite a possibilidade de uma extração de entidade incorreta com base no expressão. 

Por exemplo, suponha que tenha um padrão que contém ambas as sintaxes opcional, `[]`e a sintaxe de entidade, `{}`, combinados numa forma de extrair dados incorretamente.

Considere o padrão '[find] e-mail sobre {subject} [de {pessoa}]'.

Em expressões de com os seguintes, o **assunto** e **pessoa** entidade são extraídos corretamente e incorretamente:

|Expressão|Entidade|Extração correta|
|--|--|:--:|
|enviar um e-mail sobre cães Chris|assunto = cães<br>pessoa = Chris|✔|
|enviar um e-mail sobre o homem de La Mancha|assunto = o homem<br>pessoa = La Mancha|X|

Na tabela anterior, o assunto deve ser `the man from La Mancha` (um título de livro), mas como o assunto inclui a palavra `from`opcional, o título é previsto incorretamente. 

Para corrigir essa exceção para o padrão, adicione `the man from la mancha` como uma correspondência de lista explícita para a entidade de {subject} utilizando o [API de criação para lista explícita](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5ade550bd5b81c209ce2e5a8).

## <a name="syntax-to-mark-optional-text-in-a-template-utterance"></a>Sintaxe para marcar texto opcional numa expressão de modelo
Marcar o texto opcional na expressão usando a sintaxe de colchete de expressão regular, `[]`. O texto opcional pode aninhar Parênteses Retos até dois Retos apenas.

|Padrão com o texto opcional|Significado|
|--|--|
|`[find] email about {subject} [from {person}]`|`find`e `from {person}` são opcionais|
|' Você pode me ajudar a [?]|A marca de pontuação é opcional|

As marcas de`?`Pontuação `!`( `.`,,) devem ser ignoradas e você precisa ignorá-las usando a sintaxe de colchetes em padrões. 

## <a name="pattern-only-apps"></a>Aplicativos somente de padrão
Você pode criar um aplicativo com tentativas que não têm nenhum exemplo de declarações, contanto que haja um padrão para cada tentativa. Para um aplicativo somente de padrão, o padrão não deve conter entidades aprendidas por computador, pois elas exigem um exemplo de declarações. 

## <a name="best-practices"></a>Melhores práticas
Saiba mais [melhores práticas](luis-concept-best-practices.md).

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Saiba como implementar padrões neste tutorial](luis-tutorial-pattern.md)
