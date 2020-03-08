---
title: 'Tutorial: núcleo ASP.NET com base de dados SQL'
description: Saiba como colocar uma aplicação .NET Core a funcionar no Serviço de Aplicações do Azure, com ligação a uma Base de Dados SQL.
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 3ad011529f8b4be90fc0c108a2049c30d1c69302
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2020
ms.locfileid: "78897320"
---
# <a name="tutorial-build-an-aspnet-core-and-sql-database-app-in-azure-app-service"></a>Tutorial: Construa uma app de base de dados core e SQL ASP.NET no Serviço de Aplicações Azure

> [!NOTE]
> Este artigo implementa uma aplicação no Serviço de Aplicações no Windows. Para implementar no Serviço de Aplicações no _Linux,_ consulte [Construir uma aplicação .NET Core e SQL Database no Azure App Service no Linux](./containers/tutorial-dotnetcore-sqldb-app.md).
>

O [Serviço de Aplicações](overview.md) oferece um serviço de alojamento na Web altamente dimensionável e com correção automática no Azure. Este tutorial mostra como criar uma aplicação .NET Core e conectá-la a uma Base de Dados SQL. Quando terminar, terá uma aplicação de MVC .NET Core em execução no Serviço de Aplicações.

![aplicação em execução no Serviço de Aplicações](./media/app-service-web-tutorial-dotnetcore-sqldb/azure-app-in-browser.png)

O que aprende com o saiba como:

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

* [Instalar o Git](https://git-scm.com/)
* [Instalar .NET Core SDK](https://dotnet.microsoft.com/download)

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
dotnet restore
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

### <a name="create-a-resource-group"></a>Criar um grupo de recursos:

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-no-h.md)]

### <a name="create-a-sql-database-logical-server"></a>Criar um servidor lógico da Base de Dados SQL

