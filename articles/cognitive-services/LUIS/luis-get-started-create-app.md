---
title: 'Quickstart: criar app - LUIS'
description: Este quickstart mostra como criar uma aplicação LUIS que utiliza o domínio pré-construído `HomeAutomation` para ligar e desligar luzes e aparelhos. Este domínio pré-concebido fornece intenções, entidades e expressões de exemplo. Quando terminar, obterá um ponto final de LUIS em execução na cloud.
ms.topic: quickstart
ms.date: 05/05/2020
ms.openlocfilehash: 186a60a7fd8315d68718ceedd3b5cadb4d3645e8
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83589165"
---
# <a name="quickstart-use-prebuilt-home-automation-app"></a>Início Rápido: utilizar a aplicação Home Automation pré-concebida

Neste início rápido, crie uma aplicação LUIS que utiliza o domínio pré-concebido `HomeAutomation` para ligar e desligar as luzes e os eletrodomésticos. Este domínio pré-concebido fornece intenções, entidades e expressões de exemplo. Quando terminar, obterá um ponto final de LUIS em execução na cloud.

[!INCLUDE [Sign in to LUIS](./includes/sign-in-process.md)]

[!INCLUDE [Select authoring resource](./includes/select-authoring-resource.md)]

## <a name="create-a-new-app"></a>Criar uma nova aplicação
Pode criar e gerir as suas aplicações em **My Apps** (As Minhas Aplicações).

1. Na lista das Minhas aplicações, selecione **+ Nova aplicação para conversação,** em seguida, na lista de opções, selecione **+ Nova aplicação para conversação** novamente.

1. Na caixa de diálogo, nomeie a sua `Home Automation` aplicação.
1. Selecione **inglês** como a cultura.
1. Insira uma descrição opcional.
1. Não selecione um recurso de previsão se ainda não criou o recurso. Para utilizar o ponto final de previsão da sua aplicação (encenação ou produção), precisa de atribuir um recurso de previsão.
1. Selecione **Done** (Concluído).

    LUIS cria a app.

    ![Na caixa de diálogo, nomeie a sua aplicação 'Home Automation'](./media/create-new-app-details.png)

    >[!NOTE]
    >A cultura não pode ser alterada assim que a aplicação for criada.

## <a name="add-prebuilt-domain"></a>Adicionar um domínio pré-concebido

1. Na navegação à esquerda, selecione **domínios pré-construídos**.
1. Pesquisa por **HomeAutomation**.
1. **Selecione Adicionar domínio** no cartão HomeAutomation.

    ![Selecione 'Domínios pré-construídos' e procure 'HomeAutomation'. Selecione 'Adicionar domínio' no cartão HomeAutomation.](media/luis-quickstart-new-app/home-automation.png)

    Quando o domínio for adicionado com êxito, a caixa Prebuilt domains (Domínios pré-concebidos) apresenta um botão **Remove domain** (Remover domínio).

## <a name="intents-and-entities"></a>Intenções e entidades

1. Selecione **Intenções** para rever as intenções de domínio HomeAutomation. As intenções de domínio pré-construídatêm declarações exemplo.

    ![Screenshot da lista de intenções de HomeAutomation](media/luis-quickstart-new-app/home-automation-intents.png "Screenshot da lista de intenções de HomeAutomation")

    > [!NOTE]
    > **None** (Nenhuma) é uma intenção fornecida por todas as aplicações LUIS. Pode utilizá-la para processar expressões que não correspondem à funcionalidade que a sua aplicação fornece.

1. Selecione a intenção **HomeAutomation.TurnOff**. A intenção contém uma lista de declarações de exemplo que são rotulados com entidades.

    > [!div class="mx-imgBorder"]
    > [![Captura de ecrã da intenção HomeAutomation.TurnOff](media/luis-quickstart-new-app/home-automation-turnoff.png "Captura de ecrã da intenção HomeAutomation.TurnOff")](media/luis-quickstart-new-app/home-automation-turnoff.png)

## <a name="train-the-luis-app"></a>Preparar a aplicação LUIS

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="test-your-app"></a>Testar a aplicação
Assim que preparar a sua aplicação, pode testá-la.

1. Selecione **Teste** a partir da navegação superior direita.

1. Digite uma expressão de teste como no painel de `Turn off the lights` teste interativo e prima Enter.

    ```
    Turn off the lights
    ```

    Neste exemplo, `Turn off the lights` é corretamente identificada como a principal intenção de pontuação do **HomeAutomation.TurnOff**.

    ![Captura de ecrã do painel Test (Testar) com expressão realçada](media/luis-quickstart-new-app/review-test-inspection-pane-in-portal.png)

1. **Selecione Inspecionar** para ver mais informações sobre a previsão.

    > [!div class="mx-imgBorder"]
    > ![Screenshot do painel de teste com informações de inspeção](media/luis-quickstart-new-app/test.png)

1. Feche o painel de teste.

<a name="publish-your-app"></a>

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Publicar a aplicação para obter o URL de ponto final

[!INCLUDE [LUIS How to Publish steps](./includes/howto-publish.md)]

<a name="query-the-v2-api-prediction-endpoint"></a>

## <a name="query-the-v3-api-prediction-endpoint"></a>Consulta do ponto final da previsão v3 API

[!INCLUDE [LUIS How to get endpoint first step](./includes/v3-prediction-endpoint.md)]

2. Na barra de endereços do navegador, para a cadeia de consulta, certifique-se de que o seguinte nome e barras de valor estão no URL. Se não estiverem na corda de consulta, adicione-as:

    |Nome/par de valor|
    |--|
    |`verbose=true`|
    |`show-all-intents=true`|

3. Na barra de endereços do navegador, vá até ao fim do URL e introduza `turn off the living room light` para o valor de _consulta_ e, em seguida, prima Enter.

    ```json
    {
        "query": "turn off the living room light",
        "prediction": {
            "topIntent": "HomeAutomation.TurnOff",
            "intents": {
                "HomeAutomation.TurnOff": {
                    "score": 0.984315455
                },
                "HomeAutomation.QueryState": {
                    "score": 0.009912962
                },
                "HomeAutomation.TurnDown": {
                    "score": 0.00626645749
                },
                "HomeAutomation.TurnUp": {
                    "score": 0.00572059769
                },
                "HomeAutomation.SetDevice": {
                    "score": 0.00379381469
                },
                "HomeAutomation.TurnOn": {
                    "score": 0.00366983772
                },
                "None": {
                    "score": 0.000623856
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
                            "score": 0.907323956,
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

    Saiba mais sobre o ponto final da [previsão V3](luis-migration-api-v3.md).


## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Passos seguintes

Pode chamar o ponto final do código:

> [!div class="nextstepaction"]
> [Chamar um ponto final de LUIS com código](luis-get-started-cs-get-intent.md)
