---
title: Atualizar um comando a partir de um ponto final web
titleSuffix: Azure Cognitive Services
description: atualizar um comando a partir de um ponto final web
services: cognitive-services
author: encorona-ms
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/20/2020
ms.author: encorona
ms.openlocfilehash: 4432843ac93002bc92068db191706352234d76e6
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94571253"
---
# <a name="update-a-command-from-a-web-endpoint"></a>Atualizar um comando a partir de um ponto final web

Se a sua aplicação de cliente exigir atualizar o estado de um comando em curso sem entrada de voz, pode utilizar uma chamada para um ponto final web para atualizar o comando.

Neste artigo, você vai aprender como atualizar um comando em curso a partir de um ponto final web.

## <a name="prerequisites"></a>Pré-requisitos
> [!div class = "checklist"]
> * Uma [aplicação de Comandos Personalizados criada](quickstart-custom-commands-application.md) anteriormente

## <a name="create-an-azure-function"></a>Criar uma Função do Azure 

Para este exemplo, precisaremos de uma [função Azure](https://docs.microsoft.com/azure/azure-functions/) HTTP-Triggered que suporte a seguinte entrada (ou um subconjunto desta entrada).

```JSON
{
  "conversationId": "SomeConversationId",
  "currentCommand": {
    "name": "SomeCommandName",
    "parameters": {
      "SomeParameterName": "SomeParameterValue",
      "SomeOtherParameterName": "SomeOtherParameterValue"
    }
  },
  "currentGlobalParameters": {
      "SomeGlobalParameterName": "SomeGlobalParameterValue",
      "SomeOtherGlobalParameterName": "SomeOtherGlobalParameterValue"
  }
}
```

Vamos rever os principais atributos desta entrada.

| Atributo | Explicação |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| **conversationId** | "conversationId" é o identificador único da conversa, note que este id pode ser gerado a partir da app do cliente. |
| **atualCommand** | "ActualCommand" é o comando atualmente ativo na conversação. |
| **nome** | "Nome" é o nome do comando e "parâmetros" é um mapa com os valores atuais dos parâmetros. |
| **actuaisParametrosGlobal** | "ActualGlobalParameters" é também um mapa como "parâmetros", mas é usado para parâmetros globais. |

A saída da Função Azure necessita de suportar o seguinte formato.

```JSON
{
  "updatedCommand": {
    "name": "SomeCommandName",
    "updatedParameters": {
      "SomeParameterName": "SomeParameterValue"
    },
    "cancel": false
  },
  "updatedGlobalParameters": {
    "SomeGlobalParameterName": "SomeGlobalParameterValue"
  }
}
```

Pode reconhecer este formato uma vez que é o mesmo utilizado ao [atualizar um comando do cliente](./how-to-custom-commands-update-command-from-client.md). 

Agora, crie uma Função Azure baseada no NodeJS e copie este código

```nodejs
module.exports = async function (context, req) {
    context.log(req.body);
    context.res = {
        body: {
            updatedCommand: {
                name: "IncrementCounter",
                updatedParameters: {
                    Counter: req.body.currentCommand.parameters.Counter + 1
                }
            }
        }
    };
}
```

Quando chamarmos esta Função Azure de Comandos Personalizados, enviaremos os valores atuais da conversação e devolveremos os parâmetros que queremos atualizar ou se quisermos cancelar o comando atual.

## <a name="update-the-existing-custom-commands-app"></a>Atualizar a app de Comandos Personalizados existente

Agora vamos ligar a Função Azure com a aplicação de Comandos Personalizados existente.

1. Adicione um novo comando chamado IncrementCounter.
1. Adicione apenas uma frase de exemplo com o valor "incremento".
1. Adicione um novo parâmetro chamado Contador (o mesmo nome especificado na Função Azure acima) do número do tipo com um valor predefinido de 0.
1. Adicione um novo ponto final web chamado IncrementEndpoint com o URL da sua Função Azure e com atualizações remotas ativadas.
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-commands/set-web-endpoint-with-remote-updates.png" alt-text="Definir o ponto final da web com atualizações remotas":::
1. Crie uma nova regra de interação chamada "IncrementRule" e adicione uma ação de ponto final da Web Call.
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-commands/increment-rule-web-endpoint.png" alt-text="Regra de incremento":::
1. Na configuração de ação, selecione o Ponto IncrementEnd, configurar O sucesso para Enviar resposta de fala com o valor do contador e sobre a falha com uma mensagem de erro.
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-commands/set-increment-counter-call-endpoint.png" alt-text="Definir ponto final de chamada de contra-chamada de incremento":::
1. Desa estacie o estado pós-execução da regra para esperar pela entrada do utilizador

## <a name="test-it"></a>Testar

1. Poupe e Treine a sua app
1. Clique em Teste
1. Envie algumas vezes "incremento" (que é a frase de exemplo para o comando IncrementCounter)
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-commands/increment-counter-example.png" alt-text="Incremento exemplo contador":::

Note como o valor do parâmetro Contador é aumentado em cada volta pela Função Azure.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Ativar um processo de CI/CD para a sua aplicação de Comandos Personalizados](./how-to-custom-commands-deploy-cicd.md)