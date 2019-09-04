---
title: 'Início rápido: Implantar um aplicativo com o portal do LUIS'
titleSuffix: Azure Cognitive Services
description: Saiba como implantar seu aplicativo LUIS no ponto de extremidade de previsão depois que o aplicativo estiver pronto para retornar previsões expressão para um aplicativo cliente, como um bot de chat. Este guia de início rápido orienta como implantar um aplicativo Criando um recurso de ponto de extremidade de previsão, atribuindo o recurso ao aplicativo, treinando o aplicativo e publicando o aplicativo.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 09/02/2019
ms.author: diberry
ms.openlocfilehash: 4e9ecdad0fb4d02f160977fa28a484b2a3a5bb30
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70257082"
---
# <a name="quickstart-deploy-an-app-in-the-luis-portal"></a>Início rápido: Implantar um aplicativo no portal do LUIS

Quando seu aplicativo LUIS estiver pronto para retornar previsões de expressão para um aplicativo cliente (por exemplo, um bot de chat), você precisará implantar o aplicativo no ponto de extremidade de previsão.

Neste guia de início rápido, você aprende a implantar um aplicativo. Você cria um recurso de ponto de extremidade de previsão, atribui o recurso ao aplicativo, treina o aplicativo e publica o aplicativo.

## <a name="prerequisites"></a>Pré-requisitos

* Obtenha uma [assinatura do Azure](https://azure.microsoft.com/free).
* Conclua o [início rápido do portal anterior](get-started-portal-build-app.md) ou [Baixe e importe o aplicativo](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/in-portal/build-portal-app.json).

## <a name="create-the-endpoint-resource"></a>Criar o recurso de ponto de extremidade

Você cria o recurso de ponto de extremidade de previsão no portal do Azure. Esse recurso só deve ser usado para consultas de previsão de ponto de extremidade. Não use esse recurso para criar alterações no aplicativo.

1. Inicie sessão no [portal do Azure](https://ms.portal.azure.com/).

1. Selecione o sinal **+** verde no painel superior esquerdo. `Cognitive Services` Pesquise no Marketplace e selecione-o.

1. Configure a assinatura com as seguintes configurações:

   |Definição|Value|Objetivo|
   |--|--|--|
   |Name|`my-cognitive-service-resource`|O nome do recurso do Azure. Você precisará desse nome ao atribuir o recurso ao aplicativo no portal do LUIS.|
   |Subscription|A sua subscrição|Selecione uma das assinaturas associadas à sua conta.|
   |Location|**E.U.A. Oeste**|A região do Azure para este recurso.|
   |Escalão de preço|**S0**|O tipo de preço padrão para este recurso.|
   |Resource group|`my-cognitive-service-resource-group`|Crie um novo grupo de recursos para todos os seus recursos de serviço cognitiva. Quando terminar os recursos, você poderá excluir o grupo de recursos para limpar sua assinatura. |
   | | | |

   ![Escolha da API do Azure](./media/get-started-portal-deploy-app/create-cognitive-services-resource.png)

1. Selecione **criar** para criar o recurso do Azure.

   Na próxima seção, você aprenderá a conectar esse novo recurso a um aplicativo LUIS no portal do LUIS.

## <a name="assign-the-resource-key-to-the-luis-app-in-the-luis-portal"></a>Atribuir a chave de recurso ao aplicativo LUIS no portal do LUIS

Sempre que você criar um novo recurso para LUIS, será necessário atribuir o recurso ao aplicativo LUIS. Depois de atribuído, você não precisará executar esta etapa novamente, a menos que crie um novo recurso. Você pode criar um novo recurso para expandir as regiões do seu aplicativo ou para dar suporte a um número maior de consultas de previsão.

1. Entre no portal do [Luis](https://www.luis.ai) e escolha o aplicativo **myEnglishApp** na lista de aplicativos.

1. Selecione **gerenciar** no menu superior direito e, em seguida, selecione **recursos do Azure**.

1. Para adicionar o LUIS, selecione **Adicionar recurso de previsão**.

    <!-- TBD: get screenshot-->

1. Selecione seu locatário, assinatura e nome do recurso. Selecione **atribuir recursos**.

   ![Atribuir um recurso à sua aplicação](./media/get-started-portal-deploy-app/assign-resource.png)

1. Localize a nova linha na tabela e copie o URL de ponto final. Ele é construído corretamente para fazer uma `HTTP GET` solicitação ao ponto de extremidade da API do Luis para uma previsão.

## <a name="train-and-publish-the-app"></a>Preparar e publicar a aplicação

Treine o aplicativo quando você estiver pronto para testá-lo. Publique o aplicativo quando desejar que a versão treinada atualmente esteja disponível para aplicativos cliente do tempo de execução de ponto de extremidade de previsão de consulta.

1. Se o aplicativo for não treinado, selecione **treinar** no menu no canto superior direito.

1. Selecione **publicar** no menu superior. Aceite as configurações de ambiente padrão e selecione **publicar**.

1. Quando a barra de notificação de êxito verde aparecer na parte superior da janela do navegador, selecione **consultar a lista de pontos de extremidade**.

   ![Barra de notificação do aplicativo publicada com êxito no navegador](./media/get-started-portal-deploy-app/successfully-published-notification.png)

1. Na página **configurações de chaves e ponto de extremidade** , localize a lista de recursos atribuídos e as URLs de ponto de extremidade correspondentes na parte inferior.

1. Selecione a URL do ponto de extremidade associada ao novo nome do recurso. Essa ação abre um navegador da Web com uma URL construída corretamente para fazer `GET` uma solicitação para o tempo de execução de ponto de extremidade de previsão.

1. O `q=` no final da URL é curto para a **consulta** e é onde o expressão do usuário é anexado à solicitação get. Após o `q=`, insira o mesmo usuário expressão usado no final do início rápido anterior:

    ```Is there a form named hrf-234098```

    O navegador mostra a resposta, que é o mesmo JSON que seu aplicativo cliente receberá:

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

    Essa resposta fornece mais informações do que o painel de teste padrão no tutorial anterior. Para ver esse mesmo nível de informação no painel de teste, você deve publicar o aplicativo. Depois que o aplicativo for publicado, selecione **comparar com publicado** no painel de teste. Use a **exibição mostrar JSON** no painel de teste publicado para ver o mesmo JSON da etapa anterior. Dessa forma, você pode comparar o aplicativo atual no qual está trabalhando com um aplicativo que é publicado no ponto de extremidade.

    [![Comparar a edição atual versus a versão publicada do aplicativo](./media/get-started-portal-deploy-app/compare-test-pane.png)](./media/get-started-portal-deploy-app/compare-test-pane.png#lightbox)

## <a name="clean-up-resources"></a>Limpar recursos

Quando você terminar este guia de início rápido, selecione **meus aplicativos** no menu de navegação superior. Marque a caixa de seleção do aplicativo na lista e, em seguida, selecione **excluir** na barra de ferramentas de contexto acima da lista.

[![Excluir aplicativo da minha lista de aplicativos](./media/get-started-portal-build-app/delete-app.png)](./media/get-started-portal-build-app/delete-app.png#lightbox)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Identificar entidades e intenções comuns](luis-tutorial-prebuilt-intents-entities.md)
