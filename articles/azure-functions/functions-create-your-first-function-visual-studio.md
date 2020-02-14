---
title: Criar a sua primeira função no Azure com o Visual Studio
description: Criar e publicar uma Função do Azure acionada por HTTP com o Visual Studio.
ms.assetid: 82db1177-2295-4e39-bd42-763f6082e796
ms.topic: quickstart
ms.date: 07/19/2019
ms.custom: mvc, devcenter, vs-azure, 23113853-34f2-4f
ms.openlocfilehash: b343adeed5abeecdf55f71f8bc4298659a106e09
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/13/2020
ms.locfileid: "77198399"
---
# <a name="create-your-first-function-using-visual-studio"></a>Criar a sua primeira função com o Visual Studio

As Funções do Azure permitem-lhe executar o seu código num ambiente [sem servidor](https://azure.microsoft.com/solutions/serverless/), sem que tenha de criar primeiro uma VM ou publicar uma aplicação Web.

Neste artigo, aprende-se a usar o Visual Studio 2019 para criar e testar localmente uma função de "hello world" e depois publicá-la no Azure. Este quickstart foi projetado para o Visual Studio 2019. Ao criar um projeto Funções utilizando o Visual Studio 2017, tem primeiro de instalar as [mais recentes ferramentas de Funções Azure.](functions-develop-vs.md#check-your-tools-version)

![Resposta da função localhost no browser](./media/functions-create-your-first-function-visual-studio/functions-create-your-first-function-visual-studio-browser-local-final.png)

## <a name="prerequisites"></a>Pré-requisitos

Para completar este tutorial, tem primeiro de instalar o [Visual Studio 2019](https://azure.microsoft.com/downloads/). Certifique-se de que a carga de trabalho de **desenvolvimento do Azure** também está instalada.

![Instale o Estúdio Visual com a carga de trabalho de desenvolvimento do Azure](media/functions-create-your-first-function-visual-studio/functions-vs-workloads.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-function-app-project"></a>Criar um projeto de aplicação de função

[!INCLUDE [Create a project using the Azure Functions template](../../includes/functions-vstools-create.md)]

O Visual Studio cria um projeto e classe que contém código de placa de caldeira para o tipo de função de gatilho HTTP. O atributo `FunctionName` no método define o nome da função, que por defeito é `Function1`. O atributo `HttpTrigger` especifica que a função é desencadeada por um pedido HTTP. O código automático envia uma resposta HTTP que inclui um valor da cadeia de consulta ou de corpo de pedido.

Pode expandir as capacidades da sua função utilizando ligações de entrada e saída aplicando os atributos apropriados ao método. Para obter mais informações, veja a secção [Acionadores e enlaces](functions-dotnet-class-library.md#triggers-and-bindings) da [Referência para Programadores do Funções do Azure C#](functions-dotnet-class-library.md).

Agora que criou o projeto de função e uma função acionada por HTTP, pode testá-la no seu computador local.

## <a name="run-the-function-locally"></a>Executar localmente a função

O Visual Studio integra-se com as Ferramentas Core funções do Azure para que possa testar as suas funções localmente utilizando o tempo de funcionamento completo das Funções.  

[!INCLUDE [functions-run-function-test-local-vs](../../includes/functions-run-function-test-local-vs.md)]

Depois de verificar que a função é executada corretamente no computador local, deve publicar o projeto no Azure.

## <a name="publish-the-project-to-azure"></a>Publicar o projeto no Azure

Deverá ter uma aplicação de funções na sua subscrição do Azure antes de poder publicar o seu projeto. A publicação do Visual Studio cria uma aplicação de função para si na primeira vez que publica o seu projeto.

[!INCLUDE [Publish the project to Azure](../../includes/functions-vstools-publish.md)]

## <a name="test-your-function-in-azure"></a>Testar a sua função no Azure

1. Copie a URL de base da aplicação de funções a partir da página de perfil Publicar. Substitua a `localhost:port` parte da URL que utilizou ao testar a localmente a função com a nova URL de base. Tal como antes, certifique-se de que anexa a cadeia de consulta `?name=<YOUR_NAME>` a este URL e execute o pedido.

    O URL que chama a função de acionada por HTTP deve estar no seguinte formato:

        http://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?name=<YOUR_NAME> 

2. Cole este novo URL do pedido HTTP na barra de endereço do browser. A imagem seguinte mostra a resposta no browser em relação ao pedido GET devolvido pela função:

    ![Resposta da função no browser](./media/functions-create-your-first-function-visual-studio/functions-create-your-first-function-visual-studio-browser-azure.png)

## <a name="next-steps"></a>Passos seguintes

Usou o Visual Studio para C# criar e publicar uma aplicação de função em Azure com uma função simples de gatilho http. Para saber mais sobre o desenvolvimento de funções como bibliotecas de classes .NET, consulte a referência do desenvolvedor de [ C# Funções Azure](functions-dotnet-class-library.md).

> [!div class="nextstepaction"]
> [Adicione uma ligação de fila de armazenamento Azure à sua função](functions-add-output-binding-storage-queue-vs.md)
