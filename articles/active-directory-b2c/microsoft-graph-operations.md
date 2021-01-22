---
title: Gerir recursos com o Microsoft Graph
titleSuffix: Azure AD B2C
description: Como gerir recursos num inquilino Azure AD B2C, ligando para a Microsoft Graph API e usando uma identidade de aplicação para automatizar o processo.
services: B2C
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/21/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 1dc5b8dc8930d75456f307324ef97bd60e78eca9
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/21/2021
ms.locfileid: "98660162"
---
# <a name="manage-azure-ad-b2c-with-microsoft-graph"></a>Gerir Azure AD B2C com o Microsoft Graph

O Microsoft Graph permite-lhe gerir recursos no seu diretório Azure AD B2C. As seguintes operações de API do Microsoft Graph são suportadas para a gestão de recursos Azure AD B2C, incluindo utilizadores, fornecedores de identidade, fluxos de utilizadores, políticas personalizadas e chaves de política. Cada link nas seguintes secções tem como alvo a página correspondente na referência API do Microsoft Graph para essa operação. 

## <a name="perquisites"></a>Perquisites

Para utilizar a MS Graph API e interagir com os recursos no seu inquilino Azure AD B2C, precisa de um registo de pedido que conceda as permissões para o fazer. Siga os passos no [Manage Azure AD B2C com](microsoft-graph-get-started.md) o artigo microsoft Graph para criar um registo de aplicação que a sua aplicação de gestão pode usar. 

## <a name="user-management"></a>Gestão de utilizadores

- [Listar utilizadores](/graph/api/user-list)
- [Criar um consumidor](/graph/api/user-post-users)
- [Obter um utilizador](/graph/api/user-get)
- [Atualizar um utilizador](/graph/api/user-update)
- [Eliminar um utilizador](/graph/api/user-delete)

## <a name="user-phone-number-management"></a>Gestão de números de telefone do utilizador

- [Adicionar](/graph/api/authentication-post-phonemethods)
- [Get](/graph/api/b2cauthenticationmethodspolicy-get)
- [Atualizar](/graph/api/b2cauthenticationmethodspolicy-update)
- [Eliminar](/graph/api/phoneauthenticationmethod-delete)

Para obter mais informações sobre a gestão do número de telefone de inscrição do utilizador, consulte os Métodos de [Autenticação B2C](/graph/api/resources/b2cauthenticationmethodspolicy).

## <a name="identity-providers-user-flow"></a>Fornecedores de identidade (fluxo de utilizador)

Gerencie os fornecedores de identidade disponíveis para os fluxos do seu utilizador no seu inquilino Azure AD B2C.

- [Fornecedores de identidade registados no inquilino Azure AD B2C](/graph/api/identityprovider-list)
- [Criar um fornecedor de identidade](/graph/api/identityprovider-post-identityproviders)
- [Obter um fornecedor de identidade](/graph/api/identityprovider-get)
- [Atualizar fornecedor de identidade](/graph/api/identityprovider-update)
- [Eliminar um fornecedor de identidade](/graph/api/identityprovider-delete)

## <a name="user-flow"></a>Fluxo de utilizador

Configure as políticas pré-construídas para inscrição, inscrição, inscrição combinada e inscrição, reset de palavra-passe e atualização de perfil.

- [Listar fluxos de utilizadores](/graph/api/identitycontainer-list-b2cuserflows)
- [Criar um fluxo de utilizador](/graph/api/identitycontainer-post-b2cuserflows)
- [Obtenha um fluxo de utilizador](/graph/api/b2cidentityuserflow-get)
- [Eliminar um fluxo de utilizador](/graph/api/b2cidentityuserflow-delete)

## <a name="custom-policies"></a>Políticas personalizadas

As seguintes operações permitem-lhe gerir as suas políticas Azure AD B2C Trust Framework, conhecidas como [políticas personalizadas.](custom-policy-overview.md)

- [Listar todas as políticas de enquadramento de confiança configuradas num inquilino](/graph/api/trustframework-list-trustframeworkpolicies)
- [Criar política de quadros de confiança](/graph/api/trustframework-post-trustframeworkpolicy)
- [Leia as propriedades de uma política de enquadramento de confiança existente](/graph/api/trustframeworkpolicy-get)
- [Atualizar ou criar uma política de enquadramento de confiança.](/graph/api/trustframework-put-trustframeworkpolicy)
- [Eliminar uma política-quadro de confiança existente](/graph/api/trustframeworkpolicy-delete)

## <a name="policy-keys"></a>Chaves da política

O Quadro de Experiência de Identidade armazena os segredos referenciados numa política personalizada para estabelecer confiança entre componentes. Estes segredos podem ser chaves/valores simétricos ou assimétricos. No portal Azure, estas entidades são mostradas como **chaves de Política**.

