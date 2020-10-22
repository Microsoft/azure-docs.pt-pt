---
title: Guardar de forma segura as definições de aplicações secretas para uma aplicação web - Azure Key Vault / Microsoft Docs
description: Como guardar de forma segura as definições de aplicações secretas, tais como credenciais Azure ou teclas API de terceiros usando ASP.NET core Key Vault Provider, User Secret ou .NET 4.7.1 construtores de configuração
services: visualstudio
author: cawaMS
manager: paulyuk
editor: ''
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 07/17/2019
ms.author: cawa
ms.custom: devx-track-csharp
ms.openlocfilehash: 96b6b262765a361befeadd9b5a42d37ca5e66497
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92372060"
---
# <a name="securely-save-secret-application-settings-for-a-web-application"></a>Guardar de forma segura as definições de aplicações secretas para uma aplicação web

## <a name="overview"></a>Descrição geral
Este artigo descreve como guardar de forma segura as definições de configuração de aplicações secretas para aplicações Azure.

Tradicionalmente, todas as definições de configuração de aplicações web são guardadas em ficheiros de configuração, como Web.config. Esta prática leva a verificar em configurações secretas, como credenciais cloud, a sistemas públicos de controlo de fontes como o GitHub. Entretanto, pode ser difícil seguir as melhores práticas de segurança devido às despesas gerais necessárias para alterar o código fonte e reconfigurar as definições de desenvolvimento.

Para garantir que o processo de desenvolvimento é seguro, as bibliotecas de ferramentas e quadros são criadas para guardar as definições secretas da aplicação de forma segura, com alterações mínimas ou sem código fonte.

## <a name="aspnet-and-net-core-applications"></a>ASP.NET e .NET Aplicações core

