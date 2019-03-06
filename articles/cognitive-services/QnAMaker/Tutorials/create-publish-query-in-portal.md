---
title: Criar, publicar, responder a ferramenta QnA Maker
titleSuffix: Azure Cognitive Services
description: Este tutorial baseado no portal explica programaticamente a criação e publicação de uma base de dados de conhecimento e a resposta a uma pergunta a partir da mesma.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: tutorial
ms.date: 12/17/2018
ms.author: diberry
ms.openlocfilehash: a3800881cb310fa9484545467c0b0a9c5fe17c1c
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57406315"
---
# <a name="tutorial-create-a-knowledge-base-then-answer-question-via-the-qna-maker-portal"></a>Tutorial: Criar uma base de dados de conhecimento, em seguida, responder a perguntas através do portal do QnA Maker

Este tutorial explica a criação e publicação de uma base de dados de conhecimento e a resposta a uma pergunta a partir da mesma.

Neste tutorial, ficará a saber como: 

> [!div class="checklist"]
* Criar uma base de dados de conhecimento no portal do Criador de FAQ
* Rever, guardar e preparar a base de dados de conhecimento
* Publicar a base de dados de conhecimento
* Utilizar o Curl para consultar a base de dados de conhecimento

> [!NOTE] 
> A versão programática deste tutorial está disponível com uma solução completa da [ **Azure-amostras/cognitivos-services-qnamaker-csharp** repositório do GitHub](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/tutorials/create-publish-answer-knowledge-base).

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial requer um [serviço Criador de FAQ](../How-To/set-up-qnamaker-service-azure.md) existente. 

## <a name="create-a-knowledge-base"></a>Criar uma base de dados de conhecimento 

