---
title: Salvando com segurança as configurações do aplicativo secreto para um aplicativo Web-Azure Key Vault | Microsoft Docs
description: Como salvar com segurança as configurações do aplicativo secreto, como as credenciais do Azure ou chaves de API de terceiros usando os criadores de configuração do ASP.NET Key Vault Core, o segredo do usuário ou o .NET 4.7.1
services: visualstudio
author: cawaMS
manager: paulyuk
editor: ''
ms.service: key-vault
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: cawa
ms.openlocfilehash: 3f5196c81550446221a4524330e355c595b65c6a
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68934376"
---
# <a name="securely-save-secret-application-settings-for-a-web-application"></a>Salvar com segurança as configurações do aplicativo secreto para um aplicativo Web

## <a name="overview"></a>Descrição geral
Este artigo descreve como salvar com segurança as definições de configuração do aplicativo secreto para aplicativos do Azure.

Tradicionalmente, todas as definições de configuração de aplicativos Web são salvas em arquivos de configuração, como Web. config. Essa prática leva a verificar as configurações secretas, como credenciais de nuvem, para sistemas de controle do código-fonte público, como o GitHub. Enquanto isso, pode ser difícil seguir a prática recomendada de segurança devido à sobrecarga necessária para alterar o código-fonte e reconfigurar as definições de desenvolvimento.

Para garantir que o processo de desenvolvimento seja seguro, as bibliotecas de ferramentas e estruturas são criadas para salvar as configurações de segredo do aplicativo com segurança, com pouca ou nenhuma alteração de código-fonte.

## <a name="aspnet-and-net-core-applications"></a>Aplicativos ASP.NET e .NET Core

### <a name="save-secret-settings-in-user-secret-store-that-is-outside-of-source-control-folder"></a>Salvar configurações secretas no repositório de segredo do usuário que está fora da pasta de controle do código-fonte
Se você estiver fazendo um protótipo rápido ou não tiver acesso à Internet, comece com a movimentação de suas configurações secretas para fora da pasta de controle do código-fonte para o armazenamento de segredo do usuário. O repositório de segredos do usuário é um arquivo salvo na pasta criador de perfil do usuário, portanto, os segredos não são verificados no controle do código-fonte. O diagrama a seguir demonstra como o [segredo do usuário](https://docs.microsoft.com/aspnet/core/security/app-secrets?tabs=visual-studio) funciona.

![O segredo do usuário mantém as configurações secretas fora do controle do código-fonte](./media/vs-secure-secret-appsettings/aspnetcore-usersecret.PNG)

Se você estiver executando o aplicativo de console do .NET Core, use Key Vault para salvar seu segredo com segurança.

### <a name="save-secret-settings-in-azure-key-vault"></a>Salvar configurações de segredo no Azure Key Vault
Se você estiver desenvolvendo um projeto e precisar compartilhar o código-fonte com segurança, use [Azure Key Vault](https://azure.microsoft.com/services/key-vault/).

1. Crie um Key Vault em sua assinatura do Azure. Preencha todos os campos obrigatórios na interface do usuário e clique em *criar* na parte inferior da folha

    ![Criar Azure Key Vault](./media/vs-secure-secret-appsettings/create-keyvault.PNG)

2. Conceda a você e aos membros da sua equipe acesso ao Key Vault. Se você tiver uma equipe grande, poderá criar um [grupo de Azure Active Directory](../active-directory/active-directory-groups-create-azure-portal.md) e adicionar esse acesso ao grupo de segurança ao key Vault. No menu suspenso *permissões de segredo* , marque *obter* e *listar* em operações de gerenciamento de *segredo*.

    ![Adicionar política de acesso de Key Vault](./media/vs-secure-secret-appsettings/add-keyvault-access-policy.png)

3. Adicione seu segredo a Key Vault em portal do Azure. Para definições de configuração aninhadas, substitua ': ' por '--' para que o nome do segredo de Key Vault seja válido. ': ' não pode estar no nome de um segredo de Key Vault.

    ![Adicionar Key Vault segredo](./media/vs-secure-secret-appsettings/add-keyvault-secret.png)

    > [!NOTE] 
    > Antes do Visual Studio 2017 V 15.6, nós usamos para recomendar a instalação da extensão de autenticação dos serviços do Azure para Visual Studio. Mas ele é preterido agora, já que o funcionalidade está integrado no Visual Studio. Portanto, se você estiver em uma versão mais antiga do Visual Studio 2017, sugerimos que você atualize para pelo menos VS 2017 15,6 ou superior para que você possa usar essa funcionalidade nativamente e acessar o cofre de chaves usando a própria identidade de entrada do Visual Studio.
    >
 
4. Adicione os seguintes pacotes NuGet ao seu projeto:

    ```
    Microsoft.Azure.Services.AppAuthentication
    ```
5. Adicione o seguinte código ao arquivo Program.cs:

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
6. Adicione sua URL de Key Vault ao arquivo launchsettings. JSON. O nome da variável de ambiente *KEYVAULT_ENDPOINT* é definido no código que você adicionou na etapa 6.

    ![Adicionar URL de Key Vault como uma variável de ambiente de projeto](./media/vs-secure-secret-appsettings/add-keyvault-url.png)

7. Inicie a depuração do projeto. Ele deve ser executado com êxito.

## <a name="aspnet-and-net-applications"></a>Aplicativos ASP.NET e .NET

O .NET 4.7.1 dá suporte a Key Vault e criadores de configuração secreta, o que garante que os segredos possam ser movidos para fora da pasta de controle do código-fonte sem alterações de código
Para continuar, [Baixe o .NET 4.7.1](https://www.microsoft.com/download/details.aspx?id=56115) e migre seu aplicativo se ele estiver usando uma versão mais antiga do .NET Framework.

### <a name="save-secret-settings-in-a-secret-file-that-is-outside-of-source-control-folder"></a>Salvar configurações de segredo em um arquivo secreto que está fora da pasta de controle do código-fonte
Se você estiver escrevendo um protótipo rápido e não quiser provisionar recursos do Azure, vá para essa opção.

1. Instalar o pacote NuGet a seguir em seu projeto
    ```
    Microsoft.Configuration.ConfigurationBuilders.Basic
    ```

2. Crie um arquivo semelhante ao seguinte. Salve-o em um local fora da pasta do projeto.

    ```xml
    <root>
        <secrets ver="1.0">
            <secret name="secret1" value="foo_one" />
            <secret name="secret2" value="foo_two" />
        </secrets>
    </root>
    ```

3. Defina o arquivo secreto para ser um construtor de configuração em seu arquivo Web. config. Coloque esta seção antes da seção appSettings.

    ```xml
    <configBuilders>
        <builders>
            <add name="Secrets"
                 secretsFile="C:\Users\AppData\MyWebApplication1\secret.xml" type="Microsoft.Configuration.ConfigurationBuilders.UserSecretsConfigBuilder,
                    Microsoft.Configuration.ConfigurationBuilders, Version=1.0.0.0, Culture=neutral" />
        </builders>
    </configBuilders>
    ```

4. A seção especificar appSettings está usando o Configuration Builder do segredo. Verifique se há alguma entrada para a configuração secreta com um valor fictício.

    ```xml
        <appSettings configBuilders="Secrets">
            <add key="webpages:Version" value="3.0.0.0" />
            <add key="webpages:Enabled" value="false" />
            <add key="ClientValidationEnabled" value="true" />
            <add key="UnobtrusiveJavaScriptEnabled" value="true" />
            <add key="secret" value="" />
        </appSettings>
    ```

5. Depure seu aplicativo. Ele deve ser executado com êxito.

### <a name="save-secret-settings-in-an-azure-key-vault"></a>Salvar configurações de segredo em um Azure Key Vault
Siga as instruções da seção ASP.NET Core para configurar um Key Vault para o seu projeto.

1. Instalar o pacote NuGet a seguir em seu projeto
   ```
   Microsoft.Configuration.ConfigurationBuilders.UserSecrets
   ```

2. Defina Key Vault Construtor de configuração em Web. config. Coloque esta seção antes da seção appSettings. Substitua *vaultname* para ser o nome do Key Vault se seu Key Vault estiver no Azure público ou URI completo se você estiver usando o soberanas Cloud.

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
3. A seção especificar appSettings está usando o Key Vault Configuration Builder. Verifique se há alguma entrada para a configuração secreta com um valor fictício.

   ```xml
   <appSettings configBuilders="AzureKeyVault">
       <add key="webpages:Version" value="3.0.0.0" />
       <add key="webpages:Enabled" value="false" />
       <add key="ClientValidationEnabled" value="true" />
       <add key="UnobtrusiveJavaScriptEnabled" value="true" />
       <add key="secret" value="" />
   </appSettings>
   ```

4. Inicie a depuração do projeto. Ele deve ser executado com êxito.
