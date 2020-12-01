---
title: 'Quickstart: Adicione perguntas e resposta no portal QnA Maker'
description: Este quickstart mostra como adicionar pares de perguntas e respostas com metadados para que os seus utilizadores possam encontrar a resposta certa à sua pergunta.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 05/26/2020
ms.openlocfilehash: e56d718892d9cfdbfda9068ecd68ef31f7f2ea46
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96353075"
---
# <a name="quickstart-add-questions-and-answer-with-qna-maker-portal"></a>Quickstart: Adicione perguntas e responda com o portal QnA Maker

Uma vez criada uma base de conhecimento, adicione pares de perguntas e respostas (QnA) com metadados para filtrar a resposta. As perguntas na tabela seguinte são sobre os limites de serviço Azure, mas cada um tem a ver com um serviço de pesquisa Azure diferente.

<a name="qna-table"></a>

|Par|Perguntas|Resposta|Metadados|
|--|--|--|--|
|N.º 1|`How large a knowledge base can I create?`<br><br>`What is the max size of a knowledge base?`<br><br>`How many GB of data can a knowledge base hold?` |`The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](../concepts/azure-resources.md) for more details.`|`service=qna_maker`<br>`link_in_answer=true`|
|N.º 2|`How many knowledge bases can I have for my QnA Maker service?`<br><br>`I selected a Azure Cognitive Search tier that holds 15 knowledge bases, but I can only create 14 - what is going on?`<br><br>`What is the connection between the number of knowledge bases in my QnA Maker service and the Azure Cognitive Search service size?` |`Each knowledge base uses 1 index, and all the knowledge bases share a test index. You can have N-1 knowledge bases where N is the number of indexes your Azure Cognitive Search tier supports.`|`service=search`<br>`link_in_answer=false`|

Uma vez adicionados metadados a um par QnA, a aplicação do cliente pode:

* Solicite respostas que correspondam apenas a certos metadados.
* Receba todas as respostas, mas após o processo as respostas dependendo dos metadados para cada resposta.


## <a name="prerequisites"></a>Pré-requisitos

* Complete o [arranque rápido anterior](./create-publish-knowledge-base.md)

## <a name="sign-in-to-the-qna-maker-portal"></a>Inscreva-se no portal QnA Maker

1. Inscreva-se no [portal QnA Maker](https://www.qnamaker.ai).

1. Selecione a sua base de conhecimento existente a partir do [arranque rápido anterior](./create-publish-knowledge-base.md).

## <a name="add-additional-alternatively-phrased-questions"></a>Adicione perguntas adicionais com frases alternativas

A atual base de conhecimento tem o QnA Maker a resolver os pares QnA. Estes pares foram criados quando o URL foi adicionado à base de conhecimento durante o processo de criação.

Quando esta URL foi importada, apenas uma pergunta com uma resposta foi criada. Neste procedimento, adicione perguntas adicionais.

1. A partir da página **Editar,** use a caixa de texto de pesquisa acima dos pares de perguntas e respostas, para encontrar a pergunta `How large a knowledge base can I create?`

1. Na coluna **'Perguntas',** selecione **+ Adicione frases alternativas** e adicione cada nova frase, fornecida na tabela seguinte.

    |Frases alternativas|
    |--|
    |`What is the max size of a knowledge base?`|
    |`How many GB of data can a knowledge base hold?`|

1. **Selecione Save e treine** para treinar a base de conhecimentos.

1. Selecione **Teste**, em seguida, introduza uma pergunta que está perto de uma das novas frases alternativas, mas não é exatamente a mesma redação:

    `What GB size can a knowledge base be?`

    A resposta correta é devolvida em formato de marcação:

    `The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](../concepts/azure-resources.md) for more details.`

    Se selecionar **Inspecionar** sob a resposta devolvida, pode ver mais respostas que responderam à pergunta, mas não com o mesmo elevado nível de confiança.

    Não adicione todas as combinações possíveis de frases alternativas. Quando liga a [aprendizagem ativa](../how-to/improve-knowledge-base.md)da QnA Maker, isto encontra as frases alternativas que melhor ajudarão a sua base de conhecimento a satisfazer as necessidades dos seus utilizadores.

1. Selecione **testar** novamente para fechar a janela de teste.

## <a name="add-metadata-to-filter-the-answers"></a>Adicionar metadados para filtrar as respostas

Adicionar metadados a um par de perguntas e respostas permite que a aplicação do seu cliente solicite respostas filtradas. Este filtro é aplicado antes da aplicação do [primeiro e segundo classificadores.](../concepts/query-knowledge-base.md#ranker-process)

1. Adicione a segunda pergunta e o par de respostas, sem os metadados, da [primeira tabela neste arranque rápido,](#qna-table)e depois continue com os seguintes passos.

1. Selecione **As opções 'Ver'** e, em seguida, selecione **Mostrar metadados**.

1. Para o par QnA que acabou de adicionar, **selecione adicionar tags de metadados,** em seguida, adicione o nome `service` e o valor de `search` . É o seguinte: `service:search` .

1. Adicione outra etiqueta de metadados com o nome `link_in_answer` e o valor de `false` . É o seguinte: `link_in_answer:false` .

1. Procure a primeira resposta na `How large a knowledge base can I create?` tabela, .

1. Adicione pares de metadados para as mesmas duas tags de metadados:

    `link_in_answer` : `true`<br>
    `service`: `qna_maker`

    Tem agora duas perguntas com as mesmas etiquetas de metadados com valores diferentes.

1. **Selecione Save e treine** para treinar a base de conhecimentos.

1. Selecione **Publicar** no menu principal para ir à página de publicação.
1. Selecione o botão **Publicar** para publicar a base de conhecimento atual no ponto final.
1. Após a publicação da base de conhecimento, continue até ao próximo quickstart para aprender a gerar uma resposta a partir da sua base de conhecimento.

## <a name="what-did-you-accomplish"></a>O que conseguiu?

Editou a sua base de conhecimentos para suportar mais perguntas e forneceu pares de nome/valor para suportar a filtragem durante a busca da resposta superior ou pós-processamento, após a resposta ou respostas serem devolvidas.

## <a name="clean-up-resources"></a>Limpar os recursos

Se não continuar para o próximo quickstart, elimine os recursos-quadro do QnA Maker e bot no portal Azure.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Obter respostas com o Postman ou cURL](get-answer-from-knowledge-base-using-url-tool.md)