---
title: Criar a sua primeira função no Azure com o Visual Studio Code
description: Criar e publicar no Azure uma simples função ativada por HTTP com a extensão das Funções do Azure no Visual Studio Code.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: funções do azure, funções, processamento de eventos, computação, arquitetura sem servidor
ms.service: azure-functions
ms.devlang: multiple
ms.topic: quickstart
ms.date: 09/07/2018
ms.author: glenga
ms.custom: mvc, devcenter
ms.openlocfilehash: cbe4dbd2ae741f4225cfdc628c31508956cbb95c
ms.sourcegitcommit: 6e32f493eb32f93f71d425497752e84763070fad
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/10/2019
ms.locfileid: "59469731"
---
# <a name="create-your-first-function-using-visual-studio-code"></a>Criar a sua primeira função com o Visual Studio Code

As Funções do Azure permitem-lhe executar o seu código num ambiente [sem servidor](https://azure.microsoft.com/solutions/serverless/), sem que tenha de criar primeiro uma VM ou publicar uma aplicação Web.

Neste artigo, vai aprender a utilizar a [extensão das Funções do Azure para Visual Studio Code], para criar e testar uma função "hello world" no seu computador local com o Microsoft Visual Studio Code. Em seguida, publique o código de função no Azure a partir do Visual Studio Code.

![Código de funções do Azure num projeto do Visual Studio](./media/functions-create-first-function-vs-code/functions-vscode-intro.png)

Atualmente totalmente compatível com a extensão C#, funções de JavaScript e Java, com suporte de Python atualmente em pré-visualização. Os passos neste artigo podem variar consoante a sua escolha de linguagem para o projeto das Funções do Azure. A extensão está atualmente em pré-visualização. Para obter mais informações, veja a página de extensão [Extensão das Funções do Azure para Visual Studio Code].

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este guia de início rápido:

* Instale o [Visual Studio Code](https://code.visualstudio.com/) numa das [plataformas suportadas](https://code.visualstudio.com/docs/supporting/requirements#_platforms). Este artigo foi desenvolvido e testado num dispositivo com macOS (High Sierra).

* Instale a versão 2.x das [Ferramentas de Núcleo das Funções do Azure](functions-run-local.md#v2), que ainda está em pré-visualização.

* Instale os requisitos específicos para a linguagem escolhida:

    | Idioma | Extensão |
    | -------- | --------- |
    | **C#** | [C# para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)<br/>[Ferramentas da CLI de .NET core](https://docs.microsoft.com/dotnet/core/tools/?tabs=netcore2x)*   |
    | **Java** | [Depurador para Java](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debug)<br/>[Java 8](https://aka.ms/azure-jdks)<br/>[Maven 3 +](https://maven.apache.org/) |
    | **JavaScript** | [Nó 8.0](https://nodejs.org/)  |

    \* Também é obrigatório para as Ferramentas de Núcleo.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [functions-install-vs-code-extension](../../includes/functions-install-vs-code-extension.md)]

[!INCLUDE [functions-create-function-app-vs-code](../../includes/functions-create-function-app-vs-code.md)]

## <a name="create-an-http-triggered-function"></a>Criar uma função acionada por HTTP

1. De **Azure: As funções**, escolha o ícone de Create Function.

    ![Criar uma função](./media/functions-create-first-function-vs-code/create-function.png)

1. Selecione a pasta com o projeto da aplicação de funções e selecione o modelo de função **acionador HTTP**.

    ![Escolher o modelo do acionador HTTP](./media/functions-create-first-function-vs-code/create-function-choose-template.png)

1. Escreva `HTTPTrigger` para o nome da função e prima Enter, em seguida selecione a autenticação **Anónima**.

    ![Escolher autenticação anónima](./media/functions-create-first-function-vs-code/create-function-anonymous-auth.png)

    É criada uma função na linguagem que escolheu com o modelo de função acionada por HTTP.

    ![Modelo de função acionada por HTTP no Visual Studio Code](./media/functions-create-first-function-vs-code/new-function-full.png)

Pode adicionar enlaces de entrada e saída à sua função ao modificar o ficheiro function.json. Para obter mais informações, veja [Conceitos de acionadores e enlaces das Funções do Azure](functions-triggers-bindings.md).

Agora que criou o projeto de função e uma função acionada por HTTP, pode testá-la no seu computador local.

## <a name="test-the-function-locally"></a>Testar localmente a função

As Ferramentas de Núcleo das Funções do Azure permitem-lhe executar um projeto de funções do Azure no seu computador de programação local. Deve instalar estas ferramentas ao iniciar uma função do Visual Studio Code pela primeira vez.  

1. Para testar a sua função, defina um ponto de interrupção no código de função e prima F5 para iniciar o projeto da aplicação de funções. São apresentados os resultados das Ferramentas de Núcleo no painel **Terminal**.

1. No painel **Terminal**, copie o ponto final do URL da sua função acionada por HTTP.

    ![Resultado local do Azure](./media/functions-create-first-function-vs-code/functions-vscode-f5.png)

1. Cole o URL do pedido HTTP na barra de endereço do browser. Anexe a cadeia de consulta `?name=<yourname>` a este URL e execute o pedido. A execução é colocada em pausa quando o ponto de interrupção é atingido.

    ![Função que atinge o ponto de interrupção no Visual Studio Code](./media/functions-create-first-function-vs-code/function-debug-vscode-js.png)

1. Quando continua a execução, a imagem abaixo mostra a resposta no browser ao pedido GET:

    ![Resposta da função localhost no browser](./media/functions-create-first-function-vs-code/functions-test-local-browser.png)

1. Para parar a depuração, prima Shift + F5.

Depois de verificar que a função é executada corretamente no computador local, deve publicar o projeto no Azure.

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

[!INCLUDE [functions-publish-project-vscode](../../includes/functions-publish-project-vscode.md)]

## <a name="test-your-function-in-azure"></a>Testar a sua função no Azure

1. Copie o URL do acionador de HTTP no painel **Resultados**. Tal como antes, certifique-se de que adiciona a cadeia de consulta `?name=<yourname>` ao final deste URL e execute o pedido.

    O URL que chama a função acionada por HTTP deve estar no seguinte formato:

        http://<functionappname>.azurewebsites.net/api/<functionname>?name=<yourname> 

1. Cole este novo URL do pedido HTTP na barra de endereço do browser. A imagem seguinte mostra a resposta no browser em relação ao pedido GET devolvido pela função: 

    ![Resposta da função no browser](./media/functions-create-first-function-vs-code/functions-test-remote-browser.png)

## <a name="next-steps"></a>Passos Seguintes

Utilizou o Visual Studio Code para criar uma aplicação de funções com uma função simples acionada por HTTP. Pode também querer saber mais sobre [local de teste e depuração da linha de comandos ou Terminal](functions-run-local.md) usando as ferramentas de núcleo de funções do Azure.

> [!div class="nextstepaction"]
> [Ativar a integração do Application Insights](functions-monitoring.md#manually-connect-an-app-insights-resource)

[Azure Functions Core Tools]: functions-run-local.md
[Extensão de funções do Azure para Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
