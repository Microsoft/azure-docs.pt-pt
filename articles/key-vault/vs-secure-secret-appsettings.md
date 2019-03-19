---
title: Em segurança a guardar as definições da aplicação secreta para um aplicativo web - Azure Key Vault | Documentos da Microsoft
description: Como em segurança guardar as definições da aplicação secreta como credenciais do Azure ou de terceiros API chaves usando o ASP.NET core fornecedor do Cofre de chaves, o segredo de utilizador ou o .NET 4.7.1 construtores de configuração
services: visualstudio
documentationcenter: ''
author: cawaMS
manager: paulyuk
editor: ''
ms.assetid: ''
ms.service: key-vault
ms.workload: web, azure
ms.tgt_pltfrm: vs-getting-started
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: cawa
ms.openlocfilehash: 79b1c740bca56982243ddc130d8747fdc955247f
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58124121"
---
# <a name="securely-save-secret-application-settings-for-a-web-application"></a>Guardar em segurança as definições da aplicação secreta para uma aplicação web

## <a name="overview"></a>Descrição geral
Este artigo descreve como guardar em segurança as definições de configuração do segredo de aplicação para aplicações do Azure.

Tradicionalmente, todos os web configuração da aplicação definições são guardadas em arquivos de configuração como Web. config. Esta prática leva a dar entrada secretas definições, tais como as credenciais da Cloud em sistemas de controlo de origem público como o GitHub. Enquanto isso, é possível difíceis de acompanhar a melhor prática de segurança devido à sobrecarga necessária para alterar o código-fonte e reconfigurar as definições de desenvolvimento.

Para se certificar de que o processo de desenvolvimento é seguro, as ferramentas e a estrutura de bibliotecas são criadas para guardar as definições de segredo da aplicação em segurança com o mínimo ou nenhuma alteração de código de origem.

## <a name="aspnet-and-net-core-applications"></a>Aplicativos de núcleo do ASP.NET e .NET

