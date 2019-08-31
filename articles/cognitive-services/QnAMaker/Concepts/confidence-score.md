---
title: Pontuação de confiança-QnA Maker
titleSuffix: Azure Cognitive Services
description: A pontuação de confiança indica a confiança de que a resposta é a correspondência de direita para a consulta de utilizador especificado.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 08/30/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 48b38e88ea6d0d99a0734aa36716020ced6e3d78
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/30/2019
ms.locfileid: "70193614"
---
# <a name="confidence-score-of-a-qna-maker-knowledge-base"></a>Pontuação de confiança de uma base de dados de conhecimento do QnA Maker
Quando uma consulta de utilizador é comparada com uma base de dados de conhecimento, o QnA Maker retorna respostas relevantes, juntamente com uma pontuação de confiança. Esta pontuação indica a confiança de que a resposta é a correspondência de direita para a consulta de utilizador especificado. 

A pontuação de confiança é um número entre 0 e 100. Uma pontuação igual a 100 provavelmente é uma correspondência exata, enquanto uma pontuação igual a 0 significa que nenhuma resposta correspondente foi encontrada. Quanto maior for a pontuação - maior confiança na resposta. Para uma determinada consulta, pode haver várias respostas devolvidas. Nesse caso, as respostas são devolvidas por ordem de diminuir a pontuação de confiança.

No exemplo a seguir, pode ver uma entidade do QnA, com perguntas de 2. 


![Par de QnA de exemplo](../media/qnamaker-concepts-confidencescore/ranker-example-qna.png)

O exemplo acima-podem esperar as pontuações como o intervalo de pontuação de exemplo abaixo-para diferentes tipos de consultas do utilizador:


![Intervalo de pontuação classificador](../media/qnamaker-concepts-confidencescore/ranker-score-range.png)


A tabela seguinte indica típica confiança associada para uma determinada classificação.

|Valor de pontuação|Significado de pontuação|Consulta de exemplo|
|--|--|--|
|90 - 100|A perto de uma correspondência exata de consulta de utilizador e uma pergunta KB|"Minhas alterações não são atualizadas em KB após a publicação"|
|> 70|Alta confiança - normalmente, uma boa resposta que responde completamente a consulta do utilizador|"Publiquei meu KB, mas este não é atualizado"|
|50 - 70|Confiança média - normalmente, uma resposta muito boa que deve responder a intenção principal a consulta do utilizador|"Deve salvo meu atualizações antes da publicação do meu KB?"|
|30 - 50|Confiança de baixa - normalmente, uma resposta relacionada, que responde a parcialmente a intenção do usuário|"O que faz o salvamento e train?"|
|< a 30|Muito baixa confiança - normalmente, não à consulta do utilizador, mas tem algumas palavras ou frases correspondente |"Em que posso adicionar sinónimos para a minha BDC"|
|0|Nenhuma correspondência, para que a resposta não é devolvida.|"Quanto o serviço custa"|

## <a name="choose-a-score-threshold"></a>Escolha um limiar de pontuação
A tabela acima mostra as classificações que espera-se na maioria dos KBs. No entanto, como cada KB é diferente e tem tipos diferentes de palavras, intenções e metas-recomendamos que você teste e escolha o limite que melhor funciona para você. Por padrão, o limite é definido como 0, para que todas as respostas possíveis sejam retornadas. O limite recomendado que deve funcionar para a maioria dos KBs é de **50**.

Ao escolher o limiar, tenha em atenção o equilíbrio entre a precisão e a cobertura e ajustar o limiar com base nos seus requisitos.

- Se **precisão** (ou de precisão) é mais importante para o seu cenário, em seguida, aumente o limiar. Dessa forma, sempre que retornar alguma resposta, é um CONFIDENT muito mais maiúsculas e minúsculas e muito mais provável que os utilizadores de resposta estão procurando. Neste caso, poderá acabar deixando mais perguntas sem resposta. *Por exemplo:* se fizer o limiar **70**, que pode perder algumas gostos de exemplos ambígua "o que é salvar e treinar?".

- Se **cobertura** (ou de solicitação de recolhimento) é mais importante – e deseja responder como tantas perguntas quanto possível, mesmo que haja apenas uma relação para pergunta - o usuário parcial, em seguida, o limiar inferior. Isso significa que lá pode ser mais casos em que a resposta não à consulta de real do usuário, mas oferece alguns outra resposta um pouco relacionada. *Por exemplo:* se você tornar o limite **30**, poderá fornecer respostas para consultas como "onde posso editar meu KB?"

