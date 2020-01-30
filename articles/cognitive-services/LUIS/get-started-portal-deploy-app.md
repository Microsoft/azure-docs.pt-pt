---
title: 'Início rápido: implantar um aplicativo com o portal do LUIS'
titleSuffix: Azure Cognitive Services
description: Este guia de início rápido mostra como implantar um aplicativo Criando um recurso de ponto de extremidade de previsão, atribuindo o recurso, treinando e publicando o aplicativo.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 01/27/2020
ms.author: diberry
ms.openlocfilehash: 0ee2b33aa3388b3cb99aa42c338ded800c9679a4
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2020
ms.locfileid: "76772485"
---
# <a name="quickstart-deploy-an-app-in-the-luis-portal"></a>Início rápido: implantar um aplicativo no portal do LUIS

Quando seu aplicativo LUIS estiver pronto para retornar previsões de expressão para um aplicativo cliente (por exemplo, um bot de chat), você precisará implantar o aplicativo no ponto de extremidade de previsão.

Neste guia de início rápido, você aprende a implantar um aplicativo. Você cria um recurso de ponto de extremidade de previsão, atribui o recurso ao aplicativo, treina o aplicativo e publica o aplicativo.

## <a name="prerequisites"></a>Pré-requisitos

* Obtenha uma [assinatura do Azure](https://azure.microsoft.com/free).
* Conclua o [início rápido do portal anterior](get-started-portal-build-app.md) ou [Baixe e importe o aplicativo](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/in-portal/build-portal-app.json).
* Se você tiver aplicativos que tenham a autenticação de recursos do Azure de data de antecipação, [migre para um recurso do Azure](luis-migration-authoring.md). Algumas páginas do portal parecem diferentes quando a autenticação de email está em vigor.

## <a name="create-the-endpoint-resource"></a>Criar o recurso de ponto de extremidade

Você cria o recurso de ponto de extremidade de previsão no portal do Azure. Esse recurso só deve ser usado para consultas de previsão de ponto de extremidade. Não use esse recurso para criar alterações no aplicativo.

1. Entre e crie um recurso no [portal do Azure](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne).

1. Configure a assinatura com as seguintes configurações:

   |Definição|Valor|Finalidade|
   |--|--|--|
   |Nome|`my-luis-resource`|O nome do recurso do Azure. Você precisará desse nome ao atribuir o recurso ao aplicativo no portal do LUIS.|
   |Subscrição|A sua subscrição|Selecione uma das assinaturas associadas à sua conta.|
   |Grupo de recursos|`my-resource-group`|Crie um novo grupo de recursos para todos os seus recursos de serviço cognitiva. Quando terminar os recursos, você poderá excluir o grupo de recursos para limpar sua assinatura. |
   |Local de criação|**E.U.A. Oeste**|A região do Azure para criação.|
   |Tipo de preço de criação|**F0**|O tipo de preço padrão para criação.|
   |Local do tempo de execução|**E.U.A. Oeste**|A região do Azure para consultas de ponto de extremidade de previsão.|
   |Tipo de preço de tempo de execução|**S0**|Este nível de preços fornece sites de alto tráfego.|
   | | | |


   ![Escolha da API do Azure](./media/luis-how-to-azure-subscription/create-resource-in-azure.png)

1. Selecione **criar** para criar o recurso do Azure.

   Na próxima seção, você aprenderá a conectar esse novo recurso a um aplicativo LUIS no portal do LUIS.

## <a name="assign-the-resource-key-to-the-luis-app-in-the-luis-portal"></a>Atribuir a chave de recurso ao aplicativo LUIS no portal do LUIS

Sempre que você criar um novo recurso para LUIS, será necessário atribuir o recurso ao aplicativo LUIS. Depois de atribuído, você não precisará executar esta etapa novamente, a menos que crie um novo recurso. Você pode criar um novo recurso para expandir as regiões do seu aplicativo ou para dar suporte a um número maior de consultas de previsão.

1. Entre no portal do [Luis de visualização](https://preview.luis.ai) e escolha o aplicativo **myEnglishApp** na lista de aplicativos.

1. Selecione **gerenciar** no menu superior direito e, em seguida, selecione **recursos do Azure**.

1. Para adicionar o LUIS, selecione **Adicionar recurso de previsão**.

    ![Para adicionar o recurso de previsão LUIS, selecione Adicionar recurso de previsão](./media/get-started-portal-deploy-app/azure-resources-add-prediction-resource.png)

1. Selecione seu locatário, assinatura e nome do recurso. Selecione **atribuir recursos**.

   ![Atribuir um recurso à sua aplicação](./media/get-started-portal-deploy-app/assign-resource.png)

1. Conclua as mesmas etapas para adicionar a chave de criação ao seu aplicativo.

1. Localize a nova linha na tabela para o novo recurso de previsão e copie a URL do ponto de extremidade. Ele é construído corretamente para fazer uma solicitação de `HTTP GET` ao ponto de extremidade da API do LUIS para uma previsão.

> [!TIP]
> Se pretender utilizar o Ative learning para melhorar a sua aplicação LUIS, selecione **parâmetros** de consulta de alteração e selecione **Registos Save**. Esta ação altera o URL de exemplo adicionando o parâmetro de `log=true` consulta. Copie e use o URL de consulta de exemplo alterado ao fazer consultas de previsão para o ponto final do tempo de execução.

## <a name="train-the-app"></a>Preparar a aplicação

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="publish-the-app-to-the-prediction-endpoint"></a>Publicar o aplicativo no ponto de extremidade de previsão

[!INCLUDE [LUIS How to Train steps](includes/howto-publish.md)]

## <a name="prediction-endpoint-request"></a>Solicitação de ponto de extremidade de previsão

No portal de visualização, `query=` no final da URL é onde o expressão do usuário é acrescentado à solicitação GET. Após a `query=`, insira o mesmo usuário expressão usado no final do início rápido anterior:

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

Para ver esse mesmo nível de informação no painel de teste, você deve publicar o aplicativo. Depois que o aplicativo for publicado, selecione **comparar com publicado** no painel de teste. Use a **exibição mostrar JSON** no painel de teste publicado para ver o mesmo JSON da etapa anterior. Dessa forma, você pode comparar as alterações para o aplicativo atual em que está trabalhando com um aplicativo que é publicado no ponto de extremidade.

[![Comparar atualmente a edição versus versão publicada da app](./media/get-started-portal-deploy-app/compare-test-pane.png)](./media/get-started-portal-deploy-app/compare-test-pane.png#lightbox)

## <a name="clean-up-resources"></a>Limpar recursos

Quando você terminar este guia de início rápido, selecione **meus aplicativos** no menu de navegação superior. Marque a caixa de seleção do aplicativo na lista e, em seguida, selecione **excluir** na barra de ferramentas de contexto acima da lista.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Identificar entidades e intenções comuns](luis-tutorial-prebuilt-intents-entities.md)
