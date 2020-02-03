---
title: Tutorial para usar referências chave de configuração de aplicativos Azure numa aplicação ASP.NET Core  Microsoft Docs
description: Neste tutorial, você aprende a usar referências chave vault da Configuração de Aplicações Azure a partir de uma aplicação ASP.NET Core
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
ms.date: 01/21/2020
ms.author: lcozzens
ms.custom: mvc
ms.openlocfilehash: b35c23e6dd88af01391bf7f01a7e736a1a744fff
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76714440"
---
# <a name="tutorial-use-key-vault-references-in-an-aspnet-core-app"></a>Tutorial: Use referências chave vault em uma aplicação ASP.NET Core

Neste tutorial, você aprenderá a usar o serviço de configuração do Azure App junto com Azure Key Vault. A configuração do aplicativo e os Key Vault são serviços complementares usados lado a lado na maioria das implantações de aplicativo.

A configuração de aplicativo ajuda você a usar os serviços juntos criando chaves que fazem referência a valores armazenados em Key Vault. Quando a configuração do aplicativo cria essas chaves, ela armazena os URIs de valores Key Vault em vez dos próprios valores.

Seu aplicativo usa o provedor do cliente de configuração de aplicativo para recuperar referências de Key Vault, assim como faz para qualquer outra chave armazenada na configuração do aplicativo. Nesse caso, os valores armazenados na configuração do aplicativo são URIs que fazem referência aos valores na Key Vault. Eles não são Key Vault valores ou credenciais. Como o provedor do cliente reconhece as chaves como referências Key Vault, ele usa Key Vault para recuperar seus valores.

Seu aplicativo é responsável por autenticar corretamente tanto para a configuração do aplicativo quanto para o Key Vault. Os dois serviços não se comunicam diretamente.

Este tutorial mostra como implementar referências de Key Vault em seu código. Ele se baseia no aplicativo Web introduzido nos guias de início rápido. Antes de continuar, termine [Criar uma aplicação ASP.NET Core com configuração](./quickstart-aspnet-core-app.md) de aplicações primeiro.

