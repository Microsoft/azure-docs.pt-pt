---
title: 'Tutorial: ASP.NET Core com Base de Dados SQL'
description: Saiba como colocar uma aplicação .NET Core a funcionar no Serviço de Aplicações do Azure, com ligação a uma Base de Dados SQL.
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 05/27/2020
ms.custom: mvc, cli-validate, seodec18
ms.openlocfilehash: c020e49b12784e628661bff61fe344df0ac6049a
ms.sourcegitcommit: 34eb5e4d303800d3b31b00b361523ccd9eeff0ab
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/17/2020
ms.locfileid: "84905991"
---
# <a name="tutorial-build-an-aspnet-core-and-sql-database-app-in-azure-app-service"></a>Tutorial: Construir uma aplicação de base de dados core e SQL de ASP.NET no Azure App Service

> [!NOTE]
> Este artigo implementa uma aplicação no Serviço de Aplicações no Windows. Para implementar no Serviço de Aplicações em _Linux,_ consulte [construir uma aplicação de base de dados .NET Core e SQL em Azure App Service no Linux](./containers/tutorial-dotnetcore-sqldb-app.md).
>

O [Serviço de Aplicações](overview.md) oferece um serviço de alojamento na Web altamente dimensionável e com correção automática no Azure. Este tutorial mostra como criar uma aplicação .NET Core e conectá-la a uma Base de Dados SQL. Quando terminar, terá uma aplicação de MVC .NET Core em execução no Serviço de Aplicações.

![aplicação em execução no Serviço de Aplicações](./media/app-service-web-tutorial-dotnetcore-sqldb/azure-app-in-browser.png)

Neste tutorial, vai aprender a:

> [!div class="checklist"]
> * Criar uma Base de Dados SQL no Azure
> * Ligar uma aplicação .NET Core à Base de Dados SQL
> * Implementar a aplicação no Azure
> * Atualizar o modelo de dados e voltar a implementar a aplicação
> * Transmitir os registos de diagnóstico em fluxo a partir do Azure
> * Gerir a aplicação no portal do Azure

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

* <a href="https://git-scm.com/" target="_blank">Instalar o Git</a>
* <a href="https://dotnet.microsoft.com/download/dotnet-core/3.1" target="_blank">Instale o mais recente .NET Core 3.1 SDK</a>

## <a name="create-local-net-core-app"></a>Criar uma aplicação .NET Core local

Neste passo, vai configurar o projeto .NET Core local.

### <a name="clone-the-sample-application"></a>Clonar a aplicação de exemplo

Na janela de terminal, `cd` num diretório de trabalho.

Execute os seguintes comandos para clonar o repositório de exemplo e alterar para a respetiva raiz.

```bash
git clone https://github.com/azure-samples/dotnetcore-sqldb-tutorial
cd dotnetcore-sqldb-tutorial
```

O projeto de exemplo contém uma aplicação CRUD (create-read-update-delete) básica com o [Entity Framework Core](https://docs.microsoft.com/ef/core/).

### <a name="run-the-application"></a>Executar a aplicação

Execute os seguintes comandos para instalar os pacotes necessários, executar migrações de base de dados e iniciar a aplicação.

```bash
dotnet tool install -g dotnet-ef
dotnet ef database update
dotnet run
```

Navegue para `http://localhost:5000` num browser. Selecione a ligação **Criar Novo** e criar alguns itens _a fazer_.

![liga com êxito à Base de Dados SQL](./media/app-service-web-tutorial-dotnetcore-sqldb/local-app-in-browser.png)

Para parar o .NET Core em qualquer altura, prima `Ctrl+C` no terminal.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-production-sql-database"></a>Criar Base de Dados SQL de produção

Neste passo, vai criar uma Base de Dados SQL no Azure. Quando a aplicação for implementada no Azure, utiliza esta base de dados na cloud.

Para a Base de Dados SQL, este tutorial utiliza a [Base de Dados SQL do Azure](/azure/sql-database/).

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-no-h.md)]

### <a name="create-a-sql-database-logical-server"></a>Criar um servidor lógico da Base de Dados SQL

