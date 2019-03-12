---
title: 'Início rápido: Implementar a aplicação com o Portal de LUIS'
titleSuffix: Language Understanding - Azure Cognitive Services
description: Depois da aplicação está pronta para retornar as previsões de expressão para uma aplicação de cliente, como um chatbot, terá de implementar a aplicação para o ponto final de predição. Neste início rápido, vai aprender a implementar uma aplicação através da criação de um recurso de ponto final de predição, atribuir o recurso para a aplicação, a aplicação de treinamento e publicar a aplicação.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 03/11/2019
ms.author: diberry
ms.openlocfilehash: 9c6ce826569b9c198e747a7977de3d346b1d3230
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57783137"
---
# <a name="quickstart-deploy-an-app-in-the-luis-portal"></a>Início rápido: Implementar uma aplicação no portal do LUIS

Depois da aplicação está pronta para retornar as previsões de expressão para uma aplicação de cliente, como um chatbot, terá de implementar a aplicação para o ponto final de predição. 

Neste início rápido, vai aprender a implementar uma aplicação através da criação de um recurso de ponto final de predição, atribuir o recurso para a aplicação, a aplicação de treinamento e publicar a aplicação. 

## <a name="prerequisites"></a>Pré-requisitos

* [Subscrição do Azure](https://azure.microsoft.com/free).
* Concluir o [início rápido do portal anterior](get-started-portal-build-app.md) ou [transferir e importar a aplicação](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/in-portal/build-portal-app.json). 


## <a name="create-endpoint-resource"></a>Criar o recurso de ponto final

O recurso de ponto final de predição é criado no portal do Azure. Este recurso só deve ser utilizado para consultas de previsão de ponto final. Não utilize este recurso para criação de alterações para a aplicação. 

1. Inicie sessão para o  **[portal do Azure](https://ms.portal.azure.com/)**. 

1. Selecione o verde **+** iniciar sessão no painel superior esquerdo e procure `Cognitive Services` no marketplace, em seguida, selecioná-lo. 

1. Configure a subscrição com as seguintes definições:

    |Definição|Value|Objetivo|
    |--|--|--|
    |Name|`my-cognitive-service-resource`|O nome do recurso do Azure. Precisar deste nome quando atribui o recurso para a aplicação no portal do LUIS.|
    |Subscrição|A sua subscrição|Selecione uma das subscrições associadas à sua conta.|
    |Localização|**E.U.A. Oeste**|A região do azure para este recurso.|
    |Escalão de preço|**S0**|A predefinição de preços para este recurso.|
    |Grupo de recursos|`my-cognitive-service-resource-group`|Crie um novo grupo de recursos para todos os seus recursos de serviço cognitivo. Quando tiver terminado com os recursos, pode eliminar o grupo de recursos para limpar a sua subscrição. | 

    ![Escolha de API do Azure](./media/get-started-portal-deploy-app/create-cognitive-services-resource.png) 

1. Selecione **criar** para criar o recurso do Azure. 

    Na próxima seção, saiba como ligar esse novo recurso a uma aplicação LUIS no portal do LUIS. 

## <a name="assign-the-resource-key-to-the-luis-app-in-the-luis-portal"></a>Atribuir a chave de recurso para a aplicação do LUIS no portal do LUIS

Sempre que criar um novo recurso para o LUIS, terá de atribuir o recurso para a aplicação do LUIS. Uma vez que é atribuído, não terá de repetir este passo, a menos que crie um novo recurso. Pode criar um novo recurso para expandir as regiões da sua aplicação ou para suportar um número superior de consultas de previsão. 

1. Entrar para o [portal de LUIS](https://www.luis.ai), escolha a **myEnglishApp** aplicação a partir da lista de aplicações.

1. Selecione **Manage** no menu superior direito, em seguida, selecione **chaves e os pontos finais**.

1. Para adicionar o LUIS, selecione **atribuir recursos +**.

    [![Atribuir um recurso à sua aplicação](./media/get-started-portal-deploy-app/assign-resource-button.png)](./media/get-started-portal-deploy-app/assign-resource-button.png#lightbox)

1. Selecione o nome do inquilino, recursos e subscrição, em seguida, selecione **atribuir recursos**. 

    ![Atribuir um recurso à sua aplicação](./media/get-started-portal-deploy-app/assign-resource.png)

1. Localize a nova linha na tabela e copie o URL de ponto final. Ele é corretamente construído para fazer uma solicitação de HTTP GET para o ponto de final de API de LUIS para uma predição. 

## <a name="train-and-publish-the-app"></a>Preparar e publicar a aplicação 

Deve preparar sempre que está pronto para testá-lo. Deve publicar a aplicação sempre que quiser, a versão atualmente treinada para estar disponível para aplicativos cliente do tempo de execução de ponto final de predição. 

1. Se a aplicação é inoportuno, selecione **Train** da parte superior, com o botão direito menu.

1. Selecione **publicar** da parte superior, com o botão direito menu. Aceite as predefinições de ambiente e selecione **publicar**.

1. Quando aparece a barra de notificação de êxito verde na parte superior da janela do navegador, selecione o **consulte a lista de pontos de extremidade** ligação. 

    ![Barra de notificação de aplicação publicada com êxito no browser](./media/get-started-portal-deploy-app/successfully-published-notification.png)

1. Isto leva-o para o **chaves e ponto final definições** página. A lista de recursos atribuídos e o ponto de extremidade correspondente URLs está na parte inferior da página. 

1. Selecione o URL de ponto final associado com o novo nome do recurso. Esta ação abre um browser com um URL construído corretamente para tornar uma **obter** pedido para o tempo de execução do ponto final de predição. 

1. O `q=` no final do URL é a abreviação de **consulta** e é onde a expressão do usuário é anexado ao pedido GET. Depois do `q=`, introduza a mesma expressão de utilizador utilizado no final de início rápido anterior:

    ```Is there a form named hrf-234098```

    A resposta do navegador é o mesmo JSON irá receber a aplicação de cliente:

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

    Esta resposta dá-lhe obter mais informações que não o predefinido **teste** painel no tutorial anterior. Se desejar ver esse mesmo nível de informações no painel de teste, a aplicação tem de ser publicada. Em seguida, no painel de teste, selecione **Compare com publicado**. Uso **vista de mostrar JSON** no painel de teste de publicado para ver o JSON mesmo que o passo anterior. Isto permite-lhe comparar a aplicação atual que está a trabalhar e a aplicação que é publicada para o ponto final.

    [![Comparar atualmente a editar em comparação com a versão publicada da aplicação](./media/get-started-portal-deploy-app/compare-test-pane.png)](./media/get-started-portal-deploy-app/compare-test-pane.png#lightbox)


## <a name="clean-up-resources"></a>Limpar recursos
Quando tiver terminado com este início rápido, selecione **as minhas aplicações** no menu de navegação superior. Em seguida, selecione a caixa de verificação à esquerda da aplicação na lista e selecione **eliminar** da barra de ferramentas de contexto acima da lista. 

[![Eliminar aplicação da minha lista de aplicações](./media/get-started-portal-build-app/delete-app.png)](./media/get-started-portal-build-app/delete-app.png#lightbox)

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Identificar intenções e entidades comuns](/luis-tutorial-prebuilt-intents-entities.md)