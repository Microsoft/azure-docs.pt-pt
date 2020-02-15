---
title: Crie a sua primeira função PowerShell em Azure
description: Aprenda a criar a sua primeira função PowerShell em Azure usando o Visual Studio Code.
author: joeyaiello
ms.author: jaiello
ms.reviewer: glenga
ms.date: 04/25/2019
ms.topic: quickstart
ms.openlocfilehash: e9aeb7234f5b840d790f6358bab2426462cc7986
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2020
ms.locfileid: "77210230"
---
# <a name="create-your-first-powershell-function-in-azure"></a>Crie a sua primeira função PowerShell em Azure

Este artigo de arranque rápido ajuda-o a criar a sua primeira função PowerShell [sem servidor](https://azure.com/serverless) usando o Código do Estúdio Visual.

![Código de Funções Azure em um projeto de Código de Estúdio Visual](./media/functions-create-first-function-powershell/powershell-project-first-function.png)

Utiliza a [Extensão das Funções do Azure para o Visual Studio Code] de Estúdio Visual para criar uma função PowerShell localmente e depois implanta-a numa nova aplicação de funções no Azure. A extensão está atualmente em pré-visualização. Para obter mais informações, veja a página de extensão [Extensão das Funções do Azure para o Visual Studio Code].

Os seguintes passos são suportados nos sistemas operativos macOS, Windows e Linux.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este guia de início rápido:

* Instalar [powerShell Core](/powershell/scripting/install/installing-powershell-core-on-windows)

* Instale o [Visual Studio Code](https://code.visualstudio.com/) numa das [plataformas suportadas](https://code.visualstudio.com/docs/supporting/requirements#_platforms). 

* Instale [a extensão PowerShell para O Código](https://marketplace.visualstudio.com/items?itemName=ms-vscode.PowerShell)do Estúdio Visual .

* Instale [.NET Core SDK 2.2+](https://www.microsoft.com/net/download) (exigido pelas Ferramentas Core funções do Azure e disponível em todas as plataformas suportadas).

* Instale a versão 2.x das [Ferramentas Core funções do Azure](functions-run-local.md#v2).

* Também precisa de uma subscrição azure ativa.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [functions-install-vs-code-extension](../../includes/functions-install-vs-code-extension.md)] 

## <a name="create-a-function-app-project"></a>Criar um projeto de aplicação de função

O modelo do projeto das Funções do Azure no Visual Studio Code cria um projeto que pode ser publicado numa aplicação de funções no Azure. Uma aplicação de função permite-lhe agrupar funções como uma unidade lógica para uma gestão mais fácil, implementação, escalae e partilha de recursos.

1. No Visual Studio Code, selecione o logótipo do Azure para apresentar a área **Azure: Funções** e, em seguida, selecione o ícone Criar Novo Projeto.

    ![Criar um projeto de aplicação de função](./media/functions-create-first-function-powershell/create-function-app-project.png)

1. Escolha um local para o espaço de trabalho do projeto Funções e escolha **Select**.

    > [!NOTE]
    > Este artigo foi criado para ser concluído fora de uma área de trabalho. Neste caso, não selecione uma pasta de projeto que faz parte de uma área de trabalho.

1. Selecione o **Powershell** como o idioma para o seu projeto de aplicação de funções e, em seguida, **funções Azure v2**.

1. Escolha **http Trigger** como modelo para a sua primeira função, use `HTTPTrigger` como nome de função e escolha um nível de **funcionamento**de autorização .

    > [!NOTE]
    > O nível de autorização **de função** requer um valor chave de [função](functions-bindings-http-webhook-trigger.md#authorization-keys) ao ligar para o ponto final da função em Azure. Isto torna mais difícil para qualquer um ligar para a sua função.

1. Quando lhe for pedido, escolha **Adicionar à área de trabalho**.

O Visual Studio Code cria o projeto da aplicação de funções PowerShell num novo espaço de trabalho. Este projeto contém os ficheiros de configuração [host.json](functions-host-json.md) e [local.settings.json,](functions-run-local.md#local-settings-file) que se aplicam a todas as funções no projeto. Este [projeto PowerShell](functions-reference-powershell.md#folder-structure) é o mesmo que uma aplicação de função em funcionamento no Azure.

[!INCLUDE [functions-run-function-test-local-vs-code-ps](../../includes/functions-run-function-test-local-vs-code-ps.md)]

[!INCLUDE [functions-publish-project-vscode](../../includes/functions-publish-project-vscode.md)]

## <a name="test"></a>Executar a função em Azure

Para verificar se a sua função publicada funciona em Azure, execute o seguinte comando PowerShell, substituindo o parâmetro `Uri` pelo URL da função HTTPTrigger do passo anterior. Como antes, anexar a cadeia de consulta `&name=<yourname>` ao URL, como no seguinte exemplo:

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

## <a name="next-steps"></a>Passos seguintes

Usou o Código do Estúdio Visual para criar uma aplicação de função PowerShell com uma função simples desencadeada por HTTP. Também pode querer saber mais sobre [depurar uma função PowerShell localmente](functions-debug-powershell-local.md) usando as Ferramentas Core funções Azure. Confira o guia de desenvolvimento de [funções Azure PowerShell](functions-reference-powershell.md).

> [!div class="nextstepaction"]
> [Ativar a integração do Application Insights](functions-monitoring.md#manually-connect-an-app-insights-resource)

[Azure portal]: https://portal.azure.com
[Azure Functions Core Tools]: functions-run-local.md
[Extensão das Funções do Azure para o Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
[`Wait-Debugger`]: /powershell/module/microsoft.powershell.utility/wait-debugger?view=powershell-6
