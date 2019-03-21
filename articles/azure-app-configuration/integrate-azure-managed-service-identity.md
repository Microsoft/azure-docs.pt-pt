---
title: Tutorial para a integração com o Azure managed identidades | Documentos da Microsoft
description: Neste tutorial, irá aprender a utilizar o Azure managed identidades para autenticar com e obter acesso a configuração de aplicações do Azure
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: na
ms.topic: tutorial
ms.date: 02/24/2019
ms.author: yegu
ms.openlocfilehash: be19d37900acb8201922fa61fda61cc884d4c933
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58226016"
---
# <a name="tutorial-integrate-with-azure-managed-identities"></a>Tutorial: Integrar com o Azure managed identidades

O Azure Active Directory [geridos identidades](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) ajudar a simplificar a gestão de segredos de seu aplicativo em nuvem. Com uma identidade gerida, pode configurar o seu código para utilizar o principal de serviço que foi criado para o serviço de computação do Azure que é executada no. Utilizar uma identidade gerida em vez de uma credencial separada armazenada no Azure Key Vault ou uma cadeia de ligação local. 

Configuração de aplicações do Azure e o .NET Core, .NET e Java Spring bibliotecas de cliente vêm com suporte de identity (MSI) do serviço gerido incorporado neles. Embora não é necessários para utilizá-lo, o MSI elimina a necessidade de um token de acesso que contém segredos. O código precisa de saber apenas o ponto final de serviço para uma configuração de aplicação loja para aceder à mesma. Pode incorporar este URL no seu código diretamente sem a preocupação de expor qualquer segredo.

Este tutorial mostra como pode tirar partido do MSI para aceder a configuração de aplicações. Ele se baseia na aplicação web introduzida nos rápidos. Antes de continuar, concluir [criar uma aplicação ASP.NET Core com a configuração de aplicações](./quickstart-aspnet-core-app.md) primeiro.

