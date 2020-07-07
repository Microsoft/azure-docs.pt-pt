---
title: Adicione suporte Key Vault ao seu projeto ASP.NET utilizando o Visual Studio - Azure Key Vault ; Microsoft Docs
description: Use este tutorial para ajudá-lo a aprender a adicionar suporte key Vault a uma aplicação web ASP.NET ou ASP.NET Core.
services: key-vault
author: ghogen
manager: jillfra
ms.service: key-vault
ms.custom: vs-azure
ms.topic: conceptual
ms.date: 08/07/2019
ms.author: ghogen
ms.openlocfilehash: af0065db087595167ca71bb79b968cc4ad339acd
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "82116847"
---
# <a name="add-key-vault-to-your-web-application-by-using-visual-studio-connected-services"></a>Adicione o Cofre chave à sua aplicação web utilizando serviços conectados ao estúdio visual

Neste tutorial, você aprenderá a adicionar facilmente tudo o que precisa para começar a usar o Azure Key Vault para gerir os seus segredos para projetos web em Visual Studio, quer esteja a usar ASP.NET Core ou qualquer tipo de projeto ASP.NET. Ao utilizar a funcionalidade Serviços Conectados no Estúdio Visual, pode ter o Visual Studio a adicionar automaticamente todos os pacotes e configurações de configuração do NuGet que necessita para ligar ao Key Vault em Azure.

Para mais detalhes sobre as alterações que os Serviços Conectados fazem no seu projeto para ativar o Key Vault, consulte [Key Vault Connected Service - O que aconteceu com o meu projeto ASP.NET 4.7.1 ou key](#how-your-aspnet-framework-project-is-modified) Vault Connected Service - O que aconteceu com o meu projeto core [ASP.NET](#how-your-aspnet-core-project-is-modified).

## <a name="prerequisites"></a>Pré-requisitos

- **Uma assinatura Azure**. Se não tiver uma subscrição, inscreva-se para uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).
- **Visual Studio 2019 versão 16.3** ou posterior, ou **Visual Studio 2017 versão 15.7** com a carga de trabalho **de Desenvolvimento Web** instalada. [Transfira-a agora](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).
- Para ASP.NET (não Core) com o Visual Studio 2017, precisa do Quadro .NET 4.7.1 ou mais tarde das Ferramentas de Desenvolvimento, que não são instaladas por padrão. Para instalá-los, lançar o Instalador de Estúdio Visual, escolher **Modificar,** e depois escolher **Componentes Individuais**, em seguida, no lado direito, expandir **ASP.NET e desenvolvimento web**, e escolher **.NET Framework 4.7.1 Ferramentas de Desenvolvimento**.
- Um ASP.NET 4.7.1 ou mais tarde, ou ASP.NET Core 2.0 ou mais tarde web project open.

## <a name="add-key-vault-support-to-your-project"></a>Adicione o suporte do Key Vault ao seu projeto

Antes de começar, certifique-se de que está inscrito no Estúdio Visual. Faça sedundo com a mesma conta que utiliza para a sua subscrição Azure. Em seguida, abra um projeto web 4.7.1 ou ASP.NET ASP.NET 4.7.1 ou posterior, ou ASP.NET Core 2.0, e faça os seguintes passos:

1. No **Solution Explorer,** clique com o botão direito no projeto a que pretende adicionar o suporte do Cofre de Chaves e escolha **Add**  >  **Connected Service**.
   É apresentada a página de Connected Service (Serviço Ligado) com os serviços que pode adicionar ao seu projeto.
1. No menu dos serviços disponíveis, escolha **Secure Secrets With Azure Key Vault**.

   ![Escolha "Segredos Seguros Com Cofre de Chaves Azure"](../media/vs-key-vault-add-connected-service/KeyVaultConnectedService1.PNG)

1. Selecione a subscrição que pretende utilizar e, em seguida, escolha um cofre de chaves novo ou existente. Se escolher o novo Key Vault, aparece um link **Editar.** Selecione-o para configurar o seu novo Cofre de Chaves.

   ![Selecionar a sua subscrição](../media/vs-key-vault-add-connected-service/key-vault-connected-service-select-vault.png)

