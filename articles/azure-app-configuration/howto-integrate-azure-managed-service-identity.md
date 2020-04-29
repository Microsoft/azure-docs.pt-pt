---
title: Autenticar usando identidades geridas de Azure
titleSuffix: Azure App Configuration
description: Autenticar a Configuração de Aplicações Azure usando identidades geridas pelo Azure
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 2/25/2020
ms.openlocfilehash: bf97a1eae758778efc8d800666af4a5fcb574429
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80056828"
---
# <a name="integrate-with-azure-managed-identities"></a>Integrar com identidades geridas azure

O Azure Ative Directory [geriu identidades](../active-directory/managed-identities-azure-resources/overview.md) para simplificar a gestão de segredos para a sua aplicação na nuvem. Com uma identidade gerida, o seu código pode utilizar o principal de serviço criado para o serviço Azure em que funciona. Você usa uma identidade gerida em vez de uma credencial separada armazenada no Cofre de Chaves Azure ou uma cadeia de ligação local. 

A Configuração da Aplicação Azure e o seu Núcleo .NET, .NET Framework e as bibliotecas de clientes da Java Spring têm gerido o suporte de identidade incorporado neles. Embora não seja obrigado a usá-lo, a identidade gerida elimina a necessidade de um sinal de acesso que contenha segredos. O seu código pode aceder à loja de configuração de aplicações utilizando apenas o ponto final do serviço. Pode incorporar este URL no seu código diretamente sem expor qualquer segredo.

Este artigo mostra como pode tirar partido da identidade gerida para aceder à Configuração da App. Baseia-se na aplicação web introduzida nos quickstarts. Antes de continuar, [Crie uma aplicação ASP.NET Core com configuração](./quickstart-aspnet-core-app.md) de aplicações primeiro.

Este artigo também mostra como pode usar a identidade gerida em conjunto com as referências chave vault da Configuração da App. Com uma única identidade gerida, pode aceder perfeitamente aos dois segredos do Key Vault e valores de configuração da Configuração da App. Se desejar explorar esta capacidade, termine as referências do cofre de [chaves de utilização com ASP.NET Core](./use-key-vault-references-dotnet-core.md) primeiro.

Pode usar qualquer editor de código para fazer os passos neste tutorial. [Visual Studio Code](https://code.visualstudio.com/) é uma excelente opção disponível nas plataformas Windows, macOS e Linux.

Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Conceda um acesso de identidade gerido à Configuração de Aplicações.
> * Configure a sua aplicação para utilizar uma identidade gerida quando se ligar à Configuração de Aplicações.
> * Opcionalmente, configure a sua aplicação para utilizar uma identidade gerida quando se ligar ao Key Vault através de uma referência de teclado de configuração de aplicações.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, tem de ter:

* [.NET Core SDK](https://www.microsoft.com/net/download/windows).
* [Concha de nuvem azure configurada](https://docs.microsoft.com/azure/cloud-shell/quickstart).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="add-a-managed-identity"></a>Adicione uma identidade gerida

Para configurar uma identidade gerida no portal, primeiro cria uma aplicação e, em seguida, ativar a funcionalidade.

1. Crie uma instância de Serviços de Aplicações no [portal Azure](https://portal.azure.com) como normalmente faz. Vai para ele no portal.

1. Desloque-se até ao grupo **Definições** no painel esquerdo e selecione **Identidade**.

1. No separador designado pelo **Sistema,** mude o **Estado** para **ligar** e selecione **Guardar**.

1. Resposta **Sim** quando solicitado para ativar o sistema atribuído identidade gerida.

    ![Definir identidade gerida no Serviço de Aplicações](./media/set-managed-identity-app-service.png)

## <a name="grant-access-to-app-configuration"></a>Conceder acesso à configuração de aplicações

1. No [portal Azure,](https://portal.azure.com)selecione **Todos os recursos** e selecione a loja de Configuração de Aplicações que criou no arranque rápido.

1. Selecione **o controlo de acesso (IAM)**.

1. No separador **'Ver acesso',** selecione **Adicionar** no 'Adicionar placa de atribuição de **funções** UI'.

1. Em **funções,** selecione **App Configuration Data Reader**. Sob **acesso de atribuição a**, selecione **Serviço de Aplicações** ao abrigo **do Sistema de identidade gerida atribuída**.

1. Em **Subscrição,** selecione a sua subscrição Azure. Selecione o recurso do Serviço de Aplicações para a sua aplicação.

1. Selecione **Guardar**.

    ![Adicione uma identidade gerida](./media/add-managed-identity.png)

1. Opcional: Se deseja também conceder acesso ao Cofre chave, siga as instruções de [autenticação do Cofre-Chave com uma identidade gerida](https://docs.microsoft.com/azure/key-vault/managed-identity).

## <a name="use-a-managed-identity"></a>Utilizar uma identidade gerida

1. Adicione uma referência ao pacote *Azure.Identity:*

    ```cli
    dotnet add package Azure.Identity
    ```

1. Encontre o ponto final da sua loja de configuração de aplicações. Este URL está listado no separador **de teclas Access** para a loja no portal Azure.

1. Abra *as definições.json*e adicione o seguinte script. Substitua service_endpoint * \<>*, incluindo os suportes, com o URL na sua loja de configuração de aplicações. 

    ```json
    "AppConfig": {
        "Endpoint": "<service_endpoint>"
    }
    ```

1. Abra *Program.cs,* e adicione `Azure.Identity` uma `Microsoft.Azure.Services.AppAuthentication` referência aos espaços e nomes:

    ```csharp-interactive
    using Azure.Identity;
    ```

1. Se desejar aceder apenas a valores armazenados `CreateWebHostBuilder` diretamente na `config.AddAzureAppConfiguration()` Configuração de Aplicações, atualize o método substituindo o método.

    > [!IMPORTANT]
    > `CreateHostBuilder`substitui `CreateWebHostBuilder` em .NET Core 3.0.  Selecione a sintaxe correta com base no seu ambiente.

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
            webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();
                    config.AddAzureAppConfiguration(options =>
                        options.Connect(new Uri(settings["AppConfig:Endpoint"]), new ManagedIdentityCredential()));
                })
                .UseStartup<Startup>());
    ```
    ---

1. Para utilizar tanto os valores de Configuração de Aplicações como as referências do Key Vault, *atualize Program.cs* conforme mostrado abaixo. Este código cria `KeyVaultClient` um `AzureServiceTokenProvider` novo usando um e `UseAzureKeyVault` passa esta referência a uma chamada para o método.

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
                    })
                    .UseStartup<Startup>());
    ```
    ---

    Agora pode aceder a referências do Key Vault, tal como qualquer outra tecla de configuração de aplicações. O provedor de config usará o `KeyVaultClient` que configurado para autenticar o Cofre chave e recuperar o valor.

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

## <a name="deploy-from-local-git"></a>Implementar a partir do Git local

A maneira mais fácil de permitir a implementação local de Git para a sua aplicação com o servidor de construção Kudu é utilizar [o Azure Cloud Shell](https://shell.azure.com).

### <a name="configure-a-deployment-user"></a>Configurar um utilizador de implementação

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="enable-local-git-with-kudu"></a>Ativar git local com Kudu
Se não tiver um repositório local para a sua aplicação, terá de inicializar um. Para inicializar um repositório git local, execute os seguintes comandos do diretório de projetoda sua aplicação:

```cmd
git init
git add .
git commit -m "Initial version"
```

Para permitir a implementação local de Git para [`az webapp deployment source config-local-git`](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-local-git) a sua aplicação com o servidor de construção Kudu, corra em Cloud Shell.

```azurecli-interactive
az webapp deployment source config-local-git --name <app_name> --resource-group <group_name>
```

Este comando dá-lhe algo semelhante à seguinte saída:

```json
{
  "url": "https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git"
}
```

### <a name="deploy-your-project"></a>Implementar o seu projeto

Na janela do _terminal local,_ adicione um telecomando Azure ao seu repositório git local. Substitua _ \<o url>_ com o URL do comando Git que obteve de Enable local [Git com Kudu](#enable-local-git-with-kudu).

```bash
git remote add azure <url>
```

Envie para o remoto do Azure para implementar a sua aplicação com o comando seguinte. Quando for solicitado uma palavra-passe, introduza a palavra-passe criada no [Configure um utilizador](#configure-a-deployment-user)de implementação . Não utilize a palavra-passe que usa para iniciar sessão no portal Azure.

```bash
git push azure master
```

Você pode ver automatização específica de tempo de execução `npm install` na saída, como MSBuild para ASP.NET, para Node.js, e `pip install` para Python.

### <a name="browse-to-the-azure-web-app"></a>Navegar para a aplicação Web do Azure

Navegue na sua aplicação web utilizando um browser para verificar se o conteúdo está implementado.

```bash
http://<app_name>.azurewebsites.net
```

## <a name="use-managed-identity-in-other-languages"></a>Use a identidade gerida noutras línguas

Os fornecedores de configuração de aplicações para .NET Framework e Java Spring também têm suporte integrado para a identidade gerida. Pode utilizar o ponto final do URL da sua loja em vez da sua cadeia de ligação completa quando configurar um destes fornecedores. 

Por exemplo, pode atualizar a aplicação de consola .NET Framework criada no quickstart para especificar as seguintes definições no ficheiro *App.config:*

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

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Passos seguintes
Neste tutorial, adicionou uma identidade gerida pelo Azure para agilizar o acesso à Configuração de Aplicações e melhorar a gestão da credencial para a sua aplicação. Para saber mais sobre como utilizar a Configuração da App, continue com as amostras do Azure CLI.

> [!div class="nextstepaction"]
> [Amostras de CLI](./cli-samples.md)
