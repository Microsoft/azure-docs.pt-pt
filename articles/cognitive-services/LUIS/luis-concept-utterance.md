---
title: Bom exemplo de declarações-LUIS
titleSuffix: Azure Cognitive Services
description: Declarações são entradas do usuário que seu aplicativo precisa interpretar. Colete frases que você considera que os usuários inserirão. Incluir declarações que significam a mesma coisa, mas que são construídas de forma diferente no tamanho da palavra e no posicionamento do Word.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: diberry
ms.openlocfilehash: 7412677773b60a1894a6ece7251e797bfddee091
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74280800"
---
# <a name="understand-what-good-utterances-are-for-your-luis-app"></a>Entenda o que são bons declarações para seu aplicativo LUIS

**Declarações** são entradas do usuário que seu aplicativo precisa interpretar. Para treinar o LUIS para extrair intenções e entidades deles, é importante capturar uma variedade de declarações de exemplo diferentes para cada tentativa. O aprendizado ativo, ou o processo de continuar a treinar no novo declarações, é essencial para a inteligência aprendida por computador fornecida pelo LUIS.

Colete declarações que você considera que os usuários inserirão. Inclua declarações, o que significa a mesma coisa, mas são construídos de várias maneiras diferentes:

* Expressão comprimento-curto, médio e longo para o aplicativo cliente
* Comprimento da palavra e da frase 
* Posicionamento do Word – entidade no início, no meio e no fim de expressão
* Verifica 
* Pluralização
* Lematização
* Opção substantivo e verbo
* Pontuação – uma boa variedade usando as condições corretas, incorretas e sem gramática

## <a name="how-to-choose-varied-utterances"></a>Como escolher declarações variados

Quando você começar pela primeira vez [adicionando o exemplo declarações](luis-how-to-add-example-utterances.md) ao seu modelo Luis, aqui estão alguns princípios para ter em mente.

### <a name="utterances-arent-always-well-formed"></a>Declarações nem sempre está bem formado

Pode ser uma frase, como "marcar um tíquete para Paris para mim", ou um fragmento de uma frase, como "reservas" ou "voo de Paris".  Os usuários geralmente fazem erros de ortografia. Ao planejar seu aplicativo, considere se você usa [verificação ortográfica do Bing](luis-tutorial-bing-spellcheck.md) para corrigir a entrada do usuário antes de passá-lo para Luis. 

Se você não verificar a ortografia do usuário declarações, deverá treinar o LUIS no declarações que inclua erros de digitação e grafia.

### <a name="use-the-representative-language-of-the-user"></a>Usar o idioma representativo do usuário

Ao escolher declarações, lembre-se de que o que você acredita é que um termo comum ou frase pode não estar correto para o usuário típico do seu aplicativo cliente. Eles podem não ter experiência com o domínio. Tenha cuidado ao usar termos ou frases que um usuário diria apenas se fosse um especialista.

### <a name="choose-varied-terminology-as-well-as-phrasing"></a>Escolher uma terminologia variada, bem como frases

Você descobrirá que mesmo se fizer esforços para criar padrões de sentenças variados, ainda será repetido algum vocabulário.

Siga estes exemplos de declarações:

|Expressões de exemplo|
|--|
|Como faço para obter um computador?|
|Onde obtenho um computador?|
|Eu quero obter um computador, como posso me aprofundar?|
|Quando posso ter um computador?| 

O termo principal aqui, "Computer", não é variado. Use alternativas como computador desktop, laptop, estação de trabalho ou até mesmo computador. O LUIS pode inferir de forma inteligente sinônimos do contexto, mas quando você cria declarações para treinamento, é sempre melhor adaptá-los.

## <a name="example-utterances-in-each-intent"></a>Exemplo de declarações em cada tentativa

Cada tentativa precisa ter um exemplo de declarações, pelo menos 15. Se você tiver uma intenção que não tenha nenhum exemplo de declarações, não será possível treinar o LUIS. Se você tiver uma intenção com um ou muito poucos exemplos de declarações, o LUIS poderá não prever a intenção com precisão. 

## <a name="add-small-groups-of-15-utterances-for-each-authoring-iteration"></a>Adicionar pequenos grupos de 15 declarações para cada iteração de criação

Em cada iteração do modelo, não adicione uma grande quantidade de declarações. Adicione declarações em quantidades de 15. [Treine](luis-how-to-train.md), [publique](luis-how-to-publish-app.md)e [teste](luis-interactive-test.md) novamente.  

O LUIS cria modelos eficientes com declarações que são cuidadosamente selecionados pelo autor do modelo de LUIS. Adicionar muitas declarações não é valioso porque apresenta confusão.

É melhor começar com alguns declarações e, em seguida, [examinar o ponto de extremidade declarações](luis-how-to-review-endpoint-utterances.md) para obter a previsão de intenção e a extração de entidade corretas.

## <a name="utterance-normalization"></a>Normalização de expressão

A normalização de expressão é o processo de ignorar os efeitos de Pontuação e sinais diacríticos durante o treinamento e a previsão.

