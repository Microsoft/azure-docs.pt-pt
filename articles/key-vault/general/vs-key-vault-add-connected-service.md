---
title: Adicione o suporte key Vault ao seu projeto de ASP.NET utilizando o Visual Studio - Azure Key Vault | Microsoft Docs
description: Use este tutorial para ajudá-lo a aprender a adicionar suporte key Vault a uma aplicação web ASP.NET ou ASP.NET Core.
services: key-vault
author: ghogen
manager: jillfra
ms.service: key-vault
ms.custom: vs-azure, devx-track-csharp
ms.topic: how-to
ms.date: 08/07/2019
ms.author: ghogen
ms.openlocfilehash: 9c62534acdbfbff7fd4e718bad1f07a92c641626
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92792400"
---
# <a name="add-key-vault-to-your-web-application-by-using-visual-studio-connected-services"></a>Adicione o Cofre chave à sua aplicação web utilizando serviços conectados ao estúdio visual

Neste tutorial, você aprenderá a adicionar facilmente tudo o que precisa para começar a usar o Azure Key Vault para gerir os seus segredos para projetos web em Visual Studio, quer esteja a usar ASP.NET Core ou qualquer tipo de projeto ASP.NET. Ao utilizar a funcionalidade Serviços Conectados no Estúdio Visual, pode ter o Visual Studio a adicionar automaticamente todos os pacotes e configurações de configuração do NuGet que necessita para ligar ao Key Vault em Azure.

Para mais detalhes sobre as alterações que os Serviços Conectados fazem no seu projeto para ativar o Key Vault, consulte [Key Vault Connected Service - O que aconteceu com o meu projeto ASP.NET 4.7.1 ou key](#how-your-aspnet-framework-project-is-modified) Vault Connected Service - O que aconteceu com o meu projeto core [ASP.NET](#how-your-aspnet-core-project-is-modified).

## <a name="prerequisites"></a>Pré-requisitos

- **Uma assinatura Azure**. Se não tiver uma subscrição, inscreva-se para uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).
- **Visual Studio 2019 versão 16.3** ou mais tarde [Descarregá-lo agora](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).


## <a name="add-key-vault-support-to-your-project"></a>Adicione o suporte do Key Vault ao seu projeto

Antes de começar, certifique-se de que está inscrito no Estúdio Visual. Faça sedundo com a mesma conta que utiliza para a sua subscrição Azure. Em seguida, abra um projeto web 4.7.1 ou ASP.NET ASP.NET 4.7.1 ou posterior, ou ASP.NET Core 2.0, e faça os seguintes passos:

1. No **Solution Explorer,** clique com o botão direito no projeto a que pretende adicionar o suporte do Cofre de Chaves e escolha **Add**  >  **Connected Service**  >  **Add**.
   É apresentada a página de Connected Service (Serviço Ligado) com os serviços que pode adicionar ao seu projeto.
1. No menu dos serviços disponíveis, escolha **Azure Key Vault** e clique em **Next**.

   ![Escolha "Azure Key Vault"](../media/vs-key-vault-add-connected-service/key-vault-connected-service.png)

1. Selecione a subscrição que pretende utilizar e, em seguida, escolha um Cofre de Chaves existente e clique em **Terminar**. 

   ![Selecione a sua subscrição](../media/vs-key-vault-add-connected-service/key-vault-connected-service-select-vault.png)

Agora, a ligação com o Key Vault está estabelecida e pode aceder aos seus segredos em código. Os próximos passos são diferentes dependendo se você está usando ASP.NET 4.7.1 ou ASP.NET Core.

## <a name="access-your-secrets-in-code-aspnet-core"></a>Aceda aos seus segredos em código (ASP.NET Core)

