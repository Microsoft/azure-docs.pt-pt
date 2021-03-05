---
title: 'Quickstart: Criar uma aplicação C# ASP.NET Core'
description: Aprenda a executar aplicações web no Azure App Service implementando a sua primeira aplicação core ASP.NET.
ms.assetid: b1e6bd58-48d1-4007-9d6c-53fd6db061e3
ms.topic: quickstart
ms.date: 11/23/2020
ms.custom: devx-track-csharp, mvc, devcenter, vs-azure, seodec18, contperf-fy21q1
zone_pivot_groups: app-service-platform-windows-linux
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 2a789b4ca1261c79e8e6eb93a4ed44e7e8e9272e
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102214240"
---
# <a name="quickstart-create-an-aspnet-core-web-app-in-azure"></a>Quickstart: Criar uma aplicação web core ASP.NET em Azure

::: zone pivot="platform-windows"  

Neste quickstart, você vai aprender a criar e implementar a sua primeira aplicação web ASP.NET Core para <abbr title="Um serviço baseado em HTTP para hospedar aplicações web, APIs REST e aplicações móveis de back-end.">Serviço de Aplicações do Azure</abbr>. O Serviço de Aplicações suporta aplicações .NET 5.0.

Quando terminar, terá um Azure. <abbr title="Um recipiente lógico para recursos Azure relacionados que você pode gerir como uma unidade.">grupo de recursos</abbr>, consistindo de um <abbr title="O plano que especifica a localização, tamanho e funcionalidades da fazenda de servidores web que hospeda a sua aplicação.">Plano do Serviço de Aplicações</abbr> e um <abbr title="A representação da sua aplicação web, que contém o seu código de aplicação, nomes de anfitriões DNS, certificados e recursos relacionados.">Aplicação do Serviço de Aplicações</abbr> com uma amostra implantada ASP.NET aplicação Core.

<hr/> 

## <a name="1-prepare-your-environment"></a>1. Prepare o seu ambiente