Pode utilizar qualquer editor de código para realizar os passos neste tutorial. [Visual Studio Code](https://code.visualstudio.com/) é uma excelente opção disponível no Windows, macOS e plataformas Linux.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Conceda um acesso de identidade gerida a configuração de aplicações.
> * Configure a sua aplicação para utilizar uma identidade gerida quando se liga a configuração de aplicações.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, tem de ter:

* [.NET core SDK](https://www.microsoft.com/net/download/windows).
* [O Azure Cloud Shell, configurado](https://docs.microsoft.com/azure/cloud-shell/quickstart).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="add-a-managed-identity"></a>Adicionar uma identidade gerida

Para configurar uma identidade gerida no portal, primeiro crie uma aplicação como normal e, em seguida, ativar a funcionalidade.

1. Crie uma aplicação no [portal do Azure](https://aka.ms/azconfig/portal) como faria normalmente. Aceda ao mesmo no portal.

2. Desloque para baixo para o **configurações** grupo no painel do lado esquerdo e selecione **identidade**.

3. Sobre o **sistema atribuído** separador, mude **estado** para **no** e selecione **guardar**.

    ![Definir a identidade gerida no serviço de aplicações](./media/set-managed-identity-app-service.png)

## <a name="grant-access-to-app-configuration"></a>Conceder acesso a configuração de aplicações

1. Na [portal do Azure](https://aka.ms/azconfig/portal), selecione **todos os recursos** e selecione o arquivo de configuração de aplicação que criou no guia de introdução.

2. Selecione **controlo de acesso (IAM)**.

3. Na **verificar acesso** separador, selecione **Add** no **adicionar atribuição de função** cartão de interface do Usuário.

4. Sob **função**, selecione **contribuinte**. Sob **atribuir acesso aos**, selecione **serviço de aplicações** sob **sistema atribuído a identidade gerida**.

5. Sob **subscrição**, selecione a sua subscrição do Azure. Selecione o recurso de serviço de aplicações para a sua aplicação.

6. Selecione **Guardar**.

    ![Adicionar uma identidade gerida](./media/add-managed-identity.png)

## <a name="use-a-managed-identity"></a>Utilizar uma identidade gerida

1. Open *appSettings*e adicione o seguinte script. Substitua *< service_endpoint >*, incluindo os parênteses, com o URL para o arquivo de configuração de aplicação:

    ```json
    "AppConfig": {
        "Endpoint": "<service_endpoint>"
    }
    ```

2. Open *Program.cs*e atualizar a `CreateWebHostBuilder` método, substituindo o `config.AddAzureAppConfiguration()` método.

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();
                config.AddAzureAppConfiguration(o => o.ConnectWithManagedIdentity(settings["AppConfig:Endpoint"]));
            })
            .UseStartup<Startup>();
    ```

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

## <a name="deploy-from-local-git"></a>Implementar a partir do Git local

É a maneira mais fácil para ativar a implementação de Git local para a sua aplicação com o servidor de compilação Kudu utilizar o Azure Cloud Shell.

### <a name="configure-a-deployment-user"></a>Configurar um utilizador de implementação

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="enable-local-git-with-kudu"></a>Permitem que o Git local com Kudu

Para ativar a implementação de Git local para a sua aplicação com o servidor de compilação Kudu, execute [ `az webapp deployment source config-local-git` ](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-local-git) no Cloud Shell.

```azurecli-interactive
az webapp deployment source config-local-git --name <app_name> --resource-group <group_name>
```

Para criar uma aplicação com o Git em vez disso, execute [ `az webapp create` ](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) no Cloud Shell com o `--deployment-local-git` parâmetro.

```azurecli-interactive
az webapp create --name <app_name> --resource-group <group_name> --plan <plan_name> --deployment-local-git
```

O `az webapp create` comando dá-lhe algo semelhante à seguinte saída:

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

### <a name="deploy-your-project"></a>Implementar o seu projeto

Regresse à _janela de terminal local_ e adicione um remoto do Azure ao seu repositório Git local. Substitua  _\<url >_ com o URL do Git remoto que recebeu do [ativar o Git para a sua aplicação](#enable-local-git-with-kudu).

```bash
git remote add azure <url>
```

Envie para o remoto do Azure para implementar a sua aplicação com o comando seguinte. Quando lhe for pedida uma palavra-passe, introduza a palavra-passe que criou no [configurar um utilizador de implementação](#configure-a-deployment-user). Não utilize a palavra-passe que utiliza para iniciar sessão no portal do Azure.

```bash
git push azure master
```

Poderá ver a automatização de tempo de execução específica na saída, como o MSBuild para o ASP.NET, `npm install` para node. js, e `pip install` para Python.

### <a name="browse-to-the-azure-web-app"></a>Navegar para a aplicação Web do Azure

Navegue para a sua aplicação web com um browser para verificar se o conteúdo é implementado.

```bash
http://<app_name>.azurewebsites.net
```

![aplicação em execução no serviço de aplicações](../app-service/media/app-service-web-tutorial-dotnetcore-sqldb/azure-app-in-browser.png)

## <a name="use-managed-identity-in-other-languages"></a>Utilizar a identidade gerida em outros idiomas

Fornecedores de configuração de aplicação para o .NET Framework e Java Spring também têm suporte incorporado para identidade gerida. Nestes casos, utilize o ponto final do URL de seu arquivo de configuração de aplicação em vez de sua cadeia de ligação completo, quando configurar um fornecedor. Por exemplo, para a aplicação de consola do .NET Framework criada no guia de introdução, especifique as seguintes definições no *App. config* ficheiro:

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

Neste tutorial, adicionou do Azure para simplificar o acesso à configuração de aplicações e melhorar a gestão de credenciais para a sua aplicação, a identidade de serviço gerido. Para saber mais sobre como utilizar a configuração de aplicações, avance para os exemplos da CLI do Azure.

> [!div class="nextstepaction"]
> [Amostras de CLI](./cli-samples.md)