Na Cloud Shell, crie um servidor lógico SQL Database com o [`az sql server create`](/cli/azure/sql/server?view=azure-cli-latest#az-sql-server-create) comando.

Substitua o *\<server-name>* espaço reservado por um nome *exclusivo* da Base de Dados SQL. Este nome é usado como parte do ponto final globalmente único SQL Database, `<server-name>.database.windows.net` . Os caracteres válidos `a` - `z` `0` - `9` são, . `-` . . Além disso, substitua *\<db-username>* e por um nome de utilizador e senha à sua *\<db-password>* escolha. 


```azurecli-interactive
az sql server create --name <server-name> --resource-group myResourceGroup --location "West Europe" --admin-user <db-username> --admin-password <db-password>
```

Após criar o servidor lógico da Base de Dados SQL, a CLI do Azure mostra informações semelhantes ao exemplo seguinte:

<pre>
{
  "administratorLogin": "&lt;db-username&gt;",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "&lt;server-name&gt;.database.windows.net",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/&lt;server-name&gt;",
  "identity": null,
  "kind": "v12.0",
  "location": "westeurope",
  "name": "&lt;server-name&gt;",
  "resourceGroup": "myResourceGroup",
  "state": "Ready",
  "tags": null,
  "type": "Microsoft.Sql/servers",
  "version": "12.0"
}
</pre>

### <a name="configure-a-server-firewall-rule"></a>Configurar uma regra de firewall do servidor

Criar uma [regra de firewall ao nível do servidor da Base de Dados SQL do Azure](../sql-database/sql-database-firewall-configure.md) com o comando [`az sql server firewall create`](/cli/azure/sql/server/firewall-rule?view=azure-cli-latest#az-sql-server-firewall-rule-create). Quando os IPs inicial e final estão definidos como 0.0.0.0, a firewall apenas é aberta para outros recursos do Azure. 

```azurecli-interactive
az sql server firewall-rule create --resource-group myResourceGroup --server <server-name> --name AllowAzureIps --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!TIP] 
> Pode ser ainda mais restritivo na sua regra de firewall ao [utilizar apenas os endereços IP de saída que a aplicação utiliza](overview-inbound-outbound-ips.md#find-outbound-ips).
>

Na Cloud Shell, volte a executar o comando para permitir o acesso a partir do seu computador local, substituindo *\<your-ip-address>* o seu endereço IP [IPv4 local](https://www.whatsmyip.org/).

```azurecli-interactive
az sql server firewall-rule create --name AllowLocalClient --server <mysql_server_name> --resource-group myResourceGroup --start-ip-address=<your-ip-address> --end-ip-address=<your-ip-address>
```

### <a name="create-a-database"></a>Criar uma base de dados

Crie uma base de dados com um [nível de desempenho S0](../sql-database/sql-database-service-tiers-dtu.md) no servidor com o comando [`az sql db create`](/cli/azure/sql/db?view=azure-cli-latest#az-sql-db-create).

```azurecli-interactive
az sql db create --resource-group myResourceGroup --server <server-name> --name coreDB --service-objective S0
```

### <a name="create-connection-string"></a>Criar uma cadeia de ligação

Obtenha a cadeia de ligação usando o [`az sql db show-connection-string`](/cli/azure/sql/db?view=azure-cli-latest#az-sql-db-show-connection-string) comando.

```azurecli-interactive
az sql db show-connection-string --client ado.net --server cephalin-core --name coreDB
```

Na saída de comando, *\<username>* substitua- e com as *\<password>* credenciais de administrador de base de dados que usou anteriormente.

Esta é a cadeia de ligação para a sua aplicação .NET Core. Copie-a para utilizar mais tarde.

### <a name="configure-app-to-connect-to-production-database"></a>Configure app para ligar à base de dados de produção

No seu repositório local, abra Startup.cs e localize o seguinte código:

```csharp
services.AddDbContext<MyDatabaseContext>(options =>
        options.UseSqlite("Data Source=localdatabase.db"));
```

Substitua-o pelo seguinte código.

```csharp
services.AddDbContext<MyDatabaseContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("MyDbConnection")));
```

> [!IMPORTANT]
> Para aplicações de produção que precisam de escalar, siga as melhores práticas na [aplicação de migrações na produção.](/aspnet/core/data/ef-rp/migrations#applying-migrations-in-production)
> 

### <a name="run-database-migrations-to-the-production-database"></a>Executar migrações de base de dados para a base de dados de produção

A sua aplicação está atualmente ligada a uma base de dados local do Sqlite. Agora que configuraste uma Base de Dados Azure SQL, recria a migração inicial para a segmentar. 

A partir da raiz do repositório, executar os seguintes comandos. *\<connection-string>* Substitua-a pela cadeia de ligação que criou anteriormente.

```
# Delete old migrations
rm Migrations -r
# Recreate migrations
dotnet ef migrations add InitialCreate

