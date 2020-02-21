---
title: Gerir utilizadores com a Microsoft Graph API
titleSuffix: Azure AD B2C
description: Como gerir os utilizadores num inquilino Azure AD B2C, ligando para a Microsoft Graph API e usando uma identidade de aplicação para automatizar o processo.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/14/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 64df1188692ae9f1b6200ad880a2bf1eafd98844
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/20/2020
ms.locfileid: "77483232"
---
# <a name="manage-azure-ad-b2c-user-accounts-with-microsoft-graph"></a>Gerir contas de utilizadores do Azure AD B2C com o Microsoft Graph

O Microsoft Graph permite-lhe gerir as contas dos utilizadores no seu diretório Azure AD B2C, fornecendo métodos de criação, leitura, atualização e eliminação no Microsoft Graph API. Pode migrar uma loja de utilizadores existente para um inquilino Azure AD B2C e realizar outras operações de gestão de conta de utilizador, ligando para a Microsoft Graph API.

Nas secções que se seguem, são apresentados os principais aspetos da gestão de utilizadores do Azure AD B2C com a Microsoft Graph API. As operações, tipos e propriedades da Microsoft Graph API aqui apresentadas são um subconjunto do que aparece na documentação de referência da Microsoft Graph API.

## <a name="register-a-management-application"></a>Registar um pedido de gestão

Antes de qualquer aplicação ou script de gestão de utilizador que escreva pode interagir com os recursos do seu inquilino Azure AD B2C, precisa de um registo de candidatura que conceda as permissões para o fazer.

Siga os passos neste artigo de como criar um registo de candidatura que a sua aplicação de gestão possa utilizar:

[Gerir o Azure AD B2C com o Microsoft Graph](microsoft-graph-get-started.md)

## <a name="user-management-microsoft-graph-operations"></a>Gestão de utilizadores Operações do Microsoft Graph