### <a name="save-secret-settings-in-user-secret-store-that-is-outside-of-source-control-folder"></a>Guardar definições secretas no arquivo de segredo de utilizador que está fora da pasta de controle de origem
Se estiver fazendo um protótipo rápido ou não tem acesso à internet, comece com a mover as suas definições secretas fora da pasta de controle de origem para o arquivo de segredo de utilizador. Arquivo de segredos do utilizador é um arquivo salvo na pasta do criador de perfil de utilizador, para que segredos não são verificados controle de origem. O diagrama a seguir demonstra como [segredo de utilizador](https://docs.microsoft.com/aspnet/core/security/app-secrets?tabs=visual-studio) funciona.

![Segredo do utilizador mantém as definições de segredo fora do controlo de origem](./media/vs-secure-secret-appsettings/aspnetcore-usersecret.PNG)

Se estiver executando a aplicação de consola .NET core, utilize o Key Vault para poupar o seu segredo com segurança.

### <a name="save-secret-settings-in-azure-key-vault"></a>Guardar definições de segredo no Cofre de chaves do Azure
Se estiver desenvolvendo um projeto e precisar compartilhar o código-fonte com segurança, utilize [do Azure Key Vault](https://azure.microsoft.com/services/key-vault/).

1. Crie um cofre de chaves na sua subscrição do Azure. Preencha necessários de todos os campos da interface do Usuário e clique em *criar* na parte inferior do painel

    ![Criar Cofre de chaves do Azure](./media/vs-secure-secret-appsettings/create-keyvault.PNG)

2. Concessão e os membros da equipe de acesso ao Key Vault. Se tiver uma equipa de grandes, pode criar uma [grupo do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-groups-create-azure-portal) e adicione esse grupo de segurança de acesso ao Key Vault. Na *permissões do segredo* lista pendente, verificação *obter* e *lista* sob *operações de gestão de segredo*.

    ![Adicionar política de acesso do Cofre de chaves](./media/vs-secure-secret-appsettings/add-keyvault-access-policy.png)

3. Adicione o seu segredo ao Cofre de chaves no portal do Azure. Para aninhados de configurações, substitua ":" com "-", para o nome de segredo do Key Vault é válido. ":" não tem permissão para ser no nome de um cofre de chave secreta.

    ![Adicione o segredo do Key Vault](./media/vs-secure-secret-appsettings/add-keyvault-secret.png)

4. Instalar o [extensão de autenticação de serviços do Azure para Visual Studio](https://go.microsoft.com/fwlink/?linkid=862354). Por essa extensão, a aplicação pode aceder Key Vault com a identidade de início de sessão no Visual Studio.

5. Adicione os seguintes pacotes NuGet ao seu projeto:

    ```
    Microsoft.Azure.Services.AppAuthentication
    ```
6. Adicione o seguinte código ao ficheiro Program.cs:

    ```csharp
    public static IWebHost BuildWebHost(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((ctx, builder) =>
            {
                var keyVaultEndpoint = GetKeyVaultEndpoint();
                if (!string.IsNullOrEmpty(keyVaultEndpoint))
                {
                    var azureServiceTokenProvider = new AzureServiceTokenProvider();
                    var keyVaultClient = new KeyVaultClient(
                        new KeyVaultClient.AuthenticationCallback(
                            azureServiceTokenProvider.KeyVaultTokenCallback));
                            builder.AddAzureKeyVault(
                            keyVaultEndpoint, keyVaultClient, new DefaultKeyVaultSecretManager());
                        }
                    })
                    .UseStartup<Startup>()
                    .Build();

        private static string GetKeyVaultEndpoint() => Environment.GetEnvironmentVariable("KEYVAULT_ENDPOINT");
    ```
7. Adicione o seu URL do Key Vault ao ficheiro de launchsettings.json. O nome da variável de ambiente *KEYVAULT_ENDPOINT* é definida no código adicionado no passo 6.

    ![Adicionar o URL do Key Vault como uma variável de ambiente de projeto](./media/vs-secure-secret-appsettings/add-keyvault-url.png)

8. Inicie a depuração do projeto. Deve ser executado com êxito.

## <a name="aspnet-and-net-applications"></a>Aplicativos ASP.NET e .NET

.NET 4.7.1 suporta construtores de configuração do Cofre de chaves e o segredo, que garante a segredos podem ser movidos fora da pasta de controle de origem sem alterações de código.
Para continuar, [Baixe o .NET 4.7.1](https://www.microsoft.com/download/details.aspx?id=56115) e migrar a sua aplicação, se estiver a utilizar uma versão mais antiga do .NET framework.

### <a name="save-secret-settings-in-a-secret-file-that-is-outside-of-source-control-folder"></a>Guardar definições secretas num arquivo de segredos está fora da pasta de controle de origem
Se estiver escrevendo um protótipo rápido e não pretende Aprovisionar recursos do Azure, vá com esta opção.

1. Instalar o seguinte pacote NuGet ao seu projeto
    ```
    Microsoft.Configuration.ConfigurationBuilders.Basic
    ```

2. Crie um ficheiro que é semelhante a seguir. Guarde-o num local fora da pasta do projeto.

    ```xml
    <root>
        <secrets ver="1.0">
            <secret name="secret1" value="foo_one" />
            <secret name="secret2" value="foo_two" />
        </secrets>
    </root>
    ```

3. Defina o ficheiro de segredo para ser um construtor de configuração no ficheiro Web. config. Colocar esta secção antes *appSettings* secção.

    ```xml
    <configBuilders>
        <builders>
            <add name="Secrets"
                 secretsFile="C:\Users\AppData\MyWebApplication1\secret.xml" type="Microsoft.Configuration.ConfigurationBuilders.UserSecretsConfigBuilder,
                    Microsoft.Configuration.ConfigurationBuilders, Version=1.0.0.0, Culture=neutral" />
        </builders>
    </configBuilders>
    ```

4. Especifique a seção appSettings é usando o construtor de configuração secreta. Certifique-se de que não existe qualquer entrada para a definição secreta com um valor fictício.

    ```xml
        <appSettings configBuilders="Secrets">
            <add key="webpages:Version" value="3.0.0.0" />
            <add key="webpages:Enabled" value="false" />
            <add key="ClientValidationEnabled" value="true" />
            <add key="UnobtrusiveJavaScriptEnabled" value="true" />
            <add key="secret" value="" />
        </appSettings>
    ```

5. Depure a sua aplicação. Deve ser executado com êxito.

### <a name="save-secret-settings-in-an-azure-key-vault"></a>Salvar configurações secretas num Azure Key Vault
Siga as instruções da secção de núcleo do ASP.NET para configurar um cofre de chaves para o seu projeto.

1. Instalar o seguinte pacote NuGet ao seu projeto
   ```
   Microsoft.Configuration.ConfigurationBuilders.UserSecrets
   ```

2. Defina o construtor de configuração do Cofre de chaves em Web. config. Colocar esta secção antes *appSettings* secção. Substitua *vaultName* para ser o nome do Cofre de chave, se o seu Cofre de chaves está em público do Azure ou o URI completo, se estiver a utilizar o cloud soberana.

    ```xml
    <configSections>
        <section name="configBuilders" type="System.Configuration.ConfigurationBuildersSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a" restartOnExternalChanges="false" requirePermission="false" />
    </configSections>
    <configBuilders>
        <builders>
            <add name="AzureKeyVault" vaultName="Test911" type="Microsoft.Configuration.ConfigurationBuilders.AzureKeyVaultConfigBuilder, ConfigurationBuilders, Version=1.0.0.0, Culture=neutral" />
        </builders>
    </configBuilders>
    ```
3. Especifique a seção appSettings está a utilizar o construtor de configuração do Cofre de chaves. Certifique-se de que não existe qualquer entrada para a definição secreta com um valor fictício.

   ```xml
   <appSettings configBuilders="AzureKeyVault">
       <add key="webpages:Version" value="3.0.0.0" />
       <add key="webpages:Enabled" value="false" />
       <add key="ClientValidationEnabled" value="true" />
       <add key="UnobtrusiveJavaScriptEnabled" value="true" />
       <add key="secret" value="" />
   </appSettings>
   ```

4. Inicie a depuração do projeto. Deve ser executado com êxito.
