---
title: Criar a sua primeira função no portal do Azure
description: Saiba como criar a sua primeira Função do Azure para execução sem servidor através do portal do Azure.
ms.assetid: 96cf87b9-8db6-41a8-863a-abb828e3d06d
ms.topic: quickstart
ms.date: 03/06/2020
ms.custom: mvc, devcenter, cc996988-fb4f-47
ms.openlocfilehash: e7bb5e7b387c3ab1140a3fe475911bd0e428e2a5
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "80057101"
---
# <a name="create-your-first-function-in-the-azure-portal"></a>Criar a sua primeira função no portal do Azure

As Funções Azure permitem executar o seu código num ambiente sem servidores sem ter de criar primeiro uma máquina virtual (VM) ou publicar uma aplicação web. Neste artigo, aprende-se a usar funções Azure para criar uma função "hello world" HTTP desencadeada no portal Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Se é um desenvolvedor de C#, considere criar a [sua primeira função no Visual Studio 2019](functions-create-your-first-function-visual-studio.md) em vez de no portal. 

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inscreva-se no [portal Azure](https://portal.azure.com) com a sua conta Azure.

## <a name="create-a-function-app"></a>Criar uma aplicação de função

Precisa de uma aplicação Function App para alojar a execução das suas funções. Uma aplicação de função permite-lhe agrupar funções como uma unidade lógica para uma gestão mais fácil, implementação, escalae e partilha de recursos.

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

Em seguida, crie uma função na nova aplicação de funções.

## <a name="create-an-http-triggered-function"></a><a name="create-function"></a>Criar uma função acionada por HTTP

1. Expanda a sua nova **+** aplicação de funções, selecione o botão ao lado das **Funções,** escolha **o Portal In**, e, em seguida, selecione **Continuar**.

    ![Funciona rapidamente para escolher uma plataforma.](./media/functions-create-first-azure-function/function-app-quickstart-choose-portal.png)

1. Escolha **WebHook + API**e, em seguida, selecione **Criar**.

    ![Início rápido de funções no portal do Azure.](./media/functions-create-first-azure-function/function-app-quickstart-node-webhook.png)

   É criada uma função com um modelo específico de linguagem para uma função acionada por HTTP.

Agora, pode enviar um pedido HTTP para executar a função nova.

## <a name="test-the-function"></a>Testar a função

1. Na sua nova função, selecione **</> Obtenha** URL de função na parte superior direita. 

1. Na caixa de diálogo URL da **função Get,** selecione **'Tecla de função' (Tecla de função)** da lista de drop-down e, em seguida, selecione **Copy**. 

    ![Copiar o URL da função a partir do portal do Azure](./media/functions-create-first-azure-function/function-app-develop-tab-testing.png)

1. Cole o URL da função na barra de endereço do navegador. Adicione o valor `&name=<your_name>` de cadeia de consulta ao final deste URL e prima Introduza para executar o pedido. 

    O exemplo seguinte mostra a resposta no browser:

    ![Resposta da função no browser.](./media/functions-create-first-azure-function/function-app-browser-testing.png)

    O URL do pedido inclui uma chave que é necessária, por predefinição, para aceder à sua função através de HTTP.

1. Quando a sua função é executada, são escritas informações de rastreio nos registos. Para ver a saída de vestígios da execução anterior, volte à sua função no portal e selecione a seta na parte inferior do ecrã para expandir os **Registos**.

   ![Visualizador de registo de funções no portal do Azure.](./media/functions-create-first-azure-function/function-view-logs.png)

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

