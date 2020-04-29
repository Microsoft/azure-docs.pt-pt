---
title: Boas declarações de exemplo - LUIS
description: As expressões são os elementos introduzidos pelo utilizador que a sua aplicação tem de interpretar. Colete frases que pensa que os utilizadores vão entrar. Inclua expressões que significam a mesma coisa, mas são construídas de forma diferente no comprimento da palavra e na colocação de palavras.
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: d851082a4ec4a003619826eeffd4f4b856a67824
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "81382278"
---
# <a name="understand-what-good-utterances-are-for-your-luis-app"></a>Compreenda quais são as boas declarações para a sua app LUIS

**As declarações** são informações do utilizador que a sua aplicação precisa de interpretar. Para treinar o LUIS para extrair intenções e entidades deles, é importante capturar uma variedade de diferentes expressões de exemplo para cada intenção. A aprendizagem ativa, ou o processo de continuar a treinar em novas expressões, é essencial para a inteligência aprendida por máquinas que a LUIS fornece.

Colete declarações que pensa que os utilizadores vão entrar. Incluem expressões, que significam a mesma coisa, mas são construídas de várias maneiras diferentes:

* Comprimento de expressão - curto, médio e longo para a sua aplicação de cliente
* Comprimento de palavra e frase
* Colocação de palavras - entidade no início, meio e fim da expressão
* Gramática
* Pluralização
* Consoante
* Escolha do substantivo e do verbo
* [Pontuação](luis-reference-application-settings.md#punctuation-normalization) - uma boa variedade usando correta, incorreta e sem gramática

## <a name="how-to-choose-varied-utterances"></a>Como escolher expressões variadas

Quando começa por [dar o exemplo](luis-how-to-add-example-utterances.md) ao seu modelo LUIS, eis alguns princípios a ter em mente.

### <a name="utterances-arent-always-well-formed"></a>As expressões nem sempre estão bem formadas.

Pode ser uma frase, como "Reserve um bilhete para Paris para mim", ou um fragmento de uma frase, como "Booking" ou "Paris flight".  Os utilizadores frequentemente cometem erros ortográficos. Ao planear a sua aplicação, considere se utiliza ou não [o Bing Spell Check](luis-tutorial-bing-spellcheck.md) para corrigir a entrada do utilizador antes de a passar para a LUIS.

Se não soletrar as declarações do utilizador, deve treinar o LUIS em expressões que incluem erros de ortografia e erros ortográficos.

### <a name="use-the-representative-language-of-the-user"></a>Utilize a linguagem representativa do utilizador

Ao escolher as palavras, esteja ciente de que o que pensa ser um termo ou frase comum pode não estar correto para o utilizador típico da sua aplicação cliente. Podem não ter experiência de domínio. Tenha cuidado ao utilizar termos ou frases que um utilizador só diria se fosse um perito.

### <a name="choose-varied-terminology-as-well-as-phrasing"></a>Escolha terminologia variada, bem como frases

Verá que mesmo que faça esforços para criar padrões de frasevariados, ainda vai repetir algum vocabulário.

Tome estas declarações exemplo:

|Expressões de exemplo|
|--|
|Como consigo um computador?|
|Onde consigo um computador?|
|Quero um computador, como é que o faço?|
|Quando posso ter um computador?|

O termo principal aqui, "computador", não é variado. Utilize alternativas como computador de secretária, portátil, estação de trabalho ou até mesmo apenas uma máquina. Luis pode inferir inteligentemente sinónimos do contexto, mas quando se criam expressões para o treino, é sempre melhor vary-los.

## <a name="example-utterances-in-each-intent"></a>Declarações exemplo em cada intenção

Cada intenção precisa de ter declarações exemplo, pelo menos 15. Se tiver uma intenção que não tenha nenhum exemplo, não poderá treinar o LUIS. Se tiver uma intenção com um ou muito poucos exemplos, luis pode não prever com precisão a intenção.

## <a name="add-small-groups-of-15-utterances-for-each-authoring-iteration"></a>Adicione pequenos grupos de 15 expressões para cada iteração de autor

Em cada iteração do modelo, não adicione uma grande quantidade de expressões. Adicione expressões em quantidades de 15. [Treinar,](luis-how-to-train.md) [publicar](luis-how-to-publish-app.md)e [testar](luis-interactive-test.md) novamente.

A LUIS constrói modelos eficazes com expressões que são cuidadosamente selecionadas pelo autor modelo LUIS. Adicionar demasiadas expressões não é valioso porque introduz confusão.

É melhor começar com algumas declarações, em seguida, [rever as declarações finais](luis-how-to-review-endpoint-utterances.md) para a previsão correta das intenções e a extração de entidades.

## <a name="utterance-normalization"></a>Normalização da expressão

A normalização da expressão é o processo de ignorar os efeitos da pontuação e dos diacríticos durante o treino e a previsão. Utilize [as definições](luis-reference-application-settings.md) de aplicação para controlar como a normalização da expressão afeta as previsões de expressão.

## <a name="utterance-normalization-for-diacritics-and-punctuation"></a>Normalização da expressão para diacríticos e pontuação

A normalização da expressão é definida quando cria ou importa a app porque é uma definição no ficheiro JSON da aplicação. As definições de normalização da expressão são desligadas por defeito.

Os críticos de diasão são marcas ou sinais dentro do texto, tais como:

```
İ ı Ş Ğ ş ğ ö ü
```

Se a sua aplicação ligar a normalização, as pontuações no painel de **teste,** nos testes de lote e nas consultas de ponto final mudarão para todas as expressões utilizando diacritics ou pontuação.

Ligue a normalização da expressão para diacríticos ou pontuação no `settings` ficheiro da aplicação LUIS JSON no parâmetro.

```JSON
"settings": [
    {"name": "NormalizePunctuation", "value": "true"},
    {"name": "NormalizeDiacritics", "value": "true"}
]
```

Normalizar **a pontuação** significa que antes que os seus modelos sejam treinados e antes que as suas consultas de ponto final sejam previstas, a pontuação será removida das expressões.

Normalizar **os diacríticos** substitui os personagens por diacritics em expressões por caracteres regulares. Por `Je parle français` exemplo: `Je parle francais`torna-se .

A normalização não significa que não verá pontuação e diacríticos no seu exemplo de palavras ou respostas de previsão, apenas que serão ignoradas durante o treino e a previsão.

### <a name="punctuation-marks"></a>Marcas de pontuação

Pontuação é um símbolo separado em LUIS. Uma expressão que contém um período no final contra uma expressão que não contenha um período no final são duas expressões separadas e pode obter duas previsões diferentes.

Se a pontuação não for normalizada, a LUIS não ignora as marcas de pontuação, por defeito, porque algumas aplicações de clientes podem colocar significado nestas marcas. Certifique-se de que as expressões do seu exemplo utilizam pontuação e nenhuma pontuação para que ambos os estilos devolvam as mesmas pontuações relativas.

Certifique-se de que o modelo lida com pontuação quer nas declarações de exemplo (tendo e não tendo pontuação) quer nos [padrões](luis-concept-patterns.md) em que é mais fácil ignorar a pontuação com a sintaxe especial:`I am applying for the {Job} position[.]`

Se a pontuação não tiver um significado específico na sua aplicação de cliente, considere [ignorar a pontuação](#utterance-normalization) normalizando a pontuação.

### <a name="ignoring-words-and-punctuation"></a>Ignorando palavras e pontuação

Se quiser ignorar palavras específicas ou pontuação em padrões, use um [padrão](luis-concept-patterns.md#pattern-syntax) com `[]`a sintaxe _ignore_ os suportes quadrados, .

## <a name="training-utterances"></a>Proclamações de treino

A formação é geralmente não determinista: a previsão de expressão pode variar ligeiramente entre versões ou aplicações.
Pode remover o treino não determinístico atualizando as `UseAllTrainingData` [definições](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) da versão API com o nome/par de valor para utilizar todos os dados de treino.

## <a name="testing-utterances"></a>Declarações de teste

Os desenvolvedores devem começar a testar a sua aplicação LUIS com tráfego real, enviando expressões para o URL [final de previsão.](luis-how-to-azure-subscription.md) Estas declarações são usadas para melhorar o desempenho das intenções e entidades com declarações de [Revisão.](luis-how-to-review-endpoint-utterances.md) Os testes submetidos ao painel de testes do site da LUIS não são enviados através do ponto final, pelo que não contribuem para a aprendizagem ativa.

## <a name="review-utterances"></a>Comentários proferidos

Depois de o seu modelo ser treinado, publicado e recebendo consultas [de ponto final,](luis-glossary.md#endpoint) [reveja as declarações](luis-how-to-review-endpoint-utterances.md) sugeridas pela LUIS. A LUIS seleciona expressões de ponto final que têm pontuações baixas para a intenção ou entidade.

## <a name="best-practices"></a>Melhores práticas

Reveja [as melhores práticas](luis-concept-best-practices.md) e aplique-as como parte do seu ciclo de autoria regular.

## <a name="label-for-word-meaning"></a>Etiqueta para significado de palavra

Se a palavra escolha ou arranjo de palavras for a mesma, mas não significa a mesma coisa, não a rotule com a entidade.

As seguintes declarações, `fair` a palavra é uma homografia. É escrito o mesmo, mas tem um significado diferente:

|Expressão|
|--|
|Que tipo de feiras municipais estão a acontecer na área de Seattle este verão?|
|A classificação atual para a feira de revisão de Seattle?|

Se quiser que uma entidade do evento encontre `fair` todos os dados do evento, rotule a palavra na primeira expressão, mas não na segunda.


## <a name="next-steps"></a>Passos seguintes
Consulte [as declarações de exemplo](luis-how-to-add-example-utterances.md) para informações sobre o treino de uma app LUIS para compreender as declarações dos utilizadores.

