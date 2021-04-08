---
title: Pontuação de confiança - QnA Maker
titleSuffix: Azure Cognitive Services
description: Quando uma consulta do utilizador é compatível com uma base de conhecimento, o QnA Maker devolve respostas relevantes, juntamente com uma pontuação de confiança.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 01/27/2020
ms.custom: seodec18
ms.openlocfilehash: fcfc70f7bfb9e2bb1b1a0edbffdc1282056e5fa7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102214053"
---
# <a name="the-confidence-score-of-an-answer"></a>A pontuação de confiança de uma resposta
Quando uma consulta do utilizador é compatível com uma base de conhecimento, o QnA Maker devolve respostas relevantes, juntamente com uma pontuação de confiança. Esta pontuação indica a confiança de que a resposta é a correspondência certa para a consulta do utilizador dada.

A pontuação de confiança é um número entre 0 e 100. Uma pontuação de 100 é provavelmente uma correspondência exata, enquanto uma pontuação de 0 significa, que nenhuma resposta correspondente foi encontrada. Quanto maior for a pontuação, maior a confiança na resposta. Para uma determinada consulta, pode haver várias respostas devolvidas. Nesse caso, as respostas são devolvidas por ordem de diminuição da pontuação de confiança.

No exemplo abaixo, pode ver uma entidade QnA, com 2 perguntas.


![Par QnA amostra](../media/qnamaker-concepts-confidencescore/ranker-example-qna.png)

Para o exemplo acima, pode esperar pontuações como a gama de pontuação da amostra abaixo para diferentes tipos de consultas de utilizador:


![Gama de pontuação ranker](../media/qnamaker-concepts-confidencescore/ranker-score-range.png)


A tabela seguinte indica confiança típica associada a uma determinada pontuação.

|Valor de pontuação|Significado de pontuação|Consulta de exemplo|
|--|--|--|
|90 - 100|Uma correspondência quase exata da consulta do utilizador e uma pergunta KB|"As minhas alterações não são atualizadas no KB após a publicação"|
|> 70|Alta confiança - tipicamente uma boa resposta que responde completamente à consulta do utilizador|"Publiquei o meu KB, mas não está atualizado"|
|50 - 70|Confiança média - tipicamente uma resposta bastante boa que deve responder à intenção principal da consulta do utilizador|"Devo guardar as minhas atualizações antes de publicar o meu KB?"|
|30 - 50|Baixa confiança - tipicamente uma resposta relacionada, que responde parcialmente à intenção do utilizador|"O que faz o salvamento e o comboio?"|
|< 30|Muito pouca confiança - normalmente não responde à consulta do utilizador, mas tem algumas palavras ou frases correspondentes |"Onde posso adicionar sinónimos ao meu KB"|
|0|Não há correspondência, por isso a resposta não é devolvida.|"Quanto custa o serviço"|

## <a name="choose-a-score-threshold"></a>Escolha um limiar de pontuação
A tabela acima mostra as pontuações que são esperadas na maioria dos KBs. No entanto, uma vez que cada KB é diferente, e tem diferentes tipos de palavras, intenções e objetivos, recomendamos que teste e escolha o limiar que melhor funciona para si. Por predefinição, o limiar é definido para 0, de modo a que todas as respostas possíveis sejam devolvidas. O limiar recomendado que deve funcionar para a maioria dos KBs, é **de 50**.

Ao escolher o seu limiar, tenha em mente o equilíbrio entre precisão e cobertura e ajuste o seu limiar com base nos seus requisitos.

- Se **a precisão** (ou precisão) for mais importante para o seu cenário, aumente o seu limiar. Desta forma, cada vez que devolver uma resposta, será um caso muito mais CONFIANTE, e muito mais provável de ser a resposta que os utilizadores procuram. Neste caso, podes acabar por deixar mais perguntas sem resposta. *Por exemplo:* se fizer o limiar **70,** poderá perder alguns exemplos ambíguos como "o que é salvar e treinar?".

- Se **a cobertura** (ou recordação) for mais importante e pretender responder ao maior número possível de perguntas, mesmo que haja apenas uma relação parcial com a pergunta do utilizador, então baixe o limiar. Isto significa que pode haver mais casos em que a resposta não responde à consulta real do utilizador, mas dá outra resposta um pouco relacionada. *Por exemplo:* se fizer o limiar **30,** pode dar respostas para perguntas como "Onde posso editar o meu KB?"

