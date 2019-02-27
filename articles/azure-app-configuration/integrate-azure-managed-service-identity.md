---
title: Tutorial para a integração com identidades geridas do Azure | Documentos da Microsoft
description: Neste tutorial, irá aprender a utilizar identidades geridos do Azure para autenticar com e obter acesso a configuração de aplicações do Azure
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
ms.openlocfilehash: bf8a1955f2c8e4a72e7cf88b013f8d5d5c2e672f
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56884768"
---
# <a name="tutorial-integrate-with-azure-managed-identities"></a>Tutorial: Integrar identidades geridas do Azure

O Azure Active Directory [identidades geridas por](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) ajuda a simplificar a gestão de segredos de seu aplicativo em nuvem. Com uma identidade gerida, pode configurar o seu código para utilizar o principal de serviço que foi criado para o serviço de computação do Azure que é executado e, em vez de uma credencial separada armazenada no Azure Key Vault ou uma cadeia de ligação local. Configuração de aplicações do Azure e o .NET Core, .NET e Java Spring bibliotecas de cliente vêm com suporte MSI criado neles. Embora não tem de utilizá-lo, o MSI elimina a necessidade de um token de acesso que contém segredos. Seu código apenas tem de conhecer o ponto final de serviço para um arquivo de configuração de aplicação para aceder à mesma e pode incorporar este URL no seu código diretamente sem a preocupação de expor qualquer segredo.

Este tutorial mostra como pode tirar partido do MSI para aceder a configuração de aplicações. Ele se baseia na aplicação web introduzida nos rápidos. Concluída [criar uma aplicação ASP.NET Core com a configuração de aplicações](./quickstart-aspnet-core-app.md) primeiro antes de continuar.

Pode utilizar qualquer editor de código para concluir os passos neste tutorial. No entanto, o [Visual Studio Code](https://code.visualstudio.com/) é uma excelente opção, disponível nas plataformas Windows, macOS e Linux.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Conceder um acesso de identidade gerida a configuração de aplicações
> * Configurar a sua aplicação para utilizar uma identidade gerida ao ligar a configuração de aplicações

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, deve ter o seguinte:

* [SDK .NET Core](https://www.microsoft.com/net/download/windows)
* [O Azure Cloud Shell configurado](https://docs.microsoft.com/azure/cloud-shell/quickstart)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="add-a-managed-identity"></a>Adicionar uma identidade gerida

Para configurar uma identidade gerida no portal, primeiro criar uma aplicação como normal e, em seguida, ativar a funcionalidade.

1. Crie uma aplicação no [portal do Azure](https://aka.ms/azconfig/portal) como faria normalmente. Navegue para o mesmo no portal.

2. Desloque para baixo para o **configurações** grupo no painel de navegação esquerdo e selecione **identidade**.

3. Dentro de **sistema atribuído** separador, mude **estado** para **no** e clique em **guardar**.

    ![Definir a identidade gerida no serviço de aplicações](./media/set-managed-identity-app-service.png)

## <a name="grant-access-to-app-configuration"></a>Conceder acesso a configuração de aplicações

1. Na [portal do Azure](https://aka.ms/azconfig/portal), clique em **todos os recursos** e o armazenamento de configuração de aplicação que criou no guia de introdução.

2. Selecione **controlo de acesso (IAM)**.

3. Dentro do **verificar acesso** separador, clique em **adicionar** no **adicionar uma atribuição de função** cartão de interface do Usuário.

4. Definir **função** ser *contribuinte* e **atribuir acesso a** ser *serviço de aplicações* (em *sistema atribuído geridos identidade*).

5. Definir **subscrição** à sua subscrição do Azure e selecione o recurso de serviço de aplicações da sua aplicação.

6. Clique em **Guardar**.

    ![Adicionar identidade gerida](./media/add-managed-identity.png)

## <a name="use-a-managed-identity"></a>Utilizar uma identidade gerida

1. Open *appSettings*, adicione o seguinte e substitua *< service_endpoint >* (incluindo os parênteses) com o URL para o arquivo de configuração de aplicação:

    ```json
    "AppConfig": {
        "Url": "<service_endpoint>"
    }
    ```

2. Open *Program.cs* e atualizar a `CreateWebHostBuilder` método, substituindo o `config.AddAzureAppConfiguration()` método.

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();
                config.AddAzureAppConfiguration(o => o.ConnectWithManagedIdentity(settings["AppConfig:Url"]));
            })
            .UseStartup<Startup>();
    ```

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

## <a name="deploy-from-local-git"></a>Implementar a partir do Git local

A maneira mais fácil para ativar a implementação de Git local para a sua aplicação com o servidor de compilação Kudu é utilizar o Cloud Shell.

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

O `az webapp create` comando deve dar a algo semelhante à seguinte saída:

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

Envie para o remoto do Azure para implementar a sua aplicação com o comando seguinte. Quando lhe for pedida uma palavra-passe, confirme que introduz a palavra-passe que criou em [Configurar um utilizador de implementação](#configure-a-deployment-user) e não a que utilizou para iniciar sessão no portal do Azure.

```bash
git push azure master
```

Poderá ver a automatização de tempo de execução específica na saída, como o MSBuild para o ASP.NET, `npm install` para node. js, e `pip install` para Python.

### <a name="browse-to-the-azure-web-app"></a>Navegar para a aplicação Web do Azure

Navegue para a sua aplicação web através de um navegador para verificar se o conteúdo está implementado.

```bash
http://<app_name>.azurewebsites.net
```

![aplicação em execução no Serviço de Aplicações](../app-service/media/app-service-web-tutorial-dotnetcore-sqldb/azure-app-in-browser.png)

<!-- ### Use a managed identity in a .NET Core app -->

<!-- ### Use a managed identity in a .NET Framework app -->

<!-- ### Use a managed identity in a Java Spring app -->

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, adicionou do Azure para simplificar o acesso à configuração de aplicações e melhorar a gestão de credenciais para a sua aplicação, a identidade de serviço gerido. Para saber mais sobre como utilizar a configuração de aplicações, avance para os exemplos da CLI do Azure.

> [!div class="nextstepaction"]
> [Exemplos da CLI](./cli-samples.md)
