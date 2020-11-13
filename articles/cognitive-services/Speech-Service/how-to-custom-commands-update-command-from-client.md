---
title: Atualizar um comando a partir da aplicação do cliente
titleSuffix: Azure Cognitive Services
description: atualizar um comando a partir da aplicação do cliente
services: cognitive-services
author: encorona-ms
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/20/2020
ms.author: encorona
ms.openlocfilehash: 290f9ee9c23071ac56b1ff0c65ddc03decbc7344
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94571218"
---
# <a name="update-a-command-from-the-client"></a>Atualizar um comando do cliente

Neste artigo, aprenderemos a atualizar um comando em curso a partir de uma aplicação de cliente.

## <a name="prerequisites"></a>Pré-requisitos
> [!div class = "checklist"]
> * Uma [aplicação de Comandos Personalizados criada](quickstart-custom-commands-application.md) anteriormente

## <a name="update-the-state-of-a-command"></a>Atualizar o estado de um comando

Se a sua aplicação de cliente exigir atualizar o estado de um comando em curso sem entrada de voz, pode enviar um evento para atualizar o comando.

Para ilustrar este cenário, para atualizar o estado de um comando em curso (TurnOnOff) podemos enviar a seguinte atividade do evento. 

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

Vamos rever os principais atributos desta atividade.

| Atributo | Explicação |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| **tipo** | A atividade é do tipo "evento", e o nome do evento tem de ser "RemoteUpdate". |
| **value** | O "valor" do atributo contém os atributos necessários para atualizar o comando atual. |
| **atualizadoCommand** | O atributo "UpdatedCommand" contém o nome do comando, "updatedParameters" é um mapa com o nome dos parâmetros e os seus valores atualizados. |
| **Cancelar** | Se o comando em curso precisar de ser cancelado, desa um atributo "cancelar" para verdadeiro. |
| **atualizadosParametrosGlobal** | O atributo "updatedGlobalParameters" é também um mapa, tal como "Parâmetros atualizados" mas usado para parâmetros globais. |
| **processoTurn** | Se a curva tiver de ser processada após o envio da atividade, definirá o atributo "processTurn" para verdadeiro. |

Pode testar este cenário no portal Comandos Personalizados.

1. Abra a aplicação de Comandos Personalizados que criou anteriormente. 
1. Clique em Train e, em seguida, Teste.
1. Envie "virar".
1. Abra o painel lateral e clique em Editor de Atividade.
1. Digite e envie o evento RemoteCommand especificado na secção anterior.
    > [!div class="mx-imgBorder"]
    > ![Enviar comando remoto](media/custom-commands/send-remote-command-activity.png)

Note como o valor do parâmetro "OnOff" foi definido para "on" usando uma atividade do cliente em vez de fala ou texto.

## <a name="update-the-catalog-of-the-parameter-for-a-command"></a>Atualizar o catálogo do parâmetro para um comando

Ao configurar a lista de opções válidas para um parâmetro, os valores para o parâmetro são definidos globalmente para a aplicação. 

No nosso exemplo, o parâmetro SubjectDevice terá uma lista fixa de valores suportados independentemente da conversação.

Caso necessite de adicionar novas entradas ao catálogo do parâmetro por conversação, pode enviar a seguinte atividade.

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
Com esta atividade, adicionámos uma entrada para "estéreo" no catálogo do parâmetro "SubjectDevice" no comando "TurnOnOff".

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/custom-commands/update-catalog-with-remote-activity.png" alt-text="Atualizar catálogo":::

Reparem em algumas coisas.
1. Só precisa enviar esta atividade uma vez (idealmente logo após iniciar uma ligação).
1. Depois de enviar esta atividade, deve aguardar que o evento ParameterCatalogsUpdated seja enviado de volta para o cliente.

## <a name="add-additional-context-from-the-client-application"></a>Adicionar contexto adicional da aplicação do cliente

Pode definir o contexto adicional a partir da aplicação do cliente por conversação que pode ser posteriormente usada na sua aplicação Comandos Personalizados. 

Por exemplo, pense no cenário em que pretende enviar o ID e o nome do dispositivo ligado à aplicação Comandos Personalizados.

Para testar este cenário, vamos criar um novo comando na nossa aplicação atual.
1. Criar um novo comando chamado GetDeviceInfo.
1. Adicione uma frase de exemplo com "obter informações do dispositivo".
1. Na regra de conclusão "Feito", adicione uma Ação de Resposta à Fala de Envio.
    > ![Enviar resposta de fala com contexto](media/custom-commands/send-speech-response-context.png)
1. Salve e Treine a sua aplicação.
1. Teste a sua aplicação.
    > ![Enviar atividade de contexto de cliente](media/custom-commands/send-client-context-activity.png)

Reparem em algumas coisas.
1. Só precisa enviar esta atividade uma vez (idealmente logo após iniciar uma ligação).
1. Pode utilizar objetos complexos para o ClientContext.
1. Pode utilizar o ClientContext em respostas de fala, para enviar atividades e quando chama pontos finais da web.


## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Atualizar um comando a partir de um ponto final web](./how-to-custom-commands-update-command-from-web-endpoint.md)
