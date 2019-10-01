---
title: 'Início rápido: criar aplicativo-LUIS'
titleSuffix: Azure Cognitive Services
description: Crie uma aplicação LUIS que utilize o domínio pré-concebido `HomeAutomation` para ligar e desligar as luzes e os eletrodomésticos. Este domínio pré-concebido fornece intenções, entidades e expressões de exemplo. Quando terminar, obterá um ponto final de LUIS em execução na cloud.
services: cognitive-services
author: diberry
ms.custom: seodec18
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 748c51e74db20ac101dc2dff0d924567acded114
ms.sourcegitcommit: 6fe40d080bd1561286093b488609590ba355c261
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/01/2019
ms.locfileid: "71703230"
---
# <a name="quickstart-use-prebuilt-home-automation-app"></a>Início rápido: Usar aplicativo de automação inicial predefinido

Neste início rápido, crie uma aplicação LUIS que utiliza o domínio pré-concebido `HomeAutomation` para ligar e desligar as luzes e os eletrodomésticos. Este domínio pré-concebido fornece intenções, entidades e expressões de exemplo. Quando terminar, obterá um ponto final de LUIS em execução na cloud.

## <a name="prerequisites"></a>Pré-requisitos

Para este artigo, precisa de uma conta de LUIS gratuita criada no portal do LUIS em [https://www.luis.ai](https://www.luis.ai). 

[!INCLUDE [Sign in to LUIS](./includes/sign-in-process.md)]

## <a name="create-a-new-app"></a>Criar uma nova aplicação
Pode criar e gerir as suas aplicações em **My Apps** (As Minhas Aplicações). 

1. Selecione **Create new app** (Criar nova aplicação).

    [![Captura de ecrã da lista de aplicações](media/luis-quickstart-new-app/app-list.png "captura de ecrã da lista de aplicações")](media/luis-quickstart-new-app/app-list.png)

1. Na caixa de diálogo, atribua o nome "Home Automation" à aplicação.

    [![Captura de ecrã de criar nova aplicação pop-up caixa de diálogo](media/luis-quickstart-new-app/create-new-app-dialog.png "captura de ecrã de criar nova aplicação diálogo pop-up")](media/luis-quickstart-new-app/create-new-app-dialog.png)

1. Escolha a cultura da sua aplicação. Para esta aplicação Home Automation, selecione English (Inglês). Em seguida, selecione **Done** (Concluído). O LUIS cria a aplicação Home Automation. 

    >[!NOTE]
    >A cultura não pode ser alterada assim que a aplicação for criada. 

## <a name="add-prebuilt-domain"></a>Adicionar um domínio pré-concebido

Selecione **Prebuilt domains** (Domínios pré-concebidos) no painel de navegação do lado esquerdo. Em seguida, procure "Home". Selecione **Add domain** (Adicionar domínio).

[![Domínio de captura de ecrã da home page automatização indicadas no menu de domínio pré-criado](media/luis-quickstart-new-app/home-automation.png "domínio de captura de ecrã da home page automatização indicadas no menu de domínio pré-criado")](media/luis-quickstart-new-app/home-automation.png)

Quando o domínio for adicionado com êxito, a caixa Prebuilt domains (Domínios pré-concebidos) apresenta um botão **Remove domain** (Remover domínio).

[![Domínio de captura de ecrã da home page automatização com o botão Remover](media/luis-quickstart-new-app/remove-domain.png "domínio de captura de ecrã da home page automatização com o botão Remover")](media/luis-quickstart-new-app/remove-domain.png)

## <a name="intents-and-entities"></a>Intenções e entidades

Selecione **Intents** (Intenções) no painel de navegação do lado esquerdo para rever as intenções do domínio HomeAutomation. Cada intenção tem expressões de exemplo.

![Captura de tela da lista de tentativas de HomeAutomation](media/luis-quickstart-new-app/home-automation-intents.png "captura de tela da lista de intenções do HomeAutomation")]

> [!NOTE]
> **None** (Nenhuma) é uma intenção fornecida por todas as aplicações LUIS. Pode utilizá-la para processar expressões que não correspondem à funcionalidade que a sua aplicação fornece. 

