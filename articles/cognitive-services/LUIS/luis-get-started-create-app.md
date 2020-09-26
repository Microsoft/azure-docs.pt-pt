---
title: 'Quickstart: criar app - LUIS'
description: Este quickstart mostra como criar uma aplicação LUIS que utiliza o domínio pré-construído `HomeAutomation` para ligar e desligar luzes e aparelhos. Este domínio pré-concebido fornece intenções, entidades e expressões de exemplo. Quando terminar, obterá um ponto final de LUIS em execução na cloud.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 05/05/2020
ms.openlocfilehash: 28bf79b61c0278a3f45820a23cd2c69f0b609700
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91316495"
---
# <a name="quickstart-use-prebuilt-home-automation-app"></a>Início Rápido: utilizar a aplicação Home Automation pré-concebida

Neste início rápido, crie uma aplicação LUIS que utiliza o domínio pré-concebido `HomeAutomation` para ligar e desligar as luzes e os eletrodomésticos. Este domínio pré-concebido fornece intenções, entidades e expressões de exemplo. Quando terminar, obterá um ponto final de LUIS em execução na cloud.

[!INCLUDE [Sign in to LUIS](./includes/sign-in-process.md)]

[!INCLUDE [Select authoring resource](./includes/select-authoring-resource.md)]

## <a name="create-a-new-app"></a>Criar uma nova aplicação
Pode criar e gerir as suas aplicações em **My Apps** (As Minhas Aplicações).

1. Na lista de aplicações My, selecione **+ Nova aplicação para conversação,** em seguida, na lista de opções, selecione **+ Nova aplicação para conversação** novamente.

1. Na caixa de diálogo, diga o seu pedido `Home Automation` .
1. Selecione **o inglês** como a cultura.
1. Introduza uma descrição opcional.
1. Não selecione um recurso de previsão se ainda não criou o recurso. Para utilizar o ponto final de previsão da sua aplicação (encenação ou produção), é necessário atribuir um recurso de previsão.
1. Selecione **Concluído**.

    LUIS cria a app.

    ![Na caixa de diálogo, nomeie a sua aplicação 'Home Automation'](./media/create-new-app-details.png)

    >[!NOTE]
    >A cultura não pode ser alterada assim que a aplicação for criada.

## <a name="add-prebuilt-domain"></a>Adicionar um domínio pré-concebido

1. Na navegação à esquerda, selecione **domínios pré-construídos.**
1. Pesquisa rumo **à HomeAutomation**.
1. **Selecione Adicionar** domínio no cartão HomeAutomation.

    > [!div class="mx-imgBorder"]
    > ![Selecione 'Domínios pré-construídos' e procure por 'HomeAutomation'. Selecione 'Adicionar domínio' no cartão HomeAutomation.](media/luis-quickstart-new-app/home-automation.png)

    Quando o domínio for adicionado com êxito, a caixa Prebuilt domains (Domínios pré-concebidos) apresenta um botão **Remove domain** (Remover domínio).

## <a name="intents-and-entities"></a>Intenções e entidades

1. Selecione **Intenções** para rever as intenções do domínio homeAutomation. As intenções de domínio pré-construído têm palavras de exemplo.

    > [!div class="mx-imgBorder"]
    > ![Screenshot da lista de intenções de HomeAutomation](media/luis-quickstart-new-app/home-automation-intents.png "Screenshot da lista de intenções de HomeAutomation")

    > [!NOTE]
    > **None** (Nenhuma) é uma intenção fornecida por todas as aplicações LUIS. Pode utilizá-la para processar expressões que não correspondem à funcionalidade que a sua aplicação fornece.

1. Selecione a intenção **HomeAutomation.TurnOff**. A intenção contém uma lista de palavras de exemplo que são rotuladas com entidades.

    > [!div class="mx-imgBorder"]
    > [![Captura de ecrã da intenção HomeAutomation.TurnOff](media/luis-quickstart-new-app/home-automation-turnoff.png "Captura de ecrã da intenção HomeAutomation.TurnOff")](media/luis-quickstart-new-app/home-automation-turnoff.png)

