---
title: Ciclo de vida da base de conhecimento - QnA Maker
description: O QnA Maker aprende melhor num ciclo iterativo de mudanças de modelos, exemplos de palavras, publicação e recolha de dados de consultas de ponto final.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 09/01/2020
ms.openlocfilehash: e52e7151bc30a19bd6f6041d52effdd799a87c99
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "91776974"
---
# <a name="knowledge-base-lifecycle-in-qna-maker"></a>Ciclo de vida base de conhecimento no Fabricante QnA
O QnA Maker aprende melhor num ciclo iterativo de mudanças de modelos, exemplos de palavras, publicação e recolha de dados de consultas de ponto final.

![Ciclo de criação](../media/qnamaker-concepts-lifecycle/kb-lifecycle.png)

## <a name="creating-a-qna-maker-knowledge-base"></a>Criar uma base de dados de conhecimento do Criador de FAQ
A base de conhecimento qnA Maker (KB) fornece uma resposta de melhor correspondência a uma consulta do utilizador com base no conteúdo do KB. A criação de uma base de conhecimento é uma ação única para criar um repositório de conteúdos de perguntas, respostas e metadados associados. Um KB pode ser criado rastejando conteúdo pré-existente tais as seguintes fontes:

- Páginas de FAQ
- Manuais de produtos
- Pares Q-A

Aprenda a [criar uma base de conhecimento.](../quickstarts/create-publish-knowledge-base.md)

## <a name="testing-and-updating-the-knowledge-base"></a>Testar e atualizar a base de conhecimentos

A base de conhecimento está pronta para ser testada uma vez que é povoada com conteúdo, quer editorialmente, quer através de extração automática. Os testes interativos podem ser feitos no portal QnA Maker, através do painel **de Teste.** Introduz-se consultas comuns de utilizador. Em seguida, verifique se as respostas voltaram com a resposta correta e uma pontuação de confiança suficiente.


* **Para corrigir pontuações de baixa confiança:** adicione perguntas alternativas.
* **Quando uma consulta retornar incorretamente a [resposta por defeito:](../How-to/change-default-answer.md)** adicione novas respostas à pergunta correta.

Este ciclo apertado de atualização de testes continua até que esteja satisfeito com os resultados. Saiba como testar a [sua base de conhecimentos.](../How-To/test-knowledge-base.md)

Para grandes KBs, utilize testes automatizados com a [API generateAnswer](../how-to/metadata-generateanswer-usage.md#get-answer-predictions-with-the-generateanswer-api) e a `isTest` propriedade do corpo, que consulta a base `test` de conhecimento em vez da base de conhecimento publicada.

```json
{
  "question": "example question",
  "top": 3,
  "userId": "Default",
  "isTest": true
}
```

## <a name="publish-the-knowledge-base"></a>Publicar a base de dados de conhecimento
Uma vez terminada a testar a base de conhecimento, pode publicá-la. A publicação empurra a versão mais recente da base de conhecimento testada para um índice dedicado de Pesquisa Cognitiva Azure que representa a base de conhecimento **publicada.** Também cria um ponto final que pode ser chamado na sua aplicação ou chatbot.

Devido à ação de publicação, quaisquer outras alterações feitas na versão de teste da base de conhecimento deixam a versão publicada inalterada. A versão publicada pode estar ao vivo numa aplicação de produção.

Cada uma destas bases de conhecimento pode ser direcionada para testes separados. Utilizando as APIs, pode direcionar a versão de teste da base de conhecimento com `isTest` propriedade corporal na chamada generateAnswer.

Saiba como publicar a [sua base de conhecimentos.](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base)

## <a name="monitor-usage"></a>Monitorizar a utilização
Para poder registar os registos de chat do seu serviço, necessitaria de ativar o Application Insights quando [criar o seu serviço QnA Maker](../How-To/set-up-qnamaker-service-azure.md).

Pode obter várias análises do seu uso do serviço. Saiba mais sobre como usar insights de aplicações para obter [análises para o seu serviço QnA Maker.](../How-To/get-analytics-knowledge-base.md)

Com base no que aprende com a sua análise, faça [atualizações apropriadas para a sua base de conhecimentos.](../How-To/edit-knowledge-base.md)

## <a name="version-control-for-data-in-your-knowledge-base"></a>Controlo de versão para dados na sua base de conhecimentos

O controlo da versão para dados é fornecido através das funcionalidades de importação/exportação na página **Definições** no portal Do Criador QnA.

Pode apoiar uma base de conhecimento exportando a base de conhecimento, em qualquer um `.tsv` ou `.xls` formato. Uma vez exportado, inclua este ficheiro como parte da sua verificação regular de controlo de origem.

Quando precisa de voltar a uma versão específica, tem de importar esse ficheiro do seu sistema local. Uma base de conhecimentos exportada só **deve** ser utilizada através da importação na página **Definições.** Não pode ser usado como um ficheiro ou fonte de dados de documentos URL. Isto substituirá perguntas e respostas atualmente na base de conhecimentos com o conteúdo do ficheiro importado.

## <a name="test-and-production-knowledge-base"></a>Base de conhecimentos de teste e produção
Uma base de conhecimento é o repositório de perguntas e conjuntos de respostas criados, mantidos e usados através do QnA Maker. Cada recurso do QnA Maker pode conter várias bases de conhecimento.

Uma base de conhecimento tem dois estados: *teste* e *publicação.*

### <a name="test-knowledge-base"></a>Testar base de dados de conhecimento

A *base de conhecimento de teste* é a versão atualmente editada e guardada. A versão de teste foi testada para obter precisão e para a completude das respostas. As alterações efetuadas na base de conhecimento de teste não afetam o utilizador final da sua aplicação ou chat bot. A base de conhecimentos de teste é conhecida como `test` no pedido HTTP. O `test` conhecimento está disponível com o painel de **teste** interativo do portal QnA Maker.

### <a name="production-knowledge-base"></a>Base de conhecimentos de produção

A *base de conhecimentos publicada* é a versão que é usada no seu chat bot ou aplicação. A publicação de uma base de conhecimento coloca o conteúdo da sua versão de teste na sua versão publicada. A base de conhecimentos publicada é a versão que a aplicação utiliza através do ponto final. Certifique-se de que o conteúdo está correto e bem testado. A base de conhecimentos publicada é conhecida como `prod` no pedido HTTP.


## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Sugestões de aprendizagem ativa](./active-learning-suggestions.md)