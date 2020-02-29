---
title: Ciclo de vida da base de dados de conhecimento - QnA Maker
description: A ferramenta QnA Maker Aprende melhor num ciclo iterativo de alterações no modelo, exemplos de expressão, publicação e a recolha de dados das consultas de ponto final.
ms.topic: conceptual
ms.date: 02/27/2020
ms.openlocfilehash: 98fbd81baa717c981486f33cfb2b3a608cec27c7
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2020
ms.locfileid: "77914957"
---
# <a name="knowledge-base-lifecycle-in-qna-maker"></a>Ciclo de vida de base de dados de conhecimento no QnA Maker
A ferramenta QnA Maker Aprende melhor num ciclo iterativo de alterações no modelo, exemplos de expressão, publicação e a recolha de dados das consultas de ponto final.

![Ciclo de criação](../media/qnamaker-concepts-lifecycle/kb-lifecycle.png)

## <a name="creating-a-qna-maker-knowledge-base"></a>Criar uma base de dados de conhecimento do QnA Maker
O ponto de extremidade do QnA Maker base de dados de conhecimento (KB) fornece uma melhor correspondência resposta a uma consulta de utilizador com base no conteúdo do KB. A criação de uma base de conhecimento é uma ação única para criar um repositório de conteúdos de perguntas, respostas e metadados associados. Uma base de dados de conhecimento pode ser criada ao rastreamento de conteúdo já existente, como páginas de perguntas frequentes, manuais de produto ou estruturados pares de perguntas E-A. Aprenda a criar uma base de [conhecimento.](../quickstarts/create-publish-knowledge-base.md)

## <a name="testing-and-updating-the-knowledge-base"></a>Teste e a atualizar a base de dados de conhecimento

A base de dados de conhecimento está pronta para teste depois que ele é preenchido com conteúdo, forma editorial ou por meio de extração automática. Os testes interativos podem ser realizados no portal QnA Maker através do painel **de teste,** entrando em consultas comuns de utilizador e verificando se as respostas foram devolvidas com a resposta correta e pontuação de confiança suficiente.

* **Para corrigir pontuações**de baixa confiança: adicione perguntas alternativas.
* **Quando uma consulta devolve incorretamente a [resposta por defeito:](../How-to/change-default-answer.md)** adicione novas respostas à pergunta correta.

Esse loop estreito de atualização de teste continua até estar satisfeito com os resultados. Aprenda a testar a [sua base de conhecimento.](../How-To/test-knowledge-base.md)

Para grandes KBs, utilize testes automatizados com a [GenerateAnswer API](../how-to/metadata-generateanswer-usage.md#get-answer-predictions-with-the-generateanswer-api) e a propriedade corporal `isTest`, que questiona a base de conhecimento `test` em vez da base de conhecimento publicada.

```json
{
  "question": "example question",
  "top": 3,
  "userId": "Default",
  "isTest": true
}
```

## <a name="publish-the-knowledge-base"></a>Publicar a base de dados de conhecimento
Depois de concluída a base de dados de conhecimento de teste, pode publicá-lo. A Publicação empurra a versão mais recente da base de conhecimento testada para um índice dedicado de Pesquisa Cognitiva Azure que representa a base de conhecimento **publicada.** Também cria um ponto final que pode ser chamado na sua aplicação ou chatbot.

Dessa forma, qualquer alteração que está a ser feita para a versão de teste de base de dados de conhecimento não afetam a versão publicada que pode ser em direto num aplicativo de produção.

Cada uma destas bases de dados de conhecimento pode ser alvo para fins de teste em separado. Utilizando as APIs, pode direcionar a versão de teste da base de conhecimentos com `isTest` propriedade corporal na chamada generateAnswer.

Aprenda a publicar a sua base de [conhecimento.](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base)

## <a name="monitor-usage"></a>Monitorizar a utilização
Para poder registar os registos de chat do seu serviço, terá de ativar os Insights de Aplicação quando [criar o seu serviço QnA Maker](../How-To/set-up-qnamaker-service-azure.md).

Pode obter várias análises de utilização do seu serviço. Saiba mais sobre como usar insights de aplicação para obter [análises para o seu serviço QnA Maker](../How-To/get-analytics-knowledge-base.md).

Com base no que aprende com a sua análise, faça [atualizações apropriadas para a sua base de conhecimentos.](../How-To/edit-knowledge-base.md)

## <a name="version-control-for-data-in-your-knowledge-base"></a>Controlo de versão para dados na sua base de conhecimentos

O controlo da versão para dados é fornecido através das funcionalidades de importação/exportação na página **Definições** do portal QnA Maker.

Pode apoiar uma base de conhecimento exportando a base de conhecimentos, em formato `.tsv` ou `.xls`. Uma vez exportado, inclua este ficheiro como parte da sua verificação regular de controlo de fonte.

Quando precisa de voltar a uma versão específica, precisa de importar esse ficheiro do seu sistema local. Uma base de conhecimentos exportada só **deve** ser utilizada através da importação na página **Definições.** Não pode ser usado como uma fonte de dados de ficheiros ou documentos URL. Isto substituirá perguntas e respostas atualmente na base de conhecimentos pelo conteúdo do ficheiro importado.

## <a name="test-and-production-knowledge-base"></a>Base de conhecimentos de ensaio e produção
Uma base de conhecimento é o repositório de perguntas e conjuntos de resposta criados, mantidos e usados através do QnA Maker. Cada recurso QnA Maker pode conter múltiplas bases de conhecimento.

Uma base de conhecimento tem dois estados: *teste* e *publicado.*

### <a name="test-knowledge-base"></a>Base de conhecimentos de teste

A base de conhecimentos de *teste* é a versão atualmente editada, guardada e testada para a precisão e completude das respostas. As alterações efetuadas na base de conhecimentos de teste não afetam o utilizador final da sua aplicação ou do chat bot. A base de conhecimentos de teste é conhecida como `test` no pedido HTTP. O conhecimento `test` está disponível com o painel de **teste** interativo do portal qnA Maker.

### <a name="production-knowledge-base"></a>Base de conhecimentos de produção

A base de *conhecimento publicada* é a versão que é usada no seu chat bot ou aplicação. A ação de publicação de uma base de conhecimento coloca o conteúdo da base de conhecimentos de teste na versão publicada da base de conhecimentos. Como a base de conhecimento publicada é a versão que a aplicação utiliza através do ponto final, certifique-se de que o conteúdo está correto e bem testado. A base de conhecimento publicada é conhecida como `prod` no pedido HTTP.


## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Sugestões de aprendizagem ativa](./active-learning-suggestions.md)