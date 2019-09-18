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
ms.openlocfilehash: 4318c4b4d8f1b1f0974d0fae0a2ae5bd6e94b593
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71076542"
---
# <a name="integrate-with-azure-managed-identities"></a>Integre com identidades gerenciadas do Azure

Azure Active Directory [identidades gerenciadas](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) ajudam a simplificar o gerenciamento de segredos para seu aplicativo de nuvem. Com uma identidade gerenciada, você pode configurar seu código para usar a entidade de serviço que foi criada para o serviço de computação do Azure em que ele é executado. Você usa uma identidade gerenciada em vez de uma credencial separada armazenada em Azure Key Vault ou em uma cadeia de conexão local. 

Azure App configuração e suas bibliotecas de cliente .NET Core, .NET e Java Spring vêm com suporte a MSI (identidade de serviço gerenciada) incorporadas a elas. Embora não seja necessário usá-lo, o MSI elimina a necessidade de um token de acesso que contenha segredos. Seu código precisa saber apenas o ponto de extremidade de serviço para um repositório de configuração de aplicativo para acessá-lo. Você pode inserir essa URL em seu código diretamente sem a preocupação de expor qualquer segredo.

Este tutorial mostra como você pode tirar proveito do MSI para acessar a configuração do aplicativo. Ele se baseia no aplicativo Web introduzido nos guias de início rápido. Antes de continuar, conclua a [criação de um aplicativo ASP.NET Core com a configuração de aplicativo](./quickstart-aspnet-core-app.md) primeiro.

Você pode usar qualquer editor de código para executar as etapas neste tutorial. [Visual Studio Code](https://code.visualstudio.com/) é uma excelente opção disponível nas plataformas Windows, MacOS e Linux.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Conceder um acesso de identidade gerenciada à configuração do aplicativo.
> * Configure seu aplicativo para usar uma identidade gerenciada quando você se conectar à configuração do aplicativo.

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

2. Selecione **controlo de acesso (IAM)** .

3. Na guia **verificar acesso** , selecione **Adicionar** na interface do usuário do cartão **Adicionar atribuição de função** .

4. Em **função**, selecione **colaborador**. Em **atribuir acesso ao**, selecione **serviço de aplicativo** em **identidade gerenciada atribuída pelo sistema**.

5. Em **assinatura**, selecione sua assinatura do Azure. Selecione o recurso de serviço de aplicativo para seu aplicativo.

6. Selecione **Guardar**.

    ![Adicionar uma identidade gerenciada](./media/add-managed-identity.png)

## <a name="use-a-managed-identity"></a>Utilizar uma identidade gerida

1. Localize a URL para o repositório de configuração do aplicativo acessando sua tela de configuração na portal do Azure e, em seguida, clicando na guia **chaves de acesso** .

2. Abra *appSettings. JSON*e adicione o script a seguir. *Substitua\<service_endpoint >* , incluindo os colchetes, pela URL para o repositório de configuração do aplicativo. 

    ```json
    "AppConfig": {
        "Endpoint": "<service_endpoint>"
    }
    ```

3. Abra *Program.cs*e atualize o `CreateWebHostBuilder` método substituindo o `config.AddAzureAppConfiguration()` método.

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

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

## <a name="deploy-from-local-git"></a>Implementar a partir do Git local

A maneira mais fácil de habilitar a implantação do git local para seu aplicativo com o servidor de compilação kudu é usar Azure Cloud Shell.

### <a name="configure-a-deployment-user"></a>Configurar um utilizador de implementação

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="enable-local-git-with-kudu"></a>Habilitar git local com kudu
Se você ainda não tiver um repositório git local para seu aplicativo, será necessário inicializar um executando os seguintes comandos no diretório do projeto do aplicativo:

```cmd
git init
git add .
git commit -m "Initial version"
```

Para habilitar a implantação do git local para seu aplicativo com o servidor de compilação [`az webapp deployment source config-local-git`](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-local-git) kudu, execute em Cloud Shell.

```azurecli-interactive
az webapp deployment source config-local-git --name <app_name> --resource-group <group_name>
```

Para criar um aplicativo habilitado para git em vez disso [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) , execute em Cloud Shell `--deployment-local-git` com o parâmetro.

```azurecli-interactive
az webapp create --name <app_name> --resource-group <group_name> --plan <plan_name> --deployment-local-git
```

O `az webapp create` comando fornece algo semelhante à seguinte saída:

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

Regresse à _janela de terminal local_ e adicione um remoto do Azure ao seu repositório Git local. _Substitua\<a URL >_ pela URL do git remoto que você obteve de [habilitar o Git para seu aplicativo](#enable-local-git-with-kudu).

```bash
git remote add azure <url>
```

Envie para o remoto do Azure para implementar a sua aplicação com o comando seguinte. Quando for solicitada uma senha, insira a senha que você criou em [configurar um usuário de implantação](#configure-a-deployment-user). Não use a senha usada para entrar no portal do Azure.

```bash
git push azure master
```

Você pode ver a automação específica do tempo de execução na saída, como o MSBuild para `npm install` ASP.net, para node. js `pip install` e para Python.

### <a name="browse-to-the-azure-web-app"></a>Navegar para a aplicação Web do Azure

Navegue até seu aplicativo Web usando um navegador para verificar se o conteúdo está implantado.

```bash
http://<app_name>.azurewebsites.net
```

![aplicativo em execução no serviço de aplicativo](../app-service/media/app-service-web-tutorial-dotnetcore-sqldb/azure-app-in-browser.png)

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

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Amostras de CLI](./cli-samples.md)
