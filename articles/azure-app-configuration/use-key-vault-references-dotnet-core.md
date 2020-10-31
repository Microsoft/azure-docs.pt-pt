---
title: Tutorial para usar referências do Cofre da Chave de Configuração de Aplicações Azure numa aplicação ASP.NET Core [ Microsoft Docs
description: Neste tutorial, você aprende a usar as referências key Vault da Azure App Configuration a partir de uma aplicação core ASP.NET
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
ms.custom: devx-track-csharp, mvc
ms.openlocfilehash: ff11546225a3b07cbe9f8773dab2139636af787e
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93124803"
---
# <a name="tutorial-use-key-vault-references-in-an-aspnet-core-app"></a>Tutorial: Use referências do Cofre de Chaves numa aplicação core ASP.NET

Neste tutorial, aprende a utilizar o serviço de Configuração de Aplicações Azure juntamente com o Azure Key Vault. Configuração de aplicativos e Cofre de Chaves são serviços complementares utilizados lado a lado na maioria das implementações da aplicação.

A Configuração da Aplicação ajuda-o a utilizar os serviços em conjunto, criando teclas que referenciam valores armazenados no Key Vault. Quando a Configuração da Aplicação cria tais teclas, armazena os URIs dos valores do Cofre chave em vez dos próprios valores.

A sua aplicação utiliza o fornecedor de clientes de Configuração de Aplicações para recuperar referências do Key Vault, tal como acontece com quaisquer outras teclas armazenadas na Configuração da Aplicação. Neste caso, os valores armazenados na Configuração da Aplicação são URIs que referenciam os valores no Cofre de Chaves. Não são valores ou credenciais do Cofre-Chave. Como o fornecedor de clientes reconhece as chaves como referências do Key Vault, utiliza o Key Vault para recuperar os seus valores.

A sua aplicação é responsável pela autenticação adequada tanto na Configuração de Aplicações como no Cofre de Chaves. Os dois serviços não comunicam diretamente.

Este tutorial mostra-lhe como implementar referências do Key Vault no seu código. Baseia-se na aplicação web introduzida nos quickstarts. Antes de continuar, termine [Criar uma aplicação Core ASP.NET com a Configuração de Aplicações](./quickstart-aspnet-core-app.md) em primeiro lugar.

Você pode usar qualquer editor de código para fazer os passos neste tutorial. Por exemplo, [o Visual Studio Code](https://code.visualstudio.com/) é um editor de códigos de plataforma cruzada que está disponível para os sistemas operativos Windows, macOS e Linux.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Crie uma chave de configuração de aplicativos que faz referência a um valor armazenado no Key Vault.
> * Aceda ao valor desta chave a partir de uma aplicação web core ASP.NET.

## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar este tutorial, instale o [.NET Core SDK](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-vault"></a>Criar um cofre

1. Selecione a opção **de recurso Criar** no canto superior esquerdo do portal Azure:

    ![A screenshot mostra a opção de recurso Criar no portal Azure.](./media/quickstarts/search-services.png)
1. Na caixa de pesquisa, introduza **o Cofre de Chaves.**
1. Na lista de resultados, selecione **cofres chave** à esquerda.
1. Em **cofres chave** , selecione **Add** .
1. À direita no **cofre de chaves Create,** forneça as seguintes informações:
    - **Selecione Subscrição** para escolher uma subscrição.
    - No **Grupo de Recursos,** selecione **Criar novo** e introduza um nome de grupo de recursos.
    - Em **key vault name** , um nome único é necessário. Para este tutorial, **insira Contoso-vault2.**
    - Na lista de drop-down da **Região,** escolha um local.
1. Deixe as outras **Opções de cofre de chave** com os seus valores padrão.
1. Selecione **Criar** .

Neste momento, a sua conta Azure é a única autorizada a aceder a este novo cofre.

![A imagem mostra o cofre da chave.](./media/quickstarts/vault-properties.png)

## <a name="add-a-secret-to-key-vault"></a>Adicionar um segredo ao Key Vault

Para adicionar um segredo ao cofre, precisa dar apenas alguns passos adicionais. Neste caso, adicione uma mensagem que pode usar para testar a recuperação do Cofre de Chaves. A mensagem chama-se **Mensagem** e armazena o valor "Olá do Cofre de Chaves" nela.

1. A partir das páginas de propriedades do Cofre chave, selecione **Secrets** .
1. **Selecione Gerar/Importar.**
1. No painel Criar um painel **secreto,** insira os seguintes valores:
    - **Opções de upload** : Introduza **manual** .
    - **Nome** : **Introdução Mensagem** .
    - **Valor** : **Insira Olá a partir do Cofre da Chave** .
1. Deixar o outro **Criar propriedades secretas** com os seus valores padrão.
1. Selecione **Criar** .

## <a name="add-a-key-vault-reference-to-app-configuration"></a>Adicione uma referência do Cofre de Chaves à Configuração de Aplicações

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **todos os recursos** e, em seguida, selecione a instância da loja de Configuração de Aplicações que criou no arranque rápido.

1. Selecione **O Explorador de Configuração** .

1. Selecione **+ Criar**  >  **referência de cofre de chave** e, em seguida, especificar os seguintes valores:
    - **Tecla** : Selecione **TestApp:Definições:KeyVaultMessage** .
    - **Etiqueta** : Deixe este valor em branco.
    - **Subscrição** , **Grupo de Recursos** e Cofre **chave** : Introduza os valores correspondentes aos do cofre-chave que criou na secção anterior.
    - **Segredo** : Selecione o segredo chamado **Mensagem** que criou na secção anterior.

## <a name="connect-to-key-vault"></a>Ligue-se ao cofre de chaves

1. Neste tutorial, você usa um principal de serviço para autenticação para Key Vault. Para criar este principal serviço, utilize o comando Azure CLI [ad sp create-for-rbac:](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac)

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

1. Executar o seguinte comando para permitir que o principal de serviço aceda ao seu cofre chave:

    ```cmd
    az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --secret-permissions delete get list set --key-permissions create decrypt delete encrypt get list unwrapKey wrapKey
    ```

1. Adicione variáveis ambientais para armazenar os valores do *clientId,* *clientSecret* e *tenantId.*

    #### <a name="windows-command-prompt"></a>[Pedido de comando do Windows](#tab/cmd)

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
    > Estas credenciais key vault são usadas apenas dentro da sua aplicação. A sua aplicação autentica-se diretamente no Key Vault com estas credenciais. Nunca são passados para o serviço de Configuração de Aplicações.

1. Reinicie o seu terminal para carregar estas novas variáveis ambientais.

## <a name="update-your-code-to-use-a-key-vault-reference"></a>Atualize o seu código para utilizar uma referência do Cofre de Chaves

1. Adicione uma referência aos pacotes NuGet necessários executando o seguinte comando:

    ```dotnetcli
    dotnet add package Azure.Identity
    ```

1. Abra *Program.cs* , e adicione referências aos seguintes pacotes necessários:

    ```csharp
    using Azure.Identity;
    ```

1. Atualize o `CreateWebHostBuilder` método para utilizar a Configuração da Aplicação chamando o `config.AddAzureAppConfiguration` método. Inclua a `ConfigureKeyVault` opção e passe as credenciais corretas para o seu Cofre chave.

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

1. Quando inicializou a ligação à Configuração da Aplicação, definiu a ligação ao Key Vault chamando o `ConfigureKeyVault` método. Após a inicialização, pode aceder aos valores das referências do Key Vault da mesma forma que acede aos valores das teclas regulares de Configuração de Aplicações.

    Para ver este processo em ação, abra *Index.cshtml* na pasta **Views**  >  **Home.** Substitua os respetivos conteúdos pelo seguinte código:

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

    Aceda ao valor do TestApp de referência do Cofre **chave:Definições:KeyVaultMessage** da mesma forma que para o valor de configuração do **TestApp:Definições:Mensagem** .

## <a name="build-and-run-the-app-locally"></a>Construa e execute a app localmente

1. Para construir a aplicação utilizando o .NET Core CLI, executar o seguinte comando na concha de comando:

    ```dotnetcli
    dotnet build
    ```

1. Depois de concluída a construção, utilize o seguinte comando para executar a aplicação web localmente:

    ```dotnetcli
    dotnet run
    ```

1. Abra uma janela do navegador e vá para `http://localhost:5000` , que é o URL padrão para a aplicação web hospedada localmente.

    ![Lançamento de app local Quickstart](./media/key-vault-reference-launch-local.png)

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, criou uma chave de Configuração de Aplicações que faz referência a um valor armazenado no Key Vault. Para aprender a adicionar uma identidade de serviço gerida pelo Azure que dinamize o acesso à Configuração de Aplicações e ao Cofre de Chaves, continue para o próximo tutorial.

> [!div class="nextstepaction"]
> [Integração de identidade gerida](./howto-integrate-azure-managed-service-identity.md)