# Set connection string to production database
# PowerShell
$env:ConnectionStrings:MyDbConnection="<connection-string>"
# CMD (no quotes)
set ConnectionStrings:MyDbConnection=<connection-string>
# Bash (no quotes)
export ConnectionStrings__MyDbConnection=<connection-string>

# Run migrations
dotnet ef database update
```

### <a name="run-app-with-new-configuration"></a>Executar app com nova configuração

Agora que as migrações na base de dados são executadas na base de dados de produção, teste a sua aplicação executando:

```
dotnet run
```

Navegue para `http://localhost:5000` num browser. Selecione a ligação **Criar Novo** e criar alguns itens _a fazer_. A sua aplicação está agora a ler e a escrever dados para a base de dados de produção.

Cometa as suas mudanças locais e, em seguida, comprometa-a no seu repositório git. 

```bash
git add .
git commit -m "connect to SQLDB in Azure"
```

Está pronto para implementar o seu código.

## <a name="deploy-app-to-azure"></a>Implementar a aplicação no Azure

Neste passo, vai implementar a aplicação .NET Core ligada à Base de Dados SQL no Serviço de Aplicações.

### <a name="configure-local-git-deployment"></a>Configurar a implementação do git local

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>Crie um plano do Serviço de Aplicações

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan-no-h.md)]

### <a name="create-a-web-app"></a>Criar uma aplicação Web

[!INCLUDE [Create web app](../../includes/app-service-web-create-web-app-dotnetcore-win-no-h.md)] 

### <a name="configure-connection-string"></a>Cadeia de ligação configurada

Para definir as cordas de ligação para a sua aplicação Azure, utilize o [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) comando na Cloud Shell. No comando seguinte, *\<app-name>* substitua, bem como o parâmetro com a cadeia de *\<connection-string>* ligação que criou anteriormente.

```azurecli-interactive
az webapp config connection-string set --resource-group myResourceGroup --name <app-name> --settings MyDbConnection="<connection-string>" --connection-string-type SQLAzure
```

Em ASP.NET Core, pode utilizar esta cadeia de ligação denominada `MyDbConnection` () utilizando o padrão padrão, como qualquer fio de ligação especificado em *appsettings.jsem*. Neste caso, `MyDbConnection` também é definido no seuappsettings.js*em*. Ao executar o Serviço de Aplicações, a cadeia de ligação definida no Serviço de Aplicações tem precedência sobre a cadeia de ligação definida no seu *appsettings.jsligado*. O código utiliza o *appsettings.jssobre* valor durante o desenvolvimento local, e o mesmo código utiliza o valor do Serviço de Aplicações quando implementado.