1. Inicie sessão no portal do [Criador de FAQ](https://www.qnamaker.ai). 

1. Selecione **Create a knowledge base** (Criar uma base de dados de conhecimento) no menu superior.

    ![Passo 1 do processo de criação da BDC](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-1.png)

1. Ignore o primeiro passo porque irá utilizar o serviço Criador de FAQ existente. 

1. No próximo passo, selecione as definições existentes:  

    |Definição|Objetivo|
    |--|--|
    |Microsoft Azure Directory Id|O _ID do Microsoft Azure Directory_ está associado à conta que utiliza para iniciar sessão no portal do Azure e no portal do Criador de FAQ. |
    |Azure Subscription name|A conta de faturação na qual criou o recurso do Criador de FAQ.|
    |Azure QnA Service|O recurso do Criador de FAQ existente.|

    ![Passo 2 do processo de criação da BDC](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-2.png)

1. No próximo passo, introduza o nome da base de dados de conhecimento, `My Tutorial kb`.

    ![Passo 3 do processo de criação da BDC](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-3.png)

1. No próximo passo, preencha a sua BDC com as seguintes definições:  

    |Nome da definição|Valor da definição|Objetivo|
    |--|--|--|
    |do IdP|`https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs` |Os conteúdos de FAQ nesse URL estão formatados com uma pergunta, seguida de uma resposta. O Criador de FAQ consegue interpretar este formato para extrair perguntas e as respostas associadas.|
    |Ficheiro |_não utilizado neste tutorial_|Esta definição carrega ficheiros para perguntas e respostas. |
    |Personalidade de Chit-chat|O amigo|Esta definição dá uma personalidade informal e amigável a perguntas e respostas comuns. Pode editar estas perguntas e respostas mais tarde. |

    ![Passo 4 do processo de criação da BDC](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-4.png)

1. Selecione **Create your KB** (Criar a sua BDC) para concluir o processo de criação.

    ![Passo 5 do processo de criação da BDC](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-5.png)

## <a name="review-kb-save-and-train"></a>Rever a BDC, guardar e preparar

1. Reveja as perguntas e repostas. A primeira página inclui as perguntas e respostas a partir do URL. 

    ![Guardar e preparar](../media/qnamaker-tutorial-create-publish-query-in-portal/save-and-train-kb.png)

1. Selecione a última página de perguntas e respostas na parte inferior da tabela. A página mostra perguntas e respostas da personalidade Chit-chat. 

1. Na barra de ferramentas acima da lista de perguntas e respostas, selecione o ícone de metadados. Isso mostra as marcas de metadados para cada pergunta e resposta. As perguntas de Chit chat têm o **editorial: chit-bate-papo** metadados já definido. Estes metadados é retornado para a aplicação de cliente, juntamente com a resposta selecionada. O aplicativo de cliente, como um chatbot, pode utilizar estes metadados filtrados para determinar o processamento adicional ou as interações com o utilizador.

    ![Marcas de metadados de vista](../media/qnamaker-tutorial-create-publish-query-in-portal/save-and-train-kb-chit-chat.png)

1. Selecione **Save and train** (Guardar e preparar) na barra de menus superior.

## <a name="publish-to-get-kb-endpoints"></a>Publicar para obter pontos finais da BDC

Selecione o botão **Publish** (Publicar) no menu superior. Assim que estiver na página de publicação, selecione **Publish** (Publicar), junto ao botão **Cancel** (Cancelar).

![Publicar](../media/qnamaker-tutorial-create-publish-query-in-portal/publish-1.png)

Após a publicação da BDC, o ponto final é apresentado

![Definições de ponto final da página de publicação](../media/qnamaker-tutorial-create-publish-query-in-portal/publish-2.png)

## <a name="use-curl-to-query-for-an-faq-answer"></a>Utilizar o Curl para consultar uma resposta de FAQ

1. Selecione o separador **Curl**. 

    ![Comando Curl](../media/qnamaker-tutorial-create-publish-query-in-portal/publish-3-curl.png)

1. Copie o texto do separador **Curl** e execute num terminal preparado para Curl ou numa linha de comandos. O valor do cabeçalho de autorização inclui o texto `Endpoint ` com um espaço à direita, seguido da chave.

1. Substitua `<Your question>` por `How large can my KB be?`. Esta pergunta é parecida com a pergunta `How large a knowledge base can I create?`, mas não é exatamente igual. O Criador de FAQ aplica o processamento de linguagem natural para determinar que as duas perguntas são idênticas.     

1. Execute o comando CURL e receba a resposta JSON, incluindo a classificação e a resposta. 

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

## <a name="use-curl-to-query-for-a-chit-chat-answer"></a>Utilizar o Curl para consultar uma resposta Chit-chat

1. No terminal preparado para Curl, substitua `How large can my KB be?` por uma declaração de fim de conversa de bot do utilizador, como, por exemplo, `Thank you`.   

1. Execute o comando CURL e receba a resposta JSON, incluindo a classificação e a resposta. 

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

    Uma vez que a pergunta `Thank you` corresponde exatamente a uma pergunta Chit-chat, o Criador de FAQ está completamente confiante com a classificação 100. A ferramenta QnA Maker também devolvido todas as dúvidas relacionadas, bem como a propriedade de metadados que contém as informações de marca de metadados de Chit-bate-papo.  

## <a name="use-curl-to-query-for-the-default-answer"></a>Utilizar o Curl para consultar a resposta predefinida

Qualquer pergunta para a qual o Criador de FAQ não esteja confiante numa resposta, recebe a resposta predefinida. Esta resposta é configurada no portal do Azure. 

1. No terminal preparado para Curl, substitua `Thank you` por `x`. 

1. Execute o comando CURL e receba a resposta JSON, incluindo a classificação e a resposta. 

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
    
    O Criador de FAQ devolveu a classificação 0, o que significa nenhuma confiança, mas também devolveu a resposta predefinida. 

## <a name="next-steps"></a>Passos Seguintes

Veja [Origens de dados suportadas](../Concepts/data-sources-supported.md) para obter mais informações sobre os formatos de ficheiro suportados. 

Saiba mais sobre as [personalidades](../Concepts/best-practices.md#chit-chat) Chit-chat.

Para obter mais informações sobre a resposta predefinida, veja [Nenhuma correspondência encontrada](../Concepts/confidence-score.md#no-match-found). 

> [!div class="nextstepaction"]
> [Conceitos da base de dados de conhecimento](../Concepts/knowledge-base.md)