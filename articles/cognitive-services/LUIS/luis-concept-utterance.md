---
title: Expressões de bom exemplo
titleSuffix: Language Understanding - Azure Cognitive Services
description: Expressões com são de entrada do usuário que a sua aplicação precisa para interpretar. Recolha as frases que acredita que os utilizadores introduzirem o. Inclua expressões com o que significa que a mesma coisa, mas são construídos de forma diferente de comprimento de palavra e colocação do word.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 01/02/2019
ms.author: diberry
ms.openlocfilehash: 2fd3416824189007bfdbe55d30907d9cb56f87ca
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59792543"
---
# <a name="understand-what-good-utterances-are-for-your-luis-app"></a>Compreender o que são expressões com boa para a sua aplicação LUIS

**Expressões com** são de entrada do usuário que a sua aplicação precisa para interpretar. Para preparar o LUIS para extrair as intenções e entidades dos mesmos, é importante capturar uma variedade de expressões de exemplo diferente para cada intenção. Aprendizagem ativa, ou o processo de continuar a dar formação em novas expressões, é essencial para inteligência aprendidas por máquina, que fornece o LUIS.

Recolha expressões com que acredita que os utilizadores introduzirem o. Inclua expressões de com, o que significa que a mesma coisa, mas são construídas de diversas formas diferentes:

* Comprimento da expressão - curto, médio e longo para a sua aplicação de cliente
* Comprimento de palavras ou frases 
* Colocação de palavra - entidade no início, meio e no fim da expressão
* Gramática 
* Pluralização
* Lematização
* Escolha de nome e o verbo
* Pontuação - várias boa usando correto, incorreta e não gramática

## <a name="how-to-choose-varied-utterances"></a>Como escolher expressões com variadas

Quando obter iniciado pela primeira vez [adição de expressões de exemplo](luis-how-to-add-example-utterances.md) para o modelo do LUIS, aqui estão alguns princípios a ter em conta.

### <a name="utterances-arent-always-well-formed"></a>Expressões com não estão sempre bem formados

Pode ser uma frase, como "Reservar um pedido de suporte para Paris para mim" ou um fragmento de uma frase, como "Reserva" ou "Voo Paris."  Os utilizadores fazem, muitas vezes, os erros de ortografia. Ao planejar a sua aplicação, considere se é ou não utilizar [verificação ortográfica do Bing](luis-tutorial-bing-spellcheck.md) para corrigir a entrada do usuário antes de o transmitir para LUIS. 

Se não escrever expressões de utilizador de verificação, deve preparar o LUIS em expressões que incluem erros de digitação e erros de ortografia.

### <a name="use-the-representative-language-of-the-user"></a>Utilize a linguagem representativa do utilizador

Ao escolher expressões com, lembre-se de que sua opinião é um termo comum ou frase pode não estar correto para o usuário típico da sua aplicação de cliente. Talvez não tenham experiência de domínio. Tenha cuidado ao usar termos ou frases que um usuário apenas diria se fossem um especialista.

### <a name="choose-varied-terminology-as-well-as-phrasing"></a>Escolha a terminologia variada, bem como frases

Descobrirá que, mesmo se tornar os esforços para criar padrões de sentença variadas, ainda será repetido alguns vocabulário.

Siga estas expressões de exemplo:

|Expressões de exemplo|
|--|
|como posso obter um computador?|
|Onde posso obter um computador?|
|Eu quero chegar um computador, como posso sobre isso?|
|Quando tiver um computador?| 

O termo principal aqui, o "computador", não é diversificados. Utilize alternativas, como o computador desktop, laptop, estação de trabalho ou até mesmo apenas máquina. LUIS inteligentemente infere sinónimos do contexto, mas ao criar expressões com formação, é ainda melhor variá-los.

## <a name="example-utterances-in-each-intent"></a>Expressões com de exemplo em cada intenção

Cada intenção tem de ter, pelo menos, 15, expressões de exemplo. Se tiver um objetivo que não tem quaisquer expressões de exemplo, não será capaz de preparar o LUIS. Se tiver um objetivo com um ou muito poucas expressões de exemplo, LUIS não prever com precisão a intenção. 

## <a name="add-small-groups-of-15-utterances-for-each-authoring-iteration"></a>Adicionar grupos pequenos de 15 expressões para cada iteração de criação

Em cada iteração do modelo, não adicione uma grande quantidade de expressões. Adicione expressões em quantidades de 15. [Train](luis-how-to-train.md), [publicar](luis-how-to-publish-app.md), e [testar](luis-interactive-test.md) novamente.  

LUIS baseia-se em vigor modelos com expressões com cuidadosamente selecionadas pelo autor do modelo do LUIS. Adicionar expressões com demasiados não é importante porque introduz confusão.  

É melhor começar com algumas expressões, em seguida, [rever expressões de ponto final](luis-how-to-review-endpoint-utterances.md) para extração correta de entidade e de predição de intenção.

## <a name="punctuation-marks"></a>Marcas de pontuação

LUIS não ignora marcas de pontuação, por predefinição, uma vez que alguns aplicativos de cliente podem efetuar significância essas marcas. Certifique-se que as expressões de exemplo utilizam o pontuação e sem pontuação para que ambos os estilos para devolver as mesmo pontuações relativas. Se a pontuação não tem nenhum significado específico na aplicação de cliente, considere [Ignorar pontuação](#ignoring-words-and-punctuation) usando padrões. 

## <a name="ignoring-words-and-punctuation"></a>A ignorar a palavras e pontuação

Se pretender ignorar palavras específicas ou pontuação na expressão de exemplo, utilize um [padrão](luis-concept-patterns.md#pattern-syntax) com o _ignorar_ sintaxe. 

## <a name="training-utterances"></a>Expressões de treinamento

O treinamento é geralmente não determinística: a predição de expressão poderia variam ligeiramente entre versões ou aplicações. Pode remover o treinamento determinística ao atualizar o [definições de versão](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) API com o `UseAllTrainingData` par utilizar todos os dados de treinamento de nome/valor.

## <a name="testing-utterances"></a>Teste de expressões 

Os desenvolvedores devem começar a testar seus aplicativos de LUIS com tráfego real através do envio expressões com o [ponto final de predição](luis-how-to-azure-subscription.md) URL. Estas expressões são utilizados para melhorar o desempenho dos objetivos e entidades com [rever expressões com](luis-how-to-review-endpoint-utterances.md). Testes submetidos com o site do LUIS painel de teste não são enviados através do ponto final e não contribuem para a aprendizagem ativa. 

## <a name="review-utterances"></a>Expressões de revisão

Depois do modelo estiver preparado, publicada e de recebimento [ponto final](luis-glossary.md#endpoint) consultas, [rever as expressões](luis-how-to-review-endpoint-utterances.md) sugerido pelo LUIS. LUIS seleciona expressões com ponto final com pontuações de baixas para a intenção ou a entidade. 

## <a name="best-practices"></a>Melhores práticas

Revisão [melhores práticas](luis-concept-best-practices.md) e aplicá-las como parte do seu ciclo de criação regular.

## <a name="next-steps"></a>Passos Seguintes
Ver [adicionar expressões de exemplo](luis-how-to-add-example-utterances.md) para obter informações sobre uma aplicação LUIS para compreender as expressões de utilizador de treinamento.

