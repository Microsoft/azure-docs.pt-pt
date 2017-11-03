---
title: "Criar uma aplicação web do .NET Core e implementar no App Service no Linux | Microsoft Docs"
description: "Implemente a sua primeira aplicação .NET Core Olá, mundo no App Service no Linux em minutos."
keywords: "serviço de aplicações do Azure, aplicação web, dotnet, núcleos, linux, oss"
services: app-service
documentationCenter: 
author: cephalin
manager: syntaxc4
editor: 
ms.assetid: c02959e6-7220-496a-a417-9b2147638e2e
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: linux
ms.devlang: na
ms.topic: quickstart
ms.date: 08/30/2017
ms.author: cfowler
ms.custom: mvc
ms.openlocfilehash: 2a8000cadd6f6d7204e1790df62443a7ac7598c9
ms.sourcegitcommit: 3e3a5e01a5629e017de2289a6abebbb798cec736
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2017
---
# <a name="create-a-net-core-web-app-in-app-service-on-linux"></a>Criar uma aplicação web do .NET Core no App Service no Linux

[Serviço de aplicações no Linux](app-service-linux-intro.md) fornece uma serviço utilizando o sistema operativo Linux de alojamento na web altamente dimensionável e aplicação de patches personalizada. Este guia de introdução mostra como criar um [.NET Core](https://docs.microsoft.com/aspnet/core/) aplicação no App Service no Linux. Criar a aplicação web através de [CLI do Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli), e utilizar o Git para implementar o código de .NET Core na aplicação web.

![Aplicação de exemplo em execução no Azure](media/quickstart-dotnetcore/dotnet-browse-azure.png)

Pode seguir os passos abaixo num computador Mac, Windows ou Linux.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este guia de início rápido:

* [Instale o Git](https://git-scm.com/).
* [Instalar o SDK do .NET Core](https://www.microsoft.com/net/download/core)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-the-app-locally"></a>Criar a aplicação localmente

Numa janela de terminal no seu computador, crie um diretório com o nome `hellodotnetcore` e altere o diretório atual ao mesmo.

```bash
md hellodotnetcore
cd hellodotnetcore
```

Crie uma nova aplicação de web do .NET Core.

```bash
dotnet new web
```

## <a name="run-the-app-locally"></a>Executar a aplicação localmente

Restaurar os pacotes NuGet e execute a aplicação.

```bash
dotnet restore
dotnet run
```

Abra um browser e navegue para a aplicação no `http://localhost:5000`.

Pode ver a mensagem **Hello World** da aplicação de exemplo apresentada na página.

![Testar com o browser](media/quickstart-dotnetcore/dotnet-browse-local.png)

Na janela do terminal, prima **Ctrl+C** para sair do servidor Web. Inicializar um repositório de Git para o projeto de .NET Core.

```bash
git init
git add .
git commit -m "first commit"
```

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user.md)]

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group.md)]

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux.md)]

## <a name="create-a-web-app-with-built-in-image"></a>Criar uma aplicação web com a imagem incorporada

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-dotnetcore-no-h.md)]

Navegue até à sua aplicação web recentemente criada. Substitua  _&lt;nome da aplicação >_ com um nome de aplicação único.

```bash
http://<app name>.azurewebsites.net
```

![Página da aplicação Web vazia](media/quickstart-dotnetcore/dotnet-browse-created.png)

[!INCLUDE [Push to Azure](../../../includes/app-service-web-git-push-to-azure.md)] 

```bash
Counting objects: 22, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (18/18), done.
Writing objects: 100% (22/22), 51.21 KiB | 3.94 MiB/s, done.
Total 22 (delta 1), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id '741f16d1db'.
remote: Generating deployment script.
remote: Project file path: ./hellodotnetcore.csproj
remote: Generated deployment script files
remote: Running deployment command...
remote: Handling ASP.NET Core Web Application deployment.
remote: ...............................................................................................
remote:   Restoring packages for /home/site/repository/hellodotnetcore.csproj...
remote: ....................................
remote:   Installing System.Xml.XPath 4.0.1.
remote:   Installing System.Diagnostics.Tracing 4.1.0.
remote:   Installing System.Threading.Tasks.Extensions 4.0.0.
remote:   Installing System.Reflection.Emit.ILGeneration 4.0.1.
remote:   ...
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Deployment successful.
To https://cephalin-dotnetcore.scm.azurewebsites.net/cephalin-dotnetcore.git
 * [new branch]      master -> master
```

## <a name="browse-to-the-app"></a>Navegar para a aplicação

Utilize o browser para navegar para a aplicação implementada.

```bash
http://<app_name>.azurewebsites.net
```

O código de exemplo do Node.js está em execução numa aplicação web com a imagem incorporada.

![Aplicação de exemplo em execução no Azure](media/quickstart-dotnetcore/dotnet-browse-azure.png)

**Parabéns!** Implementou a sua primeira aplicação Node.js no App Service no Linux.

## <a name="update-and-redeploy-the-code"></a>Atualizar e voltar a implementar o código

No diretório local, abra o _Startup.cs_ ficheiro. Efetuar uma alteração de pequeno para o texto na chamada de método `context.Response.WriteAsync`:

```csharp
await context.Response.WriteAsync("Hello Azure!");
```

Consolide as suas alterações no Git e envie as alterações ao código para o Azure.

```bash
git commit -am "updated output"
git push azure master
```

Depois de concluída a implementação, volte para a janela do browser aberta no passo **Navegar para a aplicação** e clique em “atualizar”.

![Aplicação de exemplo atualizada em execução no Azure](media/quickstart-dotnetcore/dotnet-browse-azure-updated.png)

## <a name="manage-your-new-azure-web-app"></a>Gerir a sua nova aplicação Web do Azure

Aceda ao <a href="https://portal.azure.com" target="_blank">portal do Azure</a> para gerir a aplicação Web que criou.

No menu à esquerda, clique em **Serviços de Aplicações** e clique no nome da sua aplicação Web do Azure.

![Navegação no portal para a aplicação Web do Azure](./media/quickstart-dotnetcore/portal-app-service-list.png)

É apresentada a página de descrição geral da sua aplicação Web. Aqui, pode realizar tarefas de gestão básicas, como navegar, parar, iniciar, reiniciar e eliminar. 

![Página Serviço de Aplicações no portal do Azure](media/quickstart-dotnetcore/portal-app-overview.png)

O menu à esquerda fornece diferentes páginas para configurar a sua aplicação. 

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Criar uma aplicação web .NET Core e a SQL Database no App Service do Azure no Linux](tutorial-dotnetcore-sqldb-app.md)
