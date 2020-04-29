---
title: 'Quickstart: Implemente uma app com o portal LUIS'
description: Este quickstart mostra como implementar uma aplicação criando um recurso final de previsão, atribuindo o recurso, formação e publicação da app.
ms.topic: quickstart
ms.date: 04/06/2020
ms.openlocfilehash: aaf86766c2357c5382b78cd4a35fd4b159e5c0f3
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80756298"
---
# <a name="quickstart-deploy-an-app-in-the-luis-portal"></a>Quickstart: Implementar uma aplicação no portal LUIS

Quando a sua aplicação LUIS estiver pronta para devolver previsões de expressão a uma aplicação de cliente (por exemplo, um chat bot), precisa de implementar a app para o ponto final da previsão.

Neste arranque rápido, aprende-se a implementar uma aplicação. Cria um recurso de ponto final de previsão, atribui o recurso à app, treina a app e publica a aplicação.

## <a name="prerequisites"></a>Pré-requisitos

* Obtenha uma [assinatura Azure.](https://azure.microsoft.com/free)
* Complete o [portal anterior, arranque](get-started-portal-build-app.md) rápido ou [descarregamento e importe a aplicação.](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/in-portal/build-portal-app.json)
* Se tiver aplicações que antecedam a autenticação de recursos Azure, [emigra para um recurso Azure.](luis-migration-authoring.md) Algumas páginas do portal parecem diferentes quando a autenticação por e-mail está em vigor.

<a name="create-the-endpoint-resource"></a>

[!INCLUDE [Create LUIS resource](includes/create-luis-resource.md)]

## <a name="assign-the-resource-key-to-the-luis-app-in-the-luis-portal"></a>Atribuir a chave de recursos à app LUIS no portal LUIS

Sempre que cria um novo recurso de autor ou previsão de consulta para o LUIS, tem de atribuir o recurso à app LUIS. Depois de atribuído, não precisará de fazer este passo de novo, a menos que crie um novo recurso. Poderá criar um novo recurso para expandir as regiões da sua app ou para suportar um maior número de consultas de previsão.

1. Inscreva-se no [portal DE pré-visualização](https://preview.luis.ai) luis e escolha a aplicação **myEnglishApp** na lista de aplicações.

1. Selecione **Gerir** no menu superior direito e, em seguida, selecione **Recursos Azure**.

1. Para adicionar o LUIS, selecione **Adicionar recurso**de previsão .

    ![Para adicionar o recurso de previsão LUIS, selecione Adicionar recurso de previsão](./media/get-started-portal-deploy-app/azure-resources-add-prediction-resource.png)

1. Selecione o seu nome de inquilino, subscrição e recursos. Selecione **recurso atribuir**.

   ![Atribuir um recurso à sua aplicação](./media/get-started-portal-deploy-app/assign-resource.png)

1. Complete os mesmos passos para adicionar a chave de autor à sua aplicação.

1. Encontre a nova linha na tabela para o novo recurso de previsão e copie o URL do ponto final. É corretamente construído para fazer `HTTP GET` um pedido ao ponto final da API LUIS para uma previsão.

> [!TIP]
> Se pretender utilizar o Ative learning para melhorar a sua aplicação LUIS, selecione **parâmetros** de consulta de alteração e selecione **Registos Save**. Esta ação altera o URL `log=true` de exemplo adicionando o parâmetro de corda de consulta. Copie e use o URL de consulta de exemplo alterado ao fazer consultas de previsão para o ponto final do tempo de execução.

## <a name="train-the-app"></a>Preparar a aplicação

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="publish-the-app-to-the-prediction-endpoint"></a>Publique a app para o ponto final da previsão

[!INCLUDE [LUIS How to Train steps](includes/howto-publish.md)]

## <a name="prediction-endpoint-request"></a>Pedido de ponto final de previsão

No portal de `query=` pré-visualização, no final do URL é onde a expressão do utilizador está anexada ao pedido GET. Depois `query=`da , introduza a mesma expressão de utilizador utilizada no final do quickstart anterior:

```Is there a form named hrf-234098```

Certifique-se de que a corda de consulta inclui os seguintes pares:

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

Para ver este mesmo nível de informação no painel de teste, tem de publicar a aplicação. Depois da aplicação ser publicada, selecione **Compare com publicado** no painel de teste. Use **a visão JSON** do show no painel de teste publicado para ver o mesmo JSON que o passo anterior. Desta forma, pode comparar alterações à aplicação atual em que está a trabalhar com uma aplicação que é publicada no ponto final.

[![Compare atualmente a edição versus versão publicada da app](./media/get-started-portal-deploy-app/compare-test-pane.png)](./media/get-started-portal-deploy-app/compare-test-pane.png#lightbox)

## <a name="clean-up-resources"></a>Limpar recursos

Quando terminar com este quickstart, selecione **Minhas aplicações** no menu de navegação superior. Selecione a caixa de verificação da aplicação da lista e, em seguida, selecione **Eliminar** a partir da barra de ferramentas de contexto acima da lista.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Identificar intenções e entidades comuns](luis-tutorial-prebuilt-intents-entities.md)
