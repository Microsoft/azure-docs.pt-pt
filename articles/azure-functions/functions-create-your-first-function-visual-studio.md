---
title: 'Quickstart: Crie a sua primeira função em Azure usando o Visual Studio'
description: Neste arranque rápido, aprende-se a criar e publicar uma Função Azure desencadeada pela HTTP utilizando o Visual Studio.
ms.assetid: 82db1177-2295-4e39-bd42-763f6082e796
ms.topic: quickstart
ms.date: 03/06/2020
ms.custom: mvc, devcenter, vs-azure, 23113853-34f2-4f
ms.openlocfilehash: 13a03ca6820229fc90467236e0c23f236664d73f
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "80056588"
---
# <a name="quickstart-create-your-first-function-in-azure-using-visual-studio"></a>Quickstart: Crie a sua primeira função em Azure usando o Visual Studio

As Funções Azure permitem executar o seu código num ambiente sem servidores sem ter de criar primeiro um VM ou publicar uma aplicação web.

Neste quickstart, aprende-se a usar o Visual Studio 2019 para criar e testar localmente uma aplicação de função C# "hello world" desencadeada por HTTP, que depois publica para o Azure. 

![Resposta da função localhost no browser](./media/functions-create-your-first-function-visual-studio/functions-create-your-first-function-visual-studio-browser-local-final.png)

Este quickstart foi projetado para o Visual Studio 2019. 

## <a name="prerequisites"></a>Pré-requisitos

Para completar este tutorial, instale primeiro o [Visual Studio 2019.](https://azure.microsoft.com/downloads/) Certifique-se de que seleciona a carga de trabalho de **desenvolvimento do Azure** durante a instalação. Se quiser criar um projeto de Funções Azure utilizando o Visual Studio 2017, tem primeiro de instalar as [mais recentes ferramentas de Funções Azure.](functions-develop-vs.md#check-your-tools-version)

![Instale o Estúdio Visual com a carga de trabalho de desenvolvimento do Azure](media/functions-create-your-first-function-visual-studio/functions-vs-workloads.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-function-app-project"></a>Criar um projeto de aplicação de função

[!INCLUDE [Create a project using the Azure Functions template](../../includes/functions-vstools-create.md)]

O Visual Studio cria um projeto e classe que contém código de placa de caldeira para o tipo de função de gatilho HTTP. O `FunctionName` atributo do método define o nome da `Function1`função, que por defeito é . O `HttpTrigger` atributo especifica que a função é desencadeada por um pedido HTTP. O código automático envia uma resposta HTTP que inclui um valor da cadeia de consulta ou de corpo de pedido.

Expanda as capacidades da sua função com ligações de entrada e saída aplicando os atributos apropriados ao método. Para obter mais informações, veja a secção [Acionadores e enlaces](functions-dotnet-class-library.md#triggers-and-bindings) da [Referência para Programadores do Funções do Azure C#](functions-dotnet-class-library.md).

Agora que criou o projeto de função e uma função acionada por HTTP, pode testá-la no seu computador local.

## <a name="run-the-function-locally"></a>Executar localmente a função

O Visual Studio integra-se com as Ferramentas Core funções do Azure para que possa testar as suas funções localmente utilizando o tempo de funcionamento completo das Funções Azure.  

[!INCLUDE [functions-run-function-test-local-vs](../../includes/functions-run-function-test-local-vs.md)]

Depois de verificar que a função é executada corretamente no computador local, deve publicar o projeto no Azure.

## <a name="publish-the-project-to-azure"></a>Publicar o projeto no Azure

Antes de poder publicar o seu projeto, deve ter uma aplicação de função na subscrição do Azure. A publicação do Visual Studio cria uma aplicação de função para si na primeira vez que publica o seu projeto.

[!INCLUDE [Publish the project to Azure](../../includes/functions-vstools-publish.md)]

## <a name="test-your-function-in-azure"></a>Testar a sua função no Azure

1. Copie o URL base da aplicação de função a partir da página de perfil **Publicar.** Substitua `localhost:port` a parte do URL utilizado para testar a função localmente com o novo URL base. Anexar a corda `?name=<YOUR_NAME>` de consulta a este URL e executar o pedido.

    O URL que chama a função ativada http está no seguinte formato:

    `http://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?name=<YOUR_NAME>`

2. Cole este novo URL do pedido HTTP na barra de endereço do browser. A imagem seguinte mostra a resposta no navegador ao pedido remoto GET devolvido pela função:

    ![Resposta da função no browser](./media/functions-create-your-first-function-visual-studio/functions-create-your-first-function-visual-studio-browser-azure.png)

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Passos seguintes

Neste quickstart, usaste o Visual Studio para criar e publicar uma aplicação de função C# em Azure com uma simples função ativada pela HTTP. 

Avance para o próximo artigo para aprender a adicionar uma fila de fila de armazenamento Azure à sua função:
> [!div class="nextstepaction"]
> [Adicione uma ligação de fila de armazenamento Azure à sua função](functions-add-output-binding-storage-queue-vs.md)

