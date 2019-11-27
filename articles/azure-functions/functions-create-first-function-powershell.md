---
title: Criar sua primeira função do PowerShell no Azure
description: Saiba como criar sua primeira função do PowerShell no Azure usando Visual Studio Code.
author: joeyaiello
ms.author: jaiello
ms.reviewer: glenga
ms.date: 04/25/2019
ms.topic: quickstart
ms.openlocfilehash: 8f77083ae0107ed9f9c5e3943cfcec25846ff087
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74227208"
---
# <a name="create-your-first-powershell-function-in-azure"></a>Criar sua primeira função do PowerShell no Azure

Este artigo de início rápido orienta como criar sua primeira função do PowerShell sem [servidor](https://azure.com/serverless) usando o Visual Studio Code.

![Azure Functions código em um projeto Visual Studio Code](./media/functions-create-first-function-powershell/powershell-project-first-function.png)

Use a [Extensão das Funções do Azure para o Visual Studio Code] para criar uma função do PowerShell localmente e, em seguida, implantá-la em um novo aplicativo de funções no Azure. A extensão está atualmente em pré-visualização. Para obter mais informações, veja a página de extensão [Extensão das Funções do Azure para o Visual Studio Code].

> [!NOTE]  
> O suporte do PowerShell para a extensão de [Azure Functions][extensão das funções do azure para o visual studio code] está atualmente desabilitado por padrão. Habilitar o suporte do PowerShell é uma das etapas neste artigo.

As etapas a seguir são compatíveis com sistemas operacionais macOS, Windows e Linux.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este guia de início rápido:

* Instalar o [PowerShell Core](/powershell/scripting/install/installing-powershell-core-on-windows)

* Instale o [Visual Studio Code](https://code.visualstudio.com/) numa das [plataformas suportadas](https://code.visualstudio.com/docs/supporting/requirements#_platforms). 

* Instale [a extensão do PowerShell para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.PowerShell).

* Instale o [SDK do .NET Core 2.2 +](https://www.microsoft.com/net/download) (exigido pelo Azure Functions Core Tools e disponível em todas as plataformas com suporte).

* Instale a versão 2. x do [Azure Functions Core Tools](functions-run-local.md#v2).

* Você também precisa de uma assinatura ativa do Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [functions-install-vs-code-extension](../../includes/functions-install-vs-code-extension.md)] 

## <a name="create-a-function-app-project"></a>Criar um projeto de aplicação de função

O modelo do projeto das Funções do Azure no Visual Studio Code cria um projeto que pode ser publicado numa aplicação de funções no Azure. As aplicações de funções permitem-lhe agrupar funções como unidades lógicas para a gestão, implementação e partilha de recursos. 

1. No Visual Studio Code, selecione o logótipo do Azure para apresentar a área **Azure: Funções** e, em seguida, selecione o ícone Criar Novo Projeto.

    ![Criar um projeto de aplicação de função](./media/functions-create-first-function-powershell/create-function-app-project.png)

1. Escolha um local para seu espaço de trabalho de projeto do Functions e escolha **selecionar**.

    > [!NOTE]
    > Este artigo foi criado para ser concluído fora de uma área de trabalho. Neste caso, não selecione uma pasta de projeto que faz parte de uma área de trabalho.

1. Selecione o **PowerShell** como o idioma para seu projeto de aplicativo de funções e, em seguida, **Azure Functions v2**.

1. Escolha **gatilho http** como o modelo para sua primeira função, use `HTTPTrigger` como o nome da função e escolha um nível de autorização de **função**.

    > [!NOTE]
    > O nível de autorização de **função** requer um valor de [chave de função](functions-bindings-http-webhook.md#authorization-keys) ao chamar o ponto de extremidade de função no Azure. Isso torna mais difícil para apenas qualquer pessoa chamar sua função.

1. Quando lhe for pedido, escolha **Adicionar à área de trabalho**.

Visual Studio Code cria o projeto de aplicativo de funções do PowerShell em um novo espaço de trabalho. Este projeto contém os arquivos de configuração [host. JSON](functions-host-json.md) e [local. Settings. JSON](functions-run-local.md#local-settings-file) , que se aplicam a todas as funções no projeto. Este [projeto do PowerShell](functions-reference-powershell.md#folder-structure) é o mesmo que um aplicativo de funções em execução no Azure.

## <a name="run-the-function-locally"></a>Executar localmente a função

Azure Functions Core Tools integra-se com o Visual Studio Code para permitir que você execute e depure um projeto de Azure Functions localmente.  

1. Para depurar sua função, insira uma chamada para o cmdlet [`Wait-Debugger`] no código de função antes de você desejar anexar o depurador e pressione F5 para iniciar o projeto do aplicativo de funções e anexar o depurador. São apresentados os resultados das Ferramentas de Núcleo no painel **Terminal**.

1. No painel **Terminal**, copie o ponto final do URL da sua função acionada por HTTP.

    ![Resultado local do Azure](./media/functions-create-first-function-powershell/functions-vscode-f5.png)

1. Acrescente a cadeia de caracteres de consulta `?name=<yourname>` a essa URL e, em seguida, use `Invoke-RestMethod` para executar a solicitação, da seguinte maneira:

    ```powershell
    PS > Invoke-RestMethod -Method Get -Uri http://localhost:7071/api/HttpTrigger?name=PowerShell
    Hello PowerShell
    ```

    Você também pode executar a solicitação GET de um navegador.

    Quando você chama o ponto de extremidade HttpTrigger sem passar um parâmetro `name` como um parâmetro de consulta ou no corpo, a função retorna um erro [HttpStatusCode]:: BadRequest. Ao examinar o código em Run. ps1, você verá que esse erro ocorre por design.

1. Para parar a depuração, prima Shift + F5.

Depois de verificar que a função é executada corretamente no computador local, deve publicar o projeto no Azure.

> [!NOTE]
> Lembre-se de remover todas as chamadas para `Wait-Debugger` antes de publicar suas funções no Azure. 
>
> A criação de um aplicativo de funções no Azure solicita apenas o nome do aplicativo de funções. Outros valores são definidos para você.
> Defina `azureFunctions.advancedCreation` para que o `true` seja solicitado a fornecer todos os outros valores.

[!INCLUDE [functions-publish-project-vscode](../../includes/functions-publish-project-vscode.md)]

## <a name="test"></a>Executar a função no Azure

Para verificar se a função publicada é executada no Azure, execute o seguinte comando do PowerShell, substituindo o parâmetro `Uri` pela URL da função HTTPTrigger da etapa anterior. Como antes, acrescente a cadeia de caracteres de consulta `&name=<yourname>` à URL, como no exemplo a seguir:

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

Você usou Visual Studio Code para criar um aplicativo de funções do PowerShell com uma função simples disparada por HTTP. Você também pode querer saber mais sobre como [depurar uma função do PowerShell localmente](functions-debug-powershell-local.md) usando o Azure Functions Core Tools. Confira o [Guia do desenvolvedor do Azure Functions PowerShell](functions-reference-powershell.md).

> [!div class="nextstepaction"]
> [Ativar a integração do Application Insights](functions-monitoring.md#manually-connect-an-app-insights-resource)

[Azure portal]: https://portal.azure.com
[Azure Functions Core Tools]: functions-run-local.md
[Extensão das Funções do Azure para o Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
[' Aguardar depurador ']: /powershell/module/microsoft.powershell.utility/wait-debugger?view=powershell-6