## <a name="train-the-luis-app"></a>Preparar a aplicação LUIS

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="test-your-app"></a>Testar a aplicação
Assim que preparar a sua aplicação, pode testá-la.

1. Selecione **Teste** a partir da navegação superior direita.

1. Digite uma expressão de teste como `Turn off the lights` no painel de teste interativo e prima Enter.

    ```
    Turn off the lights
    ```

    Neste exemplo, `Turn off the lights` é corretamente identificado como a intenção de pontuação superior da **HomeAutomation.TurnOff**.

    ![Captura de ecrã do painel Test (Testar) com expressão realçada](media/luis-quickstart-new-app/review-test-inspection-pane-in-portal.png)

1. **Selecione Inspecionar** para ver mais informações sobre a previsão.

    > [!div class="mx-imgBorder"]
    > ![Screenshot do painel de teste com informações de inspeção](media/luis-quickstart-new-app/test.png)

1. Feche o painel de teste.

<a name="publish-your-app"></a>

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Publicar a aplicação para obter o URL de ponto final

[!INCLUDE [LUIS How to Publish steps](./includes/howto-publish.md)]

<a name="query-the-v2-api-prediction-endpoint"></a>

## <a name="query-the-v3-api-prediction-endpoint"></a>Consulta do ponto final de previsão da API V3

[!INCLUDE [LUIS How to get endpoint first step](./includes/v3-prediction-endpoint.md)]

2. Na barra de endereços do navegador, para a cadeia de consulta, certifique-se de que o seguinte nome e barras de valor estão no URL. Se não estiverem na cadeia de consulta, adicione-as:

    |Par de nome/valor|
    |--|
    |`verbose=true`|
    |`show-all-intents=true`|

3. Na barra de endereços do navegador, vá até ao final do URL e introduza `turn off the living room light` o valor de _consulta_ e, em seguida, prima Enter.

    ```json
    {
        "query": "turn off the living room light",
        "prediction": {
            "topIntent": "HomeAutomation.TurnOff",
            "intents": {
                "HomeAutomation.TurnOff": {
                    "score": 0.969448864
                },
                "HomeAutomation.QueryState": {
                    "score": 0.0122336326
                },
                "HomeAutomation.TurnUp": {
                    "score": 0.006547436
                },
                "HomeAutomation.TurnDown": {
                    "score": 0.0050634006
                },
                "HomeAutomation.SetDevice": {
                    "score": 0.004951761
                },
                "HomeAutomation.TurnOn": {
                    "score": 0.00312553928
                },
                "None": {
                    "score": 0.000552945654
                }
            },
            "entities": {
                "HomeAutomation.Location": [
                    "living room"
                ],
                "HomeAutomation.DeviceName": [
                    [
                        "living room light"
                    ]
                ],
                "HomeAutomation.DeviceType": [
                    [
                        "light"
                    ]
                ],
                "$instance": {
                    "HomeAutomation.Location": [
                        {
                            "type": "HomeAutomation.Location",
                            "text": "living room",
                            "startIndex": 13,
                            "length": 11,
                            "score": 0.902181149,
                            "modelTypeId": 1,
                            "modelType": "Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ],
                    "HomeAutomation.DeviceName": [
                        {
                            "type": "HomeAutomation.DeviceName",
                            "text": "living room light",
                            "startIndex": 13,
                            "length": 17,
                            "modelTypeId": 5,
                            "modelType": "List Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ],
                    "HomeAutomation.DeviceType": [
                        {
                            "type": "HomeAutomation.DeviceType",
                            "text": "light",
                            "startIndex": 25,
                            "length": 5,
                            "modelTypeId": 5,
                            "modelType": "List Entity Extractor",
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

    Saiba mais sobre o ponto final de [previsão V3](luis-migration-api-v3.md).


## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Passos seguintes

Pode chamar o ponto final do código:

> [!div class="nextstepaction"]
> [Chamar um ponto final de LUIS com código](luis-get-started-cs-get-intent.md)