As seguintes operações de gestão de utilizadores estão disponíveis na [Microsoft Graph API:](https://docs.microsoft.com/graph/api/resources/user)

- [Obtenha uma lista de utilizadores](https://docs.microsoft.com/graph/api/user-list)
- [Criar um utilizador](https://docs.microsoft.com/graph/api/user-post-users)
- [Obter um utilizador](https://docs.microsoft.com/graph/api/user-get)
- [Atualizar um utilizador](https://docs.microsoft.com/graph/api/user-update)
- [Eliminar um utilizador](https://docs.microsoft.com/graph/api/user-delete)

## <a name="user-properties"></a>Propriedades do utilizador

### <a name="display-name-property"></a>Propriedade de nome de exibição

O `displayName` é o nome a apresentar na gestão de utilizadores do portal Azure para o utilizador, e no acesso ao Azure AD B2C devolve à aplicação. Esta propriedade é necessária.

### <a name="identities-property"></a>Propriedade de identidades

Uma conta de cliente, que pode ser um consumidor, parceiro ou cidadão, pode ser associada a estes tipos de identidade:

- Identidade **local** - O nome de utilizador e a palavra-passe são armazenados localmente no diretório Azure AD B2C. Muitas vezes referimo-nos a estas identidades como "contas locais".
- Identidade **federada** - Também conhecida como contas *sociais* ou *empresariais,* a identidade do utilizador é gerida por um fornecedor de identidade federado como Facebook, Microsoft, ADFS ou Salesforce.

Um utilizador com uma conta de cliente pode iniciar sessão com múltiplas identidades. Por exemplo, nome de utilizador, e-mail, identificação do funcionário, identificação do governo, entre outros. Uma única conta pode ter múltiplas identidades, tanto locais como sociais, com a mesma senha.

Na API do Microsoft Graph, as identidades locais e federadas são armazenadas no utilizador `identities` atributo, que é de tipo [objetoIdentidade][graph-objectIdentity]. A recolha `identities` representa um conjunto de identidades usadas para iniciar sessão numa conta de utilizador. Esta recolha permite ao utilizador iniciar sessão na conta de utilizador com qualquer uma das suas identidades associadas.

| Propriedade   | Tipo |Descrição|
|:---------------|:--------|:----------|
|signInType|Cadeia de caracteres| Especifica os tipos de sessão de sessão do utilizador no seu diretório. Para conta local: `emailAddress`, `emailAddress1`, `emailAddress2`, `emailAddress3`, `userName`, ou qualquer outro tipo que queira. A conta social deve ser fixada para `federated`.|
|emitente|Cadeia de caracteres|Especifica o emitente da identidade. Para contas locais (onde o **signInType** não é `federated`), esta propriedade é o nome de domínio padrão do inquilino B2C local, por exemplo `contoso.onmicrosoft.com`. Para a identidade social (onde o **signInType** é `federated`), o valor é o nome do emitente, por exemplo `facebook.com`|
|emitenteAssignedId|Cadeia de caracteres|Especifica o identificador único atribuído ao utilizador pelo emitente. A combinação de **emitente** e **emitente AssignedId** deve ser única dentro do seu inquilino. Para a conta local, quando o **signInType** está definido para `emailAddress` ou `userName`, representa o nome de início de sessão para o utilizador.<br>Quando **o signInType** está definido para: <ul><li>`emailAddress` (ou começa com `emailAddress` como `emailAddress1`) **emitentes AsassignedId** deve ser um endereço de e-mail válido</li><li>`userName` (ou qualquer outro valor), o **emitente AssignedId** deve ser uma parte local válida [de um endereço de e-mail](https://tools.ietf.org/html/rfc3696#section-3)</li><li>`federated`, **emitenteAssignedId** representa o identificador único da conta federada</li></ul>|

Para identidades federadas, dependendo do fornecedor de identidade, o **emitenteAssignedId** é um valor único para um determinado utilizador por aplicação ou conta de desenvolvimento. Configure a política Azure AD B2C com o mesmo ID de aplicação que foi previamente atribuído pelo prestador social ou outra aplicação dentro da mesma conta de desenvolvimento.

### <a name="password-profile-property"></a>Propriedade de perfil de palavra-passe

Para uma identidade local, a propriedade **passwordProfile** é necessária e contém a palavra-passe do utilizador. A propriedade `forceChangePasswordNextSignIn` deve ser definida para `false`.

Para uma identidade (social) federada, a propriedade **passwordProfile** não é necessária.

```JSON
"passwordProfile" : {
    "password": "password-value",
    "forceChangePasswordNextSignIn": false
  }
```

### <a name="password-policy-property"></a>Propriedade de política de senha

A política de senhas Azure AD B2C (para contas locais) baseia-se na política de força de [senha forte](../active-directory/authentication/concept-sspr-policy.md) do Azure Ative Directory. As políticas de inscrição ou inscrição ou reset de palavra-passe do Azure AD B2C requerem esta forte força de senha e não expiram palavras-passe.

Nos cenários de migração dos utilizadores, se as contas que pretende migrar tiverem uma força de senha mais fraca do que a [forte força](../active-directory/authentication/concept-sspr-policy.md) de senha imposta pelo Azure AD B2C, pode desativar o forte requisito de senha. Para alterar a política de senha padrão, desloque a propriedade `passwordPolicies` para `DisableStrongPassword`. Por exemplo, pode modificar o pedido de criação do utilizador da seguinte forma:

```JSON
"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"
```

### <a name="extension-properties"></a>Propriedades de extensão

Cada aplicação voltada para o cliente tem requisitos únicos para que a informação seja recolhida. O seu inquilino Azure AD B2C vem com um conjunto incorporado de informação armazenada em propriedades, tais como Nome Dado, Sobrenome, Cidade e Código Postal. Com o Azure AD B2C, pode alargar o conjunto de imóveis armazenados em cada conta de cliente. Para obter mais informações sobre a definição de atributos personalizados, consulte [atributos personalizados (fluxos](user-flow-custom-attributes.md) de utilizador) e [atributos personalizados (políticas personalizadas)](custom-policy-custom-attributes.md).

O Microsoft Graph API suporta a criação e atualização de um utilizador com atributos de extensão. Os atributos de extensão na API do gráfico são nomeados utilizando a convenção `extension_ApplicationObjectID_attributename`. Por exemplo:

```JSON
"extension_831374b3bd5041bfaa54263ec9e050fc_loyaltyNumber": "212342"
```

## <a name="code-sample"></a>Exemplo de código

Esta amostra de código é uma aplicação de consola .NET Core que utiliza o [Microsoft Graph SDK](https://docs.microsoft.com/graph/sdks/sdks-overview) para interagir com a Microsoft Graph API. O seu código demonstra como chamar a API para gerir programáticamente os utilizadores num inquilino Azure AD B2C.
Pode [descarregar o arquivo da amostra](https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management/archive/master.zip) (*.zip), navegar no [repositório](https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management) no GitHub ou clonar o repositório:

```cmd
git clone https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management.git
```

Depois de obter a amostra de código, configure-a para o seu ambiente e, em seguida, construa o projeto:

1. Abra o projeto em [Visual Studio](https://visualstudio.microsoft.com) ou Visual [Studio Code](https://code.visualstudio.com).
1. Abra `src/appsettings.json`.
1. Na secção `appSettings`, substitua `your-b2c-tenant` pelo nome do seu inquilino, e `Application (client) ID` e `Client secret` os valores para o seu registo de candidatura de gestão (consulte o Registo uma secção de candidatura de [gestão](#register-a-management-application) deste artigo).
1. Abra uma janela de consola dentro do seu clone local do repo, mude para o diretório `src` e, em seguida, construa o projeto:
    ```console
    cd src
    dotnet build
    ```
1. Executar a aplicação com o comando `dotnet`:

```console
dotnet bin/Debug/netcoreapp3.0/b2c-ms-graph.dll
```

A aplicação apresenta uma lista de comandos que pode executar. Por exemplo, obtenha todos os utilizadores, obtenha um único utilizador, apague um utilizador, atualize a palavra-passe de um utilizador e importe a granel.

### <a name="code-discussion"></a>Discussão de código

O código da amostra utiliza o [Microsoft Graph SDK,](https://docs.microsoft.com/graph/sdks/sdks-overview)projetado para simplificar a construção de aplicações de alta qualidade, eficientes e resilientes que acedem ao Microsoft Graph. Então, não precisas de fazer um direct all the Microsoft Graph API.

Qualquer pedido à API do Microsoft Graph requer um sinal de acesso para autenticação. A solução utiliza o pacote [Microsoft.Graph.Auth](https://www.nuget.org/packages/Microsoft.Graph.Auth/) NuGet que fornece um invólucro baseado em cenários de autenticação da Microsoft Authentication Library (MSAL) para utilização com o Microsoft Graph SDK.

O método `RunAsync` no ficheiro _Program.cs:_

1. Lê as definições de aplicação a partir do ficheiro _appsettings.json_
1. Inicializa o fornecedor de auth utilizando credenciais de [clientes OAuth 2.0 fluxo](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md) de concessão de subvenção. Com as credenciais de cliente a conceder fluxo, a aplicação é capaz de obter um sinal de acesso para ligar para a Microsoft Graph API.
1. Configura o cliente de serviço Microsoft Graph com o fornecedor auth:

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

O *GraphServiceClient* inicializado é então utilizado em _UserService.cs_ para realizar as operações de gestão do utilizador. Por exemplo, obter uma lista das contas de utilizador no inquilino:

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

[Faça chamadas API utilizando os SDKs](https://docs.microsoft.comgraph/sdks/create-requests) do Microsoft Graph incluem informações sobre como ler e escrever informações do Microsoft Graph, usar `$select` para controlar as propriedades devolvidas, fornecer parâmetros de consulta personalizados e usar os parâmetros de consulta `$filter` e `$orderBy`.

## <a name="next-steps"></a>Passos Seguintes

Para obter um índice completo das operações da Microsoft Graph API suportadas pelos recursos Do Azure AD B2C, consulte as operações do [Microsoft Graph disponíveis para o Azure AD B2C](microsoft-graph-operations.md).

<!-- LINK -->

[graph-objectIdentity]: https://docs.microsoft.com/graph/api/resources/objectidentity
[graph-user]: (https://docs.microsoft.com/graph/api/resources/user)
