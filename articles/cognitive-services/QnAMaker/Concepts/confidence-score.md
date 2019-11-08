---
title: Pontuação de confiança-QnA Maker
titleSuffix: Azure Cognitive Services
description: A pontuação de confiança indica a confiança de que a resposta é a correspondência correta para a consulta de usuário específica.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/07/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: a80c61efbcbff569f5fed53734def3979ed70616
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73820758"
---
# <a name="confidence-score-of-a-qna-maker-knowledge-base"></a>Pontuação de confiança de uma base de dados de conhecimento QnA Maker
Quando uma consulta de usuário é correspondida em uma base de dados de conhecimento, QnA Maker retorna respostas relevantes, juntamente com uma pontuação de confiança. Essa pontuação indica a confiança de que a resposta é a correspondência correta para a consulta de usuário fornecida. 

A pontuação de confiança é um número entre 0 e 100. Uma pontuação de 100 provavelmente é uma correspondência exata, enquanto uma pontuação de 0 significa que nenhuma resposta correspondente foi encontrada. Quanto maior a pontuação, maior a confiança na resposta. Para uma determinada consulta, pode haver várias respostas retornadas. Nesse caso, as respostas são retornadas em ordem de redução da Pontuação de confiança.

No exemplo a seguir, você pode ver uma entidade QnA, com duas perguntas. 


![Par de QnA de exemplo](../media/qnamaker-concepts-confidencescore/ranker-example-qna.png)

Para o exemplo acima – você pode esperar pontuações como o intervalo de Pontuação de exemplo abaixo-para tipos diferentes de consultas de usuário:


![Intervalo de Pontuação do classificador](../media/qnamaker-concepts-confidencescore/ranker-score-range.png)


A tabela a seguir indica a confiança típica associada a uma determinada pontuação.

|Valor da Pontuação|Significado da Pontuação|Exemplo de consulta|
|--|--|--|
|90-100|Uma correspondência quase exata da consulta de usuário e uma pergunta de KB|"Minhas alterações não são atualizadas em KB após a publicação"|
|> 70|Alta confiança – normalmente uma boa resposta que responde completamente à consulta do usuário|"Publiquei minha KB, mas ele não está atualizado"|
|50-70|Confiança média – normalmente uma resposta bastante boa que deve responder à intenção principal da consulta do usuário|"Devo salvar minhas atualizações antes de publicar minha KB?"|
|30 - 50|Baixa confiança – normalmente uma resposta relacionada, que responde parcialmente à intenção do usuário|"O que o Save and Train faz?"|
|< 30|Muito baixa confiança – normalmente não responde à consulta do usuário, mas tem algumas palavras ou frases correspondentes |"Onde posso adicionar sinônimos ao meu KB"|
|0|Sem correspondência, portanto, a resposta não é retornada.|"Quanto custa o serviço"|

## <a name="choose-a-score-threshold"></a>Escolher um limite de Pontuação
A tabela acima mostra as pontuações que são esperadas na maioria dos KBs. No entanto, como cada KB é diferente e tem tipos diferentes de palavras, intenções e metas-recomendamos que você teste e escolha o limite que melhor funciona para você. Por padrão, o limite é definido como 0, para que todas as respostas possíveis sejam retornadas. O limite recomendado que deve funcionar para a maioria dos KBs é de **50**.

Ao escolher seu limite, tenha em mente o equilíbrio entre precisão e cobertura e ajuste seu limite com base em seus requisitos.

- Se a **precisão** (ou precisão) for mais importante para seu cenário, aumente o limite. Dessa forma, sempre que você retornar uma resposta, será um caso muito mais confiável e muito mais provavelmente será a resposta que os usuários estão procurando. Nesse caso, você pode acabar deixando mais perguntas sem resposta. *Por exemplo:* se você tornar o limite **70**, poderá perder alguns exemplos ambíguos gosta de "o que é salvar e treinar?".

- Se a **cobertura** (ou recall) for mais importante – e você quiser responder o máximo de perguntas possível, mesmo se houver apenas uma relação parcial com a pergunta do usuário, diminua o limite. Isso significa que pode haver mais casos em que a resposta não responde à consulta real do usuário, mas fornece alguma outra resposta relativamente relacionada. *Por exemplo:* se você tornar o limite **30**, poderá fornecer respostas para consultas como "onde posso editar meu KB?"