## <a name="utterance-normalization-for-diacritics-and-punctuation"></a>Normalização de expressão para sinais diacríticos e Pontuação

A normalização de expressão é definida quando você cria ou importa o aplicativo porque ele é uma configuração no arquivo JSON do aplicativo. As configurações de normalização de expressão são desativadas por padrão. 

Os sinais diacríticos são marcas ou sinais dentro do texto, como: 

```
İ ı Ş Ğ ş ğ ö ü
```

Se o aplicativo ativar a normalização, as pontuações no painel de **teste** , nos testes de lote e nas consultas de ponto de extremidade serão alterados para todos os declarações usando sinais diacríticos ou pontuação.

Ative a normalização de expressão para diacríticos ou pontuação em seu arquivo de aplicativo JSON LUIS no parâmetro `settings`.

```JSON
"settings": [
    {"name": "NormalizePunctuation", "value": "true"},
    {"name": "NormalizeDiacritics", "value": "true"}
] 
```

A normalização da **Pontuação** significa que, antes que seus modelos sejam treinados e antes de suas consultas de ponto de extremidade serem previstas, a pontuação será removida do declarações. 

Normalizar **diacríticos** substitui os caracteres por sinais diacríticos em declarações por caracteres normais. Por exemplo: `Je parle français` se torna `Je parle francais`. 

A normalização não significa que você não verá Pontuação e sinais diacríticos em seu exemplo de declarações ou respostas de previsão, apenas que eles serão ignorados durante o treinamento e a previsão.


### <a name="punctuation-marks"></a>Sinais de Pontuação

Pontuação é um token separado no LUIS. Um expressão que contém um ponto no final versus um expressão que não contém um ponto no final são dois declarações separados e pode obter duas previsões diferentes. 

Se a pontuação não for normalizada, o LUIS não ignorará as marcas de pontuação, por padrão, porque alguns aplicativos cliente podem inserir significância nessas marcas. Verifique se o exemplo declarações usa a pontuação e nenhuma pontuação para que ambos os estilos retornem as mesmas pontuações relativas. 

Verifique se o modelo lida com pontuação no exemplo declarações (tendo e não tem pontuação) ou nos [padrões](luis-concept-patterns.md) onde é mais fácil ignorar a pontuação com a sintaxe especial: `I am applying for the {Job} position[.]`

Se a pontuação não tiver um significado específico em seu aplicativo cliente, considere [ignorar a pontuação](#utterance-normalization) normalizando a pontuação. 

### <a name="ignoring-words-and-punctuation"></a>Ignorando palavras e Pontuação

Se você quiser ignorar palavras específicas ou pontuação em padrões, use um [padrão](luis-concept-patterns.md#pattern-syntax) com a sintaxe de _ignorar_ colchetes, `[]`. 

## <a name="training-utterances"></a>Declarações de treinamento

O treinamento geralmente é não determinístico: a previsão expressão pode variar ligeiramente em versões ou aplicativos. Você pode remover o treinamento não determinístico atualizando a API de [configurações de versão](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) com o par nome/valor `UseAllTrainingData` para usar todos os dados de treinamento.

## <a name="testing-utterances"></a>Testando declarações 

Os desenvolvedores devem começar a testar seu aplicativo LUIS com tráfego real enviando declarações para a URL do [ponto de extremidade de previsão](luis-how-to-azure-subscription.md) . Esses declarações são usados para melhorar o desempenho das intenções e das entidades com a [revisão declarações](luis-how-to-review-endpoint-utterances.md). Os testes enviados com o painel de teste do site LUIS não são enviados por meio do ponto de extremidade e, portanto, não contribuem para o aprendizado ativo. 

## <a name="review-utterances"></a>Examinar declarações

Depois que o modelo for treinado, publicado e receber consultas de [ponto de extremidade](luis-glossary.md#endpoint) , [examine o declarações](luis-how-to-review-endpoint-utterances.md) sugerido pelo Luis. LUIS seleciona ponto de extremidade declarações com pontuações baixas para a intenção ou entidade. 

## <a name="best-practices"></a>Melhores práticas

Examine [as práticas recomendadas](luis-concept-best-practices.md) e aplique-as como parte do seu ciclo de criação regular.

## <a name="label-for-word-meaning"></a>Etiqueta para o significado da palavra

Se a opção do word ou disposição do word é o mesmo, mas não significa que a mesma coisa, não classifique-o com a entidade. 

As seguintes expressões, o word `fair` é um homograph. Ele é têm a mesma, mas tem um significado diferente:

|Expressão|
|--|
|Que tipo de fairs condado de estão acontecendo na área de Seattle neste Verão?|
|É a classificação atual para a revisão de Seattle justo?|

Se quisesse uma entidade de eventos para localizar todos os dados de eventos, a palavra da etiqueta `fair` a primeira expressão, mas não na segunda.


## <a name="next-steps"></a>Passos seguintes
Consulte [Adicionar exemplo declarações](luis-how-to-add-example-utterances.md) para obter informações sobre como treinar um aplicativo Luis para entender o declarações do usuário.

