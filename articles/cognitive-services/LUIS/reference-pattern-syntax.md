---
title: Referência de sintaxe padrão - LUIS
description: Criar entidades para extrair dados-chave das palavras dos utilizadores nas aplicações de Compreensão de Línguas (LUIS). Os dados extraídos são utilizados pela aplicação do cliente.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 04/14/2020
ms.openlocfilehash: 3caccd6766226ce68b371856b081b052c1033f71
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91542194"
---
# <a name="pattern-syntax"></a>Sintaxe de padrão

A sintaxe padrão é um modelo para uma expressão. O modelo deve conter palavras e entidades que pretende combinar, bem como palavras e [pontuação](luis-reference-application-settings.md#punctuation-normalization) que pretende ignorar. **Não** é uma expressão normal.

> [!CAUTION]
> Os padrões incluem apenas pais de entidades de aprendizagem automática, não subentidades.

As entidades em padrões estão rodeadas por suportes encaracolados, `{}` . Os padrões podem incluir entidades e entidades com funções. [Padrão.qualquer](luis-concept-entity-types.md#patternany-entity) um é uma entidade usada apenas em padrões.

A sintaxe de padrão suporta a seguinte sintaxe:

|Função|Syntax|Nível de nidificação|Exemplo|
|--|--|--|--|
|entidade| {} - suportes encaracolados|2|Onde está o formulário {nome de entidade}?|
|opcional|[] - parênteses quadrados<BR><BR>Há um limite de 3 nos níveis de nidificação de qualquer combinação de opcional e agrupamento |2|O ponto de interrogação é opcional [?]|
|agrupamento|() - Parênteses|2|é (a \| b)|
|ou| \| - barra vertical (tubo)<br><br>Há um limite de 2 nas barras verticais (Ou) em um grupo |-|Onde está a forma ({form-name-short} &#x7c; {form-name-long} &#x7c; {form-number})|
|início e/ou fim da expressão|^ - cuidado|-|^começar a expressão<br>a expressão está feita^<br>^jogo literal rigoroso de toda a expressão com {number} entidade^|

## <a name="nesting-syntax-in-patterns"></a>Nidificação sintaxe em padrões

A sintaxe **opcional,** com suportes quadrados, pode ser aninhada em dois níveis. Por exemplo: `[[this]is] a new form`. Este exemplo permite as seguintes expressões:

|Exemplo de expressão opcional aninhada|Explicação|
|--|--|
|esta é uma nova forma|corresponde a todas as palavras em padrão|
|é uma nova forma|corresponde palavra opcional exterior e palavras não opcionais em padrão|
|uma nova forma|corresponde apenas palavras necessárias|

A sintaxe **de agrupamento,** com parênteses, pode ser aninhada em dois níveis. Por exemplo: `(({Entity1.RoleName1} | {Entity1.RoleName2} ) | {Entity2} )`. Esta funcionalidade permite que qualquer uma das três entidades seja correspondida.

Se a Entidade1 é uma Localização com funções como origem (Seattle) e destino (Cairo) e a Entidade 2 é um nome de construção conhecido de uma entidade de lista (RedWest-C), as seguintes expressões mapeariam para este padrão:

|Exemplo de expressão de grupo aninhado|Explicação|
|--|--|
|RedWest-C|corresponde à entidade de agrupamento exterior|
|Seattle|corresponde a uma das entidades de agrupamento interno|
|Cairo|corresponde a uma das entidades de agrupamento interno|

## <a name="nesting-limits-for-groups-with-optional-syntax"></a>Limites de nidificação para grupos com sintaxe opcional

Uma combinação de **agrupamento** com sintaxe **opcional** tem um limite de 3 níveis de nidificação.

|Permitido|Exemplo|
|--|--|
|Yes|[ [ test1 &#x7c; test2 ] ] &#x7c; test3 )|
|No|[ [ [ test1 ] &#x7c; test2 ] ] &#x7c; test3 )|

## <a name="nesting-limits-for-groups-with-or-ing-syntax"></a>Limites de nidificação para grupos com sintaxe ou de ing

Uma combinação de **agrupamento** com sintaxe **ou-ing** tem um limite de 2 barras verticais.

|Permitido|Exemplo|
|--|--|
|Yes|( ensaio 1 &#x7c; &#x7c; de ensaio (ensaio 3 &#x7c; 4)|
|No|( ensaio 1 &#x7c; teste 2 &#x7c; teste3 &#x7c; (ensaio 4 &#x7c; teste5 ) |

## <a name="syntax-to-add-an-entity-to-a-pattern-template"></a>Sintaxe para adicionar uma entidade a um modelo de padrão
Para adicionar uma entidade ao modelo de padrão, rodeie o nome da entidade com aparelhos encaracolados, tais como `Who does {Employee} manage?` .

|Padrão com entidade|
|--|
|`Who does {Employee} manage?`|

## <a name="syntax-to-add-an-entity-and-role-to-a-pattern-template"></a>Sintaxe para adicionar uma entidade e papel a um modelo de padrão
Um papel de entidade é denotado como `{entity:role}` com o nome da entidade seguido por um cólon, em seguida, o nome de função. Para adicionar uma entidade com um papel no modelo de padrão, rodeie o nome da entidade e o nome da função com aparelhos encaracolados, tais como `Book a ticket from {Location:Origin} to {Location:Destination}` .

|Padrão com funções de entidade|
|--|
|`Book a ticket from {Location:Origin} to {Location:Destination}`|

## <a name="syntax-to-add-a-patternany-to-pattern-template"></a>Sintaxe para adicionar um padrão.qualquer ao modelo de padrão
O Padrão.qualquer entidade permite-lhe adicionar uma entidade de comprimento variável ao padrão. Enquanto o modelo de padrão for seguido, o padrão.qualquer um pode ter qualquer comprimento.

Para adicionar um **Padrão.qualquer** entidade no modelo de padrão, rodeie o Padrão.qualquer entidade com os aparelhos encaracolados, tais como `How much does {Booktitle} cost and what format is it available in?` .

|Padrão com Padrão.qualquer entidade|
|--|
|`How much does {Booktitle} cost and what format is it available in?`|

|Títulos de livro no padrão|
|--|
|Quanto custa **este livro** e em que formato está disponível?|
|Quanto **custa e** em que formato está disponível?|
|Quanto custa **o Curioso Incidente do Cão no Turno da Noite** e em que formato está disponível?|

As palavras do título do livro não são confusas para LUIS porque LUIS sabe onde termina o título do livro, com base no Padrão.qualquer entidade.

## <a name="explicit-lists"></a>Listas explícitas

criar uma [Lista Explícita](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5ade550bd5b81c209ce2e5a8) através da API de autoria para permitir a exceção quando:

* O seu padrão contém um [Padrão.qualquer](luis-concept-entity-types.md#patternany-entity)
* E essa sintaxe de padrão permite a possibilidade de uma extração incorreta de entidade com base na expressão.

Por exemplo, suponha que tem um padrão que contém sintaxe `[]` opcional, e sintaxe de entidade, `{}` combinado de forma a extrair dados incorretamente.

Considere o padrão '[encontrar] e-mail sobre {subject} [de {person}]'.

Nas seguintes declarações, o **sujeito** e a entidade **pessoa** são extraídos de forma correta e incorreta:

|Expressão|Entidade|Extração correta|
|--|--|:--:|
|e-mail sobre cães de Chris|assunto=cães<br>person=Chris|✔|
|e-mail sobre o homem de La Mancha|assunto=o homem<br>person=La Mancha|X|

Na tabela anterior, o assunto deve ser `the man from La Mancha` (título de livro) mas porque o assunto inclui a palavra `from` opcional, o título está incorretamente previsto.

Para corrigir esta exceção ao padrão, adicione `the man from la mancha` como uma correspondência de lista explícita para a entidade {subject} usando a [API de autor para lista explícita](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5ade550bd5b81c209ce2e5a8).

## <a name="syntax-to-mark-optional-text-in-a-template-utterance"></a>Sintaxe para marcar texto opcional em uma expressão de modelo
Marque o texto opcional na expressão utilizando a sintaxe do suporte quadrado de expressão regular, `[]` . O texto opcional pode nidificar os suportes quadrados até dois parênteses apenas.

|Padrão com texto opcional|Significado|
|--|--|
|`[find] email about {subject} [from {person}]`|`find` e `from {person}` são opcionais|
|"Pode ajudar-me[?]|A marca de pontuação é opcional|

As marcas de pontuação devem `?` `!` ser `.` ignoradas e é necessário ignorá-las utilizando a sintaxe quadrada dos suportes em padrões.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre padrões:

* [Como adicionar padrões](luis-how-to-model-intent-pattern.md)
* [Como adicionar padrão.qualquer entidade](luis-how-to-add-entities.md#add-a-patternany-entity)
* [Conceitos de padrões](luis-concept-patterns.md)

Entenda como o [sentimento](luis-reference-prebuilt-sentiment.md) é devolvido na resposta .json.