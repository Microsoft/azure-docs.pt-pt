---
title: 'Tutorial: Criar, publicar e responder em QnA Maker'
titleSuffix: Azure Cognitive Services
description: Crie uma nova base de dados de conhecimento com perguntas e respostas de uma FAQ pública baseada na Web. Salve, treine e publique a base de dados de conhecimento. Depois que a base de dados de conhecimento for publicada, envie uma pergunta e receba uma resposta com um comando de ondulação. Em seguida, crie um bot e teste o bot com a mesma pergunta.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: tutorial
ms.date: 09/03/2019
ms.author: diberry
ms.openlocfilehash: 6cc509b9f9a9087fbe832dbd35b3dfd8d60a6577
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70308069"
---
# <a name="tutorial-from-the-qna-maker-portal-create-a-knowledge-base"></a>Tutorial: No portal de QnA Maker, crie uma base de dados de conhecimento

Crie uma nova base de dados de conhecimento com perguntas e respostas de uma FAQ pública baseada na Web. Salve, treine e publique a base de dados de conhecimento. Depois que a base de dados de conhecimento for publicada, envie uma pergunta e receba uma resposta com um comando de ondulação. Em seguida, crie um bot e teste o bot com a mesma pergunta. 

Neste tutorial, ficará a saber como: 

> [!div class="checklist"]
> * Crie uma base de dados de conhecimento no portal de QnA Maker.
> * Revise, salve e treine a base de dados de conhecimento.
> * Publique a base de dados de conhecimento.
> * Use a rotação para consultar a base de dados de conhecimento.
> * Crie um bot.
 

> [!NOTE]
> A versão programática deste tutorial está disponível com uma solução completa do repositório do [GitHub **Azure-Samples/cognitiva-Services-qnamaker-Csharp** ](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/tutorials/create-publish-answer-knowledge-base).

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial requer um [serviço Criador de FAQ](../How-To/set-up-qnamaker-service-azure.md) existente. 

## <a name="create-a-knowledge-base"></a>Criar uma base de dados de conhecimento 

