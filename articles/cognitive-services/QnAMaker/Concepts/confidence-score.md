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
ms.date: 11/19/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: e2f7136ea7b973386eeb746a74ad09fadb490e83
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74229110"
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

- Se a **precisão** (ou precisão) for mais importante para seu cenário, aumente o limite. Dessa forma, sempre que retornar alguma resposta, é um CONFIDENT muito mais maiúsculas e minúsculas e muito mais provável que os utilizadores de resposta estão procurando. Neste caso, poderá acabar deixando mais perguntas sem resposta. *Por exemplo:* se você tornar o limite **70**, poderá perder alguns exemplos ambíguos gosta de "o que é salvar e treinar?".

- Se a **cobertura** (ou recall) for mais importante – e você quiser responder o máximo de perguntas possível, mesmo se houver apenas uma relação parcial com a pergunta do usuário, diminua o limite. Isso significa que lá pode ser mais casos em que a resposta não à consulta de real do usuário, mas oferece alguns outra resposta um pouco relacionada. *Por exemplo:* se você tornar o limite **30**, poderá fornecer respostas para consultas como "onde posso editar meu KB?"

> [!NOTE]
> Versões mais recentes do QnA Maker incluem melhoramentos de lógica de classificação e afetam o limiar. Sempre que atualizar o serviço, certifique-se de testar e ajustar o limiar, se necessário. Você pode verificar a versão do serviço QnA [aqui](https://www.qnamaker.ai/UserSettings)e ver como obter as atualizações mais recentes [aqui](../How-To/set-up-qnamaker-service-azure.md#get-the-latest-runtime-updates).

## <a name="set-threshold"></a>Definir limite 

Defina a pontuação de limite como uma propriedade do [corpo JSON da API GenerateAnswer](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration). Isso significa que você o define para cada chamada para GenerateAnswer. 

Na estrutura do bot, defina a Pontuação como parte do objeto de opções com [C#](../how-to/metadata-generateanswer-usage.md?#use-qna-maker-with-a-bot-in-c) ou [node. js](../how-to/metadata-generateanswer-usage.md?#use-qna-maker-with-a-bot-in-nodejs).

## <a name="improve-confidence-scores"></a>Melhorar as pontuações de confiança
Para melhorar a pontuação de confiança de uma determinada resposta a uma consulta de utilizador, pode adicionar a consulta de utilizador para a base de dados de conhecimento como uma alternativa pergunta em que a resposta. Você também pode usar as [alterações de palavras](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/alterations/replace) que não diferenciam maiúsculas de minúsculas para adicionar sinônimos a palavras-chave em sua base de dados de conhecimento.


## <a name="similar-confidence-scores"></a>Pontuações de confiança semelhantes
Quando várias respostas tem uma pontuação de confiança semelhante, é provável que a consulta era demasiado genérico e, portanto, correspondentes com probabilidade igual com várias respostas. Tente estruturar seu QnAs melhor para que cada entidade do QnA tem uma intenção distinta.


## <a name="confidence-score-differences-between-test-and-production"></a>Diferenças de Pontuação de confiança entre teste e produção
A pontuação de confiança de uma resposta pode ser alteradas negligibly o teste e a versão publicada da base de dados de conhecimento, mesmo que o conteúdo é o mesmo. Isso ocorre porque o conteúdo do teste e a base de dados de conhecimento publicado estão localizados em diferentes índices de Pesquisa Cognitiva do Azure. 

O índice de teste contém todos os pares de QnA de suas bases de dados de conhecimento. Ao consultar o índice de teste, a consulta se aplica a todo o índice. em seguida, os resultados são restritos à partição para essa base de dados de conhecimento específica. Se os resultados da consulta de teste estiverem afetando negativamente a sua capacidade de validar a base de dados de conhecimento, você poderá:
* Organize sua base de dados de conhecimento usando uma das seguintes opções:
    * 1 recurso restrito a 1 KB: restrinja seu único recurso QnA (e o índice de teste Pesquisa Cognitiva do Azure resultante) a uma única base de dados de conhecimento. 
    * 2 recursos-1 para teste, 1 para produção: tenha dois recursos de QnA Maker, usando um para teste (com seus próprios índices de teste e produção) e outro para o produto (também tendo seus próprios índices de teste e produção)
* e sempre usam os mesmos parâmetros, como **[Top](../how-to/improve-knowledge-base.md#use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers)** ao consultar a base de dados de conhecimento de teste e de produção

Quando você publica uma base de dados de conhecimento, o conteúdo de perguntas e respostas de sua base de dados de conhecimento se move do índice de teste para um índice de produção no Azure Search. Veja como funciona a operação de [publicação](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base) .

Se você tiver uma base de dados de conhecimento em regiões diferentes, cada região usará seu próprio índice de Pesquisa Cognitiva do Azure. Como diferentes índices são usados, as pontuações não serão exatamente iguais. 


## <a name="no-match-found"></a>Nenhuma correspondência localizada
Quando for encontrada nenhuma correspondência de boa pelo classificador, é devolvida a classificação de confiança de 0,0 ou "None" e a resposta padrão não é "Nenhuma boa correspondência encontrada no artigo do BDC". Você pode substituir essa [resposta padrão](#change-default-answer) no bot ou no código do aplicativo que chama o ponto de extremidade. Como alternativa, também pode definir a resposta de substituição no Azure, e isso muda a predefinição para todas as bases de dados de conhecimento implementado num determinado serviço QnA Maker.

## <a name="change-default-answer"></a>Resposta de padrão de alteração

1. Vá para a [portal do Azure](https://portal.azure.com) e navegue até o grupo de recursos que representa o serviço de QnA Maker que você criou.

2. Clique para abrir o **serviço de aplicativo**.

    ![No portal do Azure, aceder ao serviço de aplicações para o QnA Maker](../media/qnamaker-concepts-confidencescore/set-default-response.png)

3. Clique em **configurações do aplicativo** e edite o campo **defaultanswer** para a resposta padrão desejada. Clique em **Guardar**.

    ![Selecione as definições da aplicação e, em seguida, editar DefaultAnswer para o QnA Maker](../media/qnamaker-concepts-confidencescore/change-response.png)

4. Reinicie o serviço de aplicações

    ![Depois de alterar o DefaultAnswer, reinicie o serviço de aplicações do QnA Maker](../media/qnamaker-faq/qnamaker-appservice-restart.png)


## <a name="next-steps"></a>Passos seguintes
> [!div class="nextstepaction"]
> [Fontes de dados com suporte](./data-sources-supported.md)

