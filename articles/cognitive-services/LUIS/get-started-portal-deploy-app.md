---
title: 'Início rápido: Implementar uma aplicação com o portal de LUIS'
titleSuffix: Language Understanding - Azure Cognitive Services
description: Saiba como implementar a sua aplicação do LUIS para o ponto final de predição depois da aplicação está pronta para retornar as previsões de expressão para uma aplicação de cliente, como um chatbot. Este início rápido explica como implementar uma aplicação ao criar um recurso de ponto final de predição, atribuir o recurso para a aplicação, a aplicação de treinamento e publicar a aplicação.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: db2b543fa8e5429cc8d50d7789b03239173f563d
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65154537"
---
# <a name="quickstart-deploy-an-app-in-the-luis-portal"></a>Início rápido: Implementar uma aplicação no portal do LUIS

Quando a aplicação do LUIS está pronta para retornar as previsões de expressão para uma aplicação de cliente (por exemplo, um chatbot), terá de implementar a aplicação para o ponto final de predição.

Neste início rápido, vai aprender a implementar uma aplicação. Criar um recurso de ponto final de predição, atribuir o recurso para a aplicação, preparar a aplicação e publicar a aplicação.

## <a name="prerequisites"></a>Pré-requisitos

* Obter um [subscrição do Azure](https://azure.microsoft.com/free).
* Concluir o [início rápido do portal anterior](get-started-portal-build-app.md) ou [transferir e importar a aplicação](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/in-portal/build-portal-app.json).

## <a name="create-the-endpoint-resource"></a>Criar o recurso de ponto final

Criar o recurso de ponto final de predição no portal do Azure. Este recurso só deve ser utilizado para consultas de previsão de ponto final. Não utilize este recurso para criação de alterações para a aplicação.

1. Inicie sessão no [portal do Azure](https://ms.portal.azure.com/).

1. Selecione o verde **+** iniciar sessão no painel do canto superior esquerdo. Procure `Cognitive Services` no marketplace e selecioná-lo.

1. Configure a subscrição com as seguintes definições:

   |Definição|Value|Objetivo|
   |--|--|--|
   |Name|`my-cognitive-service-resource`|O nome do recurso do Azure. Precisar deste nome quando atribui o recurso para a aplicação no portal do LUIS.|
   |Subscrição|A sua subscrição|Selecione uma das subscrições associadas à sua conta.|
   |Location|**E.U.A. Oeste**|A região do Azure para este recurso.|
   |Escalão de preço|**S0**|A predefinição de preços para este recurso.|
   |Grupo de recursos|`my-cognitive-service-resource-group`|Crie um novo grupo de recursos para todos os seus recursos de serviço cognitivo. Quando tiver terminado com os recursos, pode eliminar o grupo de recursos para limpar a sua subscrição. |
   | | | |

   ![Escolha de API do Azure](./media/get-started-portal-deploy-app/create-cognitive-services-resource.png)

1. Selecione **criar** para criar o recurso do Azure.

   Na próxima seção, saiba como ligar esse novo recurso a uma aplicação LUIS no portal do LUIS.

## <a name="assign-the-resource-key-to-the-luis-app-in-the-luis-portal"></a>Atribuir a chave de recurso para a aplicação do LUIS no portal do LUIS

Sempre que criar um novo recurso para o LUIS, terá de atribuir o recurso para a aplicação do LUIS. Depois do que é atribuído, não terá de repetir este passo, a menos que crie um novo recurso. Pode criar um novo recurso para expandir as regiões da sua aplicação ou para suportar um número superior de consultas de previsão.

1. Entrar para o [portal de LUIS](https://www.luis.ai) e escolha o **myEnglishApp** aplicação a partir da lista de aplicações.

1. Selecione **Manage** no menu do canto superior direito e, em seguida, selecione **chaves e os pontos finais**.

1. Para adicionar o LUIS, selecione **atribuir recursos +**.

   [![Atribuir um recurso à sua aplicação](./media/get-started-portal-deploy-app/assign-resource-button.png)](./media/get-started-portal-deploy-app/assign-resource-button.png#lightbox)

1. Selecione o nome de inquilino, recursos e subscrição. Selecione **atribuir recursos**.

   ![Atribuir um recurso à sua aplicação](./media/get-started-portal-deploy-app/assign-resource.png)

1. Localize a nova linha na tabela e copie o URL de ponto final. Ele é construído corretamente para tornar um `HTTP GET` pedido para o ponto de final de API de LUIS para uma predição.

## <a name="train-and-publish-the-app"></a>Preparar e publicar a aplicação

Prepare a aplicação quando estiver pronto para testá-lo. Publicar a aplicação quando pretender que a versão atualmente treinada para estar disponível para aplicativos cliente do Runtime de ponto final de predição de consulta.

1. Se a aplicação é inoportuno, selecione **Train** no menu do canto superior direito.

1. Selecione **publicar** no menu superior. Aceite as predefinições de ambiente e selecione **publicar**.

1. Quando aparece a barra de notificação de êxito verde na parte superior da janela do navegador, selecione **consulte a lista de pontos de extremidade**.

   ![Barra de notificação de aplicação publicada com êxito no browser](./media/get-started-portal-deploy-app/successfully-published-notification.png)

1. Sobre o **chaves e ponto final definições** página, encontrar a lista de recursos atribuídos e URLs de ponto final correspondentes na parte inferior.

1. Selecione o URL de ponto final associado com o novo nome do recurso. Esta ação abre um browser com um URL construído corretamente para tornar um `GET` pedido para o tempo de execução do ponto final de predição.

1. O `q=` no final do URL é a abreviação de **consulta** e é onde a expressão do usuário é anexado ao pedido GET. Depois do `q=`, introduza a mesma expressão de utilizador utilizado no final de início rápido anterior:

    ```Is there a form named hrf-234098```

    O navegador mostra a resposta, o que é o mesmo JSON irá receber a aplicação de cliente:

    ```JSON
    {
    "query": "Is there a form named hrf-234098",
    "topScoringIntent": {
        "intent": "FindForm",
        "score": 0.9768753
    },
    "intents": [
        {
        "intent": "FindForm",
        "score": 0.9768753
        },
        {
        "intent": "None",
        "score": 0.0216071066
        }
    ],
    "entities": [
        {
        "entity": "hrf-234098",
        "type": "Human Resources Form Number",
        "startIndex": 22,
        "endIndex": 31
        }
      ]
    }
    ```

    Esta resposta dá-lhe mais informações do que o painel de teste predefinidos no tutorial anterior. Para ver este mesmo nível de informações no painel de teste, tem de publicar a aplicação. Depois da aplicação for publicada, selecione **Compare com publicado** no painel de teste. Uso **vista de mostrar JSON** no painel de teste publicados para ver o JSON mesmo que o passo anterior. Dessa forma, pode comparar a aplicação atual que está a trabalhar com uma aplicação que é publicada para o ponto final.

    [![Comparar atualmente a editar em comparação com a versão publicada da aplicação](./media/get-started-portal-deploy-app/compare-test-pane.png)](./media/get-started-portal-deploy-app/compare-test-pane.png#lightbox)

## <a name="clean-up-resources"></a>Limpar recursos

Quando tiver terminado com este início rápido, selecione **as minhas aplicações** no menu de navegação superior. Selecione a caixa de verificação da aplicação na lista e, em seguida, selecione **eliminar** da barra de ferramentas de contexto acima da lista.

[![Eliminar aplicação da minha lista de aplicações](./media/get-started-portal-build-app/delete-app.png)](./media/get-started-portal-build-app/delete-app.png#lightbox)

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Identificar intenções e entidades comuns](luis-tutorial-prebuilt-intents-entities.md)