Selecione a intenção **HomeAutomation.TurnOff**. Pode ver que a intenção contém uma lista de expressões que têm etiquetas de entidades.

[![Captura de ecrã de HomeAutomation.TurnOff intenção](media/luis-quickstart-new-app/home-automation-turnoff.png "intenção de captura de ecrã de HomeAutomation.TurnOff")](media/luis-quickstart-new-app/home-automation-turnoff.png)

## <a name="train-the-luis-app"></a>Preparar a aplicação LUIS

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="test-your-app"></a>Testar a aplicação
Assim que preparar a sua aplicação, pode testá-la. Selecione **Test** (Testar) no painel de navegação superior. Escreva uma expressão de teste, tal como "Turn off the lights" (Desligar as luzes) no painel Interactive Testing (Testes Interativos) e prima Enter. 

```
Turn off the lights
```

Verifique se a intenção com a melhor classificação corresponde à intenção esperada para cada expressão de teste.

Neste exemplo, `Turn off the lights` é identificado corretamente como a principal intenção de Pontuação de **HomeAutomation.** inativação.

[![Painel de captura de ecrã de teste com a expressão realçado](media/luis-quickstart-new-app/test.png "painel de captura de ecrã de teste com a expressão realçado")](media/luis-quickstart-new-app/test.png)


Selecione **inspecionar** para examinar mais informações sobre a previsão.

![Captura de tela do painel de teste com expressão realçado](media/luis-quickstart-new-app/review-test-inspection-pane-in-portal.png)

Selecione **Test** (Testar) novamente para fechar o painel de teste. 

<a name="publish-your-app"></a>

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Publicar a aplicação para obter o URL de ponto final

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="query-the-v2-api-prediction-endpoint"></a>Consultar o ponto de extremidade de previsão da API v2

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)] 

1. Vá para o final do URL no endereço, introduza `turn off the living room light` e prima Enter. 

    #### <a name="v2-prediction-endpointtabv2"></a>[Ponto de extremidade de previsão v2](#tab/V2)

    `https://<region>.api.cognitive.microsoft.com/luis/**v2.0**/apps/<appID>?subscription-key=<YOUR_KEY>&**q=<user-utterance-text>**`

    O navegador exibe a versão da **API v2** da resposta JSON do ponto de extremidade http.

    ```json
    {
      "query": "turn off the lights",
      "topScoringIntent": {
        "intent": "HomeAutomation.TurnOff",
        "score": 0.995867
      },
      "entities": [
        {
          "entity": "lights",
          "type": "HomeAutomation.DeviceType",
          "startIndex": 13,
          "endIndex": 18,
          "resolution": {
            "values": [
              "light"
            ]
          }
        }
      ]
    }
    ```
    
    #### <a name="v3-prediction-endpointtabv3"></a>[Ponto de extremidade de previsão v3](#tab/V3)

    Para uma [consulta de API v3](luis-migration-api-v3.md), no navegador, altere a solicitação obter método HTTPS, alterando os valores entre colchetes angulares para seus próprios valores.     

    `https://<region>.api.cognitive.microsoft.com/luis/**v3.0-preview**/apps/<appID>/**slots**/**production**/**predict**?subscription-key=<YOUR_KEY>&**query=<user-utterance-text>**`

    ```json
    {
        "query": "turn off the lights",
        "prediction": {
            "normalizedQuery": "turn off the lights",
            "topIntent": "HomeAutomation.TurnOff",
            "intents": {
                "HomeAutomation.TurnOff": {
                    "score": 0.99649024
                }
            },
            "entities": {
                "HomeAutomation.DeviceType": [
                    [
                        "light"
                    ]
                ]
            }
        }
    }
    ```


    Saiba mais sobre o [ponto de extremidade de previsão v3](luis-migration-api-v3.md).
    
    * * * 

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Passos Seguintes

Pode chamar o ponto final do código:

> [!div class="nextstepaction"]
> [Chamar um ponto final de LUIS com código](luis-get-started-cs-get-intent.md)
