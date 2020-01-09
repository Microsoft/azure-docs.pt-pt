---
title: 'Início rápido: criar aplicativo-LUIS'
titleSuffix: Azure Cognitive Services
description: Este guia de início rápido mostra como criar um aplicativo LUIS que usa o domínio predefinido `HomeAutomation` para ativar e desativar as luzes e os dispositivos. Este domínio pré-concebido fornece intenções, entidades e expressões de exemplo. Quando terminar, obterá um ponto final de LUIS em execução na cloud.
services: cognitive-services
author: diberry
ms.custom: seodec18
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 12/17/2019
ms.author: diberry
ms.openlocfilehash: 302321a36a6ce7526ad5e3144f87b88edbfaaec7
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75448106"
---
# <a name="quickstart-use-prebuilt-home-automation-app"></a>Início Rápido: utilizar a aplicação Home Automation pré-concebida

Neste início rápido, crie uma aplicação LUIS que utiliza o domínio pré-concebido `HomeAutomation` para ligar e desligar as luzes e os eletrodomésticos. Este domínio pré-concebido fornece intenções, entidades e expressões de exemplo. Quando terminar, obterá um ponto final de LUIS em execução na cloud.

[!INCLUDE [Sign in to LUIS](./includes/sign-in-process.md)]

[!INCLUDE [Select authoring resource](./includes/select-authoring-resource.md)]

## <a name="create-a-new-app"></a>Criar uma nova aplicação
Pode criar e gerir as suas aplicações em **My Apps** (As Minhas Aplicações).

1. No portal do LUIS, na lista meus aplicativos, selecione **+ criar**.

    ![No portal do LUIS, na lista meus aplicativos, selecione ' + criar.](./media/create-app-in-portal.png)

1. Na caixa de diálogo, nomeie seu aplicativo `Home Automation`, em seguida, selecione **concluído**. LUIS cria o aplicativo. A descrição é opcional e não é usada para criação ou previsão. O recurso de previsão também é opcional ao criar um aplicativo LUIS. Ao publicar seu aplicativo para produção, você deve atribuir um recurso de previsão para que seu aplicativo possa lidar com muitas solicitações.

    ![Na caixa de diálogo, nomeie o aplicativo ' Home Automation '](./media/create-new-app-details.png)

    >[!NOTE]
    >A cultura não pode ser alterada assim que a aplicação for criada.

## <a name="add-prebuilt-domain"></a>Adicionar um domínio pré-concebido

Selecione **domínios pré-criados** e, em seguida, procure **HomeAutomation**. Selecione **Adicionar domínio** no cartão HomeAutomation.

![Selecione ' domínios predefinidos ' e procure ' HomeAutomation '. Selecione "Adicionar domínio" no cartão de HomeAutomation.](media/luis-quickstart-new-app/home-automation.png)

Quando o domínio for adicionado com êxito, a caixa Prebuilt domains (Domínios pré-concebidos) apresenta um botão **Remove domain** (Remover domínio).

## <a name="intents-and-entities"></a>Intenções e entidades

Selecione **tentativas** para revisar as tentativas de domínio HomeAutomation. As tentativas de domínio pré-criados têm declarações de exemplo.

![Captura de tela da lista de tentativas de HomeAutomation](media/luis-quickstart-new-app/home-automation-intents.png "Captura de tela da lista de tentativas de HomeAutomation")

> [!NOTE]
> **None** (Nenhuma) é uma intenção fornecida por todas as aplicações LUIS. Pode utilizá-la para processar expressões que não correspondem à funcionalidade que a sua aplicação fornece.

Selecione a intenção **HomeAutomation.TurnOff**. Pode ver que a intenção contém uma lista de expressões que têm etiquetas de entidades.

[![Captura de tela da intenção HomeAutomation. de desativação](media/luis-quickstart-new-app/home-automation-turnoff.png "Captura de tela da intenção HomeAutomation. de desativação")](media/luis-quickstart-new-app/home-automation-turnoff.png)

## <a name="train-the-luis-app"></a>Preparar a aplicação LUIS

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="test-your-app"></a>Testar a aplicação
Assim que preparar a sua aplicação, pode testá-la. Selecione **testar**. Digite um expressão de teste como `Turn off the lights` no painel de teste interativo e pressione Enter.

```
Turn off the lights
```

Verifique se a intenção com a melhor classificação corresponde à intenção esperada para cada expressão de teste.

Neste exemplo, `Turn off the lights` é identificado corretamente como a principal intenção de Pontuação de **HomeAutomation.** inativação.

![Captura de tela do painel de teste com expressão realçado](media/luis-quickstart-new-app/review-test-inspection-pane-in-portal.png)

Selecione **inspecionar** para examinar mais informações sobre a previsão.

![Captura de tela do painel de teste com informações de inspeção](media/luis-quickstart-new-app/test.png)

Selecione **Test** (Testar) novamente para fechar o painel de teste.

<a name="publish-your-app"></a>

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Publicar a aplicação para obter o URL de ponto final

[!INCLUDE [LUIS How to Publish steps](./includes/howto-publish.md)]

<a name="query-the-v2-api-prediction-endpoint"></a>

## <a name="query-the-v3-api-prediction-endpoint"></a>Consultar o ponto de extremidade de previsão da API v3

[!INCLUDE [LUIS How to get endpoint first step](./includes/v3-prediction-endpoint.md)]

1. Na barra de endereços do navegador, para a cadeia de caracteres de consulta, verifique se as barras de nome e valor a seguir estão na URL. Se eles não estiverem na cadeia de caracteres de consulta, adicione-os:

    |Par de nome/valor|
    |--|
    |`verbose=true`|
    |`show-all-intents=true`|

1. Na barra de endereços do navegador, vá até o final da URL e insira `turn off the living room light` para o valor da _consulta_ e pressione Enter.

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

    Saiba mais sobre o [ponto de extremidade de previsão v3](luis-migration-api-v3.md).


## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Passos seguintes

Pode chamar o ponto final do código:

> [!div class="nextstepaction"]
> [Chamar um ponto final de LUIS com código](luis-get-started-cs-get-intent.md)
