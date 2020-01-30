---
title: Pontuação de confiança - QnA Maker
titleSuffix: Azure Cognitive Services
description: Uma base de conhecimento deve ser publicada. Uma vez publicada, a base de conhecimento é consultada no ponto final da previsão do tempo de execução utilizando a API geradaAnswer.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 01/27/2020
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: d901a803311805825c22503af6098e805a67e8f6
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76843457"
---
# <a name="the-confidence-score-of-an-answer"></a>A pontuação de confiança de uma resposta
Quando uma consulta de utilizador é comparada com uma base de dados de conhecimento, o QnA Maker retorna respostas relevantes, juntamente com uma pontuação de confiança. Esta pontuação indica a confiança de que a resposta é a correspondência de direita para a consulta de utilizador especificado.

A pontuação de confiança é um número entre 0 e 100. Uma pontuação igual a 100 provavelmente é uma correspondência exata, enquanto uma pontuação igual a 0 significa que nenhuma resposta correspondente foi encontrada. Quanto maior for a pontuação - maior confiança na resposta. Para uma determinada consulta, pode haver várias respostas devolvidas. Nesse caso, as respostas são devolvidas por ordem de diminuir a pontuação de confiança.

No exemplo a seguir, pode ver uma entidade do QnA, com perguntas de 2.


![Par de QnA de exemplo](../media/qnamaker-concepts-confidencescore/ranker-example-qna.png)

O exemplo acima-podem esperar as pontuações como o intervalo de pontuação de exemplo abaixo-para diferentes tipos de consultas do utilizador:


![Intervalo de pontuação classificador](../media/qnamaker-concepts-confidencescore/ranker-score-range.png)


A tabela seguinte indica típica confiança associada para uma determinada classificação.

|Valor de pontuação|Significado de pontuação|Consulta de exemplo|
|--|--|--|
|90 - 100|A perto de uma correspondência exata de consulta de utilizador e uma pergunta KB|"As minhas alterações não são atualizadas no KB após a publicação"|
|> 70|Alta confiança - normalmente, uma boa resposta que responde completamente a consulta do utilizador|"Publiquei meu KB, mas este não é atualizado"|
|50 - 70|Confiança média - normalmente, uma resposta muito boa que deve responder a intenção principal a consulta do utilizador|"Deve salvo meu atualizações antes da publicação do meu KB?"|
|30 - 50|Confiança de baixa - normalmente, uma resposta relacionada, que responde a parcialmente a intenção do usuário|"O que faz o salvamento e train?"|
|< a 30|Muito baixa confiança - normalmente, não à consulta do utilizador, mas tem algumas palavras ou frases correspondente |"Em que posso adicionar sinónimos para a minha BDC"|
|0|Nenhuma correspondência, para que a resposta não é devolvida.|"Quanto o serviço custa"|

## <a name="choose-a-score-threshold"></a>Escolha um limiar de pontuação
A tabela acima mostra as classificações que espera-se na maioria dos KBs. No entanto, uma vez que cada KB é diferente, e tem diferentes tipos de palavras, intenções e objetivos, recomendamos que teste e escolha o limiar que melhor funciona para si. Por padrão, o limiar é definido para 0, de modo que todas as respostas possíveis sejam devolvidas. O limiar recomendado que deve funcionar para a maioria dos KBs, é **de 50**.

Ao escolher o limiar, tenha em atenção o equilíbrio entre a precisão e a cobertura e ajustar o limiar com base nos seus requisitos.

- Se **precisão** (ou de precisão) é mais importante para o seu cenário, em seguida, aumente o limiar. Dessa forma, sempre que retornar alguma resposta, é um CONFIDENT muito mais maiúsculas e minúsculas e muito mais provável que os utilizadores de resposta estão procurando. Neste caso, poderá acabar deixando mais perguntas sem resposta. *Por exemplo:* se fizer o limiar **70**, que pode perder algumas gostos de exemplos ambígua "o que é salvar e treinar?".

- Se **cobertura** (ou de solicitação de recolhimento) é mais importante – e deseja responder como tantas perguntas quanto possível, mesmo que haja apenas uma relação para pergunta - o usuário parcial, em seguida, o limiar inferior. Isso significa que lá pode ser mais casos em que a resposta não à consulta de real do usuário, mas oferece alguns outra resposta um pouco relacionada. *Por exemplo:* se fizer esmola **30,** pode dar respostas para perguntas como "Onde posso editar o meu KB?"