Você pode usar qualquer editor de código para executar as etapas neste tutorial. Por exemplo, o [Visual Studio Code](https://code.visualstudio.com/) é um editor de código seletiva que está disponível para os sistemas operativos Windows, macOS e Linux.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Crie uma chave de configuração de aplicativo que faça referência a um valor armazenado em Key Vault.
> * Aceda ao valor desta chave a partir de uma aplicação web ASP.NET Core.

## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar este tutorial, instale o [.NET Core SDK](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-vault"></a>Criar um cofre

1. Selecione a opção Criar uma opção **de recurso** no canto superior esquerdo do portal Azure:

    ![Saída após a conclusão da criação do Key Vault](./media/quickstarts/search-services.png)
1. Na caixa de pesquisa, introduza o **Cofre chave.**
1. Da lista de resultados, selecione **cofres chave** à esquerda.
1. Nos **cofres chave,** selecione **Adicionar**.
1. À direita no **cofre de chaves Create,** forneça as seguintes informações:
    - Selecione **Subscrição** para escolher uma subscrição.
    - No **Grupo de Recursos,** selecione **Criar novo** e insira um nome de grupo de recursos.
    - Em **nome do cofre chave,** é necessário um nome único. Para este tutorial, entre **contoso-abóbada2**.
    - Na lista de abandono da **Região,** escolha um local.
1. Deixe a outra Criar opções **de cofre chave** com os seus valores padrão.
1. Selecione **Criar**.

Neste ponto, sua conta do Azure é a única autorizada a acessar esse novo cofre.

![Saída após a conclusão da criação do Key Vault](./media/quickstarts/vault-properties.png)

## <a name="add-a-secret-to-key-vault"></a>Adicionar um segredo ao Key Vault

Para adicionar um segredo ao cofre, você precisa executar apenas algumas etapas adicionais. Nesse caso, adicione uma mensagem que você pode usar para testar Key Vault recuperação. A mensagem chama-se **Mensagem**, e guarda-se o valor "Olá do Cofre Chave" nela.

1. A partir das páginas de propriedades do Cofre chave, selecione **Segredos**.
1. **Selecione Generate/Import**.
1. No **Painel Criar um** painel secreto, introduza os seguintes valores:
    - **Opções de upload**: Entrar **manual**.
    - **Nome**: Introduzir **mensagem**.
    - **Valor**: **Introduza olá a partir do cofre da chave**.
1. Deixe o outro **Criar propriedades secretas** com os seus valores padrão.
1. Selecione **Criar**.

## <a name="add-a-key-vault-reference-to-app-configuration"></a>Adicionar uma referência de Key Vault à configuração do aplicativo

1. Inicie sessão no [portal do Azure](https://portal.azure.com). **Selecione Todos os recursos**e, em seguida, selecione a instância da loja de configuração de aplicações que criou no arranque rápido.

1. Selecione Explorador de **Configuração**.

1. Selecione **+ Criar** > **referência do cofre chave,** e depois especificar os seguintes valores:
    - **Chave**: Selecione **TestApp:Definições:Mensagem de cofre**de teclas .
    - **Etiqueta**: Deixe este valor em branco.
    - **Subscrição,** **Grupo de Recursos**e Cofre **chave**: Introduza os valores correspondentes aos do cofre chave que criou na secção anterior.
    - **Segredo**: Selecione o segredo chamado **Mensagem** que criou na secção anterior.

## <a name="connect-to-key-vault"></a>Conectar-se ao Key Vault

1. Neste tutorial, você usa uma entidade de serviço para autenticação para Key Vault. Para criar este diretor de serviço, utilize o comando Azure CLI [az ad sp create-for-rbac:](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac)

    ```azurecli
    az ad sp create-for-rbac -n "http://mySP" --sdk-auth
    ```

    Esta operação retorna uma série de pares de chave/valor:

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

    ```cmd
    az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --secret-permissions delete get list set --key-permissions create decrypt delete encrypt get list unwrapKey wrapKey
    ```

1. Adicione variáveis ambientais para armazenar os valores do *clienteId,* *clientSecret,* e *tenantId*.

    #### <a name="windows-command-prompttabcmd"></a>[Pedido de comando windows](#tab/cmd)

    ```cmd
    setx AZURE_CLIENT_ID <clientId-of-your-service-principal>
    setx AZURE_CLIENT_SECRET <clientSecret-of-your-service-principal>
    setx AZURE_TENANT_ID <tenantId-of-your-service-principal>
    ```

    #### <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

    ```PowerShell
    $Env:AZURE_CLIENT_ID = <clientId-of-your-service-principal>
    $Env:AZURE_CLIENT_SECRET = <clientSecret-of-your-service-principal>
    $Env:AZURE_TENANT_ID = <tenantId-of-your-service-principal>
    ```

    #### <a name="bashtabbash"></a>[Estação Bash](#tab/bash)

    ```bash
    export AZURE_CLIENT_ID = <clientId-of-your-service-principal>
    export AZURE_CLIENT_SECRET = <clientSecret-of-your-service-principal>
    export AZURE_TENANT_ID = <tenantId-of-your-service-principal>
    ```

    ---

    > [!NOTE]
    > Essas credenciais de Key Vault são usadas somente dentro de seu aplicativo. Seu aplicativo é autenticado diretamente para Key Vault com essas credenciais. Eles nunca são passados para o serviço de configuração do aplicativo.

1. Reinicie o seu terminal para carregar estas novas variáveis ambientais.

## <a name="update-your-code-to-use-a-key-vault-reference"></a>Atualizar seu código para usar uma referência de Key Vault

1. Adicione uma referência aos pacotes NuGet necessários executando o seguinte comando:

    ```dotnetcli
    dotnet add package Microsoft.Azure.KeyVault
    dotnet add package Azure.Identity
    ```

1. Abra *Program.cs*e adicione referências aos seguintes pacotes necessários:

    ```csharp
    using Microsoft.Azure.KeyVault;
    using Azure.Identity;
    ```

1. Atualize o método `CreateWebHostBuilder` para utilizar a Configuração da App, ligando para o método `config.AddAzureAppConfiguration`. Inclua a `UseAzureKeyVault` opção de passar numa nova referência `KeyVaultClient` ao seu Cofre Chave.

    #### <a name="net-core-2xtabcore2x"></a>[.NET Core 2.x](#tab/core2x)

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();

                config.AddAzureAppConfiguration(options =>
                {
                    options.Connect(settings["ConnectionStrings:AppConfig"])
                            .ConfigureKeyVault(kv =>
                            {
                                kv.SetCredential(new DefaultAzureCredential());
                            });
                });
            })
            .UseStartup<Startup>();
    ```

    #### <a name="net-core-3xtabcore3x"></a>[.NET Core 3.x](#tab/core3x)

    ```csharp
        public static IHostBuilder CreateHostBuilder(string[] args) =>
            Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();

                config.AddAzureAppConfiguration(options =>
                {
                    options.Connect(settings["ConnectionStrings:AppConfig"])
                            .ConfigureKeyVault(kv =>
                            {
                                kv.SetCredential(new DefaultAzureCredential());
                            });
                });
            })
            .UseStartup<Startup>());
    ```

1. Quando ininiciaste a ligação à Configuração da Aplicação, passaste a referência `KeyVaultClient` ao método `UseAzureKeyVault`. Após a inicialização, pode aceder aos valores das referências key vault da mesma forma que acede aos valores das teclas regulares de Configuração de Aplicações.

    Para ver este processo em ação, abra *o Index.cshtml* na pasta **Views** > **Home.** Substitua o seu conteúdo pelo seguinte código:

    ```html
    @using Microsoft.Extensions.Configuration
    @inject IConfiguration Configuration

    <style>
        body {
            background-color: @Configuration["TestApp:Settings:BackgroundColor"]
        }
        h1 {
            color: @Configuration["TestApp:Settings:FontColor"];
            font-size: @Configuration["TestApp:Settings:FontSize"]px;
        }
    </style>

    <h1>@Configuration["TestApp:Settings:Message"]
        and @Configuration["TestApp:Settings:KeyVaultMessage"]</h1>
    ```

    Acede ao valor do TestApp de referência do Cofre **chave:Definições:KeyVaultMessage** da mesma forma que para o valor de configuração do **TestApp:Definições:Mensagem**.

## <a name="build-and-run-the-app-locally"></a>Compilar e executar o aplicativo localmente

1. Para compilar o aplicativo usando o CLI do .NET Core, execute o seguinte comando no Shell de comando:

    ```
    dotnet build
    ```

1. Depois de concluída a construção, utilize o seguinte comando para executar a aplicação web localmente:

    ```
    dotnet run
    ```

1. Abra uma janela do navegador e vá para `http://localhost:5000`, que é o URL padrão para a aplicação web hospedada localmente.

    ![Lançamento de aplicações locais Quickstart](./media/key-vault-reference-launch-local.png)

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, você criou uma chave de configuração de aplicativo que faz referência a um valor armazenado em Key Vault. Para aprender a adicionar uma identidade de serviço gerida pelo Azure que dinamiza o acesso à Configuração de Aplicações e ao Cofre de Chaves, continue para o próximo tutorial.

> [!div class="nextstepaction"]
> [Integração de identidade gerida](./howto-integrate-azure-managed-service-identity.md)
