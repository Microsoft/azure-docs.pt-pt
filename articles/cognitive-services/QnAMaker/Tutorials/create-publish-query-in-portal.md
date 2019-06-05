---
title: Criar, publicar e responder a ferramenta QnA Maker
titleSuffix: Azure Cognitive Services
description: Crie uma nova base de dados de conhecimento com perguntas e respostas partir de uma secção de FAQ baseada na web pública. Guardar, formar e publicar a base de dados de conhecimento. Depois da base de dados de conhecimento é publicada, enviar uma pergunta e receber uma resposta com um comando cURL. Em seguida, crie um bot e testar o bot com a mesma pergunta.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: tutorial
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: a13e0cb0e594571344b16d007ef13475b384b73d
ms.sourcegitcommit: 18a0d58358ec860c87961a45d10403079113164d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/05/2019
ms.locfileid: "66693021"
---
# <a name="tutorial-from-the-qna-maker-portal-create-a-knowledge-base"></a>Tutorial: A partir do portal do QnA Maker, crie uma base de dados de conhecimento

Crie uma nova base de dados de conhecimento com perguntas e respostas partir de uma secção de FAQ baseada na web pública. Guardar, formar e publicar a base de dados de conhecimento. Depois da base de dados de conhecimento é publicada, enviar uma pergunta e receber uma resposta com um comando cURL. Em seguida, crie um bot e testar o bot com a mesma pergunta. 

Neste tutorial, ficará a saber como: 

> [!div class="checklist"]
> * Crie uma base de dados de conhecimento no portal do QnA Maker.
> * Rever, guardar e preparar a base de dados de conhecimento.
> * Publica a base de dados de conhecimento.
> * Utilize o cURL para consultar a base de dados de conhecimento.
> * Crie um bot.
 

> [!NOTE]
> A versão programática deste tutorial está disponível com uma solução completa da [ **Azure-amostras/cognitivos-services-qnamaker-csharp** repositório do GitHub](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/tutorials/create-publish-answer-knowledge-base).

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial requer um [serviço Criador de FAQ](../How-To/set-up-qnamaker-service-azure.md) existente. 

## <a name="create-a-knowledge-base"></a>Criar uma base de dados de conhecimento 

