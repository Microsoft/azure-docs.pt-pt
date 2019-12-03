---
title: 'Início rápido: Adicionar perguntas e respostas no portal QnA Maker'
titleSuffix: Azure Cognitive Services
description: ''
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 11/22/2019
ms.author: diberry
ms.openlocfilehash: bf88928ca24a1205ec7a1ddd2fd20af0d0e91468
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/23/2019
ms.locfileid: "74422665"
---
# <a name="quickstart-add-questions-and-answer-with-qna-maker-portal"></a>Início rápido: Adicionar perguntas e respostas com o QnA Maker Portal

Depois que uma base de dados de conhecimento é criada, adicione conjuntos de perguntas e respostas com metadados para que os usuários possam encontrar a resposta certa para sua pergunta.

A resposta certa é uma única resposta, mas pode haver muitas maneiras pelas quais um cliente pode fazer a pergunta que leva a essa única resposta.

Por exemplo, as perguntas na tabela a seguir são sobre os limites de serviço do Azure, mas cada uma tem a ver com um serviço do Azure diferente. 

<a name="qna-table"></a>


|Definir|Dúvidas|Resposta|Metadados|
|--|--|--|--|
|N.º 1|`How large a knowledge base can I create?`<br><br>`What is the max size of a knowledge base?`<br><br>`How many GB of data can a knowledge base hold?` |`The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment) for more details.`|`service=qna_maker`<br>`link_in_answer=true`|
|N.º 2|`How many knowledge bases can I have for my QnA Maker service?`<br><br>`I selected a Azure Cognitive Search tier that holds 15 knowledge bases, but I can only create 14 - what is going on?`<br><br>`What is the connection between the number of knowledge bases in my QnA Maker service and the Azure Cognitive Search service size?` |`Each knowledge base uses 1 index, and all the knowledge bases share a test index. You can have N-1 knowledge bases where N is the number of indexes your Azure Cognitive Search tier supports.`|`service=search`<br>`link_in_answer=false`|

Depois que os metadados são adicionados a um conjunto de perguntas e respostas, o aplicativo cliente pode:

* Solicite respostas que correspondam apenas a determinados metadados.
* Receba todas as respostas, mas pós-processo as respostas, dependendo dos metadados de cada resposta.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar. 

## <a name="prerequisites"></a>Pré-requisitos

* Um serviço QnA Maker
* Uma base de dados de conhecimento criada no serviço QnA Maker

Ambos foram criados no [primeiro início rápido](../how-to/create-knowledge-base.md).

## <a name="sign-in-to-the-qna-maker-portal"></a>Entrar no portal de QnA Maker

