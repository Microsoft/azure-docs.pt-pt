---
title: Atualizar um comando a partir de um ponto final Web
titleSuffix: Azure Cognitive Services
description: Saiba como atualizar o estado de um comando utilizando uma chamada para um ponto final web.
services: cognitive-services
author: nitinme
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/20/2020
ms.author: nitinme
ms.openlocfilehash: d0b77e6af36f0a71405f6c032bfdd121abeb0071
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "97560275"
---
# <a name="update-a-command-from-a-web-endpoint"></a>Atualizar um comando a partir de um ponto final Web

Se a sua aplicação de cliente necessitar de uma atualização para o estado de um comando em curso sem entrada de voz, pode utilizar uma chamada para um ponto final web para atualizar o comando.

Neste artigo, você vai aprender a atualizar um comando em curso a partir de um ponto final web.

## <a name="prerequisites"></a>Pré-requisitos
> [!div class = "checklist"]
> * Uma [aplicação de Comandos Personalizados criada](quickstart-custom-commands-application.md) anteriormente

## <a name="create-an-azure-function"></a>Criar uma função do Azure 

Para este exemplo, necessitará de uma [função Azure](../../azure-functions/index.yml) acionada por HTTP que suporte a seguinte entrada (ou um subconjunto desta entrada):

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

Vamos rever os principais atributos desta entrada:

| Atributo | Explicação |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| **conversationId** | O identificador único da conversa. Note que este ID pode ser gerado a partir da aplicação do cliente. |
| **atualCommand** | O comando que está ativo na conversa. |
| **nome** | O nome do comando. O `parameters` atributo é um mapa com os valores atuais dos parâmetros. |
| **actuaisParametrosGlobal** | Um mapa `parameters` como, mas usado para parâmetros globais. |

A saída da função Azure necessita de suportar o seguinte formato:

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

Pode reconhecer este formato porque é o mesmo que usou ao [atualizar um comando do cliente.](./how-to-custom-commands-update-command-from-client.md) 

Agora, crie uma função Azure baseada em Node.js. Copiar/colar este código:

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

Quando chamar esta função Azure a partir de Comandos Personalizados, enviará os valores atuais da conversação. Irá retornar os parâmetros que pretende atualizar ou se pretende cancelar o comando atual.

## <a name="update-the-existing-custom-commands-app"></a>Atualizar a app de Comandos Personalizados existente

Vamos ligar a função Azure com a aplicação de Comandos Personalizados existente:

1. Adicione um novo comando chamado `IncrementCounter` .
1. Adicione apenas uma frase de exemplo com o valor `increment` .
1. Adicione um novo parâmetro chamado `Counter` (mesmo nome especificado na função Azure) do tipo `Number` com um valor predefinido de `0` .
1. Adicione um novo ponto final web chamado `IncrementEndpoint` com o URL da sua função Azure, com **atualizações remotas definidas** para **Ativado**.
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-commands/set-web-endpoint-with-remote-updates.png" alt-text="Screenshot que mostra a definição de um ponto final web com atualizações remotas.":::
1. Crie uma nova regra de interação chamada **IncrementRule** e adicione uma **ação de ponta web Call.**
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-commands/increment-rule-web-endpoint.png" alt-text="Screenshot que mostra a criação de uma regra de interação.":::
1. Na configuração de ação, selecione `IncrementEndpoint` . Configure Sobre o **sucesso** de Enviar resposta de **fala** com o valor de , `Counter` e configurar Por **falha** com uma mensagem de erro.
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-commands/set-increment-counter-call-endpoint.png" alt-text="Screenshot que mostra a definição de um contador de incrementos para chamar um ponto final web.":::
1. Desa esta qual é o estado pós-execução da regra para **aguardar a entrada do utilizador**.

## <a name="test-it"></a>Testar

1. Poupe e treine a sua aplicação.
1. Selecione **Teste**.
1. Envie `increment` algumas vezes (que é a frase de exemplo para o `IncrementCounter` comando).
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-commands/increment-counter-example.png" alt-text="Screenshot que mostra um exemplo de contador incremento.":::

Note como a função Azure aumenta o valor do `Counter` parâmetro em cada curva.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Ativar um processo de CI/CD para a sua aplicação de Comandos Personalizados](./how-to-custom-commands-deploy-cicd.md)