Para ver como a cadeia de ligação é referenciada no seu código, consulte [a aplicação Configure para ligar à base de dados de produção.](#configure-app-to-connect-to-production-database)

### <a name="push-to-azure-from-git"></a>Enviar para o Azure a partir do Git

[!INCLUDE [push-to-azure-no-h](../../includes/app-service-web-git-push-to-azure-no-h.md)]

<pre>
Enumerating objects: 268, done.
Counting objects: 100% (268/268), done.
Compressing objects: 100% (171/171), done.
Writing objects: 100% (268/268), 1.18 MiB | 1.55 MiB/s, done.
Total 268 (delta 95), reused 251 (delta 87), pack-reused 0
remote: Resolving deltas: 100% (95/95), done.
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id '64821c3558'.
remote: Generating deployment script.
remote: Project file path: .\DotNetCoreSqlDb.csproj
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
remote: App container will begin restart within 10 seconds.
To https://&lt;app-name&gt;.scm.azurewebsites.net/&lt;app-name&gt;.git
 * [new branch]      master -> master
</pre>

### <a name="browse-to-the-azure-app"></a>Navegue pela app Azure

Navegue pela aplicação implementada utilizando o seu navegador web.

```bash
http://<app-name>.azurewebsites.net
```

Adicione alguns itens a fazer.

![aplicação em execução no Serviço de Aplicações](./media/app-service-web-tutorial-dotnetcore-sqldb/azure-app-in-browser.png)

**Parabéns!** Está a executar uma aplicação .NET Core condicionada por dados no Serviço de Aplicações.

## <a name="update-locally-and-redeploy"></a>Atualizar localmente e reimplementar

Neste passo, vai fazer uma alteração ao esquema da base de dados e publicá-lo no Azure.

### <a name="update-your-data-model"></a>Atualizar o modelo de dados

Abra _Models\Todo.cs_ no editor de código. Adicione a seguinte propriedade à classe `ToDo`:

```csharp
public bool Done { get; set; }
```

### <a name="rerun-database-migrations"></a>Rerun migrações de base de dados

Executar alguns comandos para fazer atualizações para a base de dados de produção.

```bash
dotnet ef migrations add AddProperty
dotnet ef database update
```

### <a name="use-the-new-property"></a>Utilizar a nova propriedade

Faça algumas alterações no seu código para utilizar a propriedade `Done`. Para simplificar, neste tutorial, apenas vai alterar as vistas `Index` e `Create` para ver a propriedade em ação.

Abra _Controllers\TodosController.cs_.

Localize o método `Create([Bind("ID,Description,CreatedDate")] Todo todo)` e adicione `Done` à lista de propriedades no atributo `Bind`. Quando terminar, a assinatura do método `Create()` é semelhante ao seguinte código:

```csharp
public async Task<IActionResult> Create([Bind("ID,Description,CreatedDate,Done")] Todo todo)
```

Abra _Views\Todos\Create.cshtml_.

No código Razor, deverá ver um elemento `<div class="form-group">` para `Description` e outro elemento `<div class="form-group">` para `CreatedDate`. Imediatamente a seguir a estes dois elementos, adicione outro elemento `<div class="form-group">` para `Done`:

```csharp
<div class="form-group">
    <label asp-for="Done" class="col-md-2 control-label"></label>
    <div class="col-md-10">
        <input asp-for="Done" class="form-control" />
        <span asp-validation-for="Done" class="text-danger"></span>
    </div>
</div>
```

Abra _Views\Todos\Index.cshtml_.

Procure o elemento `<th></th>` vazio. Imediatamente acima deste elemento, adicione o seguinte código Razor:

```csharp
<th>
    @Html.DisplayNameFor(model => model.Done)
</th>
```

Localize o elemento `<td>` que contém os programas auxiliares da etiqueta `asp-action`. Imediatamente acima deste elemento, adicione o seguinte código Razor:

```csharp
<td>
    @Html.DisplayFor(modelItem => item.Done)
</td>
```

É tudo o que precisa para ver as alterações nas vistas `Index` e `Create`.

### <a name="test-your-changes-locally"></a>Testar as alterações localmente

Execute a aplicação localmente.

```bash
dotnet run
```

No seu browser, navegue até `http://localhost:5000/`. Agora, pode adicionar um item a fazer e marcar **Concluído**. Em seguida, deve ser apresentado na sua home page como um item concluído. Lembre-se de que a vista `Edit` não apresenta o campo `Done`, porque a vista `Edit` não foi alterada.

### <a name="publish-changes-to-azure"></a>Publicar alterações no Azure

```bash
git add .
git commit -m "added done field"
git push azure master
```

Uma vez `git push` concluída, navegue na sua aplicação De Serviço de Aplicações e tente adicionar um item a fazer e verifique **'Feito'.**

![App Azure após Código Primeira Migração](./media/app-service-web-tutorial-dotnetcore-sqldb/this-one-is-done.png)

Todos os itens a fazer existentes continuam a ser apresentados. Quando republicar a sua aplicação Core ASP.NET, os dados existentes na base de dados SQL não se perdem. Além disso, as Migrações do Entity Framework Core apenas alteram o esquema de dados e mantêm os dados existentes intactos.

## <a name="stream-diagnostic-logs"></a>Transmitir registos de diagnóstico em fluxo

Enquanto a aplicação core ASP.NET é executada no Azure App Service, pode obter os registos de consola canalizados para a Cloud Shell. Dessa forma, pode obter as mesmas mensagens de diagnóstico para ajudar a depurar erros de aplicações.

O projeto da amostra já segue a orientação em [ASP.NET Core Logging in Azure](https://docs.microsoft.com/aspnet/core/fundamentals/logging#azure-app-service-provider) com duas alterações de configuração:

- Inclui uma referência a `Microsoft.Extensions.Logging.AzureAppServices` *DotNetCoreSqlDb.csproj*.
- Chamadas `loggerFactory.AddAzureWebAppDiagnostics()` em *Program.cs.*

Para definir o [nível](https://docs.microsoft.com/aspnet/core/fundamentals/logging#log-level) de registo de ASP.NET Core no Serviço de Aplicações para `Information` a partir do nível predefinido, `Error` utilize o comando na Cloud [`az webapp log config`](/cli/azure/webapp/log?view=azure-cli-latest#az-webapp-log-config) Shell.

```azurecli-interactive
az webapp log config --name <app-name> --resource-group myResourceGroup --application-logging true --level information
```

> [!NOTE]
> O nível de registo do projeto já está definido `Information` para *appsettings.jsem*.
> 

Para iniciar o streaming de registos, utilize o [`az webapp log tail`](/cli/azure/webapp/log?view=azure-cli-latest#az-webapp-log-tail) comando na Cloud Shell.

```azurecli-interactive
az webapp log tail --name <app-name> --resource-group myResourceGroup
```

Uma vez iniciado o streaming de registos, refresque a app Azure no navegador para obter algum tráfego web. Verá então os registos da consola direcionados para o terminal. Se não vir os registos da consola imediatamente, volte a consultar dentro de 30 segundos.

Para parar o streaming de registo a qualquer momento, escreva `Ctrl` + `C` .

Para obter mais informações sobre a personalização dos registos do Núcleo de ASP.NET, consulte [iniciar sessão no ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging).

## <a name="manage-your-azure-app"></a>Gerencie a sua app Azure

Para ver a aplicação que criou, no [portal Azure,](https://portal.azure.com)procure e selecione **Serviços de Aplicações.**

![Selecione Serviços de Aplicações no portal Azure](./media/app-service-web-tutorial-dotnetcore-sqldb/app-services.png)

Na página **Serviços de Aplicações,** selecione o nome da sua aplicação Azure.

![Navegação do portal para a aplicação do Azure](./media/app-service-web-tutorial-dotnetcore-sqldb/access-portal.png)

Por predefinição, o portal mostra a página **geral** da sua aplicação. Esta página proporciona-lhe uma vista do desempenho da aplicação. Aqui, também pode realizar tarefas de gestão básicas, como navegar, parar, iniciar, reiniciar e eliminar. Os separadores no lado esquerdo da página mostram as várias páginas de configuração que pode abrir.

![Página Serviço de Aplicações no portal do Azure](./media/app-service-web-tutorial-dotnetcore-sqldb/web-app-blade.png)

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

<a name="next"></a>
## <a name="next-steps"></a>Passos seguintes

O que aprendeu:

> [!div class="checklist"]
> * Criar uma Base de Dados SQL no Azure
> * Ligar uma aplicação .NET Core à Base de Dados SQL
> * Implementar a aplicação no Azure
> * Atualizar o modelo de dados e voltar a implementar a aplicação
> * Transmitir os registos do Azure para o seu terminal
> * Gerir a aplicação no portal do Azure

Avance para o próximo tutorial para aprender a mapear um nome DNS personalizado para a sua aplicação.

> [!div class="nextstepaction"]
> [Tutorial: Mapeie o nome DNS personalizado para a sua aplicação](app-service-web-tutorial-custom-domain.md)

Mais recursos:

> [!div class="nextstepaction"]
> [Configurar ASP.NET aplicação Core](configure-language-dotnetcore.md)
