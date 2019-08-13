---
title: Ciclo de vida da base de dados de conhecimento - QnA Maker
titleSuffix: Azure Cognitive Services
description: A ferramenta QnA Maker Aprende melhor num ciclo iterativo de alterações no modelo, exemplos de expressão, publicação e a recolha de dados das consultas de ponto final.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 97a4673be2a611149806855e792c5bf1f7a0942a
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/12/2019
ms.locfileid: "68955177"
---
# <a name="knowledge-base-lifecycle-in-qna-maker"></a>Ciclo de vida de base de dados de conhecimento no QnA Maker
A ferramenta QnA Maker Aprende melhor num ciclo iterativo de alterações no modelo, exemplos de expressão, publicação e a recolha de dados das consultas de ponto final. 

![Ciclo de criação](../media/qnamaker-concepts-lifecycle/kb-lifecycle.png)

## <a name="creating-a-qna-maker-knowledge-base"></a>Criar uma base de dados de conhecimento do QnA Maker
O ponto de extremidade do QnA Maker base de dados de conhecimento (KB) fornece uma melhor correspondência resposta a uma consulta de utilizador com base no conteúdo do KB. Criar uma base de dados de conhecimento é uma ação única para configurar um repositório de conteúdo de perguntas, respostas e metadados associados. Uma base de dados de conhecimento pode ser criada ao rastreamento de conteúdo já existente, como páginas de perguntas frequentes, manuais de produto ou estruturados pares de perguntas E-A. Saiba como [criar uma base de dados de conhecimento](../How-To/create-knowledge-base.md).

## <a name="testing-and-updating-the-knowledge-base"></a>Teste e a atualizar a base de dados de conhecimento

A base de dados de conhecimento está pronta para teste depois que ele é preenchido com conteúdo, forma editorial ou por meio de extração automática. O teste interativo pode ser feito no portal de QnA Maker por meio do painel de **teste** , inserindo consultas de usuário comuns e verificando se as respostas retornadas com a resposta correta e a pontuação de confiança suficiente. 

* **Para corrigir pontuações de baixa confiança**: Adicione perguntas alternativas. 
* **Quando uma consulta retorna incorretamente a [resposta padrão](confidence-score.md#change-default-answer)** : adicione novas respostas à pergunta correta. 

Esse loop estreito de atualização de teste continua até estar satisfeito com os resultados. Saiba como [testar a sua base de dados de conhecimento](../How-To/test-knowledge-base.md).

Para grandes KBS, use o teste automatizado com a [API generateAnswer](../how-to/metadata-generateanswer-usage.md#get-answer-predictions-with-the-generateanswer-api) e a `isTest` Propriedade Body, `test` que consulta a base de dados de conhecimento em vez da base de dados de conhecimento publicada. 

```json
{
  "question": "example question",
  "top": 3,
  "userId": "Default",
  "isTest": true
}
```

## <a name="publish-the-knowledge-base"></a>Publicar a base de dados de conhecimento
Depois de concluída a base de dados de conhecimento de teste, pode publicá-lo. Publicar pushes a versão mais recente da base de dados de conhecimento testada para uma pesquisa do Azure dedicado de índice que representa a **publicados** base de dados de conhecimento. Também cria um ponto final que pode ser chamado na sua aplicação ou chatbot.

Dessa forma, qualquer alteração que está a ser feita para a versão de teste de base de dados de conhecimento não afetam a versão publicada que pode ser em direto num aplicativo de produção.

Cada uma destas bases de dados de conhecimento pode ser alvo para fins de teste em separado. Usando as APIs, você pode direcionar a versão de teste da base de `isTest` dados de conhecimento com a Propriedade Body na chamada generateAnswer.

Saiba como [publicar a sua base de dados de conhecimento](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base).

## <a name="monitor-usage"></a>Monitorizar a utilização
Para poder-se de que os logs de bate-papo do seu serviço de registo, terá de ativar o Application Insights quando [criar o serviço QnA Maker](../How-To/set-up-qnamaker-service-azure.md).

Pode obter várias análises de utilização do seu serviço. Saiba mais sobre como utilizar o application insights para obter [analytics para o serviço QnA Maker](../How-To/get-analytics-knowledge-base.md).

Com base nas informações que da sua análise, certifique-se adequado [atualizações à sua base de dados de conhecimento](../How-To/edit-knowledge-base.md).

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Pontuação de confiança](./confidence-score.md)

## <a name="see-also"></a>Consulte também 

[Base de dados de conhecimento](./knowledge-base.md)
[descrição geral do QnA Maker](../Overview/overview.md)
