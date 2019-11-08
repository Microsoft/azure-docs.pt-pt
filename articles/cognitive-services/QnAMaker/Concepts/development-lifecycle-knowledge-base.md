---
title: Ciclo de vida da base de dados de conhecimento-QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker aprende melhor em um ciclo iterativo de alterações de modelo, exemplos de expressão, publicação e coleta de dados de consultas de ponto de extremidade.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 09/25/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 1cb5af13bdd309c762337e64ecde8538afc756b0
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73794847"
---
# <a name="knowledge-base-lifecycle-in-qna-maker"></a>Ciclo de vida da base de dados de conhecimento no QnA Maker
QnA Maker aprende melhor em um ciclo iterativo de alterações de modelo, exemplos de expressão, publicação e coleta de dados de consultas de ponto de extremidade. 

![Ciclo de criação](../media/qnamaker-concepts-lifecycle/kb-lifecycle.png)

## <a name="creating-a-qna-maker-knowledge-base"></a>Criando uma base de dados de conhecimento QnA Maker
QnA Maker ponto de extremidade da base de dados de conhecimento (KB) fornece uma resposta de melhor correspondência para uma consulta de usuário com base no conteúdo do KB. A criação de uma base de dados de conhecimento é uma ação única para configurar um repositório de conteúdo de perguntas, respostas e metadados associados. Uma base de dados de conhecimento pode ser criada rastreando conteúdo pré-existente, como páginas de perguntas frequentes, manuais de produto ou pares estruturados de p e r. Saiba como [criar uma base de dados de conhecimento](../quickstarts/create-publish-knowledge-base.md).

## <a name="testing-and-updating-the-knowledge-base"></a>Testando e atualizando a base de dados de conhecimento

A base de dados de conhecimento está pronta para teste depois de ser preenchida com conteúdo, seja editorial ou por meio de extração automática. O teste interativo pode ser feito no portal de QnA Maker por meio do painel de **teste** , inserindo consultas de usuário comuns e verificando se as respostas retornadas com a resposta correta e a pontuação de confiança suficiente. 

* **Para corrigir pontuações de baixa confiança**: Adicione perguntas alternativas. 
* **Quando uma consulta retorna incorretamente a [resposta padrão](confidence-score.md#change-default-answer)** : adicione novas respostas à pergunta correta. 

Esse loop rígido de atualização de teste continua até que você esteja satisfeito com os resultados. Saiba como [testar sua base de dados de conhecimento](../How-To/test-knowledge-base.md).

Para grandes KBs, use o teste automatizado com a [API generateAnswer](../how-to/metadata-generateanswer-usage.md#get-answer-predictions-with-the-generateanswer-api) e a propriedade de corpo `isTest`, que consulta a base de dados de conhecimento do `test` em vez da base de dados de conhecimento publicada. 

```json
{
  "question": "example question",
  "top": 3,
  "userId": "Default",
  "isTest": true
}
```

## <a name="publish-the-knowledge-base"></a>Publicar a base de dados de conhecimento
Quando terminar de testar a base de dados de conhecimento, você poderá publicá-la. A publicação envia por push a versão mais recente da base de dados de conhecimento testada para um índice do Azure Pesquisa Cognitiva dedicado que representa a base de dados de conhecimento **publicada** . Também cria um ponto final que pode ser chamado na sua aplicação ou chatbot.

Dessa forma, quaisquer alterações feitas na versão de teste da base de dados de conhecimento não afetarão a versão publicada que pode estar ativa em um aplicativo de produção.

Cada uma dessas bases de dados de conhecimento pode ser destinada a testes separadamente. Usando as APIs, você pode direcionar a versão de teste da base de dados de conhecimento com a propriedade de corpo `isTest` na chamada generateAnswer.

Saiba como [publicar sua base de dados de conhecimento](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base).

## <a name="monitor-usage"></a>Monitorizar a utilização
Para poder registrar os logs de chat de seu serviço, você precisará habilitar Application Insights ao [criar o serviço de QnA Maker](../How-To/set-up-qnamaker-service-azure.md).

Você pode obter várias análises do uso do seu serviço. Saiba mais sobre como usar o Application insights para obter [análises para seu serviço de QnA Maker](../How-To/get-analytics-knowledge-base.md).

Com base no que você aprende com sua análise, faça [as atualizações apropriadas para sua base de dados de conhecimento](../How-To/edit-knowledge-base.md).

## <a name="version-control-of-a-knowledge-base"></a>Controle de versão de uma base de dados de conhecimento

O controle de versão não é fornecido pelo QnA Maker. Você precisa exportar sua base de dados de conhecimento na página **configurações** e usar sua própria metodologia e ferramentas.

A exportação da base de dados de conhecimento para o formato TSV ou XLS é concluída na página **configurações** . 

Quando precisar voltar para uma versão específica, você precisará importar esse arquivo do seu sistema local. Na página **configurações** , importe o arquivo TSV ou xls. Isso substituirá as perguntas e respostas atualmente na base de dados de conhecimento pelo conteúdo do arquivo importado.   

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Pontuação de confiança](./confidence-score.md)