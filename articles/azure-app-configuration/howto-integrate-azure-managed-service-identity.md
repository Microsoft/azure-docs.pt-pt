---
title: Utilizar identidades geridas para aceder ao App Configuration
titleSuffix: Azure App Configuration
description: Autenticar para a configuração de aplicativos Azure usando identidades geridas
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.custom: devx-track-csharp, fasttrack-edit
ms.topic: conceptual
ms.date: 2/25/2020
ms.openlocfilehash: 2f446df95c795eaac378340ed0d5de7b31dfcfee
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102219047"
---
# <a name="use-managed-identities-to-access-app-configuration"></a>Utilizar identidades geridas para aceder ao App Configuration

O Azure Ative Directory [geriu identidades](../active-directory/managed-identities-azure-resources/overview.md) que simplificam a gestão de segredos para a sua aplicação em nuvem. Com uma identidade gerida, o seu código pode utilizar o principal serviço criado para o serviço Azure em que funciona. Utiliza uma identidade gerida em vez de uma credencial separada armazenada no Cofre da Chave Azure ou numa cadeia de ligação local.

A Azure App Configuration e as suas bibliotecas de clientes .NET Core, .NET e Java Spring geriram o suporte de identidade incorporado nelas. Embora não seja obrigado a usá-lo, a identidade gerida elimina a necessidade de um token de acesso que contenha segredos. O seu código pode aceder à loja de Configuração de Aplicações utilizando apenas o ponto final do serviço. Pode incorporar este URL no seu código diretamente sem expor qualquer segredo.

Este artigo mostra como pode tirar partido da identidade gerida para aceder à Configuração da Aplicação. Baseia-se na aplicação web introduzida nos quickstarts. Antes de continuar, [crie primeiro uma aplicação Core ASP.NET com Configuração de Aplicações.](./quickstart-aspnet-core-app.md)

> [!NOTE]
> Este artigo utiliza o Azure App Service como exemplo, mas o mesmo conceito aplica-se a qualquer outro serviço Azure que suporte a identidade gerida, por exemplo, [serviço Azure Kubernetes,](../aks/use-azure-ad-pod-identity.md) [Máquina Virtual Azure](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)e [Instâncias de Contentores Azure.](../container-instances/container-instances-managed-identity.md) Se a sua carga de trabalho estiver hospedada num desses serviços, também pode aproveitar o suporte de identidade gerido do serviço.

Este artigo também mostra como pode usar a identidade gerida em conjunto com as referências key vault da Configuração de aplicações. Com uma única identidade gerida, pode aceder perfeitamente aos dois segredos do Key Vault e aos valores de configuração da Configuração de Aplicações. Se desejar explorar esta capacidade, termine [use referências de cofre de chaves com ASP.NET Core](./use-key-vault-references-dotnet-core.md) primeiro.

