---
title: 'Tutorial: Anfitrião RESTful API com CORS'
description: Saiba como é que o Serviço de Aplicações do Azure o ajuda a tirar o máximo partido das suas APIs RESTful com suporte para CORS. O Serviço de Aplicações pode hospedar aplicações web front-end e APIs de back-end.
ms.assetid: a820e400-06af-4852-8627-12b3db4a8e70
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 04/28/2020
ms.custom: devx-track-csharp, mvc, devcenter, seo-javascript-september2019, seo-javascript-october2019, seodec18, devx-track-azurecli
ms.openlocfilehash: 098ee1c8aac343be5ffdbe90d18c886ef74ed72f
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107480876"
---
# <a name="tutorial-host-a-restful-api-with-cors-in-azure-app-service"></a>Tutorial: Alojar uma API RESTful com CORS no Serviço de Aplicações do Azure

O [Serviço de Aplicações do Azure](overview.md) oferece um serviço de alojamento na Web altamente dimensionável e com correção automática. Além disso, o Serviço de Aplicações tem suporte incorporado para [Partilha de Recursos de Várias Origens (CORS)](https://wikipedia.org/wiki/Cross-Origin_Resource_Sharing) para APIs RESTful. Este tutorial mostra como implementar uma aplicação API ASP.NET Core no Serviço de Aplicações com suporte para CORS. A aplicação é configurada com ferramentas de linha de comandos e implementada na aplicação através do Git. 

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar recursos do Serviço de Aplicações com a CLI do Azure
> * Implementar uma API RESTful no Azure com o Git
> * Ativar o suporte para CORS no Serviço de Aplicações

Pode seguir os passos neste tutorial em macOS, Linux e Windows.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

* <a href="https://git-scm.com/" target="_blank">Instalar o Git</a>
 * <a href="https://dotnet.microsoft.com/download/dotnet-core/3.1" target="_blank">Instale o mais recente .NET Core 3.1 SDK</a>

## <a name="create-local-aspnet-core-app"></a>Criar uma aplicação .NET Core local

Neste passo, vai configurar o projeto ASP.NET Core local. O Serviço de Aplicações suporta o mesmo fluxo de trabalho para APIs escritas noutras linguagens.

### <a name="clone-the-sample-application"></a>Clonar a aplicação de exemplo

Na janela de terminal, `cd` num diretório de trabalho.  

Execute o seguinte comando para clonar o repositório de exemplo. 

```bash
git clone https://github.com/Azure-Samples/dotnet-core-api
```

Este repositório contém uma aplicação que é criada com base no tutorial [ASP.NET Core Web API help pages using Swagger](/aspnet/core/tutorials/web-api-help-pages-using-swagger?tabs=visual-studio) (Páginas de ajuda da API Web ASP.NET Core mediante a utilização do Swagger). Utiliza um gerador do Swagger para servir a [IU do Swagger](https://swagger.io/swagger-ui/) e o ponto final JSON do Swagger.

### <a name="run-the-application"></a>Executar a aplicação

Execute os seguintes comandos para instalar os pacotes necessários, executar migrações de base de dados e iniciar a aplicação.

```bash
cd dotnet-core-api
dotnet restore
dotnet run
```

Navegue para `http://localhost:5000/swagger` num browser para reproduzir com a IU do Swagger.

![API ASP.NET Core em execução localmente](./media/app-service-web-tutorial-rest-api/azure-app-service-local-swagger-ui.png)

Navegue para `http://localhost:5000/api/todo` e veja uma lista de itens JSON ToDo

Navegue para `http://localhost:5000` e experimente a aplicação de browser. Mais tarde, vai apontar a aplicação de browser para uma API remota no Serviço de Aplicações para testar a funcionalidade CORS. O código para a aplicação de browser está no diretório _wwwroot_ do repositório.

Para parar o ASP.NET Core em qualquer altura, prima `Ctrl+C` no terminal.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="deploy-app-to-azure"></a>Implementar a aplicação no Azure

Neste passo, vai implementar a aplicação .NET Core ligada à Base de Dados SQL no Serviço de Aplicações.

### <a name="configure-local-git-deployment"></a>Configurar a implementação do git local

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-no-h.md)]

### <a name="create-an-app-service-plan"></a>Crie um plano do Serviço de Aplicações

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan-no-h.md)]

### <a name="create-a-web-app"></a>Criar uma aplicação Web

[!INCLUDE [Create web app](../../includes/app-service-web-create-web-app-dotnetcore-win-no-h.md)] 

### <a name="push-to-azure-from-git"></a>Enviar para o Azure a partir do Git

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-git-push-to-azure-no-h.md)]

<pre>
Enumerating objects: 83, done.
Counting objects: 100% (83/83), done.
Delta compression using up to 8 threads
Compressing objects: 100% (78/78), done.
Writing objects: 100% (83/83), 22.15 KiB | 3.69 MiB/s, done.
Total 83 (delta 26), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id '509236e13d'.
remote: Generating deployment script.
remote: Project file path: .\TodoApi.csproj
remote: Generating deployment script for ASP.NET MSBuild16 App
remote: Generated deployment script files
remote: Running deployment command...
remote: Handling ASP.NET Core Web Application deployment with MSBuild16.
remote: .
remote: .
remote: .
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Triggering recycle (preview mode disabled).
remote: Deployment successful.
To https://&lt;app_name&gt;.scm.azurewebsites.net/&lt;app_name&gt;.git
 * [new branch]      master -> master