> [!NOTE]
> Versões mais recentes do QnA Maker incluem melhoramentos de lógica de classificação e afetam o limiar. Sempre que atualizar o serviço, certifique-se de testar e ajustar o limiar, se necessário. Pode verificar a sua versão de serviço do QnA [aqui](https://www.qnamaker.ai/UserSettings)e veja como obter as atualizações mais recentes [aqui](../How-To/set-up-qnamaker-service-azure.md#get-the-latest-runtime-updates).

## <a name="set-threshold"></a>Definir limiar

Defino a pontuação limiar como uma propriedade do [corpo GenerateAnswer API JSON](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration). Isto significa que o definepara cada chamada para GenerateAnswer.

A partir da estrutura bot, detete [C#](../how-to/metadata-generateanswer-usage.md?#use-qna-maker-with-a-bot-in-c) a pontuação como parte do objeto de opções com ou [Node.js](../how-to/metadata-generateanswer-usage.md?#use-qna-maker-with-a-bot-in-nodejs).

## <a name="improve-confidence-scores"></a>Melhorar as pontuações de confiança
Para melhorar a pontuação de confiança de uma determinada resposta a uma consulta de utilizador, pode adicionar a consulta de utilizador para a base de dados de conhecimento como uma alternativa pergunta em que a resposta. Também pode utilizar alterações de [palavras](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/alterations/replace) insensíveis para adicionar sinónimos a palavras-chave no seu KB.


## <a name="similar-confidence-scores"></a>Pontuações de confiança semelhantes
Quando várias respostas tem uma pontuação de confiança semelhante, é provável que a consulta era demasiado genérico e, portanto, correspondentes com probabilidade igual com várias respostas. Tente estruturar seu QnAs melhor para que cada entidade do QnA tem uma intenção distinta.


## <a name="confidence-score-differences-between-test-and-production"></a>Diferenças de pontuação de confiança entre o teste e a produção
A pontuação de confiança de uma resposta pode ser alteradas negligibly o teste e a versão publicada da base de dados de conhecimento, mesmo que o conteúdo é o mesmo. Isto porque o conteúdo do teste e a base de conhecimento publicada estão localizados em diferentes índices de Pesquisa Cognitiva Azure.

O índice de teste contém todos os pares QnA das suas bases de conhecimento. Ao consultar o índice de teste, a consulta aplica-se a todo o índice, então os resultados são restritos à divisória para essa base de conhecimento específica. Se os resultados da consulta de teste estiverem a afetar negativamente a sua capacidade de validar a base de conhecimentos, pode:
* organize a sua base de conhecimentos utilizando um dos seguintes:
    * 1 recurso restrito a 1 KB: restringir o seu único recurso QnA (e o índice de teste de pesquisa cognitiva azure resultante) a uma única base de conhecimento.
    * 2 recursos - 1 para ensaio, 1 para produção: dispor de dois recursos QnA Maker, utilizando um para testes (com os seus próprios índices de ensaio e de produção) e um para o produto (que também tem os seus próprios índices de ensaio e de produção)
* e, use sempre os mesmos parâmetros, tais como **[o topo](../how-to/improve-knowledge-base.md#use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers)** ao consultar tanto a sua base de conhecimentos de teste como de produção

Ao publicar uma base de conhecimento, o conteúdo da sua base de conhecimento passa do índice de teste para um índice de produção na pesquisa Azure. Veja como funciona a operação de [publicação.](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base)

Se tiver uma base de conhecimento em diferentes regiões, cada região usa o seu próprio índice de Pesquisa Cognitiva Azure. Como são usados diferentes índices, as pontuações não serão exatamente as mesmas.


## <a name="no-match-found"></a>Nenhuma correspondência localizada
Quando for encontrada nenhuma correspondência de boa pelo classificador, é devolvida a classificação de confiança de 0,0 ou "None" e a resposta padrão não é "Nenhuma boa correspondência encontrada no artigo do BDC". Pode anular esta [resposta predefinida](../How-To/metadata-generateanswer-usage.md) no bot ou código de aplicação, ligando para o ponto final. Como alternativa, também pode definir a resposta de substituição no Azure, e isso muda a predefinição para todas as bases de dados de conhecimento implementado num determinado serviço QnA Maker.

## <a name="next-steps"></a>Passos seguintes
> [!div class="nextstepaction"]
> [Melhores práticas](./best-practices.md)