### <a name="save-secret-settings-in-user-secret-store-that-is-outside-of-source-control-folder"></a>Guarde as definições secretas na loja User Secret que está fora da pasta de controlo de fontes
Se estiver a fazer um protótipo rápido ou não tiver acesso à Internet, comece por mover as definições secretas para fora da pasta de controlo de fontes para a loja User Secret. A loja User Secret é um ficheiro guardado na pasta do perfil do utilizador, pelo que os segredos não são verificados no controlo de origem. O diagrama seguinte demonstra como funciona [o User Secret.](https://docs.microsoft.com/aspnet/core/security/app-secrets?tabs=visual-studio)

![User Secret mantém configurações secretas fora do controlo de origem](../media/vs-secure-secret-appsettings/aspnetcore-usersecret.PNG)

Se estiver a executar a aplicação de consola .NET core, utilize o Key Vault para guardar o seu segredo de forma segura.

### <a name="save-secret-settings-in-azure-key-vault"></a>Guarde as definições secretas no Cofre da Chave Azure
Se estiver a desenvolver um projeto e precisar de partilhar o código fonte de forma segura, utilize [o Cofre da Chave Azure](https://azure.microsoft.com/services/key-vault/).

1. Crie um Cofre-Chave na sua subscrição Azure. Preencha todos os campos necessários na UI e clique em *Criar* na parte inferior da lâmina

    ![Criar cofre de chaves Azure](../media/vs-secure-secret-appsettings/create-keyvault.PNG)

2. Conceda a si e aos seus membros da equipa acesso ao Cofre de Chaves. Se tiver uma grande equipa, pode criar um [grupo de Diretório Ativo Azure](../../active-directory/active-directory-groups-create-azure-portal.md) e adicionar o acesso do grupo de segurança ao Cofre-Chave. No *dropdown de Permissões Secretas,* consulte *Get* and *List* em *Operações de Gestão Secreta*.
Se já tiver a sua aplicação web criada, conceda à aplicação web acesso ao Cofre-Chave para que possa aceder ao cofre de chaves sem armazenar configuração secreta em Configurações de Aplicações ou ficheiros. Procure a sua aplicação web pelo seu nome e adicione-a da mesma forma que concede aos utilizadores acesso.

    ![Adicionar a política de acesso ao Cofre de Chaves](../media/vs-secure-secret-appsettings/add-keyvault-access-policy.png)

3. Adicione o seu segredo ao Cofre chave no portal Azure. Para configurações de configuração aninhadas, substitua ':' por '--', de modo que o nome secreto do Cofre de Chaves seja válido. ':'' não é permitido estar em nome de um segredo do Cofre chave.

    ![Adicione o segredo do Cofre da Chave](../media/vs-secure-secret-appsettings/add-keyvault-secret.png)

    > [!NOTE]
    > Antes do Visual Studio 2017 V15.6 usámos para recomendar a instalação da extensão de autenticação de serviços Azure para o Estúdio Visual. Mas é depreciado agora, uma vez que a funcionalidade está integrada no Estúdio Visual. Assim, se estiver numa versão mais antiga do Visual Studio 2017, sugerimos que atualize pelo menos VS 2017 15.6 ou acima para que possa utilizar esta funcionalidade de forma nativa e aceder ao cofre-chave de usar a própria Identidade de entrada do Estúdio Visual.
    >

4. Faça login no Azure utilizando o CLI, pode escrever:

    ```azurecli
    az login
    ```

5. Adicione os seguintes pacotes NuGet ao seu projeto:

    ```
    Azure.Identity
    Azure.Extensions.AspNetCore.Configuration.Secrets
    ```
6. Adicione o seguinte código ao ficheiro Program.cs:

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
             Host.CreateDefaultBuilder(args)
                .ConfigureAppConfiguration((ctx, builder) =>
                {
                    var keyVaultEndpoint = GetKeyVaultEndpoint();
                    if (!string.IsNullOrEmpty(keyVaultEndpoint))
                    {
                        builder.AddAzureKeyVault(new Uri(keyVaultEndpoint), new DefaultAzureCredential(), new KeyVaultSecretManager());
                    }
                })
                .ConfigureWebHostDefaults(webBuilder =>
                {
                    webBuilder.UseStartup<Startup>();
                });

        private static string GetKeyVaultEndpoint() => Environment.GetEnvironmentVariable("KEYVAULT_ENDPOINT");
    ```

7. Adicione o URL do cofre de chaves para launchsettings.jsno ficheiro. O nome variável ambiente *KEYVAULT_ENDPOINT* é definido no código adicionado no passo 7.

    ![Adicione o URL do Cofre de Chaves como uma variável do ambiente do projeto](../media/vs-secure-secret-appsettings/add-keyvault-url.png)

8. Comece a depurar o projeto. Deve funcionar com sucesso.

## <a name="aspnet-and-net-applications"></a>ASP.NET e .NET

.NET 4.7.1 suporta porta-chaves e construtores de configuração Secret, que garante que os segredos podem ser movidos para fora da pasta de controlo de fontes sem alterações de código.
Para continuar, [baixe .NET 4.7.1](https://www.microsoft.com/download/details.aspx?id=56115) e emigre a sua aplicação se estiver a utilizar uma versão mais antiga do quadro .NET.

### <a name="save-secret-settings-in-a-secret-file-that-is-outside-of-source-control-folder"></a>Guarde as definições secretas num ficheiro secreto que está fora da pasta de controlo de fontes
Se está a escrever um protótipo rápido e não quer providenciar recursos da Azure, vá com esta opção.

1. Clique no projeto e **selecione Gerir segredos de utilizador.** Isto irá instalar um pacote NuGet **Microsoft.Configuration.ConfigurationBuilders.UserSecrets,** criar um ficheiro para guardar definições secretas fora do ficheiro web.config e adicionar uma secção **ConfigBuilders** no ficheiro web.config.

2. Coloque as definições secretas sob o elemento raiz. abaixo é um exemplo

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <root>
      <secrets ver="1.0">
        <secret name="secret" value="foo"/>
        <secret name="secret1" value="foo_one" />
        <secret name="secret2" value="foo_two" />
      </secrets>
    </root>
    ```

3. Especificar aplicaçõesA secção de configuração está a utilizar o construtor de configurações secreta. Certifique-se de que existe uma entrada para a definição secreta com um valor falso.

    ```xml
        <appSettings configBuilders="Secrets">
            <add key="webpages:Version" value="3.0.0.0" />
            <add key="webpages:Enabled" value="false" />
            <add key="ClientValidationEnabled" value="true" />
            <add key="UnobtrusiveJavaScriptEnabled" value="true" />
            <add key="secret" value="" />
        </appSettings>
    ```

5. Depurar a sua aplicação. Deve funcionar com sucesso.

### <a name="save-secret-settings-in-an-azure-key-vault"></a>Guarde as configurações secretas num cofre de chaves Azure
Siga as instruções da secção central ASP.NET configurar um Cofre-Chave para o seu projeto.

1. Instale o seguinte pacote NuGet no seu projeto
   ```
   Microsoft.Configuration.ConfigurationBuilders.Azure
   ```

2. Defina o construtor de configuração do Cofre de Chaves em Web.config. Coloque esta secção antes da secção *de appsSettings.* Substitua *o nome do cofre* para ser o nome do Cofre de Chaves se o seu Cofre de Chaves estiver no Azure global, ou URI completo se estiver a usar a nuvem soberana.

    ```xml
     <configBuilders>
        <builders>
            <add name="Secrets" userSecretsId="695823c3-6921-4458-b60b-2b82bbd39b8d" type="Microsoft.Configuration.ConfigurationBuilders.UserSecretsConfigBuilder, Microsoft.Configuration.ConfigurationBuilders.UserSecrets, Version=2.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" />
            <add name="AzureKeyVault" vaultName="[VaultName]" type="Microsoft.Configuration.ConfigurationBuilders.AzureKeyVaultConfigBuilder, Microsoft.Configuration.ConfigurationBuilders.Azure, Version=2.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" />
        </builders>
      </configBuilders>
    ```
3. Especificar aplicaçõesA secção de configuração está a utilizar o construtor de configuração key Vault. Certifique-se de que existe alguma entrada para a definição secreta com um valor falso.

   ```xml
   <appSettings configBuilders="AzureKeyVault">
       <add key="webpages:Version" value="3.0.0.0" />
       <add key="webpages:Enabled" value="false" />
       <add key="ClientValidationEnabled" value="true" />
       <add key="UnobtrusiveJavaScriptEnabled" value="true" />
       <add key="secret" value="" />
   </appSettings>
   ```

4. Comece a depurar o projeto. Deve funcionar com sucesso.
