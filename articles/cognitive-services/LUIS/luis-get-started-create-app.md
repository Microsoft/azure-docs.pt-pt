---
title: 'Quickstart: criar app - LUIS'
description: Este quickstart mostra como criar uma aplicação LUIS que utiliza o domínio pré-construído `HomeAutomation` para ligar e desligar luzes e aparelhos. Este domínio pré-concebido fornece intenções, entidades e expressões de exemplo. Quando terminar, obterá um ponto final de LUIS em execução na cloud.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 10/13/2020
ms.openlocfilehash: 60151e97c64a3d61044e4b82299573ee59951d46
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93128207"
---
# <a name="quickstart-use-prebuilt-home-automation-app"></a>Início Rápido: utilizar a aplicação Home Automation pré-concebida

Neste início rápido, crie uma aplicação LUIS que utiliza o domínio pré-concebido `HomeAutomation` para ligar e desligar as luzes e os eletrodomésticos. Este domínio pré-concebido fornece intenções, entidades e expressões de exemplo. Quando terminar, obterá um ponto final de LUIS em execução na cloud.

[!INCLUDE [Sign in to LUIS](./includes/sign-in-process.md)]

[!INCLUDE [Select authoring resource](./includes/select-authoring-resource.md)]

## <a name="create-a-new-app"></a>Criar uma nova aplicação
Pode criar e gerir as suas aplicações em **My Apps** (As Minhas Aplicações).

### <a name="create-an-application"></a>Criar uma aplicação

Para criar uma aplicação, clique **em + Nova aplicação.** 

Na janela que aparece, insira as seguintes informações:

|Nome  |Descrição  |
|---------|---------|
|Nome AName     | Um nome para a sua aplicação. Por exemplo, "domótica".        |
|Cultura     | A linguagem que a sua aplicação compreende e fala.   |
|Description | Uma descrição para o seu aplicativo.
|Recurso de previsão | O recurso de previsão que receberá consultas. |

Selecione **Done** (Concluído).

>[!NOTE]
>A cultura não pode ser alterada assim que a aplicação for criada.

## <a name="add-prebuilt-domain"></a>Adicionar um domínio pré-concebido

1. Na navegação à esquerda, selecione **domínios pré-construídos.**
1. Pesquisa rumo **à HomeAutomation** .
1. **Selecione Adicionar** domínio no cartão HomeAutomation.

    > [!div class="mx-imgBorder"]
    > ![Selecione 'Domínios pré-construídos' e procure por 'HomeAutomation'. Selecione 'Adicionar domínio' no cartão HomeAutomation.](media/luis-quickstart-new-app/home-automation.png)

    Quando o domínio for adicionado com êxito, a caixa Prebuilt domains (Domínios pré-concebidos) apresenta um botão **Remove domain** (Remover domínio).

## <a name="intents-and-entities"></a>Intenções e entidades

1. Selecione **Intenções** no menu de navegação à esquerda para ver as intenções do domínio homeAutomation. Tem palavras de exemplo, tais como `HomeAutomation.QueryState`     `HomeAutomation.SetDevice` e.

    > [!NOTE]
    > **None** (Nenhuma) é uma intenção fornecida por todas as aplicações LUIS. Pode utilizá-la para processar expressões que não correspondem à funcionalidade que a sua aplicação fornece.

1. Selecione a intenção **HomeAutomation.TurnOff** . A intenção contém uma lista de palavras de exemplo que são rotuladas com entidades.

    > [!div class="mx-imgBorder"]
    > [![Captura de ecrã da intenção HomeAutomation.TurnOff](media/luis-quickstart-new-app/home-automation-turnoff.png "Captura de ecrã da intenção HomeAutomation.TurnOff")](media/luis-quickstart-new-app/home-automation-turnoff.png)

1. Se quiser ver as entidades para a app, selecione **Entidades.** Se clicar numa das entidades, como **HomeAutomation.DeviceName** verá uma lista de valores associados. 
 
    :::image type="content" source="media/luis-quickstart-new-app/entities-page.png" alt-text="Texto alt imagem" lightbox="media/luis-quickstart-new-app/entities-page.png":::

## <a name="train-the-luis-app"></a>Preparar a aplicação LUIS

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="test-your-app"></a>Testar a aplicação
Assim que preparar a sua aplicação, pode testá-la.

1. Selecione **Teste** a partir da navegação superior direita.

1. Digite uma expressão de teste como `Turn off the lights` no painel de teste interativo e prima Enter. Por exemplo, *apare as luzes.*

    Neste exemplo, `Turn off the lights` é corretamente identificado como a intenção de pontuação superior da **HomeAutomation.TurnOff** .

    ![Captura de ecrã do painel Test (Testar) com expressão realçada](media/luis-quickstart-new-app/review-test-inspection-pane-in-portal.png)

1. **Selecione Inspecionar** para ver mais informações sobre a previsão.

    > [!div class="mx-imgBorder"]
    > ![Screenshot do painel de teste com informações de inspeção](media/luis-quickstart-new-app/test.png)

1. Feche o painel de teste.

<a name="publish-your-app"></a>

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Publicar a aplicação para obter o URL de ponto final

Para receber uma previsão LUIS num chat bot ou outra aplicação de cliente, é necessário publicar a app no ponto final de previsão.

1. Selecione **Publicar** no canto superior direito da janela.

1. Selecione a ranhura **de Produção** e, em seguida, selecione **Fazer** .

    > [!div class="mx-imgBorder"]
    > ![Screenshot da publicação de LUIS para o ponto final](media/howto-publish/publish-app-popup.png)

1. Selecione o link **URLs do ponto final** no link 'Access' na notificação para aceder à página Recursos **Azure.** Os URLs de ponto final estão listados como **a Consulta exemplo** .

<!-- [!INCLUDE [LUIS How to Publish steps](./includes/howto-publish.md)] -->

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


## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Passos seguintes

Pode chamar o ponto final do código:

> [!div class="nextstepaction"]
> [Chamar um ponto final de LUIS com código](luis-get-started-cs-get-intent.md)
