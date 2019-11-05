---
title: Previsão de ajuda de padrões-LUIS
titleSuffix: Azure Cognitive Services
description: Um padrão permite-lhe obter maior precisão numa intenção sem fornecer demasiadas expressões.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: diberry
ms.openlocfilehash: 4ca3a27a63f84eccb66b24d5046e2ae7d751387d
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73487554"
---
# <a name="patterns-improve-prediction-accuracy"></a>Padrões melhoram a precisão da previsão
Padrões são projetados para melhorar a precisão quando várias declarações são muito semelhantes.  Um padrão permite-lhe obter maior precisão numa intenção sem fornecer demasiadas expressões. 

## <a name="patterns-solve-low-intent-confidence"></a>Padrões resolvem a confiança de baixa intenção
Considere um aplicativo de recursos humanos que relata o gráfico organizacional em relação a um funcionário. Considerando o nome e a relação de um funcionário, o LUIS retorna os funcionários envolvidos. Considere um funcionário, Tom, com um nome de gerente Alice e uma equipe de subordinados denominada: Michael, Rebecca e Carl.

![Imagem do organograma](./media/luis-concept-patterns/org-chart.png)

|Expressões|Intenção prevista|Pontuação da intenção|
|--|--|--|
|Quem está subordinado a José?|GetOrgChart|.30|
|Quem é o subordinado de Tom?|GetOrgChart|.30|

Se um aplicativo tiver entre 10 e 20 declarações com comprimentos diferentes de frase, ordem de palavras diferente e até mesmo palavras diferentes (sinônimos de "subordinado", "gerenciar", "relatório"), LUIS poderá retornar uma pontuação de confiança baixa. Crie um padrão para ajudar a LUIS a entender a importância da ordem das palavras. 

Os padrões resolvem as seguintes situações: 

* A pontuação da intenção é baixa
* A intenção correta não é a pontuação superior, mas muito próximo da pontuação superior. 

## <a name="patterns-are-not-a-guarantee-of-intent"></a>Padrões não são uma garantia de intenção
Os padrões usam uma combinação de tecnologias de previsão. Definir uma intenção para um modelo expressão em um padrão não é uma garantia da previsão de intenção, mas é um sinal forte. 

<a name="patterns-do-not-improve-entity-detection"/></a>

## <a name="patterns-do-not-improve-machine-learned-entity-detection"></a>Padrões não melhoram a detecção de entidade aprendida por máquina

Um padrão destina-se principalmente a ajudar a previsão de intenções e funções. O padrão. qualquer entidade é usada para extrair entidades de forma livre. Embora os padrões usem entidades, um padrão não ajuda a detectar uma entidade aprendida por computador.  

Não espere ver uma previsão de entidade aprimorada se você recolher várias declarações em um único padrão. Para que as entidades simples sejam acionadas, você precisa adicionar declarações ou usar entidades de lista caso contrário, o padrão não será acionado.

## <a name="patterns-use-entity-roles"></a>Padrões usam funções de entidade
Se duas ou mais entidades em um padrão estiverem contextualmente relacionadas, os padrões usarão [funções](luis-concept-roles.md) de entidade para extrair informações contextuais sobre entidades.  

## <a name="prediction-scores-with-and-without-patterns"></a>Pontuações de previsão com e sem padrões
Devido ao exemplo de declarações suficiente, o LUIS seria capaz de aumentar a confiança de previsão sem padrões. Os padrões aumentam a pontuação de confiança sem precisar fornecer tantas declarações.  

## <a name="pattern-matching"></a>Correspondência de padrões
Um padrão é correspondido com base na detecção das entidades dentro do padrão primeiro e, em seguida, na validação do restante das palavras e da ordem de palavras do padrão. As entidades são necessárias no padrão para correspondência de um padrão. O padrão é aplicado no nível de token, não no nível de caractere. 

## <a name="pattern-syntax"></a>Sintaxe de padrão
A sintaxe de padrão é um modelo para um expressão. O modelo deve conter palavras e entidades que você deseja corresponder, bem como palavras e pontuações que você deseja ignorar. **Não** é uma expressão regular. 

