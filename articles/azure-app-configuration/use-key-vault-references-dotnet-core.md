---
title: Tutorial para usar as referências de Key Vault de configuração do Azure App em um aplicativo ASP.NET Core | Microsoft Docs
description: Neste tutorial, você aprenderá a usar as referências Key Vault Azure App da configuração de um aplicativo ASP.NET Core
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 10/07/2019
ms.author: lcozzens
ms.custom: mvc
ms.openlocfilehash: a14cb3035c159c82df44f686da7f7b78ef942943
ms.sourcegitcommit: f9e81b39693206b824e40d7657d0466246aadd6e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/08/2019
ms.locfileid: "72035859"
---
# <a name="tutorial-use-key-vault-references-in-an-aspnet-core-app"></a>Tutorial: Usar referências de Key Vault em um aplicativo ASP.NET Core

Neste tutorial, você aprenderá a usar o serviço de configuração do Azure App junto com Azure Key Vault. Esses são serviços complementares, que serão usados lado a lado na maioria das implantações de aplicativo. Para ajudá-lo a usá-los juntos, a configuração de aplicativo permite que você crie chaves que fazem referência a valores armazenados em Key Vault. Quando você faz isso, a configuração de aplicativo armazena o URI para o valor Key Vault, em vez do próprio valor. Seu aplicativo recupera o valor dessa chave usando o provedor de cliente de configuração de aplicativo, assim como qualquer outra chave armazenada na configuração do aplicativo. O provedor do cliente o reconhece como uma referência Key Vault e chama o Key Vault para recuperar o valor. Seu aplicativo é responsável por autenticar corretamente tanto para a configuração do aplicativo quanto para o Key Vault. Os dois serviços não se comunicam diretamente.

Este tutorial mostra como você pode implementar referências de Key Vault em seu código. Ele se baseia no aplicativo Web introduzido nos guias de início rápido. Antes de continuar, conclua a [criação de um aplicativo ASP.NET Core com a configuração de aplicativo](./quickstart-aspnet-core-app.md) primeiro.