1. Inicie sessão no portal do [Criador de FAQ](https://www.qnamaker.ai). 

1. Selecione **Create a knowledge base** (Criar uma base de dados de conhecimento) no menu superior.

    ![Captura de tela do portal de QnA Maker](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-1.png)

1. Ignore a primeira etapa, pois você usará o serviço de QnA Maker existente. 

1. Selecione as configurações existentes:  

    |Definição|Objetivo|
    |--|--|
    |ID do diretório Microsoft Azure|Essa ID é associada à conta usada para entrar no portal do Azure e no portal de QnA Maker. |
    |Azure Subscription name|A conta de cobrança na qual você criou o recurso de QnA Maker.|
    |Azure QnA Service|O recurso do Criador de FAQ existente.|

    ![Captura de tela do portal de QnA Maker](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-2.png)

1. Insira o nome da base de `My Tutorial kb`dados de conhecimento,.

    ![Captura de tela do portal de QnA Maker](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-3.png)

1. Preencha sua base de dados de conhecimento com as seguintes configurações:  

    |Nome da definição|Valor da definição|Objetivo|
    |--|--|--|
    |do IdP|`https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs` |Os conteúdos de FAQ nesse URL estão formatados com uma pergunta, seguida de uma resposta. O Criador de FAQ consegue interpretar este formato para extrair perguntas e as respostas associadas.|
    |Ficheiro |_não utilizado neste tutorial_|Esta definição carrega ficheiros para perguntas e respostas. |
    |Personalidade de Chit-chat|AmigáveI|Isso oferece uma [personalidade](../Concepts/best-practices.md#chit-chat) amigável e casual a perguntas e respostas comuns. Pode editar estas perguntas e respostas mais tarde. |

    ![Captura de tela do portal de QnA Maker](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-4.png)

1. Selecione **Create your KB** (Criar a sua BDC) para concluir o processo de criação.

    ![Captura de tela do portal de QnA Maker](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-5.png)

## <a name="review-save-and-train-the-knowledge-base"></a>Rever, guardar e preparar a base de dados de conhecimento

1. Reveja as perguntas e repostas. A primeira página inclui as perguntas e respostas a partir do URL. 

    ![Captura de tela do portal de QnA Maker](../media/qnamaker-tutorial-create-publish-query-in-portal/save-and-train-kb.png)

1. Selecione a última página de perguntas e respostas na parte inferior da tabela. A página mostra perguntas e respostas da personalidade Chit-chat. 

1. Na barra de ferramentas acima da lista de perguntas e respostas, selecione o ícone **Exibir opções** e, em seguida, selecione **Mostrar metadados**. Isso mostra as marcas de metadados para cada pergunta e resposta. As perguntas de Chit têm os metadados **editorial: Chit-Chat** já definidos. Esses metadados são retornados para o aplicativo cliente, junto com a resposta selecionada. O aplicativo cliente, como um bot de chat, pode usar esses metadados filtrados para determinar o processamento adicional ou as interações com o usuário.

    ![Captura de tela do portal de QnA Maker](../media/qnamaker-tutorial-create-publish-query-in-portal/save-and-train-kb-chit-chat.png)

1. Selecione **Save and train** (Guardar e preparar) na barra de menus superior.

## <a name="publish-to-get-knowledge-base-endpoints"></a>Publicar para obter pontos de extremidade da base de dados de conhecimento

Selecione o botão **Publish** (Publicar) no menu superior. Na página de publicação, selecione **Publicar**.

![Captura de tela do portal de QnA Maker](../media/qnamaker-tutorial-create-publish-query-in-portal/publish-1.png)

Depois que a base de dados de conhecimento é publicada, o ponto de extremidade é exibido.

![Captura de tela das configurações do ponto de extremidade](../media/qnamaker-tutorial-create-publish-query-in-portal/publish-2.png)

Não feche esta página de **publicação** . Você precisará dele posteriormente no tutorial para criar um bot. 

## <a name="use-curl-to-query-for-an-faq-answer"></a>Use a ondulação para consultar uma resposta de perguntas frequentes

1. Selecione o separador **Curl**. 

    ![Captura de tela da guia ondulação](../media/qnamaker-tutorial-create-publish-query-in-portal/publish-3-curl.png)

1. Copie o texto da guia **ondulação** e execute-o em um terminal ou linha de comando habilitado para ondulação. O valor do cabeçalho de autorização inclui o `Endpoint`texto, com um espaço à direita e depois a chave.

1. Substitua `<Your question>` por `How large can my KB be?`. Esta pergunta é parecida com a pergunta `How large a knowledge base can I create?`, mas não é exatamente igual. O Criador de FAQ aplica o processamento de linguagem natural para determinar que as duas perguntas são idênticas.     

1. Execute o comando de ondulação e receba a resposta JSON, incluindo a pontuação e a resposta. 

    ```TXT
      % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                     Dload  Upload   Total   Spent    Left  Speed
    100   581  100   543  100    38    418     29  0:00:01  0:00:01 --:--:--   447{
      "answers": [
        {
          "questions": [
            "How large a knowledge base can I create?"
          ],
          "answer": "The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment)for more details.",
          "score": 42.81,
          "id": 2,
          "source": "https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs",
          "metadata": []
        }
      ]
    }
    
    ```

    O Criador de FAQ está algo confiante com a classificação 42,81%.  

## <a name="use-curl-to-query-for-a-chit-chat-answer"></a>Usar a rotação para consultar uma resposta de Chit

1. No terminal habilitado para ondulação, substitua `How large can my KB be?` por uma instrução bot Conversation-end do usuário, `Thank you`como.   

1. Execute o comando de ondulação e receba a resposta JSON, incluindo a pontuação e a resposta. 

    ```TXT
      % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                     Dload  Upload   Total   Spent    Left  Speed
    100   525  100   501  100    24    525     25 --:--:-- --:--:-- --:--:--   550{
      "answers": [
        {
          "questions": [
            "Thank you",
            "Thanks",
            "Thnx",
            "Kthx",
            "I appreciate it",
            "Thank you so much",
            "I thank you",
            "My sincere thank"
          ],
          "answer": "You're very welcome.",
          "score": 100.0,
          "id": 109,
          "source": "qna_chitchat_the_friend.tsv",
          "metadata": [
            {
              "name": "editorial",
              "value": "chitchat"
            }
          ]
        }
      ]
    }
   
    ```

    Uma vez que a pergunta `Thank you` corresponde exatamente a uma pergunta Chit-chat, o Criador de FAQ está completamente confiante com a classificação 100. QnA Maker também retornou todas as perguntas relacionadas, bem como a propriedade de metadados que contém as informações de marca de metadados Chit-Chat.  

## <a name="use-curl-to-query-for-the-default-answer"></a>Use a rotação para consultar a resposta padrão

Qualquer pergunta que QnA Maker não tenha certeza sobre o recebe a resposta padrão. Esta resposta é configurada no portal do Azure. 

1. No terminal habilitado para ondulação, substitua `Thank you` por `x`. 

1. Execute o comando de ondulação e receba a resposta JSON, incluindo a pontuação e a resposta. 

    ```TXT
      % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                     Dload  Upload   Total   Spent    Left  Speed
    100   186  100   170  100    16    272     25 --:--:-- --:--:-- --:--:--   297{
      "answers": [
        {
          "questions": [],
          "answer": "No good match found in KB.",
          "score": 0.0,
          "id": -1,
          "metadata": []
        }
      ]
    }
    ```
    
    QnA Maker retornou uma pontuação `0`de, o que significa sem confiança. Ele também retornou a resposta padrão. 

## <a name="create-a-knowledge-base-bot"></a>Criar um bot da base de dados de conhecimento

Para obter mais informações, consulte [criar um bot de chat com esta base de dados de conhecimento](create-qna-bot.md).

## <a name="clean-up-resources"></a>Limpar recursos

Quando você terminar com o bot da base de dados de conhecimento, remova o `my-tutorial-rg`grupo de recursos,, para remover todos os recursos do Azure criados no processo de bot.

Quando terminar com a base de dados de conhecimento, no portal do QnA Maker, selecione **minhas bases de dados de conhecimento**. Em seguida, selecione a base de dados de conhecimento, **meu tutorial KB**e selecione o ícone Excluir na extrema direita da linha.  

## <a name="next-steps"></a>Passos Seguintes

Veja [Origens de dados suportadas](../Concepts/data-sources-supported.md) para obter mais informações sobre os formatos de ficheiro suportados. 

Saiba mais sobre as [personalidades](../Concepts/best-practices.md#chit-chat) Chit-chat.

Para obter mais informações sobre a resposta predefinida, veja [Nenhuma correspondência encontrada](../Concepts/confidence-score.md#no-match-found). 

> [!div class="nextstepaction"]
> [Criar um bot de chat com esta base de dados de conhecimento](create-qna-bot.md)