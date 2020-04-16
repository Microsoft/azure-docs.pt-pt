---
title: Adicione suporte key vault ao seu projeto ASP.NET usando visual studio - Cofre chave Azure [ Microsoft Docs
description: Use este tutorial para ajudá-lo a aprender a adicionar suporte key vault a uma aplicação web ASP.NET ou ASP.NET Core.
services: key-vault
author: ghogen
manager: jillfra
ms.service: key-vault
ms.custom: vs-azure
ms.topic: conceptual
ms.date: 08/07/2019
ms.author: ghogen
ms.openlocfilehash: 68f8607db25b70ea5e6bfe21a7d6e002aeb127d6
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429775"
---
# <a name="add-key-vault-to-your-web-application-by-using-visual-studio-connected-services"></a>Adicione o Cofre chave à sua aplicação web utilizando serviços conectados do Estúdio Visual

Neste tutorial, você vai aprender a adicionar facilmente tudo o que precisa para começar a usar o Azure Key Vault para gerir os seus segredos para projetos web no Visual Studio, quer esteja a usar ASP.NET Core ou qualquer tipo de projeto ASP.NET. Ao utilizar a funcionalidade Serviços Conectados no Estúdio Visual, pode ter o Visual Studio a adicionar automaticamente todos os pacotes NuGet e configurações de configuração que precisa de ligar ao Key Vault em Azure.

Para mais detalhes sobre as alterações que os Serviços Conectados fazem no seu projeto para ativar o Key Vault, consulte [o Key Vault Connected Service - O que aconteceu ao meu projeto ASP.NET 4.7.1](#how-your-aspnet-framework-project-is-modified) ou [serviço de ligação ao cofre chave - O que aconteceu com o meu projeto core ASP.NET.](#how-your-aspnet-core-project-is-modified)

## <a name="prerequisites"></a>Pré-requisitos

- **Uma subscrição Azure.** Se não tiver uma subscrição, inscreva-se para uma [conta gratuita.](https://azure.microsoft.com/pricing/free-trial/)
- **Visual Studio 2019 versão 16.3** ou mais tarde, ou **Visual Studio 2017 versão 15.7** com a carga de **trabalho do Web Development** instalada. [Transfira-a agora](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).
- Para ASP.NET (não Core) com o Visual Studio 2017, é necessário o .NET Framework 4.7.1 ou posterior Ferramentas de Desenvolvimento, que não são instaladas por padrão. Para instalá-los, lance o Instalador de Estúdio Visual, escolha **Modificar**, e depois escolha **Componentes Individuais,** em seguida, no lado direito, expanda **ASP.NET e desenvolvimento web,** e escolha **.NET Framework 4.7.1 Ferramentas**de Desenvolvimento .
- Um ASP.NET 4.7.1 ou mais tarde, ou ASP.NET Core 2.0 ou mais tarde projeto web aberto.

## <a name="add-key-vault-support-to-your-project"></a>Adicione suporte chave vault ao seu projeto

Antes de começar, certifique-se de que está inscrito no Estúdio Visual. Faça o insto com a mesma conta que utiliza para a sua subscrição Azure. Em seguida, abra uma ASP.NET 4.7.1 ou mais tarde, ou ASP.NET projeto web Core 2.0, e faça os passos seguintes:

1. No **Solution Explorer,** clique no projeto a que pretende adicionar o suporte do Cofre chave e escolha **add** > **Connected Service**.
   É apresentada a página de Connected Service (Serviço Ligado) com os serviços que pode adicionar ao seu projeto.
1. No menu de serviços disponíveis, escolha **Segredos Seguros com cofre de chaves Azure**.

   ![Escolha "Segredos Seguros com cofre de chaves azure"](../media/vs-key-vault-add-connected-service/KeyVaultConnectedService1.PNG)

1. Selecione a subscrição que pretende utilizar e, em seguida, escolha um cofre de chaves novo ou existente. Se escolher o novo Cofre chave, aparece um link **de Edição.** Selecione-o para configurar o seu novo Cofre de Chaves.

   ![Selecione a sua subscrição](../media/vs-key-vault-add-connected-service/key-vault-connected-service-select-vault.png)

1. No Cofre de **Chaves Edit Azure,** introduza o nome que pretende utilizar para o Cofre chave.

1. Selecione um **Grupo de Recursos**existente, ou opte por criar um novo com um nome único gerado automaticamente.  Se quiser criar um novo grupo com um nome diferente, pode utilizar o [portal Azure](https://portal.azure.com), e depois fechar a página e reiniciar a recarga da lista de grupos de recursos.
1. Escolha o **Local** para criar o Cofre chave. Se a sua aplicação web estiver hospedada em Azure, escolha a região que acolhe a aplicação web para um desempenho ótimo.
1. Escolha um **nível**de preços . Para mais detalhes, consulte [o preço do cofre da chave](https://azure.microsoft.com/pricing/details/key-vault/).
1. Escolha **OK** para aceitar as escolhas de configuração.
1. Depois de selecionar um Cofre de Chave existente ou de ter configurado um novo Cofre chave, no separador **Azure Key Vault** do Visual Studio, selecione **Adicionar** para adicionar o Serviço Conectado.
1. Selecione os **segredos de Gestão armazenados neste** link Key Vault para abrir a página **Segredos** para o seu Cofre chave. Se fechar a página ou o projeto, pode navegar até ela no [portal Azure,](https://portal.azure.com) escolhendo **Todos os Serviços** e, sob **segurança**, escolhendo o **Cofre chave,** então escolha o seu Cofre chave.
1. Na secção Key Vault para o Cofre chave que criou, escolha **Segredos,** em **seguida, Gere/Import**.

   ![Gerar/Importar um segredo](../media/vs-key-vault-add-connected-service/azure-generate-secrets.png)

1. Introduza um segredo, como o *MySecret* e dê-lhe qualquer valor de cadeia como teste e, em seguida, selecione o botão **Criar.**

   ![Criar um segredo](../media/vs-key-vault-add-connected-service/azure-create-a-secret.png)

1. (opcional) Insira outro segredo, mas desta vez coloque-o numa categoria, *nomeando-o Secrets -- MySecret*. Esta sintaxe especifica uma categoria "Segredos" que contém um segredo "MySecret".

Agora, podes aceder aos teus segredos em código. Os próximos passos são diferentes dependendo se está a usar ASP.NET 4.7.1 ou ASP.NET Core.

## <a name="access-your-secrets-in-code-aspnet-core"></a>Aceda aos seus segredos em código (ASP.NET Core)

1. No Solution Explorer, clique à direita no seu projeto e selecione **Gerir pacotes NuGet**. No separador **Browse,** localize e instale estes dois pacotes NuGet: [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) e para .NET Core 2, adicione [Microsoft.Azure.KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault) ou para .NET Core 3, adicione[Microsoft.Azure.KeyVault.Core](https://www.nuget.org/packages/Microsoft.Azure.KeyVault.Core).

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

1. Em seguida, abra um dos ficheiros da página, como *Index.cshtml.cs* e escreva o seguinte código:
   1. Incluir uma `Microsoft.Extensions.Configuration` referência a esta diretiva de utilização:

       ```csharp
       using Microsoft.Extensions.Configuration;
       ```

   1. Adicione a variável de configuração.

      ```csharp
      private static readonly IConfiguration _configuration;
      ```

   1. Adicione este construtor ou substitua o construtor existente por isto:

       ```csharp
       public IndexModel(IConfiguration configuration)
       {
           _configuration = configuration;
       }
       ```

   1. Atualize o método `OnGet`. Atualize o valor do espaço reservado mostrado aqui com o nome secreto que criou nos comandos acima indicados.

       ```csharp
       public void OnGet()
       {
           ViewData["Message"] = "My key val = " + _configuration["<YourSecretNameThatWasCreatedAbove>"];
       }
       ```

   1. Para confirmar o valor no prazo `ViewData["Message"]` de execução, adicione código para exibir o ficheiro *.cshtml* para exibir o segredo numa mensagem.

      ```cshtml
          <p>@ViewData["Message"]</p>
      ```

Você pode executar a aplicação localmente para verificar se o segredo é obtido com sucesso a partir do Cofre chave.

## <a name="access-your-secrets-aspnet"></a>Aceda aos seus segredos (ASP.NET)

Pode configurar a configuração de modo a que o ficheiro web.config tenha um valor falso no `appSettings` elemento que é substituído pelo verdadeiro valor no tempo de execução. Em seguida, pode `ConfigurationManager.AppSettings` aceder a isto através da estrutura de dados.

1. Edite o seu ficheiro web.config.  Encontre a etiqueta de definições `configBuilders="AzureKeyVault"`de aplicações, adicione um atributo, e adicione uma linha:

   ```xml
      <add key="mysecret" value="dummy"/>
   ```

1. Editar `About` o método em *HomeController.cs,* para mostrar o valor para confirmação.

   ```csharp
   public ActionResult About()
   {
       ViewBag.Message = "Key vault value = " + ConfigurationManager.AppSettings["mysecret"];
   }
   ```
1. Execute a aplicação localmente sob o debugger, mude para o separador **About** e verifique se o valor do Cofre chave é apresentado.

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, elimine o grupo de recursos. Isto elimina o Cofre chave e recursos relacionados. Para eliminar o grupo de recursos através do portal:

1. O nome do grupo de recursos na caixa Pesquisar, na parte superior do portal. Quando vir o grupo de recursos utilizado neste início rápido nos resultados da pesquisa, selecione-o.
2. Selecione **Eliminar grupo de recursos**.
3. No TIPO O NOME DO **GRUPO DE RECURSOS:** caixa, introduza em nome do grupo de recursos e selecione **Eliminar**.

## <a name="troubleshooting"></a>Resolução de problemas

Se o seu Key Vault estiver a funcionar numa conta microsoft diferente daquela em que está a iniciar sessão no Visual Studio (por exemplo, o Key Vault está a funcionar na sua conta de trabalho, mas o Visual Studio está a usar a sua conta privada) obtém um erro no seu ficheiro Program.cs, que o Visual Studio não pode ter acesso ao Cofre de Chaves. Para resolver este problema:

1. Vá ao [portal Azure](https://portal.azure.com) e abra o seu Cofre chave.

1. Escolha **as políticas**de Acesso , em **seguida, adicione a Política**de Acesso , e escolha a conta com a qual está a iniciar sessão como Principal.

1. No Estúdio Visual, escolha**Definições**de Conta **de Ficheiros** > .
Selecione **Adicionar uma conta** na secção All **conta.** Inscreva-se na conta que escolheu como Diretor da sua política de acesso.

1. Escolha**as opções**de **ferramentas** > e procure a autenticação do **serviço Azure.** Em seguida, selecione a conta que acabou de adicionar ao Visual Studio.

Agora, quando depura a sua aplicação, o Visual Studio conecta-se à conta onde o seu Cofre chave está localizado.

## <a name="how-your-aspnet-core-project-is-modified"></a>Como o seu projeto ASP.NET Core é modificado

Esta secção identifica as alterações exatas feitas num projeto ASP.NET ao adicionar o serviço de ligação key vault utilizando o Visual Studio.

### <a name="added-references-for-aspnet-core"></a>Referências adicionais para ASP.NET Core

Afeta o ficheiro do projeto .NET referências e referências de pacote NuGet.

| Tipo | Referência |
| --- | --- |
| NuGet | Microsoft.AspNetCore.AzureKeyVault.HostingStartup |

### <a name="added-files-for-aspnet-core"></a>Ficheiros adicionados para ASP.NET Core

- `ConnectedService.json`acrescentou, que regista algumas informações sobre o fornecedor de Serviços Conectados, versão e uma ligação da documentação.

### <a name="project-file-changes-for-aspnet-core"></a>Alterações no ficheiro do projeto para ASP.NET Core

- Adicione o ItemGroup `ConnectedServices.json` de Serviços Conectados e o ficheiro.

### <a name="launchsettingsjson-changes-for-aspnet-core"></a>lançamentos.json alterações para ASP.NET Core

- Adicionou as seguintes entradas variáveis ambientais ao perfil IIS Express e ao perfil que corresponde ao nome do seu projeto web:

    ```json
      "environmentVariables": {
        "ASPNETCORE_HOSTINGSTARTUP__KEYVAULT__CONFIGURATIONENABLED": "true",
        "ASPNETCORE_HOSTINGSTARTUP__KEYVAULT__CONFIGURATIONVAULT": "<your keyvault URL>"
      }
    ```

### <a name="changes-on-azure-for-aspnet-core"></a>Alterações no Azure para ASP.NET Core

- Criou um grupo de recursos (ou utilizou um existente).
- Criou um Cofre chave no grupo de recursos especificado.

## <a name="how-your-aspnet-framework-project-is-modified"></a>Como o seu projeto ASP.NET Framework é modificado

Esta secção identifica as alterações exatas feitas num projeto ASP.NET ao adicionar o serviço de ligação key vault utilizando o Visual Studio.

### <a name="added-references-for-aspnet-framework"></a>Referências adicionais para ASP.NET Quadro

Afeta o ficheiro do projeto `packages.config` .REFERÊNCIAS NET e (referências NuGet).

| Tipo | Referência |
| --- | --- |
| .net; Nuget | Microsoft.Azure.KeyVault |
| .net; Nuget | Microsoft.Azure.KeyVault.WebKey |
| .net; Nuget | Microsoft.Rest.ClientRuntime |
| .net; Nuget | Microsoft.Rest.ClientRuntime.Azure |

### <a name="added-files-for-aspnet-framework"></a>Arquivos adicionados para ASP.NET Framework

- `ConnectedService.json`acrescentou, que regista algumas informações sobre o fornecedor de Serviços Conectados, versão e um link para a documentação.

### <a name="project-file-changes-for-aspnet-framework"></a>Alterações nos ficheiros do projeto para ASP.NET Framework

- Adicione o ficheiro Connected Services ItemGroup e ConnectedServices.json.
- Referências aos conjuntos .NET descritos na secção [de referências adicionadas.](#added-references-for-aspnet-framework)

### <a name="webconfig-or-appconfig-changes"></a>web.config ou app.config alterações

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

### <a name="changes-on-azure-for-aspnet-framework"></a>Alterações no Azure para ASP.NET Quadro

- Criou um grupo de recursos (ou utilizou um existente).
- Criou um Cofre chave no grupo de recursos especificado.

## <a name="next-steps"></a>Passos seguintes

Se seguiu este tutorial, as suas permissões key vault estão configuradas para funcionar com a sua própria subscrição Azure, mas isso pode não ser desejável para um cenário de produção. Pode criar uma identidade gerida para gerir o acesso do Key Vault à sua aplicação. Consulte [A autenticação do Cofre chave com uma identidade gerida](/azure/key-vault/managed-identity).

Saiba mais sobre o desenvolvimento do Key Vault lendo o [Guia de Desenvolvimento do Cofre chave](developers-guide.md).
