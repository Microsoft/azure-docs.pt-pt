---
title: Atualizar um comando a partir de uma aplicação de cliente
titleSuffix: Azure Cognitive Services
description: Saiba como atualizar um comando a partir de uma aplicação do cliente.
services: cognitive-services
author: nitinme
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/20/2020
ms.author: nitinme
ms.openlocfilehash: 08c674a7a7ec060a4273836064cb1c21e979e725
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "97560292"
---
# <a name="update-a-command-from-a-client-app"></a>Atualizar um comando a partir de uma aplicação de cliente

Neste artigo, você vai aprender como atualizar um comando em curso a partir de uma aplicação do cliente.

## <a name="prerequisites"></a>Pré-requisitos
> [!div class = "checklist"]
> * Uma [aplicação de Comandos Personalizados criada](quickstart-custom-commands-application.md) anteriormente

## <a name="update-the-state-of-a-command"></a>Atualizar o estado de um comando

Se a sua aplicação de cliente exigir que atualize o estado de um comando em curso sem entrada de voz, pode enviar um evento para atualizar o comando.

Para ilustrar este cenário, envie a seguinte atividade do evento para atualizar o estado de um comando em curso `TurnOnOff` ( 

```json
{
  "type": "event",
  "name": "RemoteUpdate",
  "value": {
    "updatedCommand": {
      "name": "TurnOnOff",
      "updatedParameters": {
        "OnOff": "on"
      },
      "cancel": false
    },
    "updatedGlobalParameters": {},
    "processTurn": true
  }
}
```

Vamos rever os principais atributos desta atividade:

| Atributo | Explicação |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| **tipo** | A atividade é do `"event"` tipo, e o nome do evento tem de `"RemoteUpdate"` ser. |
| **value** | O atributo `"value"` contém os atributos necessários para atualizar o comando atual. |
| **atualizadoCommand** | O atributo `"updatedCommand"` contém o nome do comando. Dentro desse atributo, `"updatedParameters"` está um mapa com os nomes dos parâmetros e os seus valores atualizados. |
| **Cancelar** | Se o comando em curso precisar de ser cancelado, desa um atributo `"cancel"` a `true` . |
| **atualizadosParametrosGlobal** | O atributo `"updatedGlobalParameters"` é um mapa tal `"updatedParameters"` como, mas é usado para parâmetros globais. |
| **processoTurn** | Se a curva tiver de ser processada após o envio da atividade, desa um atributo `"processTurn"` para `true` . |

Pode testar este cenário no portal Comandos Personalizados:

1. Abra a aplicação Comandos Personalizados que criou anteriormente. 
1. Selecione **Train** e, em seguida, **Teste**.
1. `turn`Enviar.
1. Abra o painel lateral e selecione **Editor de Atividades**.
1. Digite e envie o `RemoteCommand` evento especificado na secção anterior.
    > [!div class="mx-imgBorder"]
    > ![Screenshot que mostra o evento para um comando remoto.](media/custom-commands/send-remote-command-activity.png)

Note como o valor do parâmetro `"OnOff"` foi definido através de uma atividade do cliente em vez de fala ou `"on"` texto.

## <a name="update-the-catalog-of-the-parameter-for-a-command"></a>Atualizar o catálogo do parâmetro para um comando

Ao configurar a lista de opções válidas para um parâmetro, os valores para o parâmetro são definidos globalmente para a aplicação. 

No nosso exemplo, o `SubjectDevice` parâmetro terá uma lista fixa de valores suportados independentemente da conversação.

Se pretender adicionar novas entradas ao catálogo do parâmetro por conversação, pode enviar as seguintes atividades:

```json
{
  "type": "event",
  "name": "RemoteUpdate",
  "value": {
    "catalogUpdate": {
      "commandParameterCatalogs": {
        "TurnOnOff": [
          {
            "name": "SubjectDevice",
            "values": {
              "stereo": [
                "cd player"
              ]
            }
          }
        ]
      }
    },
    "processTurn": false
  }
}
```
Com esta atividade, adicionou uma entrada `"stereo"` para o catálogo do parâmetro no `"SubjectDevice"` `"TurnOnOff"` comando.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/custom-commands/update-catalog-with-remote-activity.png" alt-text="Screenshot que mostra uma atualização de catálogo.":::

Note algumas coisas:
- Só precisa enviar esta atividade uma vez (idealmente, logo após iniciar uma ligação).
- Depois de enviar esta atividade, deve esperar que o evento `ParameterCatalogsUpdated` seja devolvido ao cliente.

## <a name="add-more-context-from-the-client-application"></a>Adicionar mais contexto da aplicação do cliente

Pode definir o contexto adicional a partir da aplicação do cliente por conversação que pode ser posteriormente usada na sua aplicação Comandos Personalizados. 

Por exemplo, pense no cenário em que pretende enviar o ID e o nome do dispositivo ligado à aplicação Comandos Personalizados.

Para testar este cenário, vamos criar um novo comando na aplicação atual:
1. Criar um novo comando chamado `GetDeviceInfo` .
1. Adicione uma frase de exemplo de `get device info` .
1. Na regra de conclusão **Feito**, adicione uma ação **de resposta à fala enviar** que contenha os atributos de `clientContext` .
   ![Screenshot que mostra uma resposta para enviar discurso com contexto.](media/custom-commands/send-speech-response-context.png)
1. Salve, treine e teste a sua aplicação.
1. Na janela de testes, envie uma atividade para atualizar o contexto do cliente.

    ```json
    {
       "type": "event",
       "name": "RemoteUpdate",
       "value": {
         "clientContext": {
           "deviceId": "12345",
           "deviceName": "My device"
         },
         "processTurn": false
       }
    }
    ```
1. Envie o `get device info` sms.
   ![Screenshot que mostra uma atividade para o envio de contexto do cliente.](media/custom-commands/send-client-context-activity.png)

Note algumas coisas:
- Só precisa enviar esta atividade uma vez (idealmente, logo após iniciar uma ligação).
- Pode utilizar objetos complexos para `clientContext` .
- Pode utilizar `clientContext` em respostas de fala, para enviar atividades e para chamar pontos finais da web.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Atualizar um comando a partir de um ponto final Web](./how-to-custom-commands-update-command-from-web-endpoint.md)