Você pode usar qualquer editor de código para executar as etapas neste tutorial. [Visual Studio Code](https://code.visualstudio.com/) é uma excelente opção disponível nas plataformas Windows, MacOS e Linux.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma chave de configuração de aplicativo que faça referência a um valor armazenado em Key Vault
> * Acessar o valor dessa chave por meio de um aplicativo Web ASP.NET Core

## <a name="prerequisites"></a>Pré-requisitos

Para fazer este tutorial, instale o [SDK do .NET Core](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-vault"></a>Criar um cofre

1. Selecione a opção **Criar um recurso** no canto superior esquerdo do portal do Azure

    ![Resultado após a conclusão da criação do Key Vault](./media/quickstarts/search-services.png)
2. Na caixa Pesquisar, introduza **Key Vault**.
3. Na lista de resultados, selecione **Key Vault**.
4. Na secção Key Vault, selecione **Criar**.
5. Na secção **Criar cofre de chaves**, forneça as seguintes informações:
    - **Nome**: Um nome exclusivo é necessário. Para este guia de início rápido, usamos **contoso-vault2**. 
    - **Subscrição**: Escolha uma assinatura.
    - Em **grupo de recursos**, escolha **criar novo** e insira um nome de grupo de recursos.
    - No menu pendente **Localização**, selecione uma localização.
    - Deixe as outras opções com os valores predefinidos.
6. Depois de fornecer as informações acima, selecione **Criar**.

Neste ponto, sua conta do Azure é a única autorizada a acessar esse novo cofre.

![Resultado após a conclusão da criação do Key Vault](./media/quickstarts/vault-properties.png)

## <a name="add-a-secret-to-key-vault"></a>Adicionar um segredo ao Key Vault

Para adicionar um segredo ao cofre, apenas tem de efetuar alguns passos adicionais. Nesse caso, adicionamos uma mensagem que podemos usar para testar Key Vault recuperação. A mensagem é chamada de **mensagem** e armazenamos o valor de **Olá de Key Vault** .

1. Nas páginas de propriedades do Key Vault, selecione **segredos**.
1. Clique em **Gerar/Importar**.
1. No ecrã **Criar um segredo**, selecione os seguintes valores:
    - **Opções de upload**: Manual.
    - **Nome**: Message
    - **Valor**: Olá de Key Vault
    - Deixe as outras opções com os valores predefinidos. Clique em **Criar**.

## <a name="add-a-key-vault-reference-to-app-config"></a>Adicionar uma referência de Key Vault à configuração do aplicativo

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **todos os recursos**e selecione a instância do repositório de configuração de aplicativo que você criou no guia de início rápido.

1. Clique em **Gerenciador de configurações**

1. Clique em **+ criar** > **referência do cofre de chaves** e escolha os seguintes valores:
    - **Chave**: TestApp: configurações: KeyVaultMessage
    - **Rótulo**: Deixar em branco
    - **Assinatura**, **grupo de recursos**, **cofre de chaves**: Escolha as opções correspondentes à Key Vault que você criou na seção anterior.
    - **Segredo**: Selecione o segredo chamado **mensagem** que você criou na seção anterior.

## <a name="connect-to-key-vault"></a>Conectar-se ao Key Vault

1. Para este tutorial, você usará uma entidade de serviço para autenticação no keyvault. Para criar essa entidade de serviço, use o comando CLI do Azure [AZ ad SP Create-for-RBAC](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) :

    ```azurecli
    az ad sp create-for-rbac -n "http://mySP" --sdk-auth
    ```

    Essa operação retornará uma série de pares de chave/valor. 

    ```console
    {
    "clientId": "7da18cae-779c-41fc-992e-0527854c6583",
    "clientSecret": "b421b443-1669-4cd7-b5b1-394d5c945002",
    "subscriptionId": "443e30da-feca-47c4-b68f-1636b75e16b3",
    "tenantId": "35ad10f1-7799-4766-9acf-f2d946161b77",
    "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
    "resourceManagerEndpointUrl": "https://management.azure.com/",
    "activeDirectoryGraphResourceId": "https://graph.windows.net/",
    "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
    "galleryEndpointUrl": "https://gallery.azure.com/",
    "managementEndpointUrl": "https://management.core.windows.net/"
    }
    ```

1. Execute o seguinte comando para permitir que a entidade de serviço acesse o cofre de chaves:

        az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --secret-permissions delete get list set --key-permissions create decrypt delete encrypt get list unwrapKey wrapKey

1. Adicione segredos para *clientId* e *clientSecret* ao Gerenciador de segredos. Esses comandos devem ser executados no mesmo diretório que o arquivo *. csproj* .

        dotnet user-secrets set ConnectionStrings:KeyVaultClientId <clientId-of-your-service-principal>        
        dotnet user-secrets set ConnectionStrings:KeyVaultClientSecret <clientSecret-of-your-service-principal>

> [!NOTE]
> Essas credenciais de Key Vault são usadas somente dentro de seu aplicativo. Seu aplicativo é autenticado diretamente para Key Vault com essas credenciais. Eles nunca são passados para o serviço de configuração do aplicativo.

## <a name="update-your-code-to-use-a-key-vault-reference"></a>Atualizar seu código para usar uma referência de Key Vault

1. Abra *Program.cs*e adicione referências a pacotes necessários.

    ```csharp
    using Microsoft.Azure.KeyVault;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    ```

1. Atualize o método `CreateWebHostBuilder` para usar a configuração de aplicativo chamando o método `config.AddAzureAppConfiguration()`. Inclua a opção `UseAzureKeyVault`, passando uma nova referência de `KeyVaultClient` para seu Key Vault.

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();

                KeyVaultClient kvClient = new KeyVaultClient(async (authority, resource, scope) =>
                {
                    var adCredential = new ClientCredential(settings["ConnectionStrings:KeyVaultClientId"], settings["ConnectionStrings:KeyVaultClientSecret"]);
                    var authenticationContext = new AuthenticationContext(authority, null);
                    return (await authenticationContext.AcquireTokenAsync(resource, adCredential)).AccessToken;
                });

                config.AddAzureAppConfiguration(options => {
                    options.Connect(settings["ConnectionStrings:AppConfig"])
                            .UseAzureKeyVault(kvClient); });
            })
            .UseStartup<Startup>();
    ```

1. Depois de ter passado a referência de *KeyVaultClient* para o método `UseAzureKeyVault` ao inicializar a conexão com a configuração do aplicativo, você pode acessar os valores das referências Key Vault da mesma maneira que acessa os valores das chaves de configuração de aplicativo regulares. Para ver esse processo em ação, abra *index. cshtml* nas exibições > diretório base. Substitua seu conteúdo pelo código a seguir:

    ```html
    @using Microsoft.Extensions.Configuration
    @inject IConfiguration Configuration

    <style>
        body {
            background-color: @Configuration["TestApp:Settings:BackgroundColor"]
        }
        h1 {
            color: @Configuration["TestApp:Settings:FontColor"];
            font-size: @Configuration["TestApp:Settings:FontSize"];
        }
    </style>

    <h1>@Configuration["TestApp:Settings:Message"]
        and @Configuration["TestApp:Settings:KeyVaultMessage"]</h1>
    ```

    Você acessa o valor do Key Vault Reference *TestApp: Settings: KeyVaultMessage* da mesma maneira que o valor de configuração *TestApp: Settings: Message*

## <a name="build-and-run-the-app-locally"></a>Compilar e executar o aplicativo localmente

1. Para compilar o aplicativo usando o CLI do .NET Core, execute o seguinte comando no Shell de comando:

        dotnet build

2. Depois que a compilação for concluída com êxito, execute o seguinte comando para executar o aplicativo Web localmente:

        dotnet run

3. Abra uma janela do navegador e vá para `http://localhost:5000`, que é a URL padrão para o aplicativo Web hospedado localmente.

    ![Local de inicialização do aplicativo de início rápido](./media/key-vault-reference-launch-local.png)


## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você adicionou uma identidade de serviço gerenciado do Azure para simplificar o acesso à configuração de aplicativo e melhorar o gerenciamento de credenciais para seu aplicativo. Para saber mais sobre como usar a configuração de aplicativo, prossiga para os exemplos de CLI do Azure.

> [!div class="nextstepaction"]
> [Amostras de CLI](./cli-samples.md)
