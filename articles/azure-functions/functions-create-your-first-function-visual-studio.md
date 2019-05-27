---
title: Criar a sua primeira função no Azure com o Visual Studio
description: Criar e publicar uma Função do Azure acionada por HTTP com o Visual Studio.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: funções do azure, funções, processamento de eventos, computação, arquitetura sem servidor
ms.assetid: 82db1177-2295-4e39-bd42-763f6082e796
ms.service: azure-functions
ms.devlang: multiple
ms.topic: quickstart
ms.date: 10/17/2018
ms.author: glenga
ms.custom: mvc, devcenter, vs-azure, 23113853-34f2-4f
ms.openlocfilehash: 1c07da0121b8e350c55b139901d56cfe2133386d
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/17/2019
ms.locfileid: "65864500"
---
# <a name="create-your-first-function-using-visual-studio"></a>Criar a sua primeira função com o Visual Studio

As Funções do Azure permitem-lhe executar o seu código num ambiente [sem servidor](https://azure.microsoft.com/solutions/serverless/), sem que tenha de criar primeiro uma VM ou publicar uma aplicação Web.

Neste artigo, irá aprender a utilizar as ferramentas do Visual Studio 2019 para as funções do Azure para criar localmente e testar uma função de "hello world". Em seguida, publique o código de função no Azure. Estas ferramentas estão disponíveis como parte da carga de trabalho de desenvolvimento do Azure no Visual Studio 2019.

![Resposta da função localhost no browser](./media/functions-create-your-first-function-visual-studio/functions-create-your-first-function-visual-studio-browser-local-final.png)

Este tópico inclui [um vídeo](#watch-the-video) que demonstra os mesmos passos básicos.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

* Instale [Visual Studio 2019](https://azure.microsoft.com/downloads/) e certifique-se de que o **desenvolvimento do Azure** carga de trabalho também é instalada.

* Verifique se tem as [ferramentas de Funções do Azure mais recentes](functions-develop-vs.md#check-your-tools-version).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-function-app-project"></a>Criar um projeto de aplicação de função

[!INCLUDE [Create a project using the Azure Functions template](../../includes/functions-vstools-create.md)]

O Visual Studio cria um projeto e neste uma classe que contém o código automático de tipo de função de escolhida. O atributo **FunctionName** nos conjuntos define o nome da função. O atributo **HttpTrigger** especifica que a função é acionada por um pedido de HTTP. O código automático envia uma resposta HTTP que inclui um valor da cadeia de consulta ou de corpo de pedido. Pode adicionar enlaces de entrada e saída para uma função aplicando os atributos adequados para o método. Para obter mais informações, veja a secção [Acionadores e enlaces](functions-dotnet-class-library.md#triggers-and-bindings) da [Referência para Programadores do Funções do Azure C#](functions-dotnet-class-library.md).

Agora que criou o projeto de função e uma função acionada por HTTP, pode testá-la no seu computador local.

## <a name="test-the-function-locally"></a>Testar localmente a função

As Ferramentas de Núcleo das Funções do Azure permitem-lhe executar um projeto de funções do Azure no seu computador de programação local. Deverá instalar essas ferramentas da primeira vez que iniciar uma função do Visual Studio.

1. Para testar a sua função, prima F5. Se solicitado, aceite o pedido do Visual Studio para transferir e instalar as ferramentas de Núcleo das Funções do Azure (CLI). Também poderá ativar a exceção da firewall para que todas as ferramentas possam aceitar os pedidos de HTTP.

2. Copie o URL da sua função na saída do tempo de execução das funções do Azure.

    ![Tempo de execução local do Azure](./media/functions-create-your-first-function-visual-studio/functions-create-your-first-function-visual-studio-debugging.png)

3. Cole o URL do pedido HTTP na barra de endereço do browser. Anexe a cadeia de consulta `?name=<YOUR_NAME>` a este URL e execute o pedido. O exemplo apresentado em seguida mostra a resposta no browser relativamente ao pedido GET devolvido retornado pela função: 

    ![Resposta da função localhost no browser](./media/functions-create-your-first-function-visual-studio/functions-create-your-first-function-visual-studio-browser-local.png)

4. Para parar a depuração, prima **Shift + F5**.

Depois de verificar que a função é executada corretamente no computador local, deve publicar o projeto no Azure.

## <a name="publish-the-project-to-azure"></a>Publicar o projeto no Azure

Deverá ter uma aplicação de funções na sua subscrição do Azure antes de poder publicar o seu projeto. Pode criar uma aplicação de funções diretamente a partir do Visual Studio.

[!INCLUDE [Publish the project to Azure](../../includes/functions-vstools-publish.md)]

## <a name="test-your-function-in-azure"></a>Testar a sua função no Azure

1. Copie a URL de base da aplicação de funções a partir da página de perfil Publicar. Substitua a `localhost:port` parte da URL que utilizou ao testar a localmente a função com a nova URL de base. Tal como antes, certifique-se de que anexa a cadeia de consulta `?name=<YOUR_NAME>` a este URL e execute o pedido.

    O URL que chama a função de acionada por HTTP deve estar no seguinte formato:

        http://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?name=<YOUR_NAME> 

2. Cole este novo URL do pedido HTTP na barra de endereço do browser. A imagem seguinte mostra a resposta no browser em relação ao pedido GET devolvido pela função:

    ![Resposta da função no browser](./media/functions-create-your-first-function-visual-studio/functions-create-your-first-function-visual-studio-browser-azure.png)

## <a name="watch-the-video"></a>Ver o vídeo

> [!VIDEO https://www.youtube-nocookie.com/embed/DrhG-Rdm80k]

## <a name="next-steps"></a>Passos Seguintes

Utilizou o Visual Studio para criar e publicar uma aplicação de função C# com uma função simples acionada por HTTP.

* [Saiba como adicionar os enlaces de entrada e saída que se integram com outros serviços.](functions-develop-vs.md#add-bindings)
* [Saiba mais sobre como desenvolver funções como bibliotecas de classe .NET](functions-dotnet-class-library.md).