1. Abra um dos ficheiros de página, como *Index.cshtml.cs* e escreva o seguinte código:
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
           ViewData["Message"] = "My key val = " + _configuration["<YourSecretNameStoredInKeyVault>"];
       }
       ```

   1. Para confirmar o valor no tempo de execução, adicione código para visualizar `ViewData["Message"]` no ficheiro *.cshtml* para exibir o segredo numa mensagem.

      ```cshtml
          <p>@ViewData["Message"]</p>
      ```

Pode executar a aplicação localmente para verificar se o segredo é obtido com sucesso a partir do Cofre de Chaves.

## <a name="access-your-secrets-aspnet"></a>Aceda aos seus segredos (ASP.NET)
Pode configurar a configuração de modo a que o ficheiro web.config tenha um valor falso no `appSettings` elemento que é substituído pelo verdadeiro valor no tempo de execução. Em seguida, pode aceder a isso através da estrutura de `ConfigurationManager.AppSettings` dados.

1. No Solution Explorer, clique com o botão direito no seu projeto e selecione Gerir Pacotes NuGet. No separador Procurar, localizar e instalar [Microsoft.Configuration.ConfigurationBuilders.Azure](https://www.nuget.org/packages/Microsoft.Configuration.ConfigurationBuilders.Azure/)
 
1. Abra o seu ficheiro web.config e escreva o seguinte código:
    1. Adicione `configSections` `configBuilders` e:
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
    1. Encontre a etiqueta de definições de aplicativos, adicione um atributo `configBuilders="AzureKeyVault"` e adicione uma linha:
        ```xml
         <add key="<secretNameInYourKeyVault>" value="dummy"/>
        ```

1. Editar o `About` método no *HomeController.cs,* para mostrar o valor para confirmação.

   ```csharp
   public ActionResult About()
   {
       ViewBag.Message = "Key vault value = " + ConfigurationManager.AppSettings["<secretNameInYourKeyVault>"];
   }
   ```
1. Executar a aplicação localmente sob o depurador, mude para o separador **Sobre** e verifique se o valor do Cofre de Chaves é apresentado.

## <a name="troubleshooting"></a>Resolução de problemas

Se o seu Key Vault estiver a funcionar numa conta Microsoft diferente da que está a iniciar sessão no Visual Studio (por exemplo, o Key Vault está a funcionar na sua conta de trabalho, mas o Visual Studio está a utilizar a sua conta privada) obtém um erro no ficheiro .cs programa, que o Visual Studio não consegue aceder ao Cofre de Chaves. Para resolver este problema:

1. Vá ao [portal Azure](https://portal.azure.com) e abra o cofre da chave.

1. Escolha **as políticas de Acesso**, em **seguida, Adicione a Política de Acesso** e escolha a conta com a qual está iniciado como Principal.

1. No Estúdio Visual, escolha   >  **Definições de Conta de Ficheiros**.
**Selecione Adicionar uma conta** na secção Todas as **contas.** Inscreva-se na conta que escolheu como Principal da sua política de acesso.

1. Escolha   >  **opções de ferramentas** e procure **a autenticação do serviço Azure.** Em seguida, selecione a conta que acabou de adicionar ao Visual Studio.

Agora, quando desativa a sua aplicação, o Visual Studio conecta-se à conta onde o cofre da chave está localizado.

## <a name="how-your-aspnet-core-project-is-modified"></a>Como o seu projeto ASP.NET Core é modificado

Esta secção identifica as alterações exatas feitas a um projeto de ASP.NET ao adicionar o serviço de ligação key Vault utilizando o Visual Studio.

### <a name="added-references-for-aspnet-core"></a>Referências adicionais para ASP.NET Core

Afeta o ficheiro do projeto .NET referências e referências de pacotes NuGet.

| Tipo | Referência |
| --- | --- |
| NuGet | Microsoft.AspNetCore.AzureKeyVault.HostingStartup |

### <a name="added-files-for-aspnet-core"></a>Ficheiros adicionados para ASP.NET Core

- `ConnectedService.json` acrescentou, que regista algumas informações sobre o fornecedor de Serviços Conectados, versão e um link a documentação.

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
| .NET; NuGet | Azure.Identidade |
| .NET; NuGet | Azure.Security.KeyVault.Keys |
| .NET; NuGet | Azure.Security.KeyVault.Secrets |

> [!IMPORTANT] 
> Por defeito, a Azure.Identity 1.1.1 está instalada, que não suporta a Credencial de Estúdio Visual. Pode atualizar a referência do pacote manualmente para 1.2+ utilizar a Credencial de Estúdio Visual.

### <a name="added-files-for-aspnet-framework"></a>Ficheiros adicionados para ASP.NET Framework

- `ConnectedService.json` acrescentou, que regista algumas informações sobre o fornecedor de Serviços Conectados, versão e um link para a documentação.

### <a name="project-file-changes-for-aspnet-framework"></a>Alterações de ficheiros de projeto para ASP.NET Framework

- Adicionei o ItemGroup de Serviços Conectados e ConnectedServices.jsem ficheiro.
- Referências aos conjuntos .NET descritos na secção [Referências Adicionadas.](#added-references-for-aspnet-framework)

## <a name="next-steps"></a>Passos seguintes

Se seguiu este tutorial, as suas permissões key Vault estão configuras para funcionar com a sua própria assinatura Azure, mas isso pode não ser desejável para um cenário de produção. Pode criar uma identidade gerida para gerir o acesso do Key Vault à sua aplicação. Ver [como autenticar para o cofre](./authentication.md) de chaves e atribuir uma política de acesso ao cofre de [chaves](./assign-access-policy-portal.md).

Saiba mais sobre o desenvolvimento do Key Vault lendo o [Guia do Desenvolvedor de Cofres de Chaves.](developers-guide.md)