> [!NOTE]
> As versões mais recentes do QnA Maker incluem melhorias na lógica de pontuação, e podem afetar o seu limiar. Sempre que atualizar o serviço, certifique-se de testar e ajustar o limiar, se necessário. Pode consultar a sua versão do Serviço QnA [aqui,](https://www.qnamaker.ai/UserSettings)e ver como obter as últimas atualizações [aqui.](../How-To/configure-QnA-Maker-resources.md#get-the-latest-runtime-updates)

## <a name="set-threshold"></a>Definir limiar

Desavele a pontuação limiar como propriedade do [corpo JSON da API de GenerateAnswer](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration). Isto significa que o definiu para cada chamada para GenerateAnswer.

A partir da estrutura bot, desajei a pontuação como parte do objeto de opções com [C#](../how-to/metadata-generateanswer-usage.md?#use-qna-maker-with-a-bot-in-c) ou [Node.js](../how-to/metadata-generateanswer-usage.md?#use-qna-maker-with-a-bot-in-nodejs).

## <a name="improve-confidence-scores"></a>Melhorar os resultados de confiança
Para melhorar a pontuação de confiança de uma determinada resposta a uma consulta do utilizador, pode adicionar a consulta do utilizador à base de conhecimento como uma pergunta alternativa sobre essa resposta. Também pode utilizar alterações de [palavras insensíveis](/rest/api/cognitiveservices/qnamaker/alterations/replace) a casos para adicionar sinónimos a palavras-chave no seu KB.


## <a name="similar-confidence-scores"></a>Pontuações de confiança semelhantes
Quando várias respostas têm uma pontuação de confiança semelhante, é provável que a consulta fosse demasiado genérica e, portanto, correspondida com igual probabilidade com múltiplas respostas. Tente estruturar melhor os seus QnAs para que todas as entidades da QNA tenha uma intenção distinta.


## <a name="confidence-score-differences-between-test-and-production"></a>Diferenças de pontuação de confiança entre teste e produção
A pontuação de confiança de uma resposta pode mudar de forma insignificante entre o teste e a versão publicada da base de conhecimento, mesmo que o conteúdo seja o mesmo. Isto porque o conteúdo do teste e a base de conhecimentos publicada estão localizados em diferentes índices de Pesquisa Cognitiva Azure.

O índice de teste contém todos os pares QnA das suas bases de conhecimento. Ao consultar o índice de teste, a consulta aplica-se a todo o índice, então os resultados são restritos à partição para essa base de conhecimento específica. Se os resultados da consulta de teste estiverem a afetar negativamente a sua capacidade de validar a base de conhecimento, pode:
* Organizar a sua base de conhecimentos utilizando uma das seguintes:
    * 1 recurso restrito a 1 KB: restringir o seu único recurso QnA (e o índice de teste de pesquisa cognitiva Azure resultante) a uma única base de conhecimento.
    * 2 recursos - 1 para teste, 1 para produção: ter dois recursos da QnA Maker, utilizando um para testes (com índices de teste e produção próprios) e um para o produto (tendo também os seus próprios índices de teste e produção)
* e, sempre use os mesmos parâmetros, tais como **[top](../how-to/improve-knowledge-base.md#use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers)** ao consultar tanto a sua base de conhecimento de teste como de produção

Ao publicar uma base de conhecimento, o conteúdo de perguntas e respostas da sua base de conhecimentos passa do índice de teste para um índice de produção na pesquisa do Azure. Veja como funciona a [operação de publicação.](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base)

Se você tem uma base de conhecimento em diferentes regiões, cada região usa o seu próprio índice de Pesquisa Cognitiva Azure. Como são usados índices diferentes, as pontuações não serão exatamente as mesmas.


## <a name="no-match-found"></a>Nenhuma correspondência encontrada
Quando nenhum bom jogo é encontrado pelo ranker, a pontuação de confiança de 0.0 ou "Nenhuma" é devolvida e a resposta padrão é "Não há uma boa correspondência encontrada no KB". Pode anular esta [resposta predefinida](../How-To/metadata-generateanswer-usage.md) no bot ou código de aplicação que chama o ponto final. Alternadamente, também pode definir a resposta de substituição em Azure e isso altera o padrão para todas as bases de conhecimento implantadas num determinado serviço QnA Maker.

## <a name="next-steps"></a>Passos seguintes
> [!div class="nextstepaction"]
> [Melhores práticas](./best-practices.md)