As entidades em padrões são circundadas por chaves, `{}`. Os padrões podem incluir entidades e entidades com funções. [Padrão. any](luis-concept-entity-types.md#patternany-entity) é uma entidade usada apenas em padrões. 

A sintaxe de padrão oferece suporte à seguinte sintaxe:

|Função|Sintaxe|Nível de aninhamento|Exemplo|
|--|--|--|--|
|Entidade| {}-chaves|2|Onde é o formato {Entity-Name}?|
|Adicional|[]-colchetes<BR><BR>Há um limite de 3 em níveis de aninhamento de qualquer combinação de opcional e agrupamento |2|O ponto de interrogação é opcional [?]|
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

|Permitidos|Exemplo|
|--|--|
|Sim|([(Test1 &#x7c; test2)] &#x7c; test3)|
|Não|([([Test1] &#x7c; test2)] &#x7c; test3)|

## <a name="nesting-limits-for-groups-with-or-ing-syntax"></a>Limites de aninhamento para grupos com sintaxe or-ing

Uma combinação de **agrupamento** com a sintaxe **ou-ing** tem um limite de duas barras verticais.

|Permitidos|Exemplo|
|--|--|
|Sim|(Test1 &#x7c; test2 &#x7c; (test3 &#x7c; TEST4))|
|Não|(Test1 &#x7c; test2 &#x7c; test3 &#x7c; (TEST4 &#x7c; TEST5)) |

## <a name="syntax-to-add-an-entity-to-a-pattern-template"></a>Sintaxe para adicionar uma entidade a um modelo de padrão
Para adicionar uma entidade ao modelo padrão, coloque o nome da entidade entre chaves, como `Who does {Employee} manage?`. 

|Padrão com entidade|
|--|
|`Who does {Employee} manage?`|

## <a name="syntax-to-add-an-entity-and-role-to-a-pattern-template"></a>Sintaxe para adicionar uma entidade e uma função a um modelo de padrão
Uma função de entidade é denotada como `{entity:role}` com o nome da entidade seguido por dois-pontos e, em seguida, o nome da função. Para adicionar uma entidade com uma função no modelo de padrão, coloque o nome da entidade e o nome da função com chaves, como `Book a ticket from {Location:Origin} to {Location:Destination}`. 

|Padrão com funções de entidade|
|--|
|`Book a ticket from {Location:Origin} to {Location:Destination}`|

## <a name="syntax-to-add-a-patternany-to-pattern-template"></a>Sintaxe para adicionar um padrão. any ao modelo de padrão
O padrão. qualquer entidade permite que você adicione uma entidade de comprimento variável ao padrão. Desde que o modelo de padrão seja seguido, o padrão. Any pode ser qualquer comprimento. 

Para adicionar um **padrão. qualquer** entidade no modelo de padrão, circundando o padrão. qualquer entidade com as chaves, como `How much does {Booktitle} cost and what format is it available in?`.  

|Padrão com padrão. qualquer entidade|
|--|
|`How much does {Booktitle} cost and what format is it available in?`|

|Títulos de livros no padrão|
|--|
|Quanto custa **esse livro** e em qual formato ele está disponível?|
|Quanto **custa e** em que formato está disponível?|
|Quanto custa **o incidente curioso do cachorro no tempo noturno** e em qual formato ele está disponível?| 

As palavras do título do livro não são confusas para LUIS porque o LUIS sabe onde o título do livro termina, com base no padrão. qualquer entidade.

## <a name="explicit-lists"></a>Listas explícitas

Crie uma [lista explícita](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5ade550bd5b81c209ce2e5a8) por meio da API de criação para permitir a exceção quando:

* Seu padrão contém um [padrão. any](luis-concept-entity-types.md#patternany-entity)
* E essa sintaxe de padrão permite a possibilidade de uma extração de entidade incorreta com base no expressão. 

Por exemplo, suponha que você tenha um padrão contendo sintaxe opcional, `[]`e sintaxe de entidade, `{}`, combinados de forma a extrair dados incorretamente.

Considere o padrão ' [localizar] email sobre {Subject} [de {Person}] '.

No declarações a seguir, o **assunto** e a entidade **Person** são extraídos corretamente e incorretamente:

|Expressão|Entidade|Extração correta|
|--|--|:--:|
|email sobre cachorros de Chris|assunto = cachorros<br>pessoa = Chris|✔|
|email sobre o homem da la Mancha|assunto = o homem<br>Person = la Mancha|X|

Na tabela anterior, o assunto deve ser `the man from La Mancha` (um título de livro), mas como o assunto inclui a palavra opcional `from`, o título é incorretamente previsto. 

Para corrigir essa exceção ao padrão, adicione `the man from la mancha` como uma correspondência de lista explícita para a entidade {Subject} usando a [API de criação para a lista explícita](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5ade550bd5b81c209ce2e5a8).

## <a name="syntax-to-mark-optional-text-in-a-template-utterance"></a>Sintaxe para marcar texto opcional em um modelo expressão
Marque o texto opcional no expressão usando a sintaxe de colchete de expressão regular, `[]`. O texto opcional pode aninhar colchetes de até dois colchetes apenas.

|Padrão com texto opcional|Significado|
|--|--|
|`[find] email about {subject} [from {person}]`|`find` e `from {person}` são opcionais|
|' Você pode me ajudar a [?]|A marca de pontuação é opcional|

As marcas de Pontuação (`?`, `!`, `.`) devem ser ignoradas e você precisa ignorá-las usando a sintaxe de colchetes em padrões. 

## <a name="pattern-only-apps"></a>Aplicativos somente de padrão
Você pode criar um aplicativo com tentativas que não têm nenhum exemplo de declarações, contanto que haja um padrão para cada tentativa. Para um aplicativo somente de padrão, o padrão não deve conter entidades aprendidas por computador, pois elas exigem um exemplo de declarações. 

## <a name="best-practices"></a>Melhores práticas
Conheça [as práticas recomendadas](luis-concept-best-practices.md).

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba como implementar padrões neste tutorial](luis-tutorial-pattern.md)
