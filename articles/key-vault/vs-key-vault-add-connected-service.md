---
title: Adicionar suporte do Key Vault ao seu projeto ASP.NET com o Visual Studio - Azure Key Vault | Documentos da Microsoft
description: Utilize este tutorial para ajudá-lo adicionar suporte de Cofre de chaves num aplicativo da web ASP.NET ou ASP.NET Core.
services: key-vault
author: ghogen
manager: jillfra
ms.prod: visual-studio
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: ghogen
ms.openlocfilehash: d95bd114be712953b79ef5afbb0915173f6de26c
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58339283"
---
# <a name="add-key-vault-to-your-web-application-by-using-visual-studio-connected-services"></a>Adicionar o Key Vault à sua aplicação web com o Visual Studio ligado Services

Neste tutorial, irá aprender como facilmente adicionar tudo o que precisa para começar a utilizar o Azure Key Vault para gerir os seus segredos para projetos web no Visual Studio, se estiver a utilizar o ASP.NET Core ou de qualquer tipo de projeto do ASP.NET. Ao utilizar a funcionalidade de serviços ligados no Visual Studio, pode ter o Visual Studio adiciona automaticamente todos os pacotes NuGet e definições de configuração, que tem de se ligar ao Cofre de chaves no Azure. 

Para obter detalhes sobre as alterações que serviços ligados feitas em seu projeto para ativar o Key Vault, consulte [chave de Cofre de serviço ligado - o que aconteceu ao meu ASP.NET 4.7.1 project](#how-your-aspnet-framework-project-is-modified) ou [chave de Cofre de serviço ligado - o que aconteceu ao meu projeto ASP.NET Core](#how-your-aspnet-core-project-is-modified).

## <a name="prerequisites"></a>Pré-requisitos

- **Uma subscrição do Azure**. Se não tiver uma conta, pode inscrever-se numa [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).
- **Visual Studio 2019** ou **Visual Studio 2017 versão 15.7** com o **desenvolvimento Web** instalada da carga de trabalho. [Transfira-a agora](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).
- Do ASP.NET (e não núcleos) com o Visual Studio 2017, terá do .NET Framework 4.7.1 ou posterior ferramentas de desenvolvimento, que não estão instaladas por predefinição. Para instalá-las, iniciar a instalação do Visual Studio, escolha **Modify**e, em seguida, escolha **componentes individuais**, em seguida, no lado direito, expanda **desenvolvimento na web e ASP.NET**e escolha **ferramentas de desenvolvimento do .NET Framework 4.7.1**.
- Um ASP.NET 4.7.1 ou posterior, ou o projeto da web do ASP.NET Core 2.0 aberto.

## <a name="add-key-vault-support-to-your-project"></a>Adicionar suporte do Key Vault ao seu projeto

1. No **Solution Explorer** (Explorador de Soluções), selecione **Add** (Adicionar)  > **Connected Service** (Serviço Ligado).
   É apresentada a página de Connected Service (Serviço Ligado) com os serviços que pode adicionar ao seu projeto.
1. No menu de serviços disponíveis, escolha **proteger segredos com o Azure Key Vault**.

   ![Escolha "Segredos seguros com o Cofre de chaves do Azure"](media/vs-key-vault-add-connected-service/KeyVaultConnectedService1.PNG)

   Se já tiver sessão iniciada no Visual Studio e uma subscrição do Azure associada à sua conta, será apresentada uma página com uma lista pendente com as suas subscrições. Certifique-se de que tem sessão iniciada para o Visual Studio, e que a conta que iniciou sessão com a mesma conta que utiliza para a sua subscrição do Azure.

1. Selecione a subscrição que pretende utilizar e, em seguida, selecione um cofre de chaves novo ou existente, ou escolha a ligação de edição para modificar o nome gerado automaticamente.

   ![Selecione a sua subscrição](media/vs-key-vault-add-connected-service/KeyVaultConnectedService3.PNG)

1. Escreva o nome que pretende utilizar para o Cofre de chaves.

   ![Mudar o nome do Key Vault e escolha um grupo de recursos](media/vs-key-vault-add-connected-service/KeyVaultConnectedService-Edit.PNG)

1. Selecione um grupo de recursos existente ou optar por criar um novo com um nome exclusivo gerado automaticamente.  Se quiser criar um novo grupo com um nome diferente, pode utilizar o [Portal do Azure](https://portal.azure.com)e, em seguida, feche a página e reiniciar para recarregar a lista de grupos de recursos.
1. Selecione a região na qual pretende criar o Key Vault. Se seu aplicativo web está alojado no Azure, selecione a região que aloja a aplicação web para um desempenho ideal.
1. Escolha um modelo de preços. Para obter detalhes, consulte [preços do Key Vault](https://azure.microsoft.com/pricing/details/key-vault/).
1. Escolha OK para aceitar as opções de configuração.
1. Escolher **adicionar** para criar o Cofre de chaves. O processo de criação poderá falhar se escolher um nome que já foi utilizado.  Se isso acontecer, utilize o **editar** ligação para mudar o nome do Key Vault e tente novamente.

   ![Adicionar serviço ligado ao projeto](media/vs-key-vault-add-connected-service/KeyVaultConnectedService4.PNG)

1. Agora, adicione um segredo no Cofre de chaves no Azure. Para obter o lugar certo, no portal, clique no link para os segredos de gerir armazenados neste Cofre de chave. Se fechou a página ou o projeto, pode navegar para ele no [portal do Azure](https://portal.azure.com) escolhendo **todos os serviços**, em **segurança**, escolha **doKeyVault**, em seguida, selecione o Cofre de chaves que criou.

   ![Navegar para o portal](media/vs-key-vault-add-connected-service/manage-secrets-link.jpg)

1. Na secção Key Vault para a chave do cofre que criou, escolha **segredos**, em seguida, **gerar/importar**.

   ![Gerar/importar um segredo](media/vs-key-vault-add-connected-service/generate-secrets.jpg)

1. Introduza um segredo, por exemplo, "MySecret" e dê a ele qualquer valor de cadeia de caracteres como um teste, em seguida, escolha o **criar** botão.

   ![Criar um segredo](media/vs-key-vault-add-connected-service/create-a-secret.jpg)

1. (opcional) Introduza outro segredo, mas desta vez colocá-la numa categoria ao nomeá-lo "Segredos – MySecret". Essa sintaxe especifica uma categoria "Segredos" que contém um segredo "MySecret."
 
Agora, pode acessar seus segredos no código. As próximas etapas são diferentes, dependendo se está a utilizar ASP.NET 4.7.1 ou ASP.NET Core.

## <a name="access-your-secrets-in-code"></a>Os segredos no código de acesso

1. Instalar estes dois pacotes de nuget [AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) e [KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault) bibliotecas NuGet.

2. Abra o ficheiro Program.cs e atualizar o código com o código a seguir: 
   ```
    public class Program
    {
        public static void Main(string[] args)
        {
            BuildWebHost(args).Run();
        }

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
               }
            ).UseStartup<Startup>()
             .Build();

        private static string GetKeyVaultEndpoint() => "https://<YourKeyVaultName>.vault.azure.net";
    }
   ```

3. Em seguida, abra o ficheiro de About.cshtml.cs e escrever o seguinte código:
   1. Inclua uma referência ao Microsoft.Extensions.Configuration por este usando a instrução:

       ```csharp
       using Microsoft.Extensions.Configuration
       ```

   1. Adicione este construtor:

       ```csharp
       public AboutModel(IConfiguration configuration)
       {
           _configuration = configuration;
       }
       ```

   1. Atualize o método OnGet. Atualize o valor do marcador de posição mostrado aqui com o nome do segredo que criou nos comandos anteriores.

       ```csharp
       public void OnGet()
       {
           //Message = "Your application description page.";
           Message = "My key val = " + _configuration["<YourSecretNameThatWasCreatedAbove>"];
       }
       ```

Execute a aplicação localmente ao navegar para a página About. Deverá ver seu valor secreto obtido.

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, elimine o grupo de recursos. Esta ação elimina o Cofre de chaves e os recursos relacionados. Para eliminar o grupo de recursos através do portal:

1. O nome do grupo de recursos na caixa Pesquisar, na parte superior do portal. Quando vir o grupo de recursos utilizado neste Início Rápido nos resultados da pesquisa, selecione-o.
2. Selecione **Eliminar grupo de recursos**.
3. Na caixa **ESCREVA O NOME DO GRUPO DE RECURSOS:**, escreva o nome do grupo de recursos e selecione **Eliminar**.

## <a name="how-your-aspnet-core-project-is-modified"></a>Como o seu projeto ASP.NET Core é modificado

Esta secção identifica as alterações de exatas feitas a um projeto do ASP.NET quando adicionar o Key Vault ligado a serviço com o Visual Studio.

### <a name="added-references"></a>Foi adicionadas referências

Afeta as referências de pacote de NuGet e referências de .NET do arquivo de projeto.

| Type | Referência |
| --- | --- |
| NuGet | Microsoft.AspNetCore.AzureKeyVault.HostingStartup |

### <a name="added-files"></a>Ficheiros adicionados

- ConnectedService.json adicionado, que regista algumas informações sobre o fornecedor de serviços ligados, versão e uma ligação a documentação.

### <a name="project-file-changes"></a>Alterações de ficheiros de projeto

- Adicionar o ficheiro de ItemGroup de serviços ligados e ConnectedServices.json.

### <a name="launchsettingsjson-changes"></a>alterações de launchsettings.JSON

- Adicionadas as seguintes entradas de variável de ambiente para o perfil de IIS Express e o perfil que corresponda ao seu nome de projeto da web:

    ```json
      "environmentVariables": {
        "ASPNETCORE_HOSTINGSTARTUP__KEYVAULT__CONFIGURATIONENABLED": "true",
        "ASPNETCORE_HOSTINGSTARTUP__KEYVAULT__CONFIGURATIONVAULT": "<your keyvault URL>"
      }
    ```

### <a name="changes-on-azure"></a>Alterações no Azure

- Criou um grupo de recursos (ou utilizar um já existente).
- Criou um Key Vault no grupo de recursos especificado.

## <a name="how-your-aspnet-framework-project-is-modified"></a>Como o seu projeto de estrutura do ASP.NET é modificado

Esta secção identifica as alterações de exatas feitas a um projeto do ASP.NET quando adicionar o Key Vault ligado a serviço com o Visual Studio.

### <a name="added-references"></a>Foi adicionadas referências

Afeta as referências de .NET do arquivo de projeto e `packages.config` (referências de NuGet).

| Type | Referência |
| --- | --- |
| .NET; NuGet | Microsoft.Azure.KeyVault |
| .NET; NuGet | Microsoft.Azure.KeyVault.WebKey |
| .NET; NuGet | Microsoft.Rest.ClientRuntime |
| .NET; NuGet | Microsoft.Rest.ClientRuntime.Azure |

### <a name="added-files"></a>Ficheiros adicionados

- ConnectedService.json adicionado, que regista algumas informações sobre o fornecedor de serviços ligados, versão e uma ligação para a documentação.

### <a name="project-file-changes"></a>Alterações de ficheiros de projeto

- Adicionar o ficheiro de ItemGroup de serviços ligados e ConnectedServices.json.
- Referências aos assemblies do .NET, descritos a [adicionado referências](#added-references) secção.

### <a name="webconfig-or-appconfig-changes"></a>alterações de Web. config ou App. config

- Adicionadas as seguintes entradas de configuração:

    ```xml
    <configSections>
      <section
           name="configBuilders"
           type="System.Configuration.ConfigurationBuildersSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a" 
           restartOnExternalChanges="false"
           requirePermission="false" />
    </configSections>
    <configBuilders>
      <builders>
        <add 
             name="AzureKeyVault"
             vaultName="vaultname"
             type="Microsoft.Configuration.ConfigurationBuilders.AzureKeyVaultConfigBuilder, Microsoft.Configuration.ConfigurationBuilders.Azure, Version=1.0.0.0, Culture=neutral" 
             vaultUri="https://vaultname.vault.azure.net" />
      </builders>
    </configBuilders>
    ```

### <a name="changes-on-azure"></a>Alterações no Azure

- Criou um grupo de recursos (ou utilizar um já existente).
- Criou um Key Vault no grupo de recursos especificado.

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre desenvolvimento de Key Vault ao ler o [Guia do programador do Cofre de chave](key-vault-developers-guide.md)