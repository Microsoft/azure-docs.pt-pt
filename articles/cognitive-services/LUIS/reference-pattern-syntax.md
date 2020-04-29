---
title: Referência de sintaxe padrão - LUIS
description: Criar entidades para extrair dados-chave de declarações de utilizadores em aplicações de Compreensão de Idiomas (LUIS). Os dados extraídos são utilizados pela aplicação do cliente.
ms.topic: reference
ms.date: 04/14/2020
ms.author: diberry
ms.openlocfilehash: cc24667f43dfedc032f52c40fc5f8fe5c80bad70
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "81382149"
---
# <a name="pattern-syntax"></a>Sintaxe de padrão

Sintaxe padrão é um modelo para uma expressão. O modelo deve conter palavras e entidades que pretende combinar, bem como palavras e [pontuação](luis-reference-application-settings.md#punctuation-normalization) que pretende ignorar. **Não** é uma expressão regular.

> [!CAUTION]
> Os padrões apenas incluem pais de entidades aprendidas por máquinas, não subcomponentes.

As entidades em padrões estão rodeadas por suportes encaracolados, `{}`. Os padrões podem incluir entidades e entidades com funções. [Padrão.qualquer](luis-concept-entity-types.md#patternany-entity) é uma entidade usada apenas em padrões.

A sintaxe de padrão suporta a seguinte sintaxe:

|Função|Sintaxe|Nível de nidificação|Exemplo|
|--|--|--|--|
|entidade| {}- suportes encaracolados|2|Onde está o formulário {nome de entidade}?|
|opcional|[] - suportes quadrados<BR><BR>Existe um limite de 3 nos níveis de nidificação de qualquer combinação de opcional e agrupamento |2|O ponto de interrogação é opcional [?]|
|agrupamento|() - parênteses|2|é (um \| b)|
|ou| \|- barra vertical (tubo)<br><br>Há um limite de 2 nas barras verticais (Ou) em um grupo |-|Onde é formulário ({form-name-short} &#x7c; {form-name-long} &#x7c; {form-number})|
|início e/ou fim da expressão|^ - cuidado|-|^começar a expressão<br>a expressão é feita^<br>^estrita correspondência literal de total expressão com {number} entidade^|

## <a name="nesting-syntax-in-patterns"></a>Nidificação de sintaxe em padrões

A sintaxe **opcional,** com parênteses quadrados, pode ser aninhada dois níveis. Por exemplo: `[[this]is] a new form`. Este exemplo permite as seguintes declarações:

|Exemplo de expressão opcional aninhada|Explicação|
|--|--|
|esta é uma nova forma|corresponde a todas as palavras em padrão|
|é uma nova forma|corresponde à palavra opcional exterior e palavras não opcionais no padrão|
|uma nova forma|jogos requeridas palavras apenas|

A sintaxe de **agrupamento,** com parênteses, pode ser aninhada dois níveis. Por exemplo: `(({Entity1.RoleName1} | {Entity1.RoleName2} ) | {Entity2} )`. Esta funcionalidade permite que qualquer uma das três entidades seja correspondida.

Se a Entidade1 é um Local com funções como origem (Seattle) e destino (Cairo) e Entidade 2 é um nome de construção conhecido de uma entidade de lista (RedWest-C), as seguintes expressões traçariam para este padrão:

|Exemplo de proclamação de agrupamento aninhada|Explicação|
|--|--|
|RedWest-C|corresponde entidade de agrupamento exterior|
|Seattle|corresponde a uma das entidades de agrupamento interior|
|Cairo|corresponde a uma das entidades de agrupamento interior|

## <a name="nesting-limits-for-groups-with-optional-syntax"></a>Limites de nidificação para grupos com sintaxe opcional

Uma combinação de **agrupamento** com sintaxe **opcional** tem um limite de 3 níveis de nidificação.

|Permitido|Exemplo|
|--|--|
|Sim|( (teste1 &#x7c; teste2 ) ] &#x7c; teste3 )|
|Não|[ [test1 ] &#x7c; teste2 ] &#x7c; teste3 )|

## <a name="nesting-limits-for-groups-with-or-ing-syntax"></a>Limites de nidificação para grupos com sintaxe ou ing

Uma combinação de **agrupamento** com sintaxe **ou ing** tem um limite de 2 barras verticais.

|Permitido|Exemplo|
|--|--|
|Sim|(teste1 &#x7c; teste2 &#x7c; (teste 3 &#x7c; teste4 ) ) )|
|Não|(teste1 &#x7c; teste2 &#x7c; teste3 &#x7c; (test4 &#x7c; test5 ) ) ) ) |

## <a name="syntax-to-add-an-entity-to-a-pattern-template"></a>Sintaxe para adicionar uma entidade a um modelo de padrão
Para adicionar uma entidade ao modelo de padrão, rodeie `Who does {Employee} manage?`o nome da entidade com aparelhos encaracolados, tais como .

|Padrão com entidade|
|--|
|`Who does {Employee} manage?`|

## <a name="syntax-to-add-an-entity-and-role-to-a-pattern-template"></a>Sintaxe para adicionar uma entidade e papel a um modelo de padrão
Um papel de entidade `{entity:role}` é denotado como com o nome da entidade seguido por um cólon, em seguida, o nome do papel. Para adicionar uma entidade com um papel no modelo de padrão, rodeie `Book a ticket from {Location:Origin} to {Location:Destination}`o nome da entidade e o nome do papel com aparelhos encaracolados, tais como .

|Padrão com funções de entidade|
|--|
|`Book a ticket from {Location:Origin} to {Location:Destination}`|

## <a name="syntax-to-add-a-patternany-to-pattern-template"></a>Sintaxe para adicionar um padrão.qualquer ao modelo de padrão
O Padrão.qualquer entidade permite adicionar uma entidade de comprimento variado ao padrão. Desde que o modelo de padrão seja seguido, o padrão.qualquer pode ter qualquer comprimento.

Para adicionar um **Padrão.qualquer** entidade no modelo de padrão, rodeie o `How much does {Booktitle} cost and what format is it available in?`Padrão.qualquer entidade com os aparelhos encaracolados, tais como .

|Padrão com Padrão.qualquer entidade|
|--|
|`How much does {Booktitle} cost and what format is it available in?`|

|Títulos de livro no padrão|
|--|
|Quanto custa **este livro** e em que formato está disponível?|
|Quanto **custa** e em que formato está disponível?|
|Quanto custa **o Curioso Incidente do Cão no Night-Time** e em que formato está disponível?|

As palavras do título do livro não são confusas para luis porque LUIS sabe onde termina o título do livro, com base no Padrão.qualquer entidade.

## <a name="explicit-lists"></a>Listas explícitas

criar uma [Lista Explícita](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5ade550bd5b81c209ce2e5a8) através da API de autoria para permitir a exceção quando:

* O seu padrão contém um [Padrão.qualquer](luis-concept-entity-types.md#patternany-entity)
* E essa sintaxe de padrão permite a possibilidade de uma extração incorreta de entidade com base na expressão.

Por exemplo, suponha que tem um `[]`padrão que contém sintaxe opcional, e sintaxe de entidade, `{}`combinado de forma a extrair dados incorretamente.

Considere o padrão '[encontre] e-mail sobre {subject} [de {person}]'.

Nas seguintes declarações, o **sujeito** e a entidade **pessoa** são extraídos correta e incorretamente:

|Expressão|Entidade|Extração correta|
|--|--|:--:|
|e-mail sobre cães de Chris|sujeito=cães<br>pessoa=Chris|✔|
|e-mail sobre o homem de La Mancha|sujeito=o homem<br>pessoa=La Mancha|X|

Na tabela anterior, o sujeito `the man from La Mancha` deve ser (um título de livro) mas como o sujeito inclui a palavra `from`opcional, o título está incorretamente previsto.

Para corrigir esta exceção `the man from la mancha` ao padrão, adicione como uma correspondência explícita de lista para a entidade {subject} utilizando a [API de autoria para lista explícita](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5ade550bd5b81c209ce2e5a8).

## <a name="syntax-to-mark-optional-text-in-a-template-utterance"></a>Sintaxe para marcar texto opcional em uma expressão de modelo
Marque o texto opcional na expressão utilizando a `[]`sintaxe de suporte quadrado de expressão regular, . O texto opcional pode nidificar suportes quadrados até dois suportes.

|Padrão com texto opcional|Significado|
|--|--|
|`[find] email about {subject} [from {person}]`|`find`e `from {person}` são opcionais|
|"Pode ajudar-me[?]|A marca de pontuação é opcional|

As marcas de`?`pontuação `.`devem `!`ser ignoradas e é necessário ignorá-las utilizando a sintaxe de suporte quadrado em padrões.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre padrões:

* [Como adicionar padrões](luis-how-to-model-intent-pattern.md)
* [Como adicionar padrão.qualquer entidade](luis-how-to-add-entities.md#add-a-patternany-entity)
* [Conceitos de padrões](luis-concept-patterns.md)

Entenda como o [sentimento](luis-reference-prebuilt-sentiment.md) é devolvido na resposta .json.