Você pode usar qualquer editor de código para fazer os passos neste tutorial. [Visual Studio Code](https://code.visualstudio.com/) é uma excelente opção disponível nas plataformas Windows, macOS e Linux.

Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Conceder um acesso de identidade gerido à Configuração de Aplicações.
> * Configure a sua aplicação para usar uma identidade gerida quando se ligar à Configuração da Aplicação.
> * Opcionalmente, configuure a sua aplicação para utilizar uma identidade gerida quando se conecta ao Key Vault através de uma referência de Chave de Configuração de Aplicação.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, tem de ter:

* [.NET Core SDK](https://www.microsoft.com/net/download/windows).
* [Concha de nuvem Azure configurada](../cloud-shell/quickstart.md).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="add-a-managed-identity"></a>Adicionar uma identidade gerida

Para configurar uma identidade gerida no portal, primeiro cria uma aplicação e depois ativa a funcionalidade.

1. Crie uma instância de Serviços de Aplicações no [portal Azure](https://portal.azure.com) como normalmente faz. Vai até ao portal.

1. Desloque-se até ao grupo **Definições** no painel esquerdo e selecione **Identidade**.

1. No separador **'Sistema designado',** altere **o Estado** para **ligar** e selecione **Guardar**.

1. Resposta **Sim** quando solicitado para ativar o sistema atribuído identidade gerida.

    ![Definir identidade gerida no Serviço de Aplicações](./media/set-managed-identity-app-service.png)

## <a name="grant-access-to-app-configuration"></a>Conceder acesso à Configuração de Aplicações

1. No [portal Azure](https://portal.azure.com), selecione **Todos os recursos** e selecione a loja de Configuração de Aplicações que criou no arranque rápido.

1. Selecione **Controlo de acesso (IAM)** .

1. No **separador de acesso do Cheque,** selecione **Adicionar** o cartão de atribuição de **funções** Add UI.

1. Em **Função**, selecione **App Configuration Data Reader**. No **acesso de Atribuição a**, selecione Serviço de **Aplicações** no **Sistema atribuído identidade gerida.**

1. Em **Subscrição,** selecione a sua subscrição Azure. Selecione o recurso Serviço de Aplicações para a sua aplicação.

1. Selecione **Guardar**.

    ![Adicionar uma identidade gerida](./media/add-managed-identity.png)

1. Opcional: Se desejar também conceder acesso ao Key Vault, siga as instruções na [política de acesso do Cofre de Chaves](../key-vault/general/assign-access-policy-portal.md).

## <a name="use-a-managed-identity"></a>Utilizar uma identidade gerida

1. Adicione uma referência ao pacote *Azure.Identity:*

    ```bash
    dotnet add package Azure.Identity
    ```

1. Encontre o ponto final na sua loja de Configuração de Aplicações. Este URL está listado no separador **chaves de acesso** para a loja no portal Azure.

1. Abra *appsettings.jsligado* e adicione o seguinte script. Substitua *\<service_endpoint>* , incluindo os suportes, com o URL na sua loja de Configuração de Aplicações.

    ```json
    "AppConfig": {
        "Endpoint": "<service_endpoint>"
    }
    ```

1. *Programa Aberto.cs*, e adicione uma referência aos `Azure.Identity` espaços e `Microsoft.Azure.Services.AppAuthentication` nomes:

    ```csharp-interactive
    using Azure.Identity;
    ```

1. Se pretender aceder apenas aos valores armazenados diretamente na Configuração da Aplicação, atualize o `CreateWebHostBuilder` método substituindo o `config.AddAzureAppConfiguration()` método (isto encontra-se na `Microsoft.Azure.AppConfiguration.AspNetCore` embalagem).

    > [!IMPORTANT]
    > `CreateHostBuilder` substitui `CreateWebHostBuilder` em .NET Core 3.0.  Selecione a sintaxe correta com base no seu ambiente.

    ### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
               .ConfigureAppConfiguration((hostingContext, config) =>
               {
                   var settings = config.Build();
                   config.AddAzureAppConfiguration(options =>
                       options.Connect(new Uri(settings["AppConfig:Endpoint"]), new ManagedIdentityCredential()));
               })
               .UseStartup<Startup>();
    ```

    ### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
                {
                    var settings = config.Build();
                    config.AddAzureAppConfiguration(options =>
                        options.Connect(new Uri(settings["AppConfig:Endpoint"]), new ManagedIdentityCredential()));
                });
            })
            .UseStartup<Startup>());
    ```
    ---

    > [!NOTE]
    > No caso de pretender utilizar uma identidade gerida atribuída ao **utilizador, certifique-se de** especificar o clienteId ao criar o [ManagedIdentityCredential](https://docs.microsoft.com/dotnet/api/azure.identity.managedidentitycredential).
    >```
    >config.AddAzureAppConfiguration(options =>
    >   options.Connect(new Uri(settings["AppConfig:Endpoint"]), new ManagedIdentityCredential(<your_clientId>)));
    >```
    >Como explicado nas [Identidades Geridas para recursos Azure FAQs,](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/known-issues#what-identity-will-imds-default-to-if-dont-specify-the-identity-in-the-request)existe uma forma padrão de resolver que identidade gerida é usada. Neste caso, a biblioteca de identidade Azure obriga-o a especificar a identidade desejada para evitar problemas de tempo de execução posíveis no futuro (por exemplo, se for adicionada uma nova identidade gerida atribuída ao utilizador ou se a identidade gerida atribuída ao sistema estiver ativada). Assim, terá de especificar o ClientId mesmo que apenas uma identidade gerida atribuída ao utilizador seja definida, e não exista uma identidade gerida atribuída ao sistema.


1. Para utilizar tanto os valores de Configuração de Aplicações como as referências do Key Vault, atualiza *o Programa.cs* como mostrado abaixo. Este código chama `SetCredential` como parte de dizer ao fornecedor `ConfigureKeyVault` config qual a credencial a usar ao autenticar o Key Vault.

    ### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
               .ConfigureAppConfiguration((hostingContext, config) =>
               {
                   var settings = config.Build();
                   var credentials = new ManagedIdentityCredential();

                   config.AddAzureAppConfiguration(options =>
                   {
                       options.Connect(new Uri(settings["AppConfig:Endpoint"]), credentials)
                              .ConfigureKeyVault(kv =>
                              {
                                 kv.SetCredential(credentials);
                              });
                   });
               })
               .UseStartup<Startup>();
    ```

    ### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
                {
                    var settings = config.Build();
                    var credentials = new ManagedIdentityCredential();

                    config.AddAzureAppConfiguration(options =>
                    {
                        options.Connect(new Uri(settings["AppConfig:Endpoint"]), credentials)
                               .ConfigureKeyVault(kv =>
                               {
                                   kv.SetCredential(credentials);
                               });
                    });
                });
            })
            .UseStartup<Startup>());
    ```
    ---

    Agora pode aceder a referências do Key Vault como qualquer outra chave de Configuração de Aplicações. O provedor config usará o `ManagedIdentityCredential` para autenticar no Key Vault e recuperar o valor.

    > [!NOTE]
    > As `ManagedIdentityCredential` obras funcionam apenas em ambientes Azure de serviços que suportem a autenticação de identidade gerida. Não funciona no ambiente local. Utilize [`DefaultAzureCredential`](/dotnet/api/azure.identity.defaultazurecredential) para o código funcionar tanto em ambientes locais como em Azure, uma vez que irá recorrer a algumas opções de autenticação, incluindo identidade gerida.
    > 
    > Caso pretenda utilizar uma **identidade gerida com** o utilizador quando é implantado no `DefaultAzureCredential` Azure, [especifique o clienteId](https://docs.microsoft.com/dotnet/api/overview/azure/identity-readme#specifying-a-user-assigned-managed-identity-with-the-defaultazurecredential).

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

## <a name="deploy-from-local-git"></a>Implementar a partir do Git local

A maneira mais fácil de permitir a implementação local do Git para a sua aplicação com o servidor de construção Kudu é usar [a Azure Cloud Shell](https://shell.azure.com).

### <a name="configure-a-deployment-user"></a>Configurar um utilizador de implementação

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="enable-local-git-with-kudu"></a>Ativar Git local com Kudu
Se não tiver um repositório local para a sua aplicação, terá de rubricar um. Para rubricar um repositório local de git, executar os seguintes comandos a partir do diretório de projeto da sua aplicação:

```cmd
git init
git add .
git commit -m "Initial version"
```

Para ativar a implementação local do Git para a sua aplicação com o servidor de construção Kudu, corra [`az webapp deployment source config-local-git`](/cli/azure/webapp/deployment/#az-webapp-deployment-source-config-local-git) na Cloud Shell.

```azurecli-interactive
az webapp deployment source config-local-git --name <app_name> --resource-group <group_name>
```

Este comando dá-lhe algo semelhante à seguinte saída:

```json
{
  "url": "https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git"
}
```

### <a name="deploy-your-project"></a>Implemente o seu projeto

Na janela do _terminal local,_ adicione um telecomando Azure ao seu repositório git local. _\<url>_ Substitua-se pelo URL do comando Git que obteve de [Enable local Git com Kudu](#enable-local-git-with-kudu).

```bash
git remote add azure <url>
```

Envie para o remoto do Azure para implementar a sua aplicação com o comando seguinte. Quando lhe for solicitada uma palavra-passe, introduza a palavra-passe que criou no [Configure um utilizador de implementação](#configure-a-deployment-user). Não utilize a palavra-passe que utiliza para iniciar súb9 no portal Azure.

```bash
git push azure main
```

Pode ver automatização específica do tempo de execução na saída, como MSBuild para ASP.NET, `npm install` para Node.js e para `pip install` Python.

### <a name="browse-to-the-azure-web-app"></a>Navegar para a aplicação Web do Azure

Navegue na sua aplicação web utilizando um browser para verificar se o conteúdo está implantado.

```bash
http://<app_name>.azurewebsites.net
```

## <a name="use-managed-identity-in-other-languages"></a>Utilizar identidade gerida noutras línguas

Os fornecedores de Configuração de Aplicativos para .NET Framework e Java Spring também têm suporte incorporado para identidade gerida. Pode utilizar o ponto final URL da sua loja em vez da cadeia de ligação completa quando configurar um destes fornecedores.

Por exemplo, pode atualizar a aplicação de consola .NET Framework criada no arranque rápido para especificar as seguintes definições no ficheiro *App.config:*

```xml
    <configSections>
        <section name="configBuilders" type="System.Configuration.ConfigurationBuildersSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a" restartOnExternalChanges="false" requirePermission="false" />
    </configSections>

    <configBuilders>
        <builders>
            <add name="MyConfigStore" mode="Greedy" endpoint="${Endpoint}" type="Microsoft.Configuration.ConfigurationBuilders.AzureAppConfigurationBuilder, Microsoft.Configuration.ConfigurationBuilders.AzureAppConfiguration" />
            <add name="Environment" mode="Greedy" type="Microsoft.Configuration.ConfigurationBuilders.EnvironmentConfigBuilder, Microsoft.Configuration.ConfigurationBuilders.Environment" />
        </builders>
    </configBuilders>

    <appSettings configBuilders="Environment,MyConfigStore">
        <add key="AppName" value="Console App Demo" />
        <add key="Endpoint" value ="Set via an environment variable - for example, dev, test, staging, or production endpoint." />
    </appSettings>
```

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Passos seguintes
Neste tutorial, adicionou uma identidade gerida pelo Azure para agilizar o acesso à Configuração de Aplicações e melhorar a gestão credencial da sua app. Para saber mais sobre como usar a Configuração de Aplicações, continue para as amostras do Azure CLI.

> [!div class="nextstepaction"]
> [Amostras de CLI](./cli-samples.md)