> [!NOTE]
> As versões mais recentes do QnA Maker incluem melhorias na lógica de Pontuação e podem afetar o limite. Sempre que você atualizar o serviço, certifique-se de testar e ajustar o limite, se necessário. Você pode verificar a versão do serviço QnA [aqui](https://www.qnamaker.ai/UserSettings)e ver como obter as atualizações mais recentes [aqui](../How-To/set-up-qnamaker-service-azure.md#get-the-latest-runtime-updates).

## <a name="set-threshold"></a>Definir limite 

Defina a pontuação de limite como uma propriedade do [corpo JSON da API GenerateAnswer](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration). Isso significa que você o define para cada chamada para GenerateAnswer. 

Na estrutura do bot, defina a Pontuação como parte do objeto de opções com [C#](../how-to/metadata-generateanswer-usage.md?#use-qna-maker-with-a-bot-in-c) ou [node. js](../how-to/metadata-generateanswer-usage.md?#use-qna-maker-with-a-bot-in-nodejs).

## <a name="improve-confidence-scores"></a>Melhorar as pontuações de confiança
Para melhorar a pontuação de confiança de uma resposta específica para uma consulta de usuário, você pode adicionar a consulta de usuário à base de dados de conhecimento como uma pergunta alternativa sobre essa resposta. Você também pode usar as [alterações de palavras](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/alterations/replace) que não diferenciam maiúsculas de minúsculas para adicionar sinônimos a palavras-chave em sua base de dados de conhecimento.


## <a name="similar-confidence-scores"></a>Pontuações de confiança semelhantes
Quando várias respostas têm uma pontuação de confiança semelhante, é provável que a consulta seja muito genérica e, portanto, coincida com a probabilidade igual com várias respostas. Tente estruturar seu QnAs melhor para que cada entidade QnA tenha uma intenção distinta.


## <a name="confidence-score-differences"></a>Diferenças de Pontuação de confiança
A pontuação de confiança de uma resposta pode alterar negligibly entre o teste e a versão publicada da base de dados de conhecimento, mesmo que o conteúdo seja o mesmo. Isso ocorre porque o conteúdo do teste e a base de dados de conhecimento publicado estão localizados em diferentes índices de Pesquisa Cognitiva do Azure. Quando você publica uma base de dados de conhecimento, o conteúdo de perguntas e respostas de sua base de dados de conhecimento se move do índice de teste para um índice de produção no Azure Search. Veja como funciona a operação de [publicação](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base) .

Se você tiver uma base de dados de conhecimento em regiões diferentes, cada região usará seu próprio índice de Pesquisa Cognitiva do Azure. Como diferentes índices são usados, as pontuações não serão exatamente iguais. 


## <a name="no-match-found"></a>Nenhuma correspondência encontrada
Quando nenhuma correspondência adequada é encontrada pelo classificador, a pontuação de confiança de 0,0 ou "None" é retornada e a resposta padrão é "nenhuma correspondência adequada encontrada no KB". Você pode substituir essa [resposta padrão](#change-default-answer) no bot ou no código do aplicativo que chama o ponto de extremidade. Como alternativa, você também pode definir a resposta de substituição no Azure e isso altera o padrão para todas as bases de dados de conhecimento implantadas em um determinado serviço de QnA Maker.

## <a name="change-default-answer"></a>Alterar a resposta padrão

1. Vá para a [portal do Azure](https://portal.azure.com) e navegue até o grupo de recursos que representa o serviço de QnA Maker que você criou.

2. Clique para abrir o **serviço de aplicativo**.

    ![No portal do Azure, acesse o serviço de aplicativo para QnA Maker](../media/qnamaker-concepts-confidencescore/set-default-response.png)

3. Clique em **configurações do aplicativo** e edite o campo **defaultanswer** para a resposta padrão desejada. Clique em **Guardar**.

    ![Selecione Configurações do aplicativo e edite defaultanswer para QnA Maker](../media/qnamaker-concepts-confidencescore/change-response.png)

4. Reiniciar o serviço de aplicativo

    ![Depois de alterar a defaultanswer, reinicie o QnA Maker appservice](../media/qnamaker-faq/qnamaker-appservice-restart.png)


## <a name="next-steps"></a>Passos seguintes
> [!div class="nextstepaction"]
> [Fontes de dados com suporte](./data-sources-supported.md)