O recurso de alto nível para chaves de política na API do Gráfico microsoft é o [Conjunto de Chaves Quadros Fidedignos](/graph/api/resources/trustframeworkkeyset). Cada **tecla** contém pelo menos uma **chave.** Para criar uma chave, primeiro crie um tecla vazio e, em seguida, gere uma chave no tecla. Pode criar um segredo manual, fazer upload de um certificado ou uma chave PKCS12. A chave pode ser um segredo gerado, uma cadeia (como o segredo da aplicação do Facebook) ou um certificado que você carrega. Se um conjunto de teclas tiver várias teclas, apenas uma das teclas está ativa.

### <a name="trust-framework-policy-keyset"></a>Conjunto de chave de política de quadro de confiança

- [Listar os conjuntos de chaves-quadro de confiança](/graph/api/trustframework-list-keysets)
- [Criar conjuntos de chaves de quadro de confiança](/graph/api/trustframework-post-keysets)
- [Obter um tecla](/graph/api/trustframeworkkeyset-get)
- [Atualizar os chaves-chave do quadro de confiança](/graph/api/trustframeworkkeyset-update)
- [Eliminar os teclas-quadro de confiança](/graph/api/trustframeworkkeyset-delete)

### <a name="trust-framework-policy-key"></a>Chave da política do Quadro de Confiança

- [Obtenha a chave atualmente ativa no teclado](/graph/api/trustframeworkkeyset-getactivekey)
- [Gerar uma chave no tecla](/graph/api/trustframeworkkeyset-generatekey)
- [Carre faça upload de um segredo baseado em cordas](/graph/api/trustframeworkkeyset-uploadsecret)
- [Faça upload de um certificado X.509](/graph/api/trustframeworkkeyset-uploadcertificate)
- [Faça upload de um certificado de formato PKCS12](/graph/api/trustframeworkkeyset-uploadpkcs12)

## <a name="applications"></a>Aplicações

- [Listar aplicações](/graph/api/application-list)
- [Criar uma aplicação](/graph/api/resources/application)
- [Aplicação de atualização](/graph/api/application-update)
- [Criar serviçoPrincipal](/graph/api/resources/serviceprincipal)
- [Criar bolsa de oauth2Permission](/graph/api/resources/oauth2permissiongrant)
- [Excluir aplicação](/graph/api/application-delete)

## <a name="application-extension-properties"></a>Propriedades de extensão de aplicação

- [Propriedades de extensão de lista](/graph/api/application-list-extensionproperty)

O Azure AD B2C fornece um diretório que pode conter 100 atributos personalizados por utilizador. Para os fluxos de utilizador, estas propriedades de extensão são [geridas através do portal Azure](user-flow-custom-attributes.md). Para políticas personalizadas, o Azure AD B2C cria o imóvel para si, a primeira vez que a apólice escreve um valor para a propriedade de extensão.

## <a name="audit-logs"></a>Registos de auditoria

- [Listar registos de auditoria](/graph/api/directoryaudit-list)

Para obter mais informações sobre o acesso aos registos de auditoria do Azure AD B2C, consulte [os registos de auditoria do Azure AD B2C](view-audit-logs.md).

## <a name="code-sample-how-to-programmatically-manage-user-accounts"></a>Amostra de código: Como gerir programáticamente as contas dos utilizadores

Esta amostra de código é uma aplicação de consola .NET Core que utiliza o [Microsoft Graph SDK](/graph/sdks/sdks-overview) para interagir com a Microsoft Graph API. O seu código demonstra como chamar a API para gerir programaticamente os utilizadores num inquilino AZure AD B2C.
Você pode [baixar o arquivo de amostras](https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management/archive/master.zip) (*.zip), navegar no [repositório](https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management) no GitHub, ou clonar o repositório:

```cmd
git clone https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management.git
```

Depois de obter a amostra de código, configuure-a para o seu ambiente e, em seguida, construa o projeto:

1. Abra o projeto em [Visual Studio](https://visualstudio.microsoft.com) ou Visual [Studio Code.](https://code.visualstudio.com)
1. Abra `src/appsettings.json`.
1. Na `appSettings` secção, `your-b2c-tenant` substitua-se pelo nome do seu inquilino, `Application (client) ID` e `Client secret` pelos valores para o seu registo de candidatura de gestão. Para obter mais informações, consulte [registar uma aplicação de gráficos da Microsoft](microsoft-graph-get-started.md).
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

<!-- LINK -->

[graph-objectIdentity]: /graph/api/resources/objectidentity
[graph-user]: (https://docs.microsoft.com/graph/api/resources/user)