- **Obtenha uma conta Azure** com um ativo <abbr title="A estrutura organizacional básica em que gere recursos em Azure, tipicamente associados a um indivíduo ou departamento dentro de uma organização.">subscrição</abbr>. [Crie uma conta gratuita.](https://azure.microsoft.com/free/dotnet/)
- **Instale <a href="https://www.visualstudio.com/downloads/" target="_blank">o Visual Studio 2019</a>** com a ASP.NET e a carga de trabalho **de desenvolvimento web.**

<details>
<summary>Já tem Visual Studio 2019?</summary>
Se já instalou o Visual Studio 2019:

<ul>
<li><strong>Instale as últimas atualizações</strong> no Estúdio Visual selecionando <strong>o Help</strong> Check &gt; <strong>for Updates</strong>. As últimas atualizações contêm o .NET 5.0 SDK.</li>
<li><strong>Adicione a carga de trabalho</strong> selecionando <strong>Ferramentas</strong> &gt; <strong>Obter Ferramentas e Funcionalidades</strong>.</li>
</ul>
</details>

<hr/> 

## <a name="2-create-an-aspnet-core-web-app"></a>2. Criar uma aplicação web core ASP.NET

1. Abra o Estúdio Visual e selecione **Criar um novo projeto.**

1. In **Create a new project**, selecione ASP.NET Core Web **Application** e confirme que **C#** está listado nos idiomas para essa escolha e, em seguida, selecione **Next**.

1. Em **Configurar o seu novo projeto,** nomeie o seu projeto de aplicação web *myFirstAzureWebApp* e selecione **Create**.

   ![Configure o seu projeto de aplicação web](./media/quickstart-dotnetcore/configure-web-app-project.png)

1. Para uma aplicação .NET 5.0, selecione **ASP.NET Core 5.0** no dropdown. Caso contrário, utilize o valor predefinido.

1. Você pode implementar qualquer tipo de ASP.NET web app Core para Azure, mas para este arranque rápido, escolha o **modelo de ASP.NET Core Web App.** Certifique-se de que a **autenticação** está definida para **Não Autenticação** e que não está selecionada nenhuma outra opção. Em seguida, selecione **Criar**.

   ![Criar uma nova aplicação web core ASP.NET](./media/quickstart-dotnetcore/create-aspnet-core-web-app-5.png) 
   
1. A partir do menu Visual Studio, selecione **Debug**  >  **Start Without Debugging** para executar a sua aplicação web localmente.

   ![Aplicativo web correndo localmente](./media/quickstart-dotnetcore/web-app-running-locally.png)

<hr/> 

## <a name="3-publish-your-web-app"></a>3. Publique a sua aplicação web

1. No **Solution Explorer,** clique com o botão direito no **projeto myFirstAzureWebApp** e selecione **Publicar**. 

1. Em **Publicar**, selecione **Azure** e clique em **Seguinte**.

1. As suas opções dependem se já está inscrito no Azure e se tem uma conta visual studio ligada a uma conta Azure. Selecione **Adicione uma conta** ou **inscreva-se** para iniciar súmis na sua subscrição Azure. Se já fez a sua assinatura, selecione a conta que pretende.

   ![Iniciar sessão no Azure](./media/quickstart-dotnetcore/sign-in-azure-vs2019.png)

1. À direita das instâncias do Serviço de **Aplicações,** clique **+** em .

   ![Nova app de Serviço de Aplicações](./media/quickstart-dotnetcore/publish-new-app-service.png)

1. Para **Subscrição**, aceite a subscrição listada ou selecione uma nova da lista de drop-down.

1. Para **o grupo de recursos**, selecione **New**. No **nome do grupo de novos recursos,** insira o *myResourceGroup* e selecione **OK**. 

1. Para **o Plano de Hospedagem**, selecione **New**. 

1. No **Plano de Hospedagem: Criar novo** diálogo, introduzir os valores especificados no quadro seguinte:

   | Definição  | Valor sugerido |
   | -------- | --------------- |
   | **Plano de Alojamento**  | *myFirstAzureWebAppPlan* |
   | **Localização**      | *Europa Ocidental* |
   | **Tamanho**          | *Gratuito* |
   
   ![Criar novo Plano de Hospedagem](./media/quickstart-dotnetcore/create-new-hosting-plan-vs2019.png)

1. Em **Nome,** insira um nome de aplicação único.

    <details>
        <summary>Que personagens posso usar?</summary>
        Os caracteres válidos são a-z, A-Z, 0-9, e -. Pode aceitar o nome único gerado automaticamente. O URL da aplicação web é http:// <code>&lt;app-name&gt;.azurewebsites.net</code> , onde está o nome da sua <code>&lt;app-name&gt;</code> aplicação.
    </details>

1. Selecione **Criar** para criar os recursos Azure. 

   ![Criar recursos de aplicativos](./media/quickstart-dotnetcore/web-app-name-vs2019.png)

1. Espere que o feiticeiro termine de criar recursos Azure. **Selecione Acabamento** para fechar o assistente.

1. Na página **Publicar,** clique em **Publicar** para implementar o seu projeto. 

    <details>
        <summary>O que está o Estúdio Visual a fazer?</summary>
        Visual Studio constrói, embala e publica a app para o Azure, e depois lança a aplicação no navegador padrão.
    </details>

   ![Publicado ASP.NET web app em execução em Azure](./media/quickstart-dotnetcore/web-app-running-live.png)

<hr/> 

## <a name="4-update-the-app-and-redeploy"></a>4. Atualizar a app e reimplantar

1. No **Solution Explorer,** no âmbito do seu projeto, abra **as páginas**  >  **Index.cshtml**.

1. Substitua toda a `<div>` etiqueta pelo seguinte código:

   ```html
   <div class="jumbotron">
       <h1>ASP.NET in Azure!</h1>
       <p class="lead">This is a simple app that we've built that demonstrates how to deploy a .NET app to Azure App Service.</p>
   </div>
   ```

1. Para voltar a implementar no Azure, clique com o botão direito do rato no projeto **myFirstAzureWebApp**, no **Explorador de Soluções** e selecione **Publicar**.

1. Na página do resumo da **Publicação,** **selecione Publicar**.

   <!-- ![Publish update to web app](./media/quickstart-dotnetcore/publish-update-to-web-app-vs2019.png) -->

    Quando a publicação estiver concluída, o Visual Studio inicia um browser para o URL da aplicação Web.

    ![Atualizado ASP.NET aplicação web em execução em Azure](./media/quickstart-dotnetcore/updated-web-app-running-live.png)

<hr/> 

## <a name="5-manage-the-azure-app"></a>5. Gerir a app Azure

1. Vá ao [portal Azure,](https://portal.azure.com)procure e selecione **Serviços de Aplicações.**

    ![Selecione Serviços de Aplicações](./media/quickstart-dotnetcore/app-services.png)
    
1. Na página **serviços de aplicações,** selecione o nome da sua aplicação web.

    :::image type="content" source="./media/quickstart-dotnetcore/select-app-service.png" alt-text="Screenshot da página serviços de aplicações com uma aplicação web de exemplo selecionada.":::

1. A página **'Vista Geral'** para a sua aplicação web contém opções para gestão básica como navegar, parar, iniciar, reiniciar e excluir. O menu esquerdo fornece mais páginas para configurar a sua aplicação.

    ![Serviço de Aplicações no portal Azure](./media/quickstart-dotnetcore/web-app-overview-page.png)
    
<hr/> 

## <a name="6-clean-up-resources"></a>6. Limpar recursos

1. A partir do menu do portal Azure ou página **Inicial,** selecione **Grupos de Recursos**. Em seguida, na página **de grupos de recursos,** selecione **myResourceGroup**.

1. Na página **myResourceGroup,** certifique-se de que os recursos listados são os que pretende eliminar.

1. **Selecione Eliminar grupo de recursos,** digite o **myResourceGroup** na caixa de texto para confirmar e, em seguida, selecione **Delete**.

<hr/> 

## <a name="next-steps"></a>Passos seguintes

Avance para o próximo artigo para aprender a criar uma aplicação .NET Core e conectá-la a uma Base de Dados SQL:

- [ASP.NET Core com a Base de Dados SQL](tutorial-dotnetcore-sqldb-app.md)
- [Configurar ASP.NET aplicação Core](configure-language-dotnetcore.md)

::: zone-end  

::: zone pivot="platform-linux"
Este quickstart mostra como criar uma aplicação [.NET Core](/aspnet/core/) em <abbr title="O Serviço de Aplicações no Linux fornece um serviço de hospedagem web altamente escalável e auto-remendado utilizando o sistema operativo Linux.">Serviço de Aplicações no Linux</abbr>. Cria a aplicação utilizando o [Azure CLI](/cli/azure/get-started-with-azure-cli)e utiliza o Git para implementar o código .NET Core para a aplicação.

<hr/> 

## <a name="1-prepare-your-environment"></a>1. Prepare o seu ambiente

- **Obtenha uma conta Azure** com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/dotnet/)
- **Instale** o mais recente <a href="https://dotnet.microsoft.com/download/dotnet-core/3.1" target="_blank">.NET Core 3.1 SDK</a> ou <a href="https://dotnet.microsoft.com/download/dotnet/5.0" target="_blank">.NET 5.0 SDK</a>.
- **<a href="/cli/azure/install-azure-cli" target="_blank">Instale o mais recente Azure CLI</a>**.

[Tendo problemas? Deixe-nos saber.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

<hr/> 

## <a name="2-create-the-app-locally"></a>2. Criar a app localmente

1. Corra `mkdir hellodotnetcore` para criar o diretório.

    ```bash
    mkdir hellodotnetcore
    ```

1. Corre `cd hellodotnetcore` para mudar o diretório. 

    ```bash
    cd hellodotnetcore
    ```

1. Corra `dotnet new web` para criar uma nova aplicação .NET Core.

    ```bash
    dotnet new web
    ```

<hr/> 

## <a name="3-run-the-app-locally"></a>3. Executar a aplicação localmente

1. Corra `dotnet run` para ver como fica quando o colocar em Azure.

    ```bash
    dotnet run
    ```
    
1. **Abra um navegador web** e navegue para a aplicação em `http://localhost:5000` .

![Testar com o browser](media/quickstart-dotnetcore/dotnet-browse-local.png)

[Tendo problemas? Deixe-nos saber.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

<hr/> 

## <a name="4-sign-into-azure"></a>4. Inscreva-se em Azure

Corre `az login` para assinar no Azure.

```azurecli
az login
```

[Tendo problemas? Deixe-nos saber.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

<hr/> 

## <a name="5-deploy-the-app"></a>5. Implementar a aplicação

1. **Correr** `az webapp up` na sua pasta local. **Substitua** <> de nome de aplicações por um nome globalmente único.

    ```azurecli
    az webapp up --sku F1 --name <app-name> --os-type linux
    ```
    
    <details>
    <summary>Resolução de problemas</summary>
    <ul>
    <li>Se o <code>az</code> comando não for reconhecido, certifique-se de que tem o CLI Azure instalado como descrito no <a href="#1-prepare-your-environment">Preparar o seu ambiente</a>.</li>
    <li>Substitua <code>&lt;app-name&gt;</code> por um nome único em todo o Azure <em> (caracteres válidos <code>a-z</code> <code>0-9</code> são, e <code>-</code> </em> . Um bom padrão é usar uma combinação do nome da sua empresa e um identificador de aplicativos.</li>
    <li>O <code>--sku F1</code> argumento cria a aplicação web no nível de preços gratuitos. Omita este argumento para usar um nível premium mais rápido, que incorre num custo de hora a hora.</li>
    <li>Pode opcionalmente incluir o argumento <code>--location &lt;location-name&gt;</code> onde <code>&lt;location-name&gt;</code> está uma região de Azure disponível. Pode recuperar uma lista de regiões admissíveis para a sua conta Azure, executando o <a href="/cli/azure/appservice#az-appservice-list-locations"> <code>az account list-locations</code> </a> comando.</li>
    </ul>
    </details>
    
1. Espere que o comando termine. Pode demorar alguns minutos e termina com "Pode lançar a app em http:// &lt; nome de aplicações &gt; (azurewebsites.net".

    <details>
    <summary>O que está a <code>az webapp up</code> fazer?</summary>
    <p>O comando <code>az webapp up</code> executa as seguintes ações:</p>
    <ul>
    <li>Cria um grupo de recursos predefinido.</li>
    <li>Crie um plano de Serviço de Aplicações predefinido.</li>
    <li><a href="/cli/azure/webapp#az-webapp-create">Crie uma aplicação de Serviço de Aplicações</a> com o nome especificado.</li>
    <li><a href="/azure/app-service/deploy-zip">Zip implementa</a> ficheiros do diretório de trabalho atual para a aplicação.</li>
    <li>Durante a execução, fornece mensagens sobre criação de recursos, registo e implementação zip.</li>
    </ul>
    </details>
    
# <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

![Exemplo de saída do comando az webapp up](./media/quickstart-dotnetcore/az-webapp-up-output-3.1.png)

# <a name="net-50"></a>[.NET 5.0](#tab/net50)

![Exemplo de saída do comando az webapp up](./media/quickstart-dotnetcore/az-webapp-up-output-5.0.png)

---

[Tendo problemas? Deixe-nos saber.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

<hr/> 

## <a name="6-browse-to-the-app"></a>6. Navegue para a aplicação

**Navegue pela aplicação implementada** utilizando o seu navegador web.

```bash
http://<app_name>.azurewebsites.net
```

![Aplicação Web de exemplo em execução no Azure](media/quickstart-dotnetcore/dotnet-browse-azure.png)

[Tendo problemas? Deixe-nos saber.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

<hr/> 

## <a name="7-update-and-redeploy-the-code"></a>7. Atualizar e recolocar o código

1. **Abra o ficheiro _Startup.cs_** no diretório local. 

1. **Faça uma pequena alteração** ao texto na chamada de método `context.Response.WriteAsync` .

    ```csharp
    await context.Response.WriteAsync("Hello Azure!");
    ```
    
1. **Guarde as suas alterações.**

1. **Correr** `az webapp up` para a reimplantação:

    ```azurecli
    az webapp up --os-type linux
    ```
    
    <details>
    <summary>O que está <code>az webapp up</code> a fazer desta vez?</summary>
    A primeira vez que executou o comando, guardou o nome da aplicação, o grupo de recursos e o plano de Serviço de Aplicações no ficheiro <i>.azure/config</i> da raiz do projeto. Quando o executa novamente a partir da raiz do projeto, utiliza os valores guardados em <i>.azure/config,</i>deteta que os recursos do Serviço de Aplicações já existem, e executa novamente o Zip.
    </details>
    
1. Uma vez concluída a implementação, **adisque** a atualização na janela do navegador que abriu anteriormente.

    ![Aplicação de exemplo atualizada em execução no Azure](media/quickstart-dotnetcore/dotnet-browse-azure-updated.png)
    
[Tendo problemas? Deixe-nos saber.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

<hr/> 

## <a name="8-manage-your-new-azure-app"></a>8. Gerencie a sua nova app Azure

1. Aceda ao <a href="https://portal.azure.com" target="_blank">Portal do Azure</a>.

1. A partir do menu esquerdo, clique em **Serviços de Aplicação** e, em seguida, clique no nome da sua aplicação Azure.

    :::image type="content" source="./media/quickstart-dotnetcore/portal-app-service-list-up.png" alt-text="Screenshot da página serviços de aplicações mostrando um exemplo Azure app selecionado.":::

1. A página 'Vista Geral' é onde pode executar tarefas básicas de gestão como navegar, parar, iniciar, reiniciar e excluir. O menu à esquerda fornece diferentes páginas para configurar a sua aplicação. 

    ![Página Serviço de Aplicações no portal do Azure](media/quickstart-dotnetcore/portal-app-overview-up.png)
    
<hr/> 

## <a name="9-clean-up-resources"></a>9. Limpar recursos

**Correr** `az group delete --name myResourceGroup` para eliminar o grupo de recursos.

```azurecli-interactive
az group delete --name myResourceGroup
```

[Tendo problemas? Deixe-nos saber.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

<hr/> 

## <a name="next-steps"></a>Passos seguintes

- [Tutorial: ASP.NET Core app com base de dados SQL](tutorial-dotnetcore-sqldb-app.md)
- [Configurar ASP.NET aplicação Core](configure-language-dotnetcore.md)

::: zone-end
