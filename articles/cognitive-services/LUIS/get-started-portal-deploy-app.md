---
title: 'Quickstart: Implementar uma app com o portal LUIS'
description: Este quickstart mostra como implementar uma aplicação criando um recurso de ponto final de previsão, atribuindo o recurso, formação e publicação da app.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 05/06/2020
ms.openlocfilehash: b051f349716b5597adee4509cfe774978a8f61aa
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2020
ms.locfileid: "95972521"
---
# <a name="quickstart-deploy-an-app-in-the-luis-portal"></a>Quickstart: Implementar uma aplicação no portal LUIS

Quando a sua aplicação LUIS estiver pronta para devolver as previsões de expressão a uma aplicação do cliente (por exemplo, um chat bot), é necessário implementar a app no ponto final da previsão.

Neste arranque rápido, aprende-se a implementar uma aplicação. Cria-se um recurso de ponto final de previsão, atribui o recurso à app, treina a aplicação e publica a app.

## <a name="prerequisites"></a>Pré-requisitos

* Obtenha uma [assinatura Azure.](https://azure.microsoft.com/free)
* Complete o [portal anterior quickstart](get-started-portal-build-app.md) ou [download e importe a app.](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/apps/portal-build-app.json)
* Se tiver aplicações que pré-datam a autenticação de recursos Azure, [migra para um recurso Azure](luis-migration-authoring.md). Algumas páginas do portal parecem diferentes quando a autenticação de e-mail está em vigor.

<a name="create-the-endpoint-resource"></a>

[!INCLUDE [Create LUIS resource](includes/create-luis-resource.md)]

## <a name="assign-the-resource-key-to-the-luis-app-in-the-luis-portal"></a>Atribuir a chave de recursos à app LUIS no portal LUIS

Sempre que cria um novo recurso de autoria ou previsão de consulta para o LUIS, tem de atribuir o recurso à app LUIS. Depois de atribuído, não precisará de fazer este passo novamente a menos que crie um novo recurso. Pode criar um novo recurso para expandir as regiões da sua app ou para suportar um maior número de consultas de previsão.

1. Inscreva-se no [portal LUIS](https://www.luis.ai) e escolha a aplicação **myEnglishApp** da lista de aplicações.

1. **Selecione Gerir** no menu superior direito e, em seguida, selecione **Recursos Azure**.

1. Para adicionar o LUIS, **selecione Adicionar recurso de previsão**.

    ![Para adicionar o recurso de previsão LUIS, selecione Adicionar recurso de previsão](./media/get-started-portal-deploy-app/azure-resources-add-prediction-resource.png)

1. Selecione o seu inquilino, subscrição e nome de recurso. Selecione **Atribuir recurso**.

   > [!div class="mx-imgBorder"]
   > ![Atribua um recurso à sua aplicação](./media/get-started-portal-deploy-app/assign-resource.png)

1. Complete os mesmos passos para adicionar a chave de autoria à sua aplicação.

1. Encontre a nova linha na tabela para o novo recurso de previsão e copie o URL do ponto final. Foi construído corretamente para fazer um `HTTP GET` pedido ao ponto final da API luis para uma previsão.

> [!TIP]
> Se pretender utilizar a aprendizagem ativa para melhorar a sua aplicação LUIS, selecione **Alterar parâmetros de consulta** e selecione Guardar **registos**. Esta ação altera o URL de exemplo adicionando o `log=true` parâmetro de consulta. Copie e use o URL de consulta de exemplo alterado ao fazer consultas de previsão para o ponto final de tempo de execução.

## <a name="train-the-app"></a>Preparar a aplicação

Se mudou a aplicação desde que a treinou pela última vez, [treine](get-started-portal-build-app.md) a aplicação.

## <a name="publish-the-app-to-the-prediction-endpoint"></a>Publique a app no ponto final da previsão

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

## <a name="prediction-endpoint-request"></a>Pedido de previsão de ponto final

No portal, `query=` no final do URL é onde a expressão do utilizador é anexada ao pedido GET. Após a `query=` , introduza a mesma expressão de utilizador utilizada no final do início rápido anterior:

```Is there a form named hrf-234098```

Certifique-se de que a cadeia de consulta inclui os seguintes pares:

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

Para ver este mesmo nível de informação no painel de teste, tem de publicar a aplicação. Depois de a aplicação ser publicada, **selecione Compare com a publicação** no painel de teste. Use **a vista JSON** do Show no painel de teste publicado para ver o mesmo JSON do passo anterior. Desta forma, pode comparar alterações à aplicação atual em que está a trabalhar com uma aplicação que é publicada no ponto final.

[![Compare atualmente editando versus versão publicada da app](./media/get-started-portal-deploy-app/compare-test-pane.png)](./media/get-started-portal-deploy-app/compare-test-pane.png#lightbox)

## <a name="clean-up-resources"></a>Limpar os recursos

Quando terminar este arranque rápido, selecione **As minhas aplicações** a partir do menu de navegação superior. Selecione a caixa de verificação da aplicação da lista e, em seguida, **selecione Eliminar** a partir da barra de ferramentas de contexto acima da lista.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Identificar intenções e entidades comuns](./tutorial-machine-learned-entity.md)