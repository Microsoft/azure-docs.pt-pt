---
title: Gerir utilizadores com a Microsoft Graph API
titleSuffix: Azure AD B2C
description: Como gerir os utilizadores num inquilino AZure AD B2C, ligando para a Microsoft Graph API e usando uma identidade de aplicação para automatizar o processo.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/13/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ff3cd858de86d21637f4a7a9ab9d9a83c7022f5a
ms.sourcegitcommit: c136985b3733640892fee4d7c557d40665a660af
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/13/2021
ms.locfileid: "98178879"
---
# <a name="manage-azure-ad-b2c-user-accounts-with-microsoft-graph"></a>Gerir contas de utilizadores AZure AD B2C com Gráfico microsoft

O Microsoft Graph permite-lhe gerir as contas de utilizador no seu diretório AZure AD B2C, fornecendo métodos de criação, leitura, atualização e eliminação no Microsoft Graph API. Pode migrar uma loja de utilizadores existente para um inquilino AZure AD B2C e realizar outras operações de gestão de conta de utilizador, ligando para a Microsoft Graph API.

Nas secções que se seguem, são apresentados os principais aspetos da gestão do utilizador Azure AD B2C com a API do Gráfico microsoft. As operações, tipos e propriedades da Microsoft Graph API aqui apresentadas são um subconjunto do que aparece na documentação de referência da Microsoft Graph API.

## <a name="register-a-management-application"></a>Registar um pedido de gestão

Antes de qualquer aplicação de gestão de utilizador ou script que escrever pode interagir com os recursos no seu inquilino Azure AD B2C, precisa de um registo de pedido que conceda as permissões para o fazer.

Siga os passos neste artigo de como fazer para criar um registo de candidatura que o seu pedido de gestão pode utilizar:

[Gerir Azure AD B2C com o Microsoft Graph](microsoft-graph-get-started.md)

## <a name="user-management-microsoft-graph-operations"></a>Gestão de utilizadores Operações do Microsoft Graph

As seguintes operações de gestão de utilizadores estão disponíveis na [Microsoft Graph API:](/graph/api/resources/user)

- [Obtenha uma lista de utilizadores](/graph/api/user-list)
- [Criar um utilizador](/graph/api/user-post-users)
- [Obter um utilizador](/graph/api/user-get)
- [Atualizar um utilizador](/graph/api/user-update)
- [Eliminar um utilizador](/graph/api/user-delete)


## <a name="code-sample-how-to-programmatically-manage-user-accounts"></a>Amostra de código: Como gerir programáticamente as contas dos utilizadores

Esta amostra de código é uma aplicação de consola .NET Core que utiliza o [Microsoft Graph SDK](/graph/sdks/sdks-overview) para interagir com a Microsoft Graph API. O seu código demonstra como chamar a API para gerir programaticamente os utilizadores num inquilino AZure AD B2C.
Você pode [baixar o arquivo de amostras](https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management/archive/master.zip) (*.zip), navegar no [repositório](https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management) no GitHub, ou clonar o repositório:

```cmd
git clone https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management.git
```

Depois de obter a amostra de código, configuure-a para o seu ambiente e, em seguida, construa o projeto:

1. Abra o projeto em [Visual Studio](https://visualstudio.microsoft.com) ou Visual [Studio Code.](https://code.visualstudio.com)
1. Abra `src/appsettings.json`.
1. Na `appSettings` secção, `your-b2c-tenant` substitua-se pelo nome do seu inquilino, `Application (client) ID` e `Client secret` pelos valores para o seu registo de candidatura de gestão (consulte o Registo de uma secção de candidatura de [gestão](#register-a-management-application) deste artigo).
1. Abra uma janela de consola dentro do clone local do repo, mude para o `src` diretório e, em seguida, construa o projeto:
    ```console
    cd src
    dotnet build
    ```
1. Executar a aplicação com o `dotnet` comando:

    ```console
    dotnet bin/Debug/netcoreapp3.1/b2c-ms-graph.dll
    ```

A aplicação apresenta uma lista de comandos que pode executar. Por exemplo, obter todos os utilizadores, obter um único utilizador, excluir um utilizador, atualizar a palavra-passe de um utilizador e importar em massa.

### <a name="code-discussion"></a>Discussão de código

O código de amostra utiliza o [Microsoft Graph SDK,](/graph/sdks/sdks-overview)que foi concebido para simplificar a construção de aplicações de alta qualidade, eficientes e resilientes que acedem ao Microsoft Graph.

Qualquer pedido à API do Gráfico microsoft requer um token de acesso para autenticação. A solução utiliza o pacote [Microsoft.Graph.Auth](https://www.nuget.org/packages/Microsoft.Graph.Auth/) NuGet que fornece um invólucro baseado em cenários de autenticação da Microsoft Authentication Library (MSAL) para utilização com o Microsoft Graph SDK.

O `RunAsync` método no ficheiro _Program.cs:_

1. Lê as definições de aplicação a partir do _appsettings.jsno_ ficheiro
1. Inicializa o fornecedor de auth utilizando o fluxo [de concessão de credenciais de cliente OAuth 2.0.](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md) Com o fluxo de concessão de credenciais do cliente, a aplicação é capaz de obter um token de acesso para ligar para a Microsoft Graph API.
1. Configura o cliente de serviço Microsoft Graph com o fornecedor de auth:

    ```csharp
    // Read application settings from appsettings.json (tenant ID, app ID, client secret, etc.)
    AppSettings config = AppSettingsFile.ReadFromJsonFile();

    // Initialize the client credential auth provider
    IConfidentialClientApplication confidentialClientApplication = ConfidentialClientApplicationBuilder
        .Create(config.AppId)
        .WithTenantId(config.TenantId)
        .WithClientSecret(config.ClientSecret)
        .Build();
    ClientCredentialProvider authProvider = new ClientCredentialProvider(confidentialClientApplication);

    // Set up the Microsoft Graph service client with client credentials
    GraphServiceClient graphClient = new GraphServiceClient(authProvider);
    ```

O *GraphServiceClient* inicializado é então utilizado em _UserService.cs_ para realizar as operações de gestão do utilizador. Por exemplo, obter uma lista das contas de utilizador no arrendatário:

```csharp
public static async Task ListUsers(GraphServiceClient graphClient)
{
    Console.WriteLine("Getting list of users...");

    // Get all users (one page)
    var result = await graphClient.Users
        .Request()
        .Select(e => new
        {
            e.DisplayName,
            e.Id,
            e.Identities
        })
        .GetAsync();

    foreach (var user in result.CurrentPage)
    {
        Console.WriteLine(JsonConvert.SerializeObject(user));
    }
}
```

[Fazer chamadas API utilizando os SDKs do Microsoft Graph](/graph/sdks/create-requests) inclui informações sobre como ler e escrever informações a partir do Microsoft Graph, usar `$select` para controlar as propriedades devolvidas, fornecer parâmetros de consulta personalizados e usar os `$filter` parâmetros e `$orderBy` consultar.

## <a name="next-steps"></a>Passos seguintes

Para obter um índice completo das operações da Microsoft Graph API suportadas para os recursos AZure AD B2C, consulte [as operações do Microsoft Graph disponíveis para Azure AD B2C](microsoft-graph-operations.md).

<!-- LINK -->

[graph-objectIdentity]: /graph/api/resources/objectidentity
[graph-user]: (https://docs.microsoft.com/graph/api/resources/user)
