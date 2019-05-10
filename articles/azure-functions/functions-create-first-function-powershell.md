---
title: Criar a sua primeira função do PowerShell com as funções do Azure
description: Saiba como criar a sua primeira função do PowerShell no Azure com o Visual Studio Code.
services: functions
keywords: ''
author: joeyaiello
manager: jeconnoc
ms.author: jaiello, glenga
ms.date: 04/25/2019
ms.topic: quickstart
ms.service: azure-functions
ms.devlang: powershell
ms.openlocfilehash: 24640c9013f7a5b81cd5b1b6f45de49c5baad0e6
ms.sourcegitcommit: 399db0671f58c879c1a729230254f12bc4ebff59
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/09/2019
ms.locfileid: "65473273"
---
# <a name="create-your-first-powershell-function-in-azure-preview"></a>Criar a sua primeira função do PowerShell no Azure (pré-visualização)

[!INCLUDE [functions-powershell-preview-note](../../includes/functions-powershell-preview-note.md)]

Este artigo de início rápido explica-lhe como criar a sua primeira [sem servidor](https://azure.com/serverless) função do PowerShell com o Visual Studio Code.

![Código de funções do Azure num projeto do Visual Studio Code](./media/functions-create-first-function-powershell/powershell-project-first-function.png)

Utilizar o [Extensão das Funções do Azure para o Visual Studio Code] para criar uma função PowerShell localmente e, em seguida, implementou-a para uma nova aplicação de função no Azure. A extensão está atualmente em pré-visualização. Para obter mais informações, veja a página de extensão [Extensão das Funções do Azure para o Visual Studio Code].

> [!NOTE]  
> Suporte do PowerShell para o [extensão de funções do Azure][extensão das funções do azure para o visual studio code] está atualmente desativada por predefinição. Ativar o suporte do PowerShell é um dos passos neste artigo.

Os seguintes passos são suportados em macOS, Windows e sistemas operacionais baseados em Linux.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este guia de início rápido:

* Instalar [o PowerShell Core](/powershell/scripting/install/installing-powershell#powershell-core)

* Instale o [Visual Studio Code](https://code.visualstudio.com/) numa das [plataformas suportadas](https://code.visualstudio.com/docs/supporting/requirements#_platforms). 

* Instale [extensão de PowerShell para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.PowerShell).

* Instale [.NET Core SDK 2.2 +](https://www.microsoft.com/net/download) (exigido por ferramentas de núcleo de funções do Azure e disponível em todas as plataformas suportadas).

* Instalar a versão 2.x dos [ferramentas de núcleo de funções do Azure](functions-run-local.md#v2).

* Terá também uma subscrição do Azure Active Directory.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [functions-install-vs-code-extension](../../includes/functions-install-vs-code-extension.md)] 

## <a name="create-a-function-app-project"></a>Criar um projeto de aplicação de função

O modelo do projeto das Funções do Azure no Visual Studio Code cria um projeto que pode ser publicado numa aplicação de funções no Azure. As aplicações de funções permitem-lhe agrupar funções como unidades lógicas para a gestão, implementação e partilha de recursos. 

1. No Visual Studio Code, selecione o logótipo do Azure para apresentar o **Azure: As funções** área e, em seguida, selecione o ícone de criar novo projeto.

    ![Criar um projeto de aplicação de função](./media/functions-create-first-function-powershell/create-function-app-project.png)

1. Escolha uma localização para a área de trabalho do projeto de funções e escolha **selecione**.

    > [!NOTE]
    > Este artigo foi criado para ser concluído fora de uma área de trabalho. Neste caso, não selecione uma pasta de projeto que faz parte de uma área de trabalho.

1. Selecione o **Powershell (pré-visualização)** como o idioma para o seu projeto de aplicação de função e, em seguida **as funções do Azure v2**.

1. Escolher **acionador de HTTP** como o modelo para a sua primeira função, utilize `HTTPTrigger` como a função dê um nome e escolha um nível de autorização de **função**.

    > [!NOTE]
    > O **função** nível de autorização requer um [tecla de função](functions-bindings-http-webhook.md#authorization-keys) valor ao chamar o ponto final de função no Azure. Isso torna mais difícil que qualquer pessoa chamar sua função.

1. Quando lhe for pedido, escolha **Adicionar à área de trabalho**.

Código do Visual Studio cria o projeto de aplicação de função do PowerShell numa nova área de trabalho. Este projeto contém a [Host. JSON](functions-host-json.md) e [Settings](functions-run-local.md#local-settings-file) ficheiros de configuração, o que se aplicam a função all no projeto. Isso [PowerShell projeto](functions-reference-powershell.md#folder-structure) é o mesmo que uma aplicação de funções em execução no Azure.

## <a name="run-the-function-locally"></a>Executar localmente a função

As ferramentas de núcleo das funções do Azure integra-se com o Visual Studio Code, para permitir que execute e Depure localmente um projeto de funções do Azure.  

1. Para depurar a sua função, insira uma chamada para o [ `Wait-Debugger` ] cmdlet no código de função antes de que pretende anexar o depurador, em seguida, prima F5 para iniciar o projeto de aplicação de função e anexe o depurador. São apresentados os resultados das Ferramentas de Núcleo no painel **Terminal**.

1. No painel **Terminal**, copie o ponto final do URL da sua função acionada por HTTP.

    ![Resultado local do Azure](./media/functions-create-first-function-powershell/functions-vscode-f5.png)

1. Anexe a cadeia de consulta `?name=<yourname>` a este URL e, em seguida, utilize `Invoke-RestMethod` para executar o pedido, da seguinte forma:

    ```powershell
    PS > Invoke-RestMethod -Method Get -Uri http://localhost:7071/api/HttpTrigger?name=PowerShell
    Hello PowerShell
    ```

    Também pode executar o pedido de obtenção de um navegador.

    Quando chamar o ponto de extremidade HttpTrigger sem passar um `name` parâmetro como um parâmetro de consulta ou no corpo, a função devolve um erro de 500. Ao rever o código na run.ps1, verá que este erro ocorre por predefinição.

1. Para parar a depuração, prima Shift + F5.

Depois de verificar que a função é executada corretamente no computador local, deve publicar o projeto no Azure.

> [!NOTE]
> Não se esqueça de remover todas as chamadas para `Wait-Debugger` antes de publicar as suas funções no Azure. 

> [!NOTE]
> Nome da aplicação de funções só pede ao criar uma aplicação de funções no Azure. Defina azureFunctions.advancedCreation como true para ser-lhe pedido para todos os outros valores.

[!INCLUDE [functions-publish-project-vscode](../../includes/functions-publish-project-vscode.md)]

## <a name="test"></a>Executar a função no Azure

Para verificar que a função de publicada é executada no Azure, execute o seguinte comando do PowerShell, substituindo o `Uri` parâmetro com o URL da função HTTPTrigger do passo anterior. Como antes, anexe a cadeia de consulta `&name=<yourname>` para o URL, como no exemplo seguinte:

```powershell
PS > Invoke-WebRequest -Method Get -Uri "https://glengatest-vscode-powershell.azurewebsites.net/api/HttpTrigger?code=nrY05eZutfPqLo0som...&name=PowerShell"

StatusCode        : 200
StatusDescription : OK
Content           : Hello PowerShell
RawContent        : HTTP/1.1 200 OK
                    Content-Length: 16
                    Content-Type: text/plain; charset=utf-8
                    Date: Thu, 25 Apr 2019 16:01:22 GMT

                    Hello PowerShell
Forms             : {}
Headers           : {[Content-Length, 16], [Content-Type, text/plain; charset=utf-8], [Date, Thu, 25 Apr 2019 16:01:22 GMT]}
Images            : {}
InputFields       : {}
Links             : {}
ParsedHtml        : mshtml.HTMLDocumentClass
RawContentLength  : 16
```

## <a name="next-steps"></a>Passos Seguintes

Utilizou o Visual Studio Code para criar uma aplicação de funções do PowerShell com uma função acionada por HTTP simples. Pode também querer saber mais sobre [depuração localmente uma função de PowerShell](functions-debug-powershell-local.md) usando as ferramentas de núcleo de funções do Azure. Veja a [Guia do programador do PowerShell de funções do Azure](functions-reference-powershell.md).

> [!div class="nextstepaction"]
> [Ativar a integração do Application Insights](functions-monitoring.md#manually-connect-an-app-insights-resource)

[Azure portal]: https://portal.azure.com
[Azure Functions Core Tools]: functions-run-local.md
[Extensão das Funções do Azure para o Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
[`Wait-Debugger`]: /powershell/module/microsoft.powershell.utility/wait-debugger?view=powershell-6
