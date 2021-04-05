---
title: Utilizar as Funções para adicionar mensagens a uma fila do Armazenamento do Azure
description: Utilize as Funções do Azure para criar uma função sem servidores que é invocada por um pedido de HTTP e cria uma mensagem numa fila do Armazenamento do Azure.
ms.assetid: 0b609bc0-c264-4092-8e3e-0784dcc23b5d
ms.topic: how-to
ms.date: 04/24/2020
ms.custom: devx-track-csharp, mvc
ms.openlocfilehash: d2821a16e0b72b32cc392b7ae626d782734458a6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98674206"
---
# <a name="add-messages-to-an-azure-storage-queue-using-functions"></a>Utilizar as Funções para adicionar mensagens a uma fila do Armazenamento do Azure

Nas Funções do Azure, os enlaces de entrada e saída proporcionam uma forma declarativa para tornar os dados dos serviços externos disponíveis para o seu código. Neste início rápido, utilize um enlace de saída para criar uma mensagem numa fila quando uma função é acionada por um pedido de HTTP. Utilize o recipiente de armazenamento Azure para visualizar as mensagens de fila que a sua função cria.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este guia de início rápido:

- Uma subscrição do Azure. Se não tiver uma, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

- Siga as indicações em [Criar a primeira função a partir do portal do Azure](./functions-get-started.md) e não siga o passo **Limpar recursos**. Esse início rápido cria a aplicação de função e a função que vai utilizar aqui.

## <a name="add-an-output-binding"></a><a name="add-binding"></a>Adicionar um enlace de saída

Nesta secção, utilize a IU do portal para adicionar um enlace de saída de armazenamento de filas à função que criou anteriormente. Esta ligação permite escrever código mínimo para criar uma mensagem numa fila. Não tem de escrever código para tarefas, tais como abrir uma ligação de armazenamento, criar uma fila ou obter uma referência para uma fila. O tempo de execução das Funções do Azure e o enlace de saída da fila tratam dessas tarefas por si.

1. No portal do Azure, abra a página da aplicação de função da aplicação de função que criou em [Criar a primeira função a partir do portal do Azure](./functions-get-started.md). Para abrir a página, procure e selecione **App de função**. Em seguida, selecione a sua aplicação de função.

1. Selecione a sua aplicação de função e, em seguida, selecione a função que criou no início rápido anterior.

1. Selecione **Integração** e, em seguida, **selecione + Adicionar saída**.

   :::image type="content" source="./media/functions-integrate-storage-queue-output-binding/function-create-output-binding.png" alt-text="Crie uma ligação de saída para a sua função." border="true":::

1. Selecione o tipo de encadernação **de armazenamento de fila Azure** e adicione as definições conforme especificado na tabela que segue esta imagem: 

    :::image type="content" source="./media/functions-integrate-storage-queue-output-binding/function-create-output-binding-details.png" alt-text="Adicione um enlace de saída do Armazenamento de filas a uma função no portal do Azure." border="true":::
    
    | Definição      |  Valor sugerido   | Descrição                              |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nome do parâmetro da mensagem** | outputQueueItem | O nome do parâmetro de enlace de saída. | 
    | **Nome da fila**   | outqueue  | O nome da fila à qual ligar na sua conta de Armazenamento. |
    | **Ligação da conta de armazenamento** | AzureWebJobsStorage | Pode utilizar a ligação da conta de armazenamento que já está a ser utilizada pela sua aplicação Function App ou criar uma nova.  |

1. Selecione **OK** para adicionar a ligação.

Agora que tem um enlace de saída definido, tem de atualizar o código para utilizar o enlace para adicionar mensagens a uma fila.  

## <a name="add-code-that-uses-the-output-binding"></a>Adicione código que utiliza o enlace de saída

Nesta secção, adicione código que escreve uma mensagem para a fila de saída. A mensagem inclui o valor que é transferido para o acionador HTTP na cadeia de consulta. Por exemplo, se a cadeia de consulta inclui `name=Azure`, a mensagem de fila será *Nome transmitido para a função: Azure*.

1. Na sua função, selecione **Código + Teste** para exibir o código de função no editor.

1. Atualize o código de função consoante a linguagem da sua função:

    # <a name="c"></a>[C\#](#tab/csharp)

    Adicionar um parâmetro **outputQueueItem** à assinatura de método conforme mostrado no exemplo seguinte.

    ```cs
    public static async Task<IActionResult> Run(HttpRequest req,
        ICollector<string> outputQueueItem, ILogger log)
    {
        ...
    }
    ```

    No corpo da função, imediatamente antes da declaração `return`, adicione o código que utiliza o parâmetro para criar uma mensagem de fila.

    ```cs
    outputQueueItem.Add("Name passed to the function: " + name);
    ```

    # <a name="javascript"></a>[JavaScript](#tab/nodejs)

    Adicione o código que utiliza o enlace de saída no objeto `context.bindings` para criar uma mensagem de fila. Adicione este código antes da declaração `context.done`.

    ```javascript
    context.bindings.outputQueueItem = "Name passed to the function: " + 
                (req.query.name || req.body.name);
    ```

    ---

1. Selecione **Guardar** para guardar as alterações.

## <a name="test-the-function"></a>Testar a função

1. Depois de guardar as alterações de código, selecione **Teste**.
1. Confirme que o seu teste corresponde à imagem abaixo e selecione **Executar**. 

    :::image type="content" source="./media/functions-integrate-storage-queue-output-binding/functions-test-run-function.png" alt-text="Teste a ligação de armazenamento de fila no portal Azure." border="true":::

    Tenha em atenção que o **Corpo do pedido** contém o valor `name`*Azure*. Este valor é apresentado na mensagem de fila que é criada quando a função é invocada.
    
    Como alternativa à seleção de **Executar** aqui, pode chamar a função de introduzir um URL num browser e especificar o valor `name` na cadeia de consulta. O método de browser é apresentado no [início rápido anterior](./functions-get-started.md).

1. Verifique os registos para se certificar de que a função foi bem-sucedida. 

Da primeira vez que o enlace de saída é utilizado, o runtime das Funções cria uma fila nova com o nome **outqueue** na sua conta de Armazenamento. Você usará a conta de armazenamento para verificar se a fila e uma mensagem nele foram criadas.

### <a name="find-the-storage-account-connected-to-azurewebjobsstorage"></a>Encontre a conta de armazenamento ligada ao AzureWebJobsStorage


1. Vá à sua aplicação de função e selecione **Configuração.**

1. Nas **definições de Aplicação**, selecione **AzureWebJobsStorage**.

    :::image type="content" source="./media/functions-integrate-storage-queue-output-binding/function-find-storage-account.png" alt-text="A screenshot mostra a página de Configuração com AzureWebJobsStorage selecionado." border="true":::

1. Localize e tome nota do nome da conta.

    :::image type="content" source="./media/functions-integrate-storage-queue-output-binding/function-storage-account-name.png" alt-text="Localize a conta de armazenamento ligada ao AzureWebJobsStorage." border="true":::

### <a name="examine-the-output-queue"></a>Examinar a fila de saída

1. No grupo de recursos para a sua aplicação de função, selecione a conta de armazenamento que está a usar para este arranque rápido.

1. No **serviço Queue**, selecione **Filas** e selecione a fila chamada **outqueue**. 

   A fila contém a mensagem que a fila de enlace de saída da fila criou quando executou a função acionada por HTTP. Se invocou a função com o valor predefinido `name` do *Azure*, a mensagem de fila é *Nome transmitido para a função: Azure*.

1. Execute novamente a função e verá uma nova mensagem aparecer na fila.  

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [Clean up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Passos seguintes

Neste início rápido, adicionou um enlace de saída a uma função já existente. Para obter mais informações sobre o enlace para o Armazenamento de filas, veja [Azure Functions Storage queue bindings](functions-bindings-storage-queue.md) (Enlaces da fila de Armazenamento das Funções do Azure).

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps-2.md)]
