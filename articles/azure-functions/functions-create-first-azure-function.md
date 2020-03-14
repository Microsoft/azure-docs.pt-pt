---
title: Crie a sua primeira função sem servidor estoque do portal Azure
description: Saiba como criar a sua primeira Função do Azure para execução sem servidor através do portal do Azure.
ms.assetid: 96cf87b9-8db6-41a8-863a-abb828e3d06d
ms.topic: quickstart
ms.date: 03/28/2018
ms.custom: mvc, devcenter, cc996988-fb4f-47
ms.openlocfilehash: 3e5513d8730f455b220fa480eb99ce9c29216210
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79241291"
---
# <a name="create-your-first-function-in-the-azure-portal"></a>Criar a sua primeira função no portal do Azure

As Funções do Azure permitem-lhe executar o seu código num ambiente [sem servidor](https://azure.microsoft.com/solutions/serverless/), sem que tenha de criar primeiro uma VM ou publicar uma aplicação Web. Neste artigo, aprenda a usar funções para criar uma função de "olá mundo" no portal Azure.

![Criar uma aplicação de função no portal do Azure](./media/functions-create-first-azure-function/function-app-in-portal-editor.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

> [!NOTE]
> C#os desenvolvedores devem considerar [a criação da sua primeira função no Visual Studio 2019](functions-create-your-first-function-visual-studio.md) em vez de no portal. 

## <a name="log-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no portal do Azure em <https://portal.azure.com> com a sua conta do Azure.

## <a name="create-a-function-app"></a>Criar uma aplicação de função

Precisa de uma aplicação Function App para alojar a execução das suas funções. Uma aplicação de função permite-lhe agrupar funções como uma unidade lógica para uma gestão mais fácil, implementação, escalae e partilha de recursos.

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

Em seguida, vai criar uma função na aplicação Function App nova.

## <a name="create-function"></a>Criar uma função acionada por HTTP

1. Expanda a sua nova aplicação de funções, selecione o botão **+** junto a **Funções**, escolha **No portal** e selecione **Continuar**.

    ![Funciona rapidamente para escolher uma plataforma.](./media/functions-create-first-azure-function/function-app-quickstart-choose-portal.png)

1. Escolha **WebHook + API** e selecione **Criar**.

    ![Início rápido de funções no portal do Azure.](./media/functions-create-first-azure-function/function-app-quickstart-node-webhook.png)

É criada uma função com um modelo específico de linguagem para uma função acionada por HTTP.

Agora, pode enviar um pedido HTTP para executar a função nova.

## <a name="test-the-function"></a>Testar a função

1. Na sua nova função, clique em **</> Obter URL da função** no canto superior direito, selecione **predefinição (tecla de Função)** e, em seguida, clique em **Copiar**. 

    ![Copiar o URL da função a partir do portal do Azure](./media/functions-create-first-azure-function/function-app-develop-tab-testing.png)

2. Cole o URL da função na barra de endereço do navegador. Adicione o valor da cadeia de consulta `&name=<yourname>` ao final deste URL e prima a tecla `Enter` no teclado para executar o pedido. Deverá ver a resposta devolvida pela função apresentada no browser.  

    O exemplo seguinte mostra a resposta no browser:

    ![Resposta da função no browser.](./media/functions-create-first-azure-function/function-app-browser-testing.png)

    O URL do pedido inclui uma chave que é necessária, por predefinição, para aceder à sua função através de HTTP.

3. Quando a sua função é executada, são escritas informações de rastreio nos registos. Para ver a saída de rastreio da execução anterior, regresse à função no portal e clique na seta, na parte inferior do ecrã, para expandir os **Registos**.

   ![Visualizador de registo de funções no portal do Azure.](./media/functions-create-first-azure-function/function-view-logs.png)

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Passos seguintes

Criou uma aplicação de função com uma função simples de http desencadeada.  

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

Para obter mais informações, veja [Enlaces de HTTP das Funções do Azure](functions-bindings-http-webhook.md).