No Cloud Shell, crie um servidor lógico de Base de Dados SQL com o comando [`az sql server create`](/cli/azure/sql/server?view=azure-cli-latest#az-sql-server-create).

Substitua o marcador de posição *\<server_name>* por um nome exclusivo da Base de Dados SQL. Este nome é utilizado como parte do ponto final da Base de Dados SQL, `<server_name>.database.windows.net`, por isso, o nome tem de ser exclusivo em todos os servidores lógicos no Azure. O nome só pode conter letras minúsculas, números, o caráter hífen (-) e tem de ter entre três e 50 carateres. Substitua também, *\<db_username>* e *\<db_password>* por um nome de utilizador e palavra-passe à sua escolha. 


```azurecli-interactive
az sql server create --name <server_name> --resource-group myResourceGroup --location "West Europe" --admin-user <db_username> --admin-password <db_password>
```

Após criar o servidor lógico da Base de Dados SQL, a CLI do Azure mostra informações semelhantes ao exemplo seguinte:

```json
{
  "administratorLogin": "sqladmin",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "<server_name>.database.windows.net",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/<server_name>",
  "identity": null,
  "kind": "v12.0",
  "location": "westeurope",
  "name": "<server_name>",
  "resourceGroup": "myResourceGroup",
  "state": "Ready",
  "tags": null,
  "type": "Microsoft.Sql/servers",
  "version": "12.0"
}
```

### <a name="configure-a-server-firewall-rule"></a>Configurar uma regra de firewall do servidor

Criar uma [regra de firewall ao nível do servidor da Base de Dados SQL do Azure](../sql-database/sql-database-firewall-configure.md) com o comando [`az sql server firewall create`](/cli/azure/sql/server/firewall-rule?view=azure-cli-latest#az-sql-server-firewall-rule-create). Quando os IPs inicial e final estão definidos como 0.0.0.0, a firewall apenas é aberta para outros recursos do Azure. 

```azurecli-interactive
az sql server firewall-rule create --resource-group myResourceGroup --server <server_name> --name AllowAllIps --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!TIP] 
> Pode ser ainda mais restritivo na sua regra de firewall ao [utilizar apenas os endereços IP de saída que a aplicação utiliza](overview-inbound-outbound-ips.md#find-outbound-ips).
>

### <a name="create-a-database"></a>Criar uma base de dados

Crie uma base de dados com um [nível de desempenho S0](../sql-database/sql-database-service-tiers-dtu.md) no servidor com o comando [`az sql db create`](/cli/azure/sql/db?view=azure-cli-latest#az-sql-db-create).

```azurecli-interactive
az sql db create --resource-group myResourceGroup --server <server_name> --name coreDB --service-objective S0
```

### <a name="create-connection-string"></a>Criar uma cadeia de ligação

Substitua a string seguinte pelo *\<server_name>* , *\<db_username>* e *\<db_password>* utilizados anteriormente.

```
Server=tcp:<server_name>.database.windows.net,1433;Database=coreDB;User ID=<db_username>;Password=<db_password>;Encrypt=true;Connection Timeout=30;
```

Esta é a cadeia de ligação para a sua aplicação .NET Core. Copie-a para utilizar mais tarde.

## <a name="deploy-app-to-azure"></a>Implementar a aplicação no Azure

Neste passo, vai implementar a aplicação .NET Core ligada à Base de Dados SQL no Serviço de Aplicações.

### <a name="configure-local-git-deployment"></a>Configurar a implementação do git local

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>Crie um plano do Serviço de Aplicações

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan-no-h.md)]

### <a name="create-a-web-app"></a>Criar uma aplicação Web

[!INCLUDE [Create web app](../../includes/app-service-web-create-web-app-dotnetcore-win-no-h.md)] 

### <a name="configure-connection-string"></a>Configurar a cadeia de ligação

Para definir cadeias de ligação para a sua aplicação do Azure, utilize o comando [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) no Cloud Shell. No comando seguinte, substitua *\<app name>* , bem como o parâmetro *\<connection_string>* pela cadeia de ligação que criou anteriormente.

```azurecli-interactive
az webapp config connection-string set --resource-group myResourceGroup --name <app name> --settings MyDbConnection="<connection_string>" --connection-string-type SQLServer
```

Em ASP.NET Core, pode utilizar esta cadeia de ligação denominada (`MyDbConnection`) utilizando o padrão padrão, como qualquer cadeia de ligação especificada em *appssettings.json*. Neste caso, `MyDbConnection` também está definido nas suas *definições de apps.json*. Ao ser recorrido no Serviço de Aplicações, a cadeia de ligação definida no Serviço de Aplicações tem precedência sobre a cadeia de ligação definida nas suas *definições.json*. O código utiliza o valor *appsettings.json* durante o desenvolvimento local, e o mesmo código utiliza o valor do Serviço de Aplicações quando implementado.

Para ver como a cadeia de ligação é referenciada no seu código, consulte A Base de [Dados Connect to SQL em produção](#connect-to-sql-database-in-production).

### <a name="configure-environment-variable"></a>Configure variável ambiental

Em seguida, configure a definição da aplicação `ASPNETCORE_ENVIRONMENT` como _Produção_. Esta configuração permite-lhe saber se está a funcionar em Azure, porque utiliza o SQLite para o seu ambiente de desenvolvimento local e base de dados SQL para o seu ambiente Azure.

O exemplo seguinte configura uma definição de aplicação `ASPNETCORE_ENVIRONMENT` na sua aplicação Azure. Substitua o marcador de posição *\<app_name>* .

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings ASPNETCORE_ENVIRONMENT="Production"
```

Para ver como a variável ambiental é referenciada no seu código, consulte [Connect to SQL Database em produção](#connect-to-sql-database-in-production).

### <a name="connect-to-sql-database-in-production"></a>Ligar à Base de Dados SQL em produção

No seu repositório local, abra Startup.cs e localize o seguinte código:

```csharp
services.AddDbContext<MyDatabaseContext>(options =>
        options.UseSqlite("Data Source=localdatabase.db"));
```

Substitua-o pelo código seguinte, que utiliza as variáveis de ambiente que configurou anteriormente.

```csharp
// Use SQL Database if in Azure, otherwise, use SQLite
if(Environment.GetEnvironmentVariable("ASPNETCORE_ENVIRONMENT") == "Production")
    services.AddDbContext<MyDatabaseContext>(options =>
            options.UseSqlServer(Configuration.GetConnectionString("MyDbConnection")));
else
    services.AddDbContext<MyDatabaseContext>(options =>
            options.UseSqlite("Data Source=localdatabase.db"));

// Automatically perform database migration
services.BuildServiceProvider().GetService<MyDatabaseContext>().Database.Migrate();
```

Se este código detetar que está em produção (o que indica o ambiente Azure), então utiliza a cadeia de ligação configurada para ligar à Base de Dados SQL.

A chamada `Database.Migrate()` ajuda-o quando é executado em Azure, porque cria automaticamente as bases de dados de que a sua aplicação .NET Core necessita, com base na sua configuração de migração. 

> [!IMPORTANT]
> Para aplicações de produção que precisam de escala, siga as melhores práticas na aplicação das [migrações na produção.](/aspnet/core/data/ef-rp/migrations#applying-migrations-in-production)
> 

Guarde as alterações e, em seguida, consolide-as no seu repositório de Git. 

```bash
git add .
git commit -m "connect to SQLDB in Azure"
```

### <a name="push-to-azure-from-git"></a>Enviar para o Azure a partir do Git

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-git-push-to-azure-no-h.md)]

```bash
Counting objects: 98, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (92/92), done.
Writing objects: 100% (98/98), 524.98 KiB | 5.58 MiB/s, done.
Total 98 (delta 8), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: .
remote: Updating submodules.
remote: Preparing deployment for commit id '0c497633b8'.
remote: Generating deployment script.
remote: Project file path: ./DotNetCoreSqlDb.csproj
remote: Generated deployment script files
remote: Running deployment command...
remote: Handling ASP.NET Core Web Application deployment.
remote: .
remote: .
remote: .
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Deployment successful.
remote: App container will begin restart within 10 seconds.
To https://<app_name>.scm.azurewebsites.net/<app_name>.git
 * [new branch]      master -> master
```

### <a name="browse-to-the-azure-app"></a>Navegue na app Azure

Navegue na aplicação implementada utilizando o seu navegador web.

```bash
http://<app_name>.azurewebsites.net
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

### <a name="run-code-first-migrations-locally"></a>Executar Primeiras Migrações de Código localmente

Execute alguns comandos para fazer atualizações à base de dados local.

```bash
dotnet ef migrations add AddProperty
```

Atualize a base de dados local:

```bash
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

Assim que o `git push` estiver completo, navegue para a sua app App Service e tente adicionar um item a fazer e verifique **Done**.

![Aplicação Azure após código primeira migração](./media/app-service-web-tutorial-dotnetcore-sqldb/this-one-is-done.png)

Todos os itens a fazer existentes continuam a ser apresentados. Quando republica a sua aplicação .NET Core, os dados existentes na sua Base de Dados SQL não se perdem. Além disso, as Migrações do Entity Framework Core apenas alteram o esquema de dados e mantêm os dados existentes intactos.

## <a name="stream-diagnostic-logs"></a>Transmitir registos de diagnóstico

Enquanto a aplicação core ASP.NET funciona no Azure App Service, pode supor os registos das consolas para a Cloud Shell. Dessa forma, pode obter as mesmas mensagens de diagnóstico para ajudar a depurar erros de aplicações.

O projeto da amostra já segue a orientação da [ASP.NET Core Logging em Azure](https://docs.microsoft.com/aspnet/core/fundamentals/logging#azure-app-service-provider) com duas alterações de configuração:

- Inclui uma referência a `Microsoft.Extensions.Logging.AzureAppServices` em *DotNetCoreSqlDb.csproj*.
- Liga `loggerFactory.AddAzureWebAppDiagnostics()` *Program.cs.*

Para definir o [nível](https://docs.microsoft.com/aspnet/core/fundamentals/logging#log-level) de registo core ASP.NET no Serviço de Aplicações para `Information` a partir do nível padrão `Error`, utilize o comando [`az webapp log config`](/cli/azure/webapp/log?view=azure-cli-latest#az-webapp-log-config) na Cloud Shell.

```azurecli-interactive
az webapp log config --name <app_name> --resource-group myResourceGroup --application-logging true --level information
```

> [!NOTE]
> O nível de registo do projeto já está definido para `Information` em *appsettings.json*.
> 

Para iniciar a transmissão em fluxo do registo, utilize o comando [`az webapp log tail`](/cli/azure/webapp/log?view=azure-cli-latest#az-webapp-log-tail) no Cloud Shell.

```azurecli-interactive
az webapp log tail --name <app_name> --resource-group myResourceGroup
```

Uma vez iniciado o streaming de log, refresque a aplicação Azure no navegador para obter algum tráfego web. Verá então os registos da consola direcionados para o terminal. Se não vir os registos da consola imediatamente, volte a consultar dentro de 30 segundos.

Para parar o streaming de registo a qualquer momento, escreva `Ctrl`+`C`.

Para obter mais informações sobre a personalização dos registos core ASP.NET, consulte [o Loglogging in ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging).

## <a name="manage-your-azure-app"></a>Gerencie a sua app Azure

Para ver a aplicação que criou, no [portal Azure,](https://portal.azure.com)procure e selecione Serviços de **Aplicações.**

![Selecione Serviços de Aplicações no portal Azure](./media/app-service-web-tutorial-dotnetcore-sqldb/app-services.png)

Na página de Serviços de **Aplicações,** selecione o nome da sua aplicação Azure.

![Navegação do portal para a aplicação do Azure](./media/app-service-web-tutorial-dotnetcore-sqldb/access-portal.png)

Por padrão, o portal mostra a página **de visão geral** da sua aplicação. Esta página proporciona-lhe uma vista do desempenho da aplicação. Aqui, também pode realizar tarefas de gestão básicas, como navegar, parar, iniciar, reiniciar e eliminar. O lado esquerdo da página mostra as diferentes páginas de configuração que pode abrir.

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
> [Mapeie um nome dNS personalizado existente para o Serviço de Aplicações Azure](app-service-web-tutorial-custom-domain.md)
