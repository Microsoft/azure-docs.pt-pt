---
title: Criar a sua primeira função no Azure com o Visual Studio
description: Criar e publicar uma Função do Azure acionada por HTTP com o Visual Studio.
author: ggailey777
manager: gwallace
keywords: funções do azure, funções, processamento de eventos, computação, arquitetura sem servidor
ms.assetid: 82db1177-2295-4e39-bd42-763f6082e796
ms.service: azure-functions
ms.topic: quickstart
ms.date: 07/19/2019
ms.author: glenga
ms.custom: mvc, devcenter, vs-azure, 23113853-34f2-4f
ms.openlocfilehash: 1d798be3a0cde39de208105c4b3d4458fa5b124c
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72329495"
---
# <a name="create-your-first-function-using-visual-studio"></a>Criar a sua primeira função com o Visual Studio

As Funções do Azure permitem-lhe executar o seu código num ambiente [sem servidor](https://azure.microsoft.com/solutions/serverless/), sem que tenha de criar primeiro uma VM ou publicar uma aplicação Web.

Neste artigo, você aprenderá a usar o Visual Studio 2019 para criar e testar localmente uma função "Olá, mundo" e, em seguida, publicá-la no Azure. Este guia de início rápido foi projetado para o Visual Studio 2019. Ao criar um projeto do Functions usando o Visual Studio 2017, você deve primeiro instalar as [ferramentas de Azure Functions mais recentes](functions-develop-vs.md#check-your-tools-version).

![Resposta da função localhost no browser](./media/functions-create-your-first-function-visual-studio/functions-create-your-first-function-visual-studio-browser-local-final.png)

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você deve primeiro instalar o [Visual Studio 2019](https://azure.microsoft.com/downloads/). Verifique se a carga de trabalho de **desenvolvimento do Azure** também está instalada.

![Instalar o Visual Studio com a carga de trabalho de desenvolvimento do Azure](media/functions-create-your-first-function-visual-studio/functions-vs-workloads.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-function-app-project"></a>Criar um projeto de aplicação de função

[!INCLUDE [Create a project using the Azure Functions template](../../includes/functions-vstools-create.md)]

O Visual Studio cria um projeto e uma classe que contém código clichê para o tipo de função de gatilho HTTP. O atributo `FunctionName` no método define o nome da função, que por padrão é `HttpTrigger`. O atributo `HttpTrigger` especifica que a função é disparada por uma solicitação HTTP. O código automático envia uma resposta HTTP que inclui um valor da cadeia de consulta ou de corpo de pedido.

Você pode expandir os recursos de sua função usando associações de entrada e saída aplicando os atributos apropriados ao método. Para obter mais informações, veja a secção [Acionadores e enlaces](functions-dotnet-class-library.md#triggers-and-bindings) da [Referência para Programadores do Funções do Azure C#](functions-dotnet-class-library.md).

Agora que criou o projeto de função e uma função acionada por HTTP, pode testá-la no seu computador local.

## <a name="run-the-function-locally"></a>Executar localmente a função

O Visual Studio integra-se com Azure Functions Core Tools para que você possa testar suas funções localmente usando o tempo de execução de funções completas.  

[!INCLUDE [functions-run-function-test-local-vs](../../includes/functions-run-function-test-local-vs.md)]

Depois de verificar que a função é executada corretamente no computador local, deve publicar o projeto no Azure.

## <a name="publish-the-project-to-azure"></a>Publicar o projeto no Azure

Deverá ter uma aplicação de funções na sua subscrição do Azure antes de poder publicar o seu projeto. A publicação do Visual Studio cria um aplicativo de funções para você na primeira vez que você publicar seu projeto.

[!INCLUDE [Publish the project to Azure](../../includes/functions-vstools-publish.md)]

## <a name="test-your-function-in-azure"></a>Testar a sua função no Azure

1. Copie a URL de base da aplicação de funções a partir da página de perfil Publicar. Substitua a `localhost:port` parte da URL que utilizou ao testar a localmente a função com a nova URL de base. Tal como antes, certifique-se de que anexa a cadeia de consulta `?name=<YOUR_NAME>` a este URL e execute o pedido.

    O URL que chama a função de acionada por HTTP deve estar no seguinte formato:

        http://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?name=<YOUR_NAME> 

2. Cole este novo URL do pedido HTTP na barra de endereço do browser. A imagem seguinte mostra a resposta no browser em relação ao pedido GET devolvido pela função:

    ![Resposta da função no browser](./media/functions-create-your-first-function-visual-studio/functions-create-your-first-function-visual-studio-browser-azure.png)

## <a name="next-steps"></a>Passos seguintes

Você usou o Visual Studio para criar e publicar um C# aplicativo de funções no Azure com uma simples função disparada por http. Para saber mais sobre como desenvolver funções como bibliotecas de classes do .NET, confira [ C# Azure Functions referência do desenvolvedor](functions-dotnet-class-library.md).

> [!div class="nextstepaction"]
> [Adicionar uma associação de fila de armazenamento do Azure à sua função](functions-add-output-binding-storage-queue-vs.md)