1. No **EditAr Azure Key Vault,** insira o nome que pretende utilizar para o Cofre de Chaves.

1. Selecione um Grupo de **Recursos**existente, ou opte por criar um novo com um nome único gerado automaticamente.  Se pretender criar um novo grupo com um nome diferente, pode utilizar o [portal Azure](https://portal.azure.com), e depois fechar a página e reiniciar para recarregar a lista de grupos de recursos.
1. Escolha a **localização** para criar o Cofre da Chave. Se a sua aplicação web estiver hospedada no Azure, escolha a região que acolhe a aplicação web para um desempenho ideal.
1. Escolha um **nível de preços**. Para mais detalhes, consulte [o Preço do Cofre chave](https://azure.microsoft.com/pricing/details/key-vault/).
1. Escolha **OK** para aceitar as escolhas de configuração.
1. Depois de selecionar um Cofre de Chaves existente ou ter configurado um novo Cofre de Chaves, no **separador Azure Key Vault** do Visual Studio, selecione **Adicionar** para adicionar o Serviço Conectado.
1. Selecione os **segredos de Gestão armazenados neste** link Key Vault para abrir a página **Secrets** para o seu Cofre de Chaves. Se fechou a página ou o projeto, pode navegar para ele no [portal Azure,](https://portal.azure.com) escolhendo **Todos os Serviços** e, em **Segurança,** escolhendo **o Key Vault,** e escolha o seu Cofre-Chave.
1. Na secção Cofre-Chave para o Cofre-Chave que criou, escolha **Segredos**e, em seguida, **Gere/Importe**.

   ![Gerar/Importar um segredo](../media/vs-key-vault-add-connected-service/azure-generate-secrets.png)

1. Introduza um segredo, como *o MySecret* e dê-lhe qualquer valor de cadeia como teste e, em seguida, selecione o botão **Criar.**

   ![Criar um segredo](../media/vs-key-vault-add-connected-service/azure-create-a-secret.png)

1. (opcional) Introduza outro segredo, mas desta vez colocá-lo numa categoria, *nomeando-o Secrets-- MySecret*. Esta sintaxe especifica uma categoria "Segredos" que contém um "MySecret" secreto.

Agora, pode aceder aos seus segredos em código. Os próximos passos são diferentes dependendo se você está usando ASP.NET 4.7.1 ou ASP.NET Core.

## <a name="access-your-secrets-in-code-aspnet-core"></a>Aceda aos seus segredos em código (ASP.NET Core)

1. No Solution Explorer, clique com o botão direito no seu projeto e **selecione Gerir Pacotes NuGet**. No separador **Browse,** localize e instale estes dois pacotes NuGet: [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) e para .NET Core 2, adicione [Microsoft.Azure.KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault) ou para .NET Core 3, adicione[Microsoft.Azure.KeyVault.Core](https://www.nuget.org/packages/Microsoft.Azure.KeyVault.Core).

1. Para .NET Core 2, selecione o `Program.cs` separador e altere a `BuildWebHost` definição na classe Programa para o seguinte:

   ```csharp
        public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
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
            ).UseStartup<Startup>();

        private static string GetKeyVaultEndpoint() => "https://<YourKeyVaultName>.vault.azure.net";
    }
   ```

   Para .NET Core 3, utilize o seguinte código.

   ```csharp
        public static IHostBuilder CreateHostBuilder(string[] args) =>
            Host.CreateDefaultBuilder(args)
                .ConfigureAppConfiguration((context, config) =>
                {
                    var keyVaultEndpoint = GetKeyVaultEndpoint();
                    if (!string.IsNullOrEmpty(keyVaultEndpoint))
                    {
                        var azureServiceTokenProvider = new AzureServiceTokenProvider();
                        var keyVaultClient = new KeyVaultClient(
                            new KeyVaultClient.AuthenticationCallback(
                                azureServiceTokenProvider.KeyVaultTokenCallback));
                        config.AddAzureKeyVault(keyVaultEndpoint, keyVaultClient, new DefaultKeyVaultSecretManager());
                    }
                })
                .ConfigureWebHostDefaults(webBuilder =>
                {
                    webBuilder.UseStartup<Startup>();
                });
        private static string GetKeyVaultEndpoint() => "https://<YourKeyVaultName>.vault.azure.net";
    ```

1. Em seguida, abra um dos ficheiros de página, como *Index.cshtml.cs* e escreva o seguinte código:
   1. Incluir uma referência `Microsoft.Extensions.Configuration` a esta diretiva:

       ```csharp
       using Microsoft.Extensions.Configuration;
       ```

   1. Adicione a variável de configuração.

      ```csharp
      private static IConfiguration _configuration;
      ```

   1. Adicione este construtor ou substitua o construtor existente por este:

       ```csharp
       public IndexModel(IConfiguration configuration)
       {
           _configuration = configuration;
       }
       ```

   1. Atualize o método `OnGet`. Atualize o valor do espaço reservado mostrado aqui com o nome secreto que criou nos comandos acima.

       ```csharp
       public void OnGet()
       {
           ViewData["Message"] = "My key val = " + _configuration["<YourSecretNameThatWasCreatedAbove>"];
       }
       ```

   1. Para confirmar o valor no tempo de execução, adicione código para visualizar `ViewData["Message"]` no ficheiro *.cshtml* para exibir o segredo numa mensagem.

      ```cshtml
          <p>@ViewData["Message"]</p>
      ```

Pode executar a aplicação localmente para verificar se o segredo é obtido com sucesso a partir do Cofre de Chaves.

## <a name="access-your-secrets-aspnet"></a>Aceda aos seus segredos (ASP.NET)

Pode configurar a configuração de modo a que o ficheiro web.config tenha um valor falso no `appSettings` elemento que é substituído pelo verdadeiro valor no tempo de execução. Em seguida, pode aceder a isso através da estrutura de `ConfigurationManager.AppSettings` dados.

1. Edite o seu ficheiro web.config.  Encontre a etiqueta de definições de aplicativos, adicione um atributo `configBuilders="AzureKeyVault"` e adicione uma linha:

   ```xml
      <add key="mysecret" value="dummy"/>
   ```

1. Editar o `About` método em *HomeController.cs*, para mostrar o valor para confirmação.

   ```csharp
   public ActionResult About()
   {
       ViewBag.Message = "Key vault value = " + ConfigurationManager.AppSettings["mysecret"];
   }
   ```
1. Executar a aplicação localmente sob o depurador, mude para o separador **Sobre** e verifique se o valor do Cofre de Chaves é apresentado.

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, elimine o grupo de recursos. Isto elimina o Cofre-Chave e recursos relacionados. Para eliminar o grupo de recursos através do portal:

1. O nome do grupo de recursos na caixa Pesquisar, na parte superior do portal. Quando vir o grupo de recursos utilizado neste início rápido nos resultados da pesquisa, selecione-o.
2. Selecione **Eliminar grupo de recursos**.
3. No **TIPO O NOME DO GRUPO DE RECURSOS:** caixa, insira em nome do grupo de recursos e selecione **Delete**.

## <a name="troubleshooting"></a>Resolução de problemas

Se o seu Key Vault estiver a funcionar numa conta Microsoft diferente da que está a iniciar sessão no Visual Studio (por exemplo, o Key Vault está a funcionar na sua conta de trabalho, mas o Visual Studio está a utilizar a sua conta privada) obtém um erro no seu ficheiro Program.cs, que o Visual Studio não consegue aceder ao Cofre de Chaves. Para resolver este problema:

1. Vá ao [portal Azure](https://portal.azure.com) e abra o cofre da chave.

1. Escolha **as políticas de Acesso**, em **seguida, Adicione a Política de Acesso**e escolha a conta com a qual está iniciado como Principal.

1. No Estúdio Visual, escolha **File**  >  **Definições de Conta de Ficheiros**.
**Selecione Adicionar uma conta** na secção Todas as **contas.** Inscreva-se na conta que escolheu como Principal da sua política de acesso.

1. Escolha **Tools**  >  **opções de ferramentas**e procure **a autenticação do serviço Azure.** Em seguida, selecione a conta que acabou de adicionar ao Visual Studio.

Agora, quando desativa a sua aplicação, o Visual Studio conecta-se à conta onde o cofre da chave está localizado.

## <a name="how-your-aspnet-core-project-is-modified"></a>Como o seu projeto ASP.NET Core é modificado

Esta secção identifica as alterações exatas feitas a um projeto de ASP.NET ao adicionar o serviço de ligação key Vault utilizando o Visual Studio.

### <a name="added-references-for-aspnet-core"></a>Referências adicionais para ASP.NET Core

Afeta o ficheiro do projeto .NET referências e referências de pacotes NuGet.

| Tipo | Referência |
| --- | --- |
| NuGet | Microsoft.AspNetCore.AzureKeyVault.HostingStartup |

### <a name="added-files-for-aspnet-core"></a>Ficheiros adicionados para ASP.NET Core

- `ConnectedService.json`acrescentou, que regista algumas informações sobre o fornecedor de Serviços Conectados, versão e um link a documentação.

### <a name="project-file-changes-for-aspnet-core"></a>Alterações de ficheiros de projeto para ASP.NET Core

- Adicionei o ItemGroup de Serviços Conectados e `ConnectedServices.json` o ficheiro.

### <a name="launchsettingsjson-changes-for-aspnet-core"></a>launchsettings.jssobre alterações para ASP.NET Core

- Adicionou as seguintes entradas variáveis de ambiente ao perfil IIS Express e ao perfil que corresponde ao nome do seu projeto web:

    ```json
      "environmentVariables": {
        "ASPNETCORE_HOSTINGSTARTUP__KEYVAULT__CONFIGURATIONENABLED": "true",
        "ASPNETCORE_HOSTINGSTARTUP__KEYVAULT__CONFIGURATIONVAULT": "<your keyvault URL>"
      }
    ```

### <a name="changes-on-azure-for-aspnet-core"></a>Alterações no Azure para ASP.NET Core

- Criei um grupo de recursos (ou utilizou um existente).
- Criei um Cofre-Chave no grupo de recursos especificado.

## <a name="how-your-aspnet-framework-project-is-modified"></a>Como o seu projeto-quadro de ASP.NET é modificado

Esta secção identifica as alterações exatas feitas a um projeto de ASP.NET ao adicionar o serviço de ligação key Vault utilizando o Visual Studio.

### <a name="added-references-for-aspnet-framework"></a>Referências adicionais para o Quadro ASP.NET

Afeta o ficheiro do projeto .NET referências e `packages.config` (referências NuGet).

| Tipo | Referência |
| --- | --- |
| .NET; NuGet | Microsoft.Azure.KeyVault |
| .NET; NuGet | Microsoft.Azure.keyVault.WebKey |
| .NET; NuGet | Microsoft.Rest.ClientRuntime |
| .NET; NuGet | Microsoft.Rest.ClientRuntime.Azure |

### <a name="added-files-for-aspnet-framework"></a>Ficheiros adicionados para ASP.NET Framework

- `ConnectedService.json`acrescentou, que regista algumas informações sobre o fornecedor de Serviços Conectados, versão e um link para a documentação.

### <a name="project-file-changes-for-aspnet-framework"></a>Alterações de ficheiros de projeto para ASP.NET Framework

- Adicionei o ItemGroup de Serviços Conectados e ConnectedServices.jsem ficheiro.
- Referências aos conjuntos .NET descritos na secção [Referências Adicionadas.](#added-references-for-aspnet-framework)

### <a name="webconfig-or-appconfig-changes"></a>alterações web.config ou app.config

- Adicionei as seguintes entradas de configuração:

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

### <a name="changes-on-azure-for-aspnet-framework"></a>Alterações no Azure para o Quadro ASP.NET

- Criei um grupo de recursos (ou utilizou um existente).
- Criei um Cofre-Chave no grupo de recursos especificado.

## <a name="next-steps"></a>Passos seguintes

Se seguiu este tutorial, as suas permissões key Vault estão configuras para funcionar com a sua própria assinatura Azure, mas isso pode não ser desejável para um cenário de produção. Pode criar uma identidade gerida para gerir o acesso do Key Vault à sua aplicação. Consulte [a autenticação do Cofre de Chaves com uma identidade gerida](/azure/key-vault/managed-identity).

Saiba mais sobre o desenvolvimento do Key Vault lendo o [Guia do Desenvolvedor de Cofres de Chaves.](developers-guide.md)
