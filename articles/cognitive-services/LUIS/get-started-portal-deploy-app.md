---
title: 'Início rápido: implantar um aplicativo com o portal do LUIS'
titleSuffix: Azure Cognitive Services
description: Saiba como implantar seu aplicativo LUIS no ponto de extremidade de previsão depois que o aplicativo estiver pronto para retornar previsões expressão para um aplicativo cliente, como um bot de chat. Este guia de início rápido orienta como implantar um aplicativo Criando um recurso de ponto de extremidade de previsão, atribuindo o recurso ao aplicativo, treinando o aplicativo e publicando o aplicativo.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 10/17/2019
ms.author: diberry
ms.openlocfilehash: ecae5c7db02436fe34fec19989f174504fd1e03a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73488709"
---
# <a name="quickstart-deploy-an-app-in-the-luis-portal"></a>Início rápido: implantar um aplicativo no portal do LUIS

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]


Quando seu aplicativo LUIS estiver pronto para retornar previsões de expressão para um aplicativo cliente (por exemplo, um bot de chat), você precisará implantar o aplicativo no ponto de extremidade de previsão.

Neste guia de início rápido, você aprende a implantar um aplicativo. Você cria um recurso de ponto de extremidade de previsão, atribui o recurso ao aplicativo, treina o aplicativo e publica o aplicativo.

## <a name="prerequisites"></a>Pré-requisitos

* Obtenha uma [assinatura do Azure](https://azure.microsoft.com/free).
* Conclua o [início rápido do portal anterior](get-started-portal-build-app.md) ou [Baixe e importe o aplicativo](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/in-portal/build-portal-app.json).

## <a name="create-the-endpoint-resource"></a>Criar o recurso de ponto de extremidade

Você cria o recurso de ponto de extremidade de previsão no portal do Azure. Esse recurso só deve ser usado para consultas de previsão de ponto de extremidade. Não use esse recurso para criar alterações no aplicativo.

1. Iniciar sessão no [portal do Azure](https://ms.portal.azure.com/).

1. Selecione o sinal de **+** verde no painel superior esquerdo. Pesquise `Cognitive Services` no Marketplace e selecione-o.

1. Configure a assinatura com as seguintes configurações:

   |Definição|Valor|Objetivo|
   |--|--|--|
   |Nome|`my-cognitive-service-resource`|O nome do recurso do Azure. Você precisará desse nome ao atribuir o recurso ao aplicativo no portal do LUIS.|
   |Subscrição|A sua subscrição|Selecione uma das assinaturas associadas à sua conta.|
   |Localização|**E.U.A. Oeste**|A região do Azure para este recurso.|
   |Escalão de preço|**S0**|O tipo de preço padrão para este recurso.|
   |Grupo de recursos|`my-cognitive-service-resource-group`|Crie um novo grupo de recursos para todos os seus recursos de serviço cognitiva. Quando terminar os recursos, você poderá excluir o grupo de recursos para limpar sua assinatura. |
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

1. Selecione seu locatário, assinatura e nome do recurso. Selecione **atribuir recurso**.

   ![Atribuir um recurso ao seu aplicativo](./media/get-started-portal-deploy-app/assign-resource.png)

1. Localize a nova linha na tabela e copie a URL do ponto de extremidade. Ele é construído corretamente para fazer uma solicitação de `HTTP GET` ao ponto de extremidade da API do LUIS para uma previsão.

## <a name="train-and-publish-the-app"></a>Preparar e publicar a aplicação

Treine o aplicativo quando você estiver pronto para testá-lo. Publique o aplicativo quando desejar que a versão treinada atualmente esteja disponível para aplicativos cliente do tempo de execução de ponto de extremidade de previsão de consulta.

1. Se o aplicativo for não treinado, selecione **treinar** no menu no canto superior direito.

1. Selecione **publicar** no menu superior. Selecione o slot de produção e publique.

1. Quando a barra de notificação for exibida, a publicação será concluída.

1. Na página gerenciar recursos do **Azure** da seção, localize a lista de recursos atribuídos e as URLs de ponto de extremidade correspondentes.

1. Copie a consulta de exemplo em uma janela do navegador e adicione o usuário expressão como o parâmetro `query`.

## <a name="prediction-endpoint-request"></a>Solicitação de ponto de extremidade de previsão

O `query=` no final da URL é curto para a **consulta** e é onde o expressão do usuário é acrescentado à solicitação get. Após a `query=`, insira o mesmo usuário expressão usado no final do início rápido anterior:

```Is there a form named hrf-234098```

Verifique se a cadeia de caracteres de consulta inclui os seguintes pares:

* `show-all-intents=true`
* `verbose=true`

O navegador mostra a resposta:

```JSON
{
    "query": "Is there a form named hrf-234098",
    "prediction": {
        "topIntent": "FindForm",
        "intents": {
            "FindForm": {
                "score": 0.9768753
            },
            "None": {
                "score": 0.0216071177
            }
        },
        "entities": {
            "Human Resources Form Number": [
                "hrf-234098"
            ],
            "$instance": {
                "Human Resources Form Number": [
                    {
                        "type": "Human Resources Form Number",
                        "text": "hrf-234098",
                        "startIndex": 22,
                        "length": 10,
                        "modelTypeId": 8,
                        "modelType": "Regex Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ]
            }
        }
    }
}
```

Para ver esse mesmo nível de informação no painel de teste, você deve publicar o aplicativo. Depois que o aplicativo for publicado, selecione **comparar com publicado** no painel de teste. Use a **exibição mostrar JSON** no painel de teste publicado para ver o mesmo JSON da etapa anterior. Dessa forma, você pode comparar o aplicativo atual no qual está trabalhando com um aplicativo que é publicado no ponto de extremidade.

[![comparar a edição atual versus a versão publicada do aplicativo](./media/get-started-portal-deploy-app/compare-test-pane.png)](./media/get-started-portal-deploy-app/compare-test-pane.png#lightbox)

## <a name="clean-up-resources"></a>Limpar recursos

Quando você terminar este guia de início rápido, selecione **meus aplicativos** no menu de navegação superior. Marque a caixa de seleção do aplicativo na lista e, em seguida, selecione **excluir** na barra de ferramentas de contexto acima da lista.

[![excluir aplicativo da minha lista de aplicativos](./media/get-started-portal-build-app/delete-app.png)](./media/get-started-portal-build-app/delete-app.png#lightbox)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Identificar entidades e intenções comuns](luis-tutorial-prebuilt-intents-entities.md)