> [!NOTE]
> Versões mais recentes do QnA Maker incluem melhoramentos de lógica de classificação e afetam o limiar. Sempre que atualizar o serviço, certifique-se de testar e ajustar o limiar, se necessário. Pode verificar a sua versão de serviço do QnA [aqui](https://www.qnamaker.ai/UserSettings)e veja como obter as atualizações mais recentes [aqui](../How-To/set-up-qnamaker-service-azure.md#get-latest-runtime-updates).

## <a name="set-threshold"></a>Definir limite 

Defina a pontuação de limite como uma propriedade do [corpo JSON da API GenerateAnswer](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration). Isso significa que você o define para cada chamada para GenerateAnswer. 

Na estrutura do bot, defina a Pontuação como parte do objeto de opções com [C#](../how-to/metadata-generateanswer-usage.md?#use-qna-maker-with-a-bot-in-c) ou [node. js](../how-to/metadata-generateanswer-usage.md?#use-qna-maker-with-a-bot-in-nodejs).

## <a name="improve-confidence-scores"></a>Melhorar as pontuações de confiança
Para melhorar a pontuação de confiança de uma determinada resposta a uma consulta de utilizador, pode adicionar a consulta de utilizador para a base de dados de conhecimento como uma alternativa pergunta em que a resposta. Você também pode usar as [alterações de palavras](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/alterations/replace) que não diferenciam maiúsculas de minúsculas para adicionar sinônimos a palavras-chave em sua base de dados de conhecimento.


## <a name="similar-confidence-scores"></a>Pontuações de confiança semelhantes
Quando várias respostas tem uma pontuação de confiança semelhante, é provável que a consulta era demasiado genérico e, portanto, correspondentes com probabilidade igual com várias respostas. Tente estruturar seu QnAs melhor para que cada entidade do QnA tem uma intenção distinta.


## <a name="confidence-score-differences"></a>Diferenças de pontuação de confiança
A pontuação de confiança de uma resposta pode ser alteradas negligibly o teste e a versão publicada da base de dados de conhecimento, mesmo que o conteúdo é o mesmo. Isto acontece porque o conteúdo de teste e a base de dados de conhecimento publicada estão localizados em diferentes índices da Azure Search. Quando você publica uma base de dados de conhecimento, o conteúdo de perguntas e respostas de sua base de dados de conhecimento se move do índice de teste para um índice de produção no Azure Search. Veja como funciona a operação de [publicação](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base) .

Se você tiver uma base de dados de conhecimento em regiões diferentes, cada região usará seu próprio índice Azure Search. Como diferentes índices são usados, as pontuações não serão exatamente iguais. 


## <a name="no-match-found"></a>Nenhuma correspondência localizada
Quando for encontrada nenhuma correspondência de boa pelo classificador, é devolvida a classificação de confiança de 0,0 ou "None" e a resposta padrão não é "Nenhuma boa correspondência encontrada no artigo do BDC". Você pode substituir essa [resposta padrão](#change-default-answer) no bot ou no código do aplicativo que chama o ponto de extremidade. Como alternativa, também pode definir a resposta de substituição no Azure, e isso muda a predefinição para todas as bases de dados de conhecimento implementado num determinado serviço QnA Maker.

## <a name="change-default-answer"></a>Resposta de padrão de alteração

1. Vá para o [portal do Azure](https://portal.azure.com) e navegue para o grupo de recursos que representa o serviço QnA Maker que criou.

2. Clique para abrir o **serviço de aplicações**.

    ![No portal do Azure, aceder ao serviço de aplicações para o QnA Maker](../media/qnamaker-concepts-confidencescore/set-default-response.png)

3. Clique em **as configurações do aplicativo** e editar a **DefaultAnswer** campo para a resposta padrão pretendido. Clique em **Guardar**.

    ![Selecione as definições da aplicação e, em seguida, editar DefaultAnswer para o QnA Maker](../media/qnamaker-concepts-confidencescore/change-response.png)

4. Reinicie o serviço de aplicações

    ![Depois de alterar o DefaultAnswer, reinicie o serviço de aplicações do QnA Maker](../media/qnamaker-faq/qnamaker-appservice-restart.png)


## <a name="next-steps"></a>Passos Seguintes
> [!div class="nextstepaction"]
> [Origens de dados suportadas](./data-sources-supported.md)

