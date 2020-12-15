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
ms.date: 08/03/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 6abc3316e18fc70a2969bc220fd75e10e10f0e6e
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/15/2020
ms.locfileid: "97507783"
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

## <a name="user-properties"></a>Propriedades do utilizador

### <a name="display-name-property"></a>Propriedade do nome de exibição

`displayName`É o nome a apresentar na gestão do utilizador do portal Azure para o utilizador, e no token AD B2C regressa à aplicação. Esta propriedade é necessária.

### <a name="identities-property"></a>Propriedade de identidades

Uma conta de cliente, que pode ser um consumidor, parceiro ou cidadão, pode ser associada a estes tipos de identidade:

- **Identidade local** - O nome de utilizador e a palavra-passe são armazenados localmente no diretório Azure AD B2C. Muitas vezes nos referimos a estas identidades como "contas locais".
- Identidade **federada** - Também conhecida como contas *sociais* ou *empresariais,* a identidade do utilizador é gerida por um fornecedor de identidade federado como Facebook, Microsoft, ADFS ou Salesforce.

Um utilizador com uma conta de cliente pode iniciar súmuta com múltiplas identidades. Por exemplo, nome de utilizador, e-mail, identificação de funcionários, ID do governo, e outros. Uma única conta pode ter múltiplas identidades, tanto locais como sociais, com a mesma senha.

Na API do Gráfico da Microsoft, as identidades locais e federadas são armazenadas no atributo do `identities` utilizador, que é de tipo [objetoIdentity][graph-objectIdentity]. A `identities` coleção representa um conjunto de identidades usadas para iniciar scontabilidade de um utilizador. Esta recolha permite ao utilizador iniciar seduções na conta do utilizador com qualquer uma das suas identidades associadas.

| Propriedade   | Tipo |Descrição|
|:---------------|:--------|:----------|
|signInType|string| Especifica os tipos de inscrição do utilizador no seu diretório. Para a conta local:  `emailAddress` , ou qualquer outro tipo que você `emailAddress1` `emailAddress2` `emailAddress3`  `userName` goste. A conta social deve ser definida para  `federated` .|
|issuer|string|Especifica o emitente da identidade. Para contas locais (onde **o signInType** não `federated` está), esta propriedade é o nome de domínio padrão do inquilino B2C local, por `contoso.onmicrosoft.com` exemplo. Para a identidade social (onde **o signInType**  `federated` é) o valor é o nome do emitente, por exemplo `facebook.com`|
|emitenteAssignedId|string|Especifica o identificador único atribuído ao utilizador pelo emitente. A combinação de **emitente** e **emitente AssignedId** deve ser única dentro do seu inquilino. Para a conta local, quando **o signInType** está definido para `emailAddress` `userName` ou, representa o nome de inscrição para o utilizador.<br>Quando **o signInType** estiver definido para: <ul><li>`emailAddress` (ou começa com `emailAddress` como `emailAddress1` ) **emitenteAssignedId** deve ser um endereço de e-mail válido</li><li>`userName` (ou qualquer outro valor), **emitenteAssignedId** deve ser uma parte local válida [de um endereço de e-mail](https://tools.ietf.org/html/rfc3696#section-3)</li><li>`federated`, **emitenteAssignedId** representa o identificador único da conta federada</li></ul>|

A seguinte **propriedade identidades,** com uma identidade de conta local com nome de inscrição, um endereço de e-mail como s-in, e com uma identidade social. 

 ```json
 "identities": [
     {
       "signInType": "userName",
       "issuer": "contoso.onmicrosoft.com",
       "issuerAssignedId": "johnsmith"
     },
     {
       "signInType": "emailAddress",
       "issuer": "contoso.onmicrosoft.com",
       "issuerAssignedId": "jsmith@yahoo.com"
     },
     {
       "signInType": "federated",
       "issuer": "facebook.com",
       "issuerAssignedId": "5eecb0cd"
     }
   ]
 ```

Para identidades federadas, dependendo do fornecedor de identidade, o **emitenteAssignedId** é um valor único para um dado utilizador por aplicação ou conta de desenvolvimento. Configure a política Azure AD B2C com o mesmo ID de aplicação que foi previamente atribuído pelo prestador social ou outro pedido dentro da mesma conta de desenvolvimento.

### <a name="password-profile-property"></a>Propriedade de perfil de senha

Para uma identidade local, a **propriedade passwordProfile** é necessária e contém a senha do utilizador. A `forceChangePasswordNextSignIn` propriedade deve definir para `false` .

Para uma identidade federada (social), a **propriedade passwordProfile** não é necessária.

```json
"passwordProfile" : {
    "password": "password-value",
    "forceChangePasswordNextSignIn": false
  }
```

### <a name="password-policy-property"></a>Propriedade política de senha

A política de senha Azure AD B2C (para contas locais) baseia-se na política [de força de senha forte](../active-directory/authentication/concept-sspr-policy.md) do Azure Ative Directory. As políticas de inscrição ou de início de sposição do Azure AD B2C ou de início de sposição e de redefinição de palavras-passe requerem esta forte força de senha e não expiram palavras-passe.

Nos cenários de migração do utilizador, se as contas que pretende migrar tiverem uma força de senha mais fraca do que a [forte força](../active-directory/authentication/concept-sspr-policy.md) de senha aplicada pelo Azure AD B2C, pode desativar o requisito de senha forte. Para alterar a política de senha padrão, desafine a `passwordPolicies` propriedade para `DisableStrongPassword` . Por exemplo, pode modificar o pedido de criação do utilizador da seguinte forma:

```json
"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"
```

### <a name="extension-properties"></a>Propriedades de extensão

Cada aplicação virada para o cliente tem requisitos únicos para que a informação seja recolhida. O seu inquilino Azure AD B2C vem com um conjunto incorporado de informações armazenadas em propriedades, tais como Nome Dado, Apelido, Cidade e Código Postal. Com o Azure AD B2C, pode alargar o conjunto de propriedades armazenadas em cada conta de cliente. Para obter mais informações sobre a definição de atributos personalizados, consulte [atributos personalizados](user-flow-custom-attributes.md).

A Microsoft Graph API suporta a criação e atualização de um utilizador com atributos de extensão. Os atributos de extensão na API do Gráfico são nomeados através da `extension_ApplicationClientID_attributename` convenção, onde é o `ApplicationClientID` **ID da aplicação (cliente)** da `b2c-extensions-app` aplicação (encontrado nos **registos da App** Todas as  >  **Aplicações** no portal Azure). Note que o **ID de Aplicação (cliente)** tal como está representado no nome do atributo de extensão não inclui hífenes. Por exemplo:

```json
"extension_831374b3bd5041bfaa54263ec9e050fc_loyaltyNumber": "212342"
```

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