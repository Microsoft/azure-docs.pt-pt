---
title: Pontuação de confiança - serviços cognitivos da Microsoft | Documentos da Microsoft
titleSuffix: Azure
description: A pontuação de confiança indica a confiança de que a resposta é a correspondência de direita para a consulta de utilizador especificado.
services: cognitive-services
author: tulasim88
manager: pchoudh
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 02/21/2019
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: f144262f43396ac5d4f37939d35fca2645acc4bd
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56650177"
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
|90 - 100|A perto de uma correspondência exata de consulta de utilizador e uma pergunta KB|"Minhas alterações não forem atualizadas no artigo BDC após a publicação"|
|> 70|Alta confiança - normalmente, uma boa resposta que responde completamente a consulta do utilizador|"Publiquei meu KB, mas este não é atualizado"|
|50 - 70|Confiança média - normalmente, uma resposta muito boa que deve responder a intenção principal a consulta do utilizador|"Deve salvo meu atualizações antes da publicação do meu KB?"|
|30 - 50|Confiança de baixa - normalmente, uma resposta relacionada, que responde a parcialmente a intenção do usuário|"O que faz o salvamento e train?"|
|< a 30|Muito baixa confiança - normalmente, não à consulta do utilizador, mas tem algumas palavras ou frases correspondente |"Em que posso adicionar sinónimos para a minha BDC"|
|0|Nenhuma correspondência, para que a resposta não é devolvida.|"Quanto o serviço custa"|

## <a name="choose-a-score-threshold"></a>Escolha um limiar de pontuação
A tabela acima mostra as classificações que espera-se na maioria dos KBs. No entanto, uma vez que cada KB é diferente e tem diferentes tipos de palavras, objetivos e metas, que é recomendável testar e escolha o limiar que melhor funcione para si. É o predefinido e recomendado limiar que deve funcionar para a maioria dos KBs, **50**.

Ao escolher o limiar, tenha em atenção o equilíbrio entre a precisão e a cobertura e ajustar o limiar com base nos seus requisitos.

- Se **precisão** (ou de precisão) é mais importante para o seu cenário, em seguida, aumente o limiar. Dessa forma, sempre que retornar alguma resposta, é um CONFIDENT muito mais maiúsculas e minúsculas e muito mais provável que os utilizadores de resposta estão procurando. Neste caso, poderá acabar deixando mais perguntas sem resposta. *Por exemplo:* se fizer o limiar **70**, que pode perder algumas gostos de exemplos ambígua "o que é salvar e treinar?".

- Se **cobertura** (ou de solicitação de recolhimento) é mais importante – e deseja responder como tantas perguntas quanto possível, mesmo que haja apenas uma relação para pergunta - o usuário parcial, em seguida, o limiar inferior. Isso significa que lá pode ser mais casos em que a resposta não à consulta de real do usuário, mas oferece alguns outra resposta um pouco relacionada. *Por exemplo:* se fizer o limiar **30**, poderá dar respostas não muito relacionadas, como, responder com o exemplo acima, para consultas como "onde posso editar meu KB?"

> [!NOTE]
> Versões mais recentes do QnA Maker incluem melhoramentos de lógica de classificação e afetam o limiar. Sempre que atualizar o serviço, certifique-se de testar e ajustar o limiar, se necessário. Pode verificar a sua versão de serviço do QnA [aqui](https://www.qnamaker.ai/UserSettings)e veja como obter as atualizações mais recentes [aqui](../How-To/troubleshooting-runtime.md).

## <a name="improve-confidence-scores"></a>Melhorar as pontuações de confiança
Para melhorar a pontuação de confiança de uma determinada resposta a uma consulta de utilizador, pode adicionar a consulta de utilizador para a base de dados de conhecimento como uma alternativa pergunta em que a resposta. Também pode utilizar maiúsculas e minúsculas [alterações de palavras](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75fd) para adicionar sinónimos para palavras-chave em seu KB.


## <a name="similar-confidence-scores"></a>Pontuações de confiança semelhantes
Quando várias respostas tem uma pontuação de confiança semelhante, é provável que a consulta era demasiado genérico e, portanto, correspondentes com probabilidade igual com várias respostas. Tente estruturar seu QnAs melhor para que cada entidade do QnA tem uma intenção distinta.


## <a name="confidence-score-differences"></a>Diferenças de pontuação de confiança
A pontuação de confiança de uma resposta pode ser alteradas negligibly o teste e a versão publicada da base de dados de conhecimento, mesmo que o conteúdo é o mesmo. Isto acontece porque o conteúdo de teste e a base de dados de conhecimento publicada estão localizados em diferentes índices da Azure Search. Quando publica uma base de dados de conhecimento, move o conteúdo de perguntas e respostas da sua base de dados de conhecimento do índice de teste para um índice de produção no Azure search. Veja como o [publicar](../How-To/publish-knowledge-base.md) funciona a operação.

Se tiver uma base de dados de conhecimento em regiões diferentes, cada região usa seu próprio índice da Azure Search. Porque os índices diferentes são usados, as pontuações não será exatamente o mesmo. 


## <a name="no-match-found"></a>Nenhuma correspondência localizada
Quando for encontrada nenhuma correspondência de boa pelo classificador, é devolvida a classificação de confiança de 0,0 ou "None" e a resposta padrão não é "Nenhuma boa correspondência encontrada no artigo do BDC". Pode substituir esta [predefinido resposta](#change-default-answer) no código de aplicação ou bot chamar o ponto final. Como alternativa, também pode definir a resposta de substituição no Azure, e isso muda a predefinição para todas as bases de dados de conhecimento implementado num determinado serviço QnA Maker.

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

