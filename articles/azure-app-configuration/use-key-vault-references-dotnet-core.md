---
title: Tutorial para usar referências chave de configuração de aplicativos Azure numa aplicação ASP.NET Core [ Microsoft Docs
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
ms.date: 04/08/2020
ms.author: lcozzens
ms.custom: mvc
ms.openlocfilehash: 4641c50f0579e2a8db514df58c0401eb2173d793
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81309057"
---
# <a name="tutorial-use-key-vault-references-in-an-aspnet-core-app"></a>Tutorial: Use referências chave vault em uma aplicação ASP.NET Core

Neste tutorial, você aprende a usar o serviço de configuração de aplicações Azure juntamente com o Cofre chave Azure. Configuração de aplicativos e cofre de chaves são serviços complementares utilizados lado a lado na maioria das implementações de aplicações.

A Configuração da Aplicação ajuda-o a utilizar os serviços em conjunto, criando chaves que referenciam valores armazenados no Key Vault. Quando a Configuração da Aplicação cria tais chaves, armazena os URIs dos valores do Cofre Chave em vez dos próprios valores.

A sua aplicação utiliza o fornecedor de clientes de Configuração de Aplicações para recuperar referências do Cofre chave, tal como acontece com quaisquer outras chaves armazenadas na Configuração de Aplicações. Neste caso, os valores armazenados na Configuração de Aplicações são URIs que referenciam os valores no Cofre chave. Não são valores ou credenciais do Cofre Chave. Como o fornecedor de clientes reconhece as chaves como referências chave vault, usa o Key Vault para recuperar os seus valores.

A sua aplicação é responsável por autenticar adequadamente tanto a Configuração de Aplicações como a Chave Vault. Os dois serviços não comunicam diretamente.

Este tutorial mostra-lhe como implementar referências do Cofre chave no seu código. Baseia-se na aplicação web introduzida nos quickstarts. Antes de continuar, termine [Criar uma aplicação ASP.NET Core com configuração](./quickstart-aspnet-core-app.md) de aplicações primeiro.

Pode usar qualquer editor de código para fazer os passos neste tutorial. Por exemplo, o [Visual Studio Code](https://code.visualstudio.com/) é um editor de código seletiva que está disponível para os sistemas operativos Windows, macOS e Linux.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Crie uma chave de configuração de aplicações que refira um valor armazenado no Cofre chave.
> * Aceda ao valor desta chave a partir de uma aplicação web ASP.NET Core.

## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar este tutorial, instale o [.NET Core SDK](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-vault"></a>Criar um cofre

1. Selecione a opção Criar uma opção **de recurso** no canto superior esquerdo do portal Azure:

    ![A saída após a criação do cofre chave está completa](./media/quickstarts/search-services.png)
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

Neste ponto, a sua conta Azure é a única autorizada a aceder a este novo cofre.

![A saída após a criação do cofre chave está completa](./media/quickstarts/vault-properties.png)

## <a name="add-a-secret-to-key-vault"></a>Adicionar um segredo ao Key Vault

Para adicionar um segredo ao cofre, precisa de dar apenas alguns passos adicionais. Neste caso, adicione uma mensagem que pode usar para testar a recuperação do Cofre chave. A mensagem chama-se **Mensagem**, e guarda-se o valor "Olá do Cofre Chave" nela.

1. A partir das páginas de propriedades do Cofre chave, selecione **Segredos**.
1. **Selecione Generate/Import**.
1. No **Painel Criar um** painel secreto, introduza os seguintes valores:
    - **Opções de upload**: Entrar **manual**.
    - **Nome**: Introduzir **mensagem**.
    - **Valor**: **Introduza olá a partir do cofre da chave**.
1. Deixe o outro **Criar propriedades secretas** com os seus valores padrão.
1. Selecione **Criar**.

## <a name="add-a-key-vault-reference-to-app-configuration"></a>Adicione uma referência chave vault à configuração da aplicação

1. Inicie sessão no [portal do Azure](https://portal.azure.com). **Selecione Todos os recursos**e, em seguida, selecione a instância da loja de configuração de aplicações que criou no arranque rápido.

1. Selecione Explorador de **Configuração**.

1. Selecione **+ Criar** > **referência de cofre chave,** e depois especificar os seguintes valores:
    - **Chave**: Selecione **TestApp:Definições:Mensagem de cofre**de teclas .
    - **Etiqueta**: Deixe este valor em branco.
    - **Subscrição,** **Grupo de Recursos**e Cofre **chave**: Introduza os valores correspondentes aos do cofre chave que criou na secção anterior.
    - **Segredo**: Selecione o segredo chamado **Mensagem** que criou na secção anterior.

## <a name="connect-to-key-vault"></a>Ligar ao cofre da chave

1. Neste tutorial, você usa um diretor de serviço para autenticação no Cofre chave. Para criar este diretor de serviço, utilize o comando Azure CLI [az ad sp create-for-rbac:](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac)

    ```azurecli
    az ad sp create-for-rbac -n "http://mySP" --sdk-auth
    ```

    Esta operação devolve uma série de pares chave/valor:

    ```console
    {
    "clientId": "7da18cae-779c-41fc-992e-0527854c6583",
    "clientSecret": "b421b443-1669-4cd7-b5b1-394d5c945002",
    "subscriptionId": "443e30da-feca-47c4-b68f-1636b75e16b3",
    "tenantId": "35ad10f1-7799-4766-9acf-f2d946161b77",
    "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
    "resourceManagerEndpointUrl": "https://management.azure.com/",
    "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
    "galleryEndpointUrl": "https://gallery.azure.com/",
    "managementEndpointUrl": "https://management.core.windows.net/"
    }
    ```

1. Execute o seguinte comando para permitir que o diretor de serviço aceda ao seu cofre chave:

    ```cmd
    az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --secret-permissions delete get list set --key-permissions create decrypt delete encrypt get list unwrapKey wrapKey
    ```

1. Adicione variáveis ambientais para armazenar os valores do *clienteId,* *clientSecret,* e *tenantId*.

    #### <a name="windows-command-prompt"></a>[Pedido de comando windows](#tab/cmd)

    ```cmd
    setx AZURE_CLIENT_ID <clientId-of-your-service-principal>
    setx AZURE_CLIENT_SECRET <clientSecret-of-your-service-principal>
    setx AZURE_TENANT_ID <tenantId-of-your-service-principal>
    ```

    #### <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```PowerShell
    $Env:AZURE_CLIENT_ID = <clientId-of-your-service-principal>
    $Env:AZURE_CLIENT_SECRET = <clientSecret-of-your-service-principal>
    $Env:AZURE_TENANT_ID = <tenantId-of-your-service-principal>
    ```

    #### <a name="bash"></a>[Bash](#tab/bash)

    ```bash
    export AZURE_CLIENT_ID = <clientId-of-your-service-principal>
    export AZURE_CLIENT_SECRET = <clientSecret-of-your-service-principal>
    export AZURE_TENANT_ID = <tenantId-of-your-service-principal>
    ```

    ---

    > [!NOTE]
    > Estas credenciais key vault são usadas apenas dentro da sua aplicação. A sua aplicação autentica-se diretamente no Cofre chave com estas credenciais. Nunca são passados para o serviço de Configuração de Aplicações.

1. Reinicie o seu terminal para carregar estas novas variáveis ambientais.

## <a name="update-your-code-to-use-a-key-vault-reference"></a>Atualize o seu código para utilizar uma referência do Cofre de Chaves

1. Adicione uma referência aos pacotes NuGet necessários executando o seguinte comando:

    ```dotnetcli
    dotnet add package Azure.Identity
    ```

1. Abra *Program.cs*e adicione referências aos seguintes pacotes necessários:

    ```csharp
    using Azure.Identity;
    ```

1. Atualize `CreateWebHostBuilder` o método para utilizar `config.AddAzureAppConfiguration` a Configuração da Aplicação, ligando para o método. Inclua `ConfigureKeyVault` a opção e passe as credenciais corretas para o seu Cofre chave.

    #### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

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

    #### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

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

1. Quando ininiciaste a ligação à Configuração da Aplicação, `ConfigureKeyVault` configuraste a ligação ao Cofre chave, ligando para o método. Após a inicialização, pode aceder aos valores das referências key vault da mesma forma que acede aos valores das teclas regulares de Configuração de Aplicações.

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

## <a name="build-and-run-the-app-locally"></a>Construir e executar a app localmente

1. Para construir a aplicação utilizando o CLI .NET Core, execute o seguinte comando na concha de comando:

    ```dotnetcli
    dotnet build
    ```

1. Depois de concluída a construção, utilize o seguinte comando para executar a aplicação web localmente:

    ```dotnetcli
    dotnet run
    ```

1. Abra uma janela do `http://localhost:5000`navegador e vá para , que é o URL padrão para a aplicação web hospedada localmente.

    ![Lançamento de aplicações locais Quickstart](./media/key-vault-reference-launch-local.png)

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, criou uma chave de configuração de aplicações que refere um valor armazenado no Key Vault. Para aprender a adicionar uma identidade de serviço gerida pelo Azure que dinamiza o acesso à Configuração de Aplicações e ao Cofre de Chaves, continue para o próximo tutorial.

> [!div class="nextstepaction"]
> [Integração de identidade gerida](./howto-integrate-azure-managed-service-identity.md)