</pre>

### <a name="browse-to-the-azure-app"></a>Navegue pela app Azure

Navegue para `http://<app_name>.azurewebsites.net/swagger` num browser experimente a IU do Swagger.

![API ASP.NET Core em execução no Serviço de Aplicações do Azure](./media/app-service-web-tutorial-rest-api/azure-app-service-browse-app.png)

Navegue para `http://<app_name>.azurewebsites.net/swagger/v1/swagger.json` para ver o _swagger.json_ da API implementada.

Navegue para `http://<app_name>.azurewebsites.net/api/todo` para ver a API implementada em funcionamento.

## <a name="add-cors-functionality"></a>Adicionar a funcionalidade CORS

Em seguida, ative o suporte de CORS no Serviço de Aplicações para a sua API.

### <a name="test-cors-in-sample-app"></a>Testar CORS na aplicação de exemplo

No seu repositório local, abra _wwwroot/index.html_.

Na Linha 51, defina a variável `apiEndpoint` como o URL da API implementada (`http://<app_name>.azurewebsites.net`). Substitua _\<appname>_ o nome da sua aplicação no Serviço de Aplicações.

Na janela de terminal local, execute novamente a aplicação de exemplo.

```bash
dotnet run
```

Navegue para a aplicação de browser em `http://localhost:5000`. Abra a janela de ferramentas de desenvolvimento no seu navegador `Ctrl` + `Shift` + `i` (no Chrome para Windows) e inspecione o **separador Consola.** Deve agora ver a mensagem de `No 'Access-Control-Allow-Origin' header is present on the requested resource` erro.

![Erro CORS no cliente de browser](./media/app-service-web-tutorial-rest-api/azure-app-service-cors-error.png)

Devido à falta de correspondência de domínio entre a aplicação de browser (`http://localhost:5000`) e o recurso remoto (`http://<app_name>.azurewebsites.net`) e o facto de a sua API no Serviço de Aplicações não estar a enviar o cabeçalho `Access-Control-Allow-Origin`, o browser impediu o carregamento de conteúdos de vários domínios na sua aplicação de browser.

Na produção, a aplicação de browser teria um URL público em vez do URL de localhost, mas a forma de ativar o CORS para um URL de localhost é igual à dos URLs públicos.

### <a name="enable-cors"></a>Ativar o CORS 

Na Cloud Shell, ative o CORS para o URL do seu cliente utilizando o [`az webapp cors add`](/cli/azure/webapp/cors#az-webapp-cors-add) comando. Substitua o _&lt; nome de aplicação>_ espaço reservado.

```azurecli-interactive
az webapp cors add --resource-group myResourceGroup --name <app-name> --allowed-origins 'http://localhost:5000'
```

Pode definir mais de um URL do cliente em `properties.cors.allowedOrigins` (`"['URL1','URL2',...]"`). Também pode ativar todos os URLs do cliente com `"['*']"`.

> [!NOTE]
> Se a sua aplicação necessitar de credenciais como cookies ou fichas de autenticação a serem enviadas, o navegador poderá necessitar do `ACCESS-CONTROL-ALLOW-CREDENTIALS` cabeçalho na resposta. Para o ativar no Serviço de Aplicações, `properties.cors.supportCredentials` desafie `true` no seu config CORS. Isto não pode ser ativado quando `allowedOrigins` inclui `'*'` .

### <a name="test-cors-again"></a>Testar o CORS novamente

Ative a aplicação de browser em `http://localhost:5000`. A mensagem de erro na janela **Consola** deixa de estar visível e pode ver os dados da API implementada e interagir com os mesmos. A API remota suporta agora CORS na sua aplicação de browser em execução no local. 

![CORS bem-sucedido no cliente de browser](./media/app-service-web-tutorial-rest-api/azure-app-service-cors-success.png)

Parabéns! Está a executar uma API no Serviço de Aplicações do Azure com suporte para CORS.

## <a name="app-service-cors-vs-your-cors"></a>CORS do Serviço de Aplicações vs. o seu CORS

Pode utilizar os seus próprios utilitários CORS em vez do CORS do Serviço de Aplicações para uma maior flexibilidade. Por exemplo, poderá querer especificar diferentes origens permitidas para rotas ou métodos diferentes. Uma vez que o CORS do Serviço de Aplicações lhe permite especificar um conjunto de origens aceites para todas as rotas e métodos da API, poderá pretender utilizar o seu próprio código de CORS. Veja como é que o ASP.NET Core o faz em [Enabling Cross-Origin Requests (CORS)](/aspnet/core/security/cors) (Ativar Pedidos de Várias Origens [CORS]).

> [!NOTE]
> Não tente utilizar o CORS do Serviço de Aplicações o seu próprio código CORS em conjunto. Quando utilizados em conjunto, o CORS do Serviço de Aplicações tem precedência e o seu código não tem qualquer efeito.
>
>

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

<a name="next"></a>
## <a name="next-steps"></a>Passos seguintes

O que aprendeu:

> [!div class="checklist"]
> * Criar recursos do Serviço de Aplicações com a CLI do Azure
> * Implementar uma API RESTful no Azure com o Git
> * Ativar o suporte para CORS no Serviço de Aplicações

Avançar para o próximo tutorial para saber como autenticar e autorizar utilizadores.

> [!div class="nextstepaction"]
> [Tutorial: Autenticar e autorizar utilizadores ponto a ponto](tutorial-auth-aad.md)