1. Entre no portal de [QnA Maker](https://www.qnamaker.ai).

1. Selecione sua base de dados de conhecimento existente. Se você não tiver uma base de dados de conhecimento, retorne ao [início rápido anterior](../how-to/create-knowledge-base.md) e conclua as etapas para criar sua base de dados de conhecimento.

## <a name="add-additional-alternatively-phrased-questions"></a>Adicionar outras perguntas com frase secreta alternativa 

A base de dados de conhecimento atual, do [início rápido anterior](../how-to/create-knowledge-base.md), tem a QnA Maker solução de problemas de conjuntos de perguntas e respostas. Esses conjuntos foram criados quando a URL foi adicionada à base de dados de conhecimento durante o processo de criação. 

Quando essa URL foi importada, apenas uma pergunta com uma resposta foi criada. 

Neste procedimento, adicione outras perguntas.

1. Na página **Editar** , use a caixa de texto Pesquisar acima dos conjuntos de perguntas e respostas para localizar a pergunta `How large a knowledge base can I create?`

1. Na coluna **pergunta** , selecione **+ Adicionar frases alternativas** e, em seguida, adicione todas as novas frases, fornecidas na tabela a seguir.

    |Frases alternativas|
    |--|
    |`What is the max size of a knowledge base?`|
    |`How many GB of data can a knowledge base hold?`| 

1. Selecione **salvar e treinar** para treinar novamente a base de dados de conhecimento. 

1. Selecione **teste**e, em seguida, insira uma pergunta que esteja próxima de uma das novas frases alternativas, mas que não seja exatamente o mesmo texto:

    `What GB size can a knowledge base be?`

    A resposta correta é retornada no formato de redução: `The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment) for more details.`

    Se você selecionar **inspecionar** na resposta retornada, poderá ver que mais respostas atenderam à pergunta, mas não com o mesmo alto nível de confiança. 

    Não adicione todas as combinações possíveis de frases alternativas. Ative o [aprendizado ativo](../how-to/improve-knowledge-base.md)do QnA Maker, que encontra as frases alternativas que melhor ajudarão a sua base de dados de conhecimento a atender às necessidades dos usuários.

1. Selecione **testar** novamente para fechar a janela de teste.

## <a name="add-metadata-to-filter-the-answers"></a>Adicionar metadados para filtrar as respostas

A adição de metadados a um conjunto de perguntas e respostas permite que o aplicativo cliente solicite respostas filtradas. Esse filtro é aplicado antes que o [primeiro e o segundo classificadores](../concepts/knowledge-base.md#ranker-process) sejam aplicados.

1. Adicione a segunda pergunta e conjunto de respostas, sem os metadados, da [primeira tabela neste guia de início rápido](#qna-table)e continue com as etapas a seguir. 

1. Selecione **Opções de exibição**e, em seguida, selecione **Mostrar metadados**. 

1. Para o conjunto de perguntas e respostas que você acabou de adicionar, selecione **Adicionar marcas de metadados**e, em seguida, adicione o nome de `service` e o valor de `search``service:search`.

1. Adicione outras marcas de metadados com o nome de `link_in_answer` e o valor de `false``link_in_answer:false`.

1. Procure a primeira resposta na tabela `How large a knowledge base can I create?`. 
1. Adicione pares de metadados para as mesmas duas marcas de metadados:

    `link_in_answer`: `true`<br>
    `server`: `qna_maker`

    Agora você tem duas perguntas com as mesmas marcas de metadados com valores diferentes. 

1. Selecione **salvar e treinar** para treinar novamente a base de dados de conhecimento. 

1. Selecione **publicar** no menu superior para ir para a página publicar. 
1. Selecione o botão **publicar** para publicar a base de dados de conhecimento atual em um ponto de extremidade passível de consulta. 
1. Depois que a base de dados de conhecimento for publicada, selecione a guia **ondulação** para ver um exemplo de comando de ondulação usado para gerar uma resposta da base de dados de conhecimento.
1. Copie o comando para um painel de notas ou outro ambiente editável para que você possa editar o comando. Edite para seu próprio nome de recurso, ID da base de dados de conhecimento e chave do ponto de extremidade:

    |Substituir|
    |--|
    |`your-resource-name`|
    |`your-knowledge-base-id`|
    |`your-endpoint-key`|

    ```curl
    curl -X POST https://your-resource-name.azurewebsites.net/qnamaker/knowledgebases/your-knowledge-base-id/generateAnswer -H "Authorization: EndpointKey your-endpoint-key" -H "Content-type: application/json" -d "{'top':30, 'question':'size','strictFilters': [{'name':'service','value':'qna_maker'}]}"
    ```

    Observe que a pergunta é apenas uma única palavra, `size`, que pode retornar o conjunto de perguntas e respostas. A matriz de `strictFilters` informa a resposta para reduzir apenas as respostas `qna_maker`. 

    [!INCLUDE [Tip for debug property to JSON request](../includes/tip-debug-json.md)]

1. A resposta inclui apenas a resposta que atende aos critérios de filtro. 

    A seguinte resposta de rotação foi formatada para facilitar a leitura:

    ```JSON
    {
        "answers": [
            {
                "questions": [
                    "How large a knowledge base can I create?",
                    "What is the max size of a knowledge base?",
                    "How many GB of data can a knowledge base hold?"
                ],
                "answer": "The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment)for more details.",
                "score": 68.76,
                "id": 3,
                "source": "https://docs.microsoft.com/azure/cognitive-services/qnamaker/troubleshooting",
                "metadata": [
                    {
                        "name": "link_in_answer",
                        "value": "true"
                    },
                    {
                        "name": "service",
                        "value": "qna_maker"
                    }
                ],
                "context": {
                    "isContextOnly": false,
                    "prompts": []
                }
            }
        ],
        "debugInfo": null
    }
    ```

    Se houver um conjunto de perguntas e respostas que não atendeu ao termo de pesquisa, mas que atendeu ao filtro, ele não será retornado. Em vez disso, a resposta geral `No good match found in KB.` é retornada.

    Certifique-se de manter seus pares de nome de metadados e valor dentro dos limites necessários. 

## <a name="clean-up-resources"></a>Limpar recursos

Se você quiser limpar e remover uma assinatura de serviços cognitivas, poderá excluir o recurso ou grupo de recursos. Excluir o grupo de recursos também exclui todos os outros recursos associados a ele.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [CLI do Azure](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Obter resposta com o postmaster ou a ondulação](get-answer-from-knowledge-base-using-url-tool.md)