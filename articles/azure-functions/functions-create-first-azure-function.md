---
title: Criar a sua primeira função no portal do Azure
description: Saiba como criar a sua primeira Função do Azure para execução sem servidor através do portal do Azure.
ms.assetid: 96cf87b9-8db6-41a8-863a-abb828e3d06d
ms.topic: how-to
ms.date: 03/26/2020
ms.custom: devx-track-csharp, mvc, devcenter, cc996988-fb4f-47
ms.openlocfilehash: 746ec7aa3ee41204e051e843ff94ed0771a9d244
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96326187"
---
# <a name="create-your-first-function-in-the-azure-portal"></a>Criar a sua primeira função no portal do Azure

As Funções Azure permitem executar o seu código num ambiente sem servidor sem ter de criar primeiro uma máquina virtual (VM) ou publicar uma aplicação web. Neste artigo, aprende a usar as Funções Azure para criar uma função de gatilho HTTP "hello world" no portal Azure.

Recomendamos que [desenvolva as suas funções localmente](functions-develop-local.md) e publique numa aplicação de função em Azure.  
Use um dos seguintes links para começar com o seu ambiente de desenvolvimento local escolhido e linguagem:

| Visual Studio Code | Solicitação de terminal/comando | Visual Studio |
| --- | --- | --- |
|  &bull;&nbsp;[Começar com C #](./create-first-function-vs-code-csharp.md)<br/>&bull;&nbsp;[Começa com a Java.](./create-first-function-vs-code-java.md)<br/>&bull;&nbsp;[Começar com JavaScript](./create-first-function-vs-code-node.md)<br/>&bull;&nbsp;[Começa com o PowerShell](./create-first-function-vs-code-powershell.md)<br/>&bull;&nbsp;[Começa com a Python](./create-first-function-vs-code-python.md) |&bull;&nbsp;[Começar com C #](./create-first-function-cli-csharp.md)<br/>&bull;&nbsp;[Começa com a Java.](./create-first-function-cli-java.md)<br/>&bull;&nbsp;[Começar com JavaScript](./create-first-function-cli-node.md)<br/>&bull;&nbsp;[Começa com o PowerShell](./create-first-function-cli-powershell.md)<br/>&bull;&nbsp;[Começa com a Python](./create-first-function-cli-python.md) | [Começar com C #](functions-create-your-first-function-visual-studio.md) |

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no [portal do Azure](https://portal.azure.com) com a sua conta do Azure.

## <a name="create-a-function-app"></a>Criar uma aplicação de funções

Precisa de uma aplicação Function App para alojar a execução das suas funções. Uma aplicação de função permite-lhe agrupar funções como uma unidade lógica para facilitar a gestão, implementação, escala e partilha de recursos.

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

Em seguida, crie uma função na nova aplicação de função.

## <a name="create-an-http-trigger-function"></a><a name="create-function"></a>Criar uma função de gatilho HTTP

1. A partir do menu esquerdo da janela **Funções,** selecione **Funções** e selecione **Adicione** no menu superior. 
 
1. A partir da janela **Nova Função,** selecione **'Desír do gatilho'.**

    ![Escolha a função de gatilho HTTP](./media/functions-create-first-azure-function/function-app-select-http-trigger.png)

1. Na janela **Nova Função,** aceite o nome predefinido para **Nova Função,** ou introduza um novo nome. 

1. Escolha **o Anónimo** na lista de abandono do **nível** de autorização e, em seguida, selecione **Criar Função**.

    Azure cria a função de gatilho HTTP. Agora, pode enviar um pedido HTTP para executar a função nova.

## <a name="test-the-function"></a>Testar a função

1. Na sua nova função de gatilho HTTP, selecione **Código + Teste** a partir do menu esquerdo e, em seguida, selecione Obter URL de **função** a partir do menu superior.

    ![Selecione Obter URL de função](./media/functions-create-first-azure-function/function-app-select-get-function-url.png)

1. Na caixa de diálogo URL da **função Get,** selecione o **predefinimento** da lista de drop-down e, em seguida, selecione a Cópia para o ícone **da área de transferência.** 

    ![Copiar o URL da função a partir do portal do Azure](./media/functions-create-first-azure-function/function-app-develop-tab-testing.png)

1. Cole o URL da função na barra de endereço do navegador. Adicione o valor da cadeia de consulta `?name=<your_name>` ao final deste URL e prima Enter para executar o pedido. 

    O exemplo seguinte mostra a resposta no browser:

    ![Resposta da função no browser.](./media/functions-create-first-azure-function/function-app-browser-testing.png)

    O URL do pedido inclui uma chave que é necessária, por predefinição, para aceder à sua função através de HTTP.

1. Quando a sua função é executada, são escritas informações de rastreio nos registos. Para ver a saída de **vestígios,** volte à página **Code + Test** no portal e expanda a seta de Logs na parte inferior da página.

   ![Visualizador de registo de funções no portal do Azure.](./media/functions-create-first-azure-function/function-view-logs.png)

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]