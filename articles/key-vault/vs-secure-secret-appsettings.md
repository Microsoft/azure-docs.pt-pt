---
title: Guardar de forma segura as definições de aplicação secretas para uma aplicação web - Azure Key Vault / Microsoft Docs
description: Como guardar de forma segura configurações de aplicações secretas, tais como credenciais Azure ou chaves API de terceiros usando ASP.NET fornecedor de cofre sinuoso, segredo de utilizador ou .NET 4.7.1 construtores de configuração
services: visualstudio
author: cawaMS
manager: paulyuk
editor: ''
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 07/17/2019
ms.author: cawa
ms.openlocfilehash: 6bbadd0deb1b7b6fe1056ed7bb47dc3e666a7b9c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78197713"
---
# <a name="securely-save-secret-application-settings-for-a-web-application"></a>Guarde de forma segura as definições de aplicação secretas para uma aplicação web

## <a name="overview"></a>Descrição geral
Este artigo descreve como guardar de forma segura as configurações de configuração de aplicações secretas para aplicações Azure.

Tradicionalmente, todas as definições de configuração de aplicações web são guardadas em ficheiros de configuração como Web.config. Esta prática leva a verificar em configurações secretas, tais como credenciais cloud para sistemas de controlo de fontes públicas como gitHub. Entretanto, pode ser difícil seguir as melhores práticas de segurança devido às despesas gerais necessárias para alterar o código fonte e reconfigurar as definições de desenvolvimento.

Para garantir que o processo de desenvolvimento é seguro, são criadas bibliotecas de ferramentas e quadros para salvar as definições secretas da aplicação de forma segura, com uma alteração mínima ou sem código fonte.

## <a name="aspnet-and-net-core-applications"></a>ASP.NET e .NET Core aplicações

### <a name="save-secret-settings-in-user-secret-store-that-is-outside-of-source-control-folder"></a>Guarde as definições secretas na loja User Secret que está fora da pasta de controlo de fontes
Se estiver a fazer um protótipo rápido ou se não tiver acesso à Internet, comece por mover as definições secretas para fora da pasta de controlo de fontes para a loja User Secret. A loja User Secret é um ficheiro guardado na pasta do perfil do utilizador, pelo que os segredos não são verificados no controlo de origem. O diagrama seguinte demonstra como funciona o [User Secret.](https://docs.microsoft.com/aspnet/core/security/app-secrets?tabs=visual-studio)

![User Secret mantém definições secretas fora do controlo de fonte](./media/vs-secure-secret-appsettings/aspnetcore-usersecret.PNG)

Se estiver a executar a aplicação de consola .NET core, utilize o Key Vault para guardar o seu segredo de forma segura.

### <a name="save-secret-settings-in-azure-key-vault"></a>Guardar configurações secretas no Cofre de Chaves Azure
Se estiver a desenvolver um projeto e precisar de partilhar o código fonte de forma segura, utilize o [Cofre de Chaves Azure](https://azure.microsoft.com/services/key-vault/).

1. Crie um Cofre chave na sua subscrição Azure. Preencha todos os campos necessários na UI e clique em *Criar* na parte inferior da lâmina

    ![Criar cofre de chaves azure](./media/vs-secure-secret-appsettings/create-keyvault.PNG)

2. Conceda-lhe a si e aos membros da sua equipa acesso ao Cofre chave. Se tiver uma grande equipa, pode criar um grupo de [Diretórios Ativos Azure](../active-directory/active-directory-groups-create-azure-portal.md) e adicionar o acesso do grupo de segurança ao Cofre chave. No dropdown *de Permissões Secretas,* verifique *Get* and *List* em *operações de gestão secreta*.
Se já tiver a sua aplicação web criada, conceda à aplicação web acesso ao Cofre chave para que possa aceder ao cofre da chave sem armazenar configuração secreta em Definições de Apps ou ficheiros. Procure a sua aplicação web pelo seu nome e adicione-a da mesma forma que concede aos utilizadores acesso.

    ![Adicionar a política de acesso ao cofre chave](./media/vs-secure-secret-appsettings/add-keyvault-access-policy.png)

3. Adicione o seu segredo ao Cofre chave no portal Azure. Para configurações de configuração aninhadas, substitua ':' por '--- para que o nome secreto do Cofre chave seja válido. ':' não é permitido estar em nome de um segredo do Cofre Chave.

    ![Adicionar segredo do cofre chave](./media/vs-secure-secret-appsettings/add-keyvault-secret.png)

    > [!NOTE]
    > Antes do Visual Studio 2017 V15.6, recomendamos a instalação da extensão de autenticação de serviços Azure para Estúdio Visual. Mas agora está depreciada, uma vez que a funcionalidade está integrada no Estúdio Visual. Assim, se estiver numa versão mais antiga do Visual Studio 2017, sugerimos que atualize pelo menos VS 2017 15.6 ou mais para que possa usar esta funcionalidade de forma nativa e aceder ao Key-vault a partir da utilização do próprio Visual Studio.
    >

4. Adicione os seguintes pacotes NuGet ao seu projeto:

    ```
    Microsoft.Azure.KeyVault
    Microsoft.Azure.Services.AppAuthentication
    Microsoft.Extensions.Configuration.AzureKeyVault
    ```
5. Adicione o seguinte código ao ficheiro Program.cs:

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
             Host.CreateDefaultBuilder(args)
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
                .ConfigureWebHostDefaults(webBuilder =>
                {
                    webBuilder.UseStartup<Startup>();
                });

        private static string GetKeyVaultEndpoint() => Environment.GetEnvironmentVariable("KEYVAULT_ENDPOINT");
    ```
6. Adicione o url do cofre de chaves ao ficheiro launchsettings.json. O nome variável ambiente *KEYVAULT_ENDPOINT* é definido no código que adicionou no passo 6.

    ![Adicione o URL do cofre chave como uma variável de ambiente de projeto](./media/vs-secure-secret-appsettings/add-keyvault-url.png)

7. Comece a depurar o projeto. Deve correr com sucesso.

## <a name="aspnet-and-net-applications"></a>ASP.NET e .NET aplicações

.NET 4.7.1 suporta os construtores de configuração Key Vault e Secret, que garantem que os segredos podem ser movidos para fora da pasta de controlo de fontes sem alterações de código.
Para prosseguir, [baixe .NET 4.7.1](https://www.microsoft.com/download/details.aspx?id=56115) e emigra a sua aplicação se estiver a utilizar uma versão mais antiga da estrutura .NET.

### <a name="save-secret-settings-in-a-secret-file-that-is-outside-of-source-control-folder"></a>Guarde as definições secretas num ficheiro secreto que está fora da pasta de controlo de fontes
Se está a escrever um protótipo rápido e não quer fornecer recursos Azure, vá com esta opção.

1. Instale o seguinte pacote NuGet no seu projeto
    ```
    Microsoft.Configuration.ConfigurationBuilders.Base
    ```

2. Crie um ficheiro semelhante ao seguinte. Guarde-o num local fora da pasta do projeto.

    ```xml
    <root>
        <secrets ver="1.0">
            <secret name="secret1" value="foo_one" />
            <secret name="secret2" value="foo_two" />
        </secrets>
    </root>
    ```

3. Defina o ficheiro secreto como um construtor de configuração no seu ficheiro Web.config. Coloque esta secção antes da secção Definições de *aplicações.*

    ```xml
    <configBuilders>
        <builders>
            <add name="Secrets"
                 secretsFile="C:\Users\AppData\MyWebApplication1\secret.xml" type="Microsoft.Configuration.ConfigurationBuilders.UserSecretsConfigBuilder,
                    Microsoft.Configuration.ConfigurationBuilders, Version=1.0.0.0, Culture=neutral" />
        </builders>
    </configBuilders>
    ```

4. Especificar a secção Definições de aplicativos está a usar o construtor de configuração secreto. Certifique-se de que há uma entrada para o cenário secreto com um valor de boneco.

    ```xml
        <appSettings configBuilders="Secrets">
            <add key="webpages:Version" value="3.0.0.0" />
            <add key="webpages:Enabled" value="false" />
            <add key="ClientValidationEnabled" value="true" />
            <add key="UnobtrusiveJavaScriptEnabled" value="true" />
            <add key="secret" value="" />
        </appSettings>
    ```

5. Desinvia a tua aplicação. Deve correr com sucesso.

### <a name="save-secret-settings-in-an-azure-key-vault"></a>Guarde as definições secretas num Cofre de Chaves Azure
Siga as instruções de ASP.NET secção central para configurar um Cofre chave para o seu projeto.

1. Instale o seguinte pacote NuGet no seu projeto
   ```
   Microsoft.Configuration.ConfigurationBuilders.UserSecrets
   ```

2. Defina o construtor de configuração do Cofre chave em Web.config. Coloque esta secção antes da secção Definições de *aplicações.* Substitua o *nome do cofre* para ser o nome key vault se o seu Cofre Chave estiver em Azure público, ou URI completo se estiver a usar nuvem soberana.

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
3. Especificar a secção Definições de aplicativos está a utilizar o construtor de configuração do Cofre chave. Certifique-se de que há alguma entrada para o cenário secreto com um valor de boneco.

   ```xml
   <appSettings configBuilders="AzureKeyVault">
       <add key="webpages:Version" value="3.0.0.0" />
       <add key="webpages:Enabled" value="false" />
       <add key="ClientValidationEnabled" value="true" />
       <add key="UnobtrusiveJavaScriptEnabled" value="true" />
       <add key="secret" value="" />
   </appSettings>
   ```

4. Comece a depurar o projeto. Deve correr com sucesso.