1. Inicie sessão no portal do [Criador de FAQ](https://www.qnamaker.ai). 

1. Selecione **Create a knowledge base** (Criar uma base de dados de conhecimento) no menu superior.

    ![Portal de captura de ecrã do QnA Maker](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-1.png)

1. Ignore a primeira etapa, porque irá utilizar o serviço QnA Maker existente. 

1. Selecione as definições existentes:  

    |Definição|Objetivo|
    |--|--|
    |ID de diretório do Microsoft Azure|Este ID está associado com a conta que utiliza para iniciar sessão no portal do Azure e o portal do QnA Maker. |
    |Azure Subscription name|A conta de faturação em que criou o recurso do QnA Maker.|
    |Azure QnA Service|O recurso do Criador de FAQ existente.|

    ![Portal de captura de ecrã do QnA Maker](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-2.png)

1. Introduza o nome de base de dados de conhecimento, `My Tutorial kb`.

    ![Portal de captura de ecrã do QnA Maker](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-3.png)

1. Povoar a base de dados de conhecimento com as seguintes definições:  

    |Nome da definição|Valor da definição|Objetivo|
    |--|--|--|
    |do IdP|`https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs` |Os conteúdos de FAQ nesse URL estão formatados com uma pergunta, seguida de uma resposta. O Criador de FAQ consegue interpretar este formato para extrair perguntas e as respostas associadas.|
    |Ficheiro |_não utilizado neste tutorial_|Esta definição carrega ficheiros para perguntas e respostas. |
    |Personalidade de Chit-chat|Amigável|Isso dá um amigável e informais [personalidade](../Concepts/best-practices.md#chit-chat) para perguntas e respostas comuns. Pode editar estas perguntas e respostas mais tarde. |

    ![Portal de captura de ecrã do QnA Maker](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-4.png)

1. Selecione **Create your KB** (Criar a sua BDC) para concluir o processo de criação.

    ![Portal de captura de ecrã do QnA Maker](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-5.png)

## <a name="review-save-and-train-the-knowledge-base"></a>Rever, guardar e preparar a base de dados de conhecimento

1. Reveja as perguntas e repostas. A primeira página inclui as perguntas e respostas a partir do URL. 

    ![Portal de captura de ecrã do QnA Maker](../media/qnamaker-tutorial-create-publish-query-in-portal/save-and-train-kb.png)

1. Selecione a última página de perguntas e respostas na parte inferior da tabela. A página mostra perguntas e respostas da personalidade Chit-chat. 

1. Na barra de ferramentas acima da lista de perguntas e respostas, selecione o **ver opções** ícone e, em seguida, selecione **Mostrar metadados**. Isso mostra as marcas de metadados para cada pergunta e resposta. As perguntas de Chit chat têm o **editorial: chit-bate-papo** metadados já definido. Estes metadados é retornado para a aplicação de cliente, juntamente com a resposta selecionada. O aplicativo de cliente, como um chatbot, pode utilizar estes metadados filtrados para determinar o processamento adicional ou as interações com o utilizador.

    ![Portal de captura de ecrã do QnA Maker](../media/qnamaker-tutorial-create-publish-query-in-portal/save-and-train-kb-chit-chat.png)

1. Selecione **Save and train** (Guardar e preparar) na barra de menus superior.

## <a name="publish-to-get-knowledge-base-endpoints"></a>Publicar para obter pontos finais de base de dados de conhecimento

Selecione o botão **Publish** (Publicar) no menu superior. Na página de publicação, selecione **Publicar**.

![Portal de captura de ecrã do QnA Maker](../media/qnamaker-tutorial-create-publish-query-in-portal/publish-1.png)

Depois da base de dados de conhecimento é publicado, o ponto final é apresentado.

![Captura de ecrã das definições de ponto final](../media/qnamaker-tutorial-create-publish-query-in-portal/publish-2.png)

Não feche esta **publicar** página. Precisar dele mais tarde no tutorial, para criar um bot. 

## <a name="use-curl-to-query-for-an-faq-answer"></a>Utilize o cURL para consultar uma resposta de FAQ

1. Selecione o separador **Curl**. 

    ![Separador de captura de ecrã de Curl](../media/qnamaker-tutorial-create-publish-query-in-portal/publish-3-curl.png)

1. Copie o texto do **Curl** separador e executá-la numa capacidade de cURL terminal ou da linha de comandos. Valor do cabeçalho de autorização inclui o texto `Endpoint`, com um espaço à direita e, em seguida, a chave.

1. Substitua `<Your question>` por `How large can my KB be?`. Esta pergunta é parecida com a pergunta `How large a knowledge base can I create?`, mas não é exatamente igual. O Criador de FAQ aplica o processamento de linguagem natural para determinar que as duas perguntas são idênticas.     

1. Execute o comando cURL e receber a resposta JSON, incluindo a classificação e a resposta. 

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

## <a name="use-curl-to-query-for-a-chit-chat-answer"></a>Utilize o cURL para consultar uma resposta do Chit chat

1. No terminal habilitados para cURL, substitua `How large can my KB be?` com uma instrução de fim de conversação de bot do usuário, como `Thank you`.   

1. Execute o comando cURL e receber a resposta JSON, incluindo a classificação e a resposta. 

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

## <a name="use-curl-to-query-for-the-default-answer"></a>Utilize o cURL para consulta para a resposta padrão

Qualquer pergunta que a ferramenta QnA Maker não está confiante sobre recebe a resposta padrão. Esta resposta é configurada no portal do Azure. 

1. No terminal habilitados para cURL, substitua `Thank you` com `x`. 

1. Execute o comando cURL e receber a resposta JSON, incluindo a classificação e a resposta. 

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
    
    A ferramenta QnA Maker devolveu uma classificação de `0`, que significa que nenhuma confiança. Ele também retornado a resposta padrão. 

## <a name="create-a-knowledge-base-bot"></a>Criar um bot de base de dados de conhecimento

Para obter mais informações, consulte [criar um bot de bate-papo com esta base de dados de conhecimento](create-qna-bot.md).

## <a name="clean-up-resources"></a>Limpar recursos

Quando tiver terminado com o bot de base de dados de conhecimento, remova o grupo de recursos, `my-tutorial-rg`, para remover todos os recursos do Azure criados no processo de bot.

Quando tiver terminado com a base de dados de conhecimento, no portal do QnA Maker, selecione **meu bases de dados de conhecimento**. Em seguida, selecione a base de dados de conhecimento **kb My Tutorial**e selecione o ícone Eliminar na extremidade direita nessa linha.  

## <a name="next-steps"></a>Passos Seguintes

Veja [Origens de dados suportadas](../Concepts/data-sources-supported.md) para obter mais informações sobre os formatos de ficheiro suportados. 

Saiba mais sobre as [personalidades](../Concepts/best-practices.md#chit-chat) Chit-chat.

Para obter mais informações sobre a resposta predefinida, veja [Nenhuma correspondência encontrada](../Concepts/confidence-score.md#no-match-found). 

> [!div class="nextstepaction"]
> [Crie um bot de bate-papo com esta base de dados de conhecimento](create-qna-bot.md)