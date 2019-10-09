---
title: Integre com identidades gerenciadas do Azure | Microsoft Docs
description: Saiba como usar identidades gerenciadas do Azure para autenticar com e obter acesso à configuração de Azure App
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: na
ms.topic: conceptual
ms.date: 02/24/2019
ms.author: yegu
ms.openlocfilehash: 3a5517c31cdac0bf6f5ea386a8614d15521d4479
ms.sourcegitcommit: f9e81b39693206b824e40d7657d0466246aadd6e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/08/2019
ms.locfileid: "72035545"
---
# <a name="integrate-with-azure-managed-identities"></a>Integre com identidades gerenciadas do Azure

Azure Active Directory [identidades gerenciadas](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) ajudam a simplificar o gerenciamento de segredos para seu aplicativo de nuvem. Com uma identidade gerenciada, você pode configurar seu código para usar a entidade de serviço que foi criada para o serviço de computação do Azure em que ele é executado. Você usa uma identidade gerenciada em vez de uma credencial separada armazenada em Azure Key Vault ou em uma cadeia de conexão local. 

Azure App configuração e suas bibliotecas de cliente .NET Core, .NET e Java Spring vêm com suporte a MSI (identidade de serviço gerenciada) incorporadas a elas. Embora não seja necessário usá-lo, o MSI elimina a necessidade de um token de acesso que contenha segredos. Seu código pode acessar o repositório de configuração de aplicativo usando apenas o ponto de extremidade de serviço. Você pode inserir essa URL em seu código diretamente sem a preocupação de expor qualquer segredo.

Este tutorial mostra como você pode tirar proveito do MSI para acessar a configuração do aplicativo. Ele se baseia no aplicativo Web introduzido nos guias de início rápido. Antes de continuar, conclua a [criação de um aplicativo ASP.NET Core com a configuração de aplicativo](./quickstart-aspnet-core-app.md) primeiro.

Além disso, este tutorial mostra, opcionalmente, como você pode usar o MSI em conjunto com as referências de Key Vault da configuração de aplicativo. Isso permite que você acesse diretamente os segredos armazenados em Key Vault, bem como os valores de configuração na configuração do aplicativo. Se você quiser explorar esse recurso, conclua o [uso de Key Vault referências com ASP.NET Core](./use-key-vault-references-dotnet-core.md) primeiro.

Você pode usar qualquer editor de código para executar as etapas neste tutorial. [Visual Studio Code](https://code.visualstudio.com/) é uma excelente opção disponível nas plataformas Windows, MacOS e Linux.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Conceder um acesso de identidade gerenciada à configuração do aplicativo.
> * Configure seu aplicativo para usar uma identidade gerenciada quando você se conectar à configuração do aplicativo.
> * Opcionalmente, configure seu aplicativo para usar uma identidade gerenciada quando você se conectar a Key Vault por meio de uma referência de Key Vault de configuração de aplicativo.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, tem de ter:

* [SDK do .NET Core](https://www.microsoft.com/net/download/windows).
* [Azure cloud shell configurado](https://docs.microsoft.com/azure/cloud-shell/quickstart).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="add-a-managed-identity"></a>Adicionar uma identidade gerenciada

Para configurar uma identidade gerenciada no portal, primeiro crie um aplicativo como normal e, em seguida, habilite o recurso.

1. Crie uma instância dos serviços de aplicativos na [portal do Azure](https://portal.azure.com) normalmente. Vá para ele no Portal.

2. Role para baixo até o grupo **configurações** no painel esquerdo e selecione **identidade**.

3. Na guia **atribuído pelo sistema** , alterne **o status** para **ativado** e selecione **salvar**.

4. Responda **Sim** quando solicitado a habilitar a identidade gerenciada atribuída ao sistema.

    ![Definir identidade gerenciada no serviço de aplicativo](./media/set-managed-identity-app-service.png)

## <a name="grant-access-to-app-configuration"></a>Conceder acesso à configuração do aplicativo

1. Na [portal do Azure](https://portal.azure.com), selecione **todos os recursos** e selecione o repositório de configuração de aplicativo que você criou no guia de início rápido.

1. Selecione **controlo de acesso (IAM)** .

1. Na guia **verificar acesso** , selecione **Adicionar** na interface do usuário do cartão **Adicionar atribuição de função** .

1. Em **função**, selecione **colaborador**. Em **atribuir acesso ao**, selecione **serviço de aplicativo** em **identidade gerenciada atribuída pelo sistema**.

1. Em **assinatura**, selecione sua assinatura do Azure. Selecione o recurso de serviço de aplicativo para seu aplicativo.

1. Selecione **Guardar**.

    ![Adicionar uma identidade gerenciada](./media/add-managed-identity.png)

1. Opcional: Se você quiser conceder acesso a Key Vault também, siga as instruções em [fornecer autenticação de Key Vault com uma identidade gerenciada](https://docs.microsoft.com/azure/key-vault/managed-identity).

## <a name="use-a-managed-identity"></a>Utilizar uma identidade gerida

1. Localize a URL para o repositório de configuração do aplicativo acessando sua tela de configuração na portal do Azure e, em seguida, clicando na guia **chaves de acesso** .

1. Abra *appSettings. JSON*e adicione o script a seguir. Substitua *\<service_endpoint >* , incluindo os colchetes, pela URL para o repositório de configuração do aplicativo. 

    ```json
    "AppConfig": {
        "Endpoint": "<service_endpoint>"
    }
    ```

1. Se você quiser acessar apenas os valores armazenados diretamente na configuração do aplicativo, abra *Program.cs*e atualize o método `CreateWebHostBuilder` substituindo o método `config.AddAzureAppConfiguration()`.

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();
                config.AddAzureAppConfiguration(options =>
                    options.ConnectWithManagedIdentity(settings["AppConfig:Endpoint"]));
            })
            .UseStartup<Startup>();
    ```

1. Se você quiser usar valores de configuração de aplicativo, bem como referências de Key Vault, abra *Program.cs*e atualize o método `CreateWebHostBuilder`, conforme mostrado abaixo. Isso cria um novo `KeyVaultClient` usando um `AzureServiceTokenProvider` e passa essa referência para uma chamada para o método `UseAzureKeyVault`.

    ```csharp
        public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
            WebHost.CreateDefaultBuilder(args)
                .ConfigureAppConfiguration((hostingContext, config) =>
                {
                    var settings = config.Build();
                    AzureServiceTokenProvider azureServiceTokenProvider = new AzureServiceTokenProvider();
                    KeyVaultClient kvClient = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider.KeyVaultTokenCallback));
                    
                    config.AddAzureAppConfiguration(options => options.ConnectWithManagedIdentity(settings["AppConfig:Endpoint"])).UseAzureKeyVault(kvClient));
                })
                .UseStartup<Startup>();
    ```

    Agora você pode acessar Key Vault referências, assim como qualquer outra chave de configuração de aplicativo. O provedor de configuração usará o `KeyVaultClient` que você configurou para autenticar para Key Vault e recuperar o valor.

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

## <a name="deploy-from-local-git"></a>Implementar a partir do Git local

A maneira mais fácil de habilitar a implantação do git local para seu aplicativo com o servidor de compilação kudu é usar Azure Cloud Shell.

### <a name="configure-a-deployment-user"></a>Configurar um utilizador de implementação

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="enable-local-git-with-kudu"></a>Habilitar git local com kudu
Se você não tiver um repositório git local para seu aplicativo, será necessário inicializar um. Para fazer isso, execute os seguintes comandos no diretório do projeto do seu aplicativo:

```cmd
git init
git add .
git commit -m "Initial version"
```

Para habilitar a implantação do git local para seu aplicativo com o servidor de compilação kudu, execute [`az webapp deployment source config-local-git`](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-local-git) em Cloud Shell.

```azurecli-interactive
az webapp deployment source config-local-git --name <app_name> --resource-group <group_name>
```

Para criar um aplicativo habilitado para git, execute [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) em Cloud shell com o parâmetro `--deployment-local-git`.

```azurecli-interactive
az webapp create --name <app_name> --resource-group <group_name> --plan <plan_name> --deployment-local-git
```

O comando `az webapp create` fornece algo semelhante à seguinte saída:

```json
Local git is configured with url of 'https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

### <a name="deploy-your-project"></a>Implantar seu projeto

Regresse à _janela de terminal local_ e adicione um remoto do Azure ao seu repositório Git local. Substitua o _\<url >_ pela URL do git remoto que você obteve ao [habilitar o Git para seu aplicativo](#enable-local-git-with-kudu).

```bash
git remote add azure <url>
```

Envie para o remoto do Azure para implementar a sua aplicação com o comando seguinte. Quando for solicitada uma senha, insira a senha que você criou em [configurar um usuário de implantação](#configure-a-deployment-user). Não use a senha usada para entrar no portal do Azure.

```bash
git push azure master
```

Você pode ver a automação específica do tempo de execução na saída, como o MSBuild para ASP.NET, `npm install` para node. js e `pip install` para Python.

### <a name="browse-to-the-azure-web-app"></a>Navegar para a aplicação Web do Azure

Navegue até seu aplicativo Web usando um navegador para verificar se o conteúdo está implantado.

```bash
http://<app_name>.azurewebsites.net
```

![Aplicativo em execução no serviço de aplicativo](../app-service/media/app-service-web-tutorial-dotnetcore-sqldb/azure-app-in-browser.png)

## <a name="use-managed-identity-in-other-languages"></a>Usar identidade gerenciada em outros idiomas

Provedores de configuração de aplicativo para .NET Framework e Spring de Java também têm suporte interno para identidade gerenciada. Nesses casos, use o ponto de extremidade de URL do repositório de configuração de aplicativo em vez de sua cadeia de conexão completa ao configurar um provedor. Por exemplo, para o aplicativo de console .NET Framework criado no guia de início rápido, especifique as seguintes configurações no arquivo *app. config* :

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

> [!div class="nextstepaction"]
> [Amostras de CLI](./cli-samples.md)
