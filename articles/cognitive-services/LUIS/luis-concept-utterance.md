---
title: Boas declarações de exemplo - LUIS
description: As expressões são os elementos introduzidos pelo utilizador que a sua aplicação tem de interpretar. Colete frases que pensa que os utilizadores vão introduzir. Inclua expressões que significam a mesma coisa, mas são construídas de forma diferente no comprimento de palavras e na colocação de palavras.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/19/2020
ms.openlocfilehash: 96b9754908f437ccf81e002e9e9dd17af0bab4e3
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2020
ms.locfileid: "95019080"
---
# <a name="understand-what-good-utterances-are-for-your-luis-app"></a>Compreenda o que são boas declarações para a sua app LUIS

**As declarações** são entradas do utilizador que a sua aplicação precisa de interpretar. Para treinar o LUIS para extrair intenções e entidades deles, é importante capturar uma variedade de diferentes exemplos para cada intenção. A aprendizagem ativa, ou o processo de continuar a treinar em novas expressões, é essencial para a inteligência de aprendizagem automática que o LUIS fornece.

Colete as expressões que pensa que os utilizadores vão introduzir. Inclua expressões, que significam a mesma coisa, mas são construídas de várias maneiras:

* Comprimento de expressão - curto, médio e longo para a sua aplicação ao cliente
* Comprimento da palavra e da frase
* Colocação de palavras - entidade no início, meio e fim da expressão
* Gramática
* Pluralização
* Estação Stemming
* Escolha do substantivo e do verbo
* [Pontuação](luis-reference-application-settings.md#punctuation-normalization) - uma boa variedade usando correta, incorreta, e sem gramática

## <a name="how-to-choose-varied-utterances"></a>Como escolher expressões variadas

Quando começa por [adicionar palavras de exemplo](./luis-how-to-add-entities.md) ao seu modelo LUIS, eis alguns princípios a ter em mente.

### <a name="utterances-arent-always-well-formed"></a>As expressões nem sempre estão bem formadas.

Pode ser uma frase, como "Reserve um bilhete para Paris para mim", ou um fragmento de uma frase, como "Booking" ou "Paris flight".  Os utilizadores geralmente cometem erros ortográficos. Ao planear a sua aplicação, considere se utiliza ou não [o Bing Spell Check](luis-tutorial-bing-spellcheck.md) para corrigir a entrada do utilizador antes de a passar para a LUIS.

Se não soletrar as declarações dos utilizadores, deve treinar o LUIS em expressões que incluem erros ortográficos e erros ortográficos.

### <a name="use-the-representative-language-of-the-user"></a>Utilize o idioma representativo do utilizador

Ao escolher expressões, esteja ciente de que o que você acha que é um termo ou frase comum pode não ser correto para o utilizador típico da sua aplicação de cliente. Podem não ter experiência de domínio. Tenha cuidado ao usar termos ou frases que um utilizador só diria se fosse um perito.

### <a name="choose-varied-terminology-as-well-as-phrasing"></a>Escolha terminologia variada, bem como fraseamento

Verá que, mesmo que faça esforços para criar padrões de frases variados, continuará a repetir algum vocabulário.

Vejamos estas declarações de exemplo:

|Expressões de exemplo|
|--|
|Como consigo um computador?|
|Onde consigo um computador?|
|Quero um computador, como faço?|
|Quando posso ter um computador?|

O termo principal aqui, "computador", não é variado. Utilize alternativas como computador de secretária, computador portátil, estação de trabalho ou até mesmo apenas máquina. LUIS pode inferir os sinónimos de contexto, mas quando se criam expressões para o treino, é sempre melhor variar.

## <a name="example-utterances-in-each-intent"></a>Exemplo de declarações em cada intenção

Cada intenção tem de ter palavras de exemplo, pelo menos 15. Se tiver uma intenção que não tenha qualquer exemplo de declarações, não poderá treinar o LUIS. Se tiver uma intenção com uma ou muito poucas declarações de exemplo, o LUIS pode não prever com precisão a intenção.

## <a name="add-small-groups-of-15-utterances-for-each-authoring-iteration"></a>Adicione pequenos grupos de 15 expressões para cada iteração de autoria

Em cada iteração do modelo, não adicione uma grande quantidade de expressões. Adicione expressões em quantidades de 15. [Treine,](luis-how-to-train.md) [publique](luis-how-to-publish-app.md)e [teste](luis-interactive-test.md) novamente.

A LUIS constrói modelos eficazes com expressões cuidadosamente selecionadas pelo autor do modelo LUIS. Adicionar muitas expressões não é valioso porque introduz confusão.

É melhor começar com algumas declarações, em seguida, [rever as declarações](luis-how-to-review-endpoint-utterances.md) de ponto final para a correta previsão de intenção e extração de entidades.

## <a name="utterance-normalization"></a>Normalização da expressão

A normalização da expressão é o processo de ignorar os efeitos de tipos de texto, como pontuação e diacríticos, durante o treino e previsão.

As definições de normalização da expressão são desligadas por defeito. Estas definições incluem:

* Formas de palavras
* Diacríticos
* Pontuação

Se ligar uma definição de normalização, as pontuações no painel **de teste,** nos testes de lote e nas consultas de ponto final mudarão para todas as expressões para essa definição de normalização.

Quando se clona uma versão no portal LUIS, as definições da versão continuam para a nova versão clonada.

Defina as definições da versão através do portal LUIS, na secção **'Gerir',** na página **'Definições de Aplicação'** ou na [API de Definições de Versão de Atualização](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings). Saiba mais sobre estas mudanças de normalização na [Referência.](luis-reference-application-settings.md)

### <a name="word-forms"></a>Formas de palavras

Normalizar **formas de palavra** ignora as diferenças de palavras que se expandem para além da raiz.

<a name="utterance-normalization-for-diacritics-and-punctuation"></a>

### <a name="diacritics"></a>Diacríticos

Os diacríticos são marcas ou sinais dentro do texto, tais como:

```
İ ı Ş Ğ ş ğ ö ü
```

### <a name="punctuation-marks"></a>Marcas de pontuação
Normalizar a **pontuação** significa que antes que os seus modelos sejam treinados e antes que as suas consultas de ponto final sejam previstas, a pontuação será removida das expressões.

Pontuação é um símbolo separado em LUIS. Uma expressão que contém um período no final contra uma expressão que não contenha um período no final são duas expressões separadas e podem obter duas previsões diferentes.

Se a pontuação não for normalizada, a LUIS não ignora as marcas de pontuação, por defeito, porque algumas aplicações de clientes podem colocar significado nestas marcas. Certifique-se de que as suas expressões de exemplo utilizam pontuação e nenhuma pontuação para que ambos os estilos devolvam as mesmas pontuações relativas.

Certifique-se de que o modelo lida com a pontuação quer nas expressões de exemplo (ter e não ter pontuação) quer nos [padrões](luis-concept-patterns.md) em que é mais fácil ignorar a pontuação com a sintaxe especial: `I am applying for the {Job} position[.]`

Se a pontuação não tiver um significado específico na aplicação do seu cliente, considere [ignorar a pontuação](#utterance-normalization) normalizando a pontuação.

### <a name="ignoring-words-and-punctuation"></a>Ignorando palavras e pontuação

Se quiser ignorar palavras específicas ou pontuação em padrões, utilize um [padrão](luis-concept-patterns.md#pattern-syntax) com a sintaxe _ignorante_ dos suportes quadrados, `[]` .

<a name="training-utterances"></a>

## <a name="training-with-all-utterances"></a>Treino com todas as palavras

A formação é geralmente não determinística: a previsão de expressão pode variar ligeiramente entre versões ou apps.
Pode remover a formação não determinística atualizando as [definições](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) de versão API com o `UseAllTrainingData` par de nome/valor para utilizar todos os dados de treino.

## <a name="testing-utterances"></a>Testando expressões

Os desenvolvedores devem começar a testar a sua aplicação LUIS com tráfego real, enviando expressões para o URL do ponto final de [previsão.](luis-how-to-azure-subscription.md) Estas expressões são usadas para melhorar o desempenho das intenções e entidades com [comentários de revisão.](luis-how-to-review-endpoint-utterances.md) Os testes submetidos com o painel de testes do site LUIS não são enviados através do ponto final, pelo que não contribuem para a aprendizagem ativa.

## <a name="review-utterances"></a>Rever declarações

Depois de o seu modelo ser treinado, publicado e recebendo consultas [de endpoint,](luis-glossary.md#endpoint) [reveja as declarações](luis-how-to-review-endpoint-utterances.md) sugeridas por LUIS. LUIS seleciona expressões de ponto final que têm pontuações baixas para a intenção ou entidade.

## <a name="best-practices"></a>Melhores práticas

Reveja as [melhores práticas](luis-concept-best-practices.md) e aplique-as como parte do seu ciclo regular de autoria.

## <a name="label-for-word-meaning"></a>Etiqueta para significado de palavra

Se a escolha da palavra ou arranjo de palavras for a mesma, mas não significa a mesma coisa, não rotule-a com a entidade.

As seguintes declarações, a palavra `fair` é um homográfico. Escreve-se o mesmo, mas tem um significado diferente:

|Expressão|
|--|
|Que tipo de feiras do condado estão acontecendo na área de Seattle este verão?|
|A classificação atual para a revisão de Seattle é justa?|

Se queria que uma entidade do evento encontrasse todos os dados do evento, rotule a palavra `fair` na primeira expressão, mas não na segunda.


## <a name="next-steps"></a>Próximos passos
Consulte [as declarações de exemplo](./luis-how-to-add-entities.md) para obter informações sobre a formação de uma aplicação LUIS para compreender as declarações dos utilizadores.