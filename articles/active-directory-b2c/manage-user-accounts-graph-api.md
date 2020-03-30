---
title: Gerir utilizadores com a Microsoft Graph API
titleSuffix: Azure AD B2C
description: Como gerir os utilizadores num inquilino Azure AD B2C, ligando para a Microsoft Graph API e usando uma identidade de aplicação para automatizar o processo.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 3bd166572cea23fbb710cd053c28f51e76ba534a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79476676"
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

É `displayName` o nome a apresentar na gestão de utilizadores do portal Azure para o utilizador, e no acesso ao Azure AD B2C devolve à aplicação. Esta propriedade é necessária.

### <a name="identities-property"></a>Propriedade de identidades

Uma conta de cliente, que pode ser um consumidor, parceiro ou cidadão, pode ser associada a estes tipos de identidade:

- Identidade **local** - O nome de utilizador e a palavra-passe são armazenados localmente no diretório Azure AD B2C. Muitas vezes referimo-nos a estas identidades como "contas locais".
- Identidade **federada** - Também conhecida como contas *sociais* ou *empresariais,* a identidade do utilizador é gerida por um fornecedor de identidade federado como Facebook, Microsoft, ADFS ou Salesforce.

Um utilizador com uma conta de cliente pode iniciar sessão com múltiplas identidades. Por exemplo, nome de utilizador, e-mail, identificação do funcionário, identificação do governo, entre outros. Uma única conta pode ter múltiplas identidades, tanto locais como sociais, com a mesma senha.

Na API do Microsoft Graph, as identidades locais `identities` e federadas são armazenadas no atributo do utilizador, que é de tipo [objetoIdentidade][graph-objectIdentity]. A `identities` coleção representa um conjunto de identidades usadas para iniciar sessão numa conta de utilizador. Esta recolha permite ao utilizador iniciar sessão na conta de utilizador com qualquer uma das suas identidades associadas.

| Propriedade   | Tipo |Descrição|
|:---------------|:--------|:----------|
|signInType|string| Especifica os tipos de sessão de sessão do utilizador no seu diretório. Para a `emailAddress`conta `emailAddress1` `emailAddress2`local: , , `emailAddress3`, ou `userName`qualquer outro tipo que você goste. A conta social `federated`deve ser definida para .|
|issuer|string|Especifica o emitente da identidade. Para contas locais (onde o **signInType** não `federated`é), esta propriedade é `contoso.onmicrosoft.com`o nome de domínio padrão do inquilino B2C local, por exemplo. Para a identidade social (onde é `federated`o **signInType)** o valor é o nome do emitente, por exemplo`facebook.com`|
|emitenteAssignedId|string|Especifica o identificador único atribuído ao utilizador pelo emitente. A combinação de **emitente** e **emitente AssignedId** deve ser única dentro do seu inquilino. Para a conta local, quando `emailAddress` o `userName` **signInType** está definido para ou , representa o nome de início de sessão para o utilizador.<br>Quando **o signInType** está definido para: <ul><li>`emailAddress`(ou começa `emailAddress` `emailAddress1`com como) **emitenteAssignedId** deve ser um endereço de e-mail válido</li><li>`userName`(ou qualquer outro valor), o **emitenteAssignedId** deve ser uma parte local válida [de um endereço de e-mail](https://tools.ietf.org/html/rfc3696#section-3)</li><li>`federated`, **emitenteAssignedId** representa o identificador único da conta federada</li></ul>|

A seguinte propriedade **identidades,** com uma identidade de conta local com nome de inscrição, um endereço de e-mail como sign-in, e com uma identidade social. 

 ```JSON
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

Para identidades federadas, dependendo do fornecedor de identidade, o **emitenteAssignedId** é um valor único para um determinado utilizador por aplicação ou conta de desenvolvimento. Configure a política Azure AD B2C com o mesmo ID de aplicação que foi previamente atribuído pelo prestador social ou outra aplicação dentro da mesma conta de desenvolvimento.

### <a name="password-profile-property"></a>Propriedade de perfil de palavra-passe

Para uma identidade local, a propriedade **passwordProfile** é necessária e contém a palavra-passe do utilizador. A `forceChangePasswordNextSignIn` propriedade deve `false`ser definida para .

Para uma identidade (social) federada, a propriedade **passwordProfile** não é necessária.

```JSON
"passwordProfile" : {
    "password": "password-value",
    "forceChangePasswordNextSignIn": false
  }
```

### <a name="password-policy-property"></a>Propriedade de política de senha

A política de senhas Azure AD B2C (para contas locais) baseia-se na política de força de [senha forte](../active-directory/authentication/concept-sspr-policy.md) do Azure Ative Directory. As políticas de inscrição ou inscrição ou reset de palavra-passe do Azure AD B2C requerem esta forte força de senha e não expiram palavras-passe.

Nos cenários de migração dos utilizadores, se as contas que pretende migrar tiverem uma força de senha mais fraca do que a [forte força](../active-directory/authentication/concept-sspr-policy.md) de senha imposta pelo Azure AD B2C, pode desativar o forte requisito de senha. Para alterar a política de `passwordPolicies` senha `DisableStrongPassword`padrão, desloque a propriedade para . Por exemplo, pode modificar o pedido de criação do utilizador da seguinte forma:

```JSON
"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"
```

### <a name="extension-properties"></a>Propriedades de extensão

Cada aplicação voltada para o cliente tem requisitos únicos para que a informação seja recolhida. O seu inquilino Azure AD B2C vem com um conjunto incorporado de informação armazenada em propriedades, tais como Nome Dado, Sobrenome, Cidade e Código Postal. Com o Azure AD B2C, pode alargar o conjunto de imóveis armazenados em cada conta de cliente. Para obter mais informações sobre a definição de atributos personalizados, consulte [atributos personalizados (fluxos](user-flow-custom-attributes.md) de utilizador) e [atributos personalizados (políticas personalizadas)](custom-policy-custom-attributes.md).

O Microsoft Graph API suporta a criação e atualização de um utilizador com atributos de extensão. Os atributos de extensão na `extension_ApplicationObjectID_attributename`API do gráfico são nomeados utilizando a convenção . Por exemplo:

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
1. Na `appSettings` secção, `your-b2c-tenant` substitua-se pelo nome `Application (client) ID` `Client secret` do seu inquilino, e pelos valores para o seu registo de candidatura de gestão (consulte o Registo de uma secção de candidatura de [gestão](#register-a-management-application) deste artigo).
1. Abra uma janela de consola dentro do seu clone local do repo, mude para o `src` diretório e, em seguida, construa o projeto:
    ```console
    cd src
    dotnet build
    ```
1. Executar o pedido `dotnet` com o comando:

    ```console
    dotnet bin/Debug/netcoreapp3.0/b2c-ms-graph.dll
    ```

A aplicação apresenta uma lista de comandos que pode executar. Por exemplo, obtenha todos os utilizadores, obtenha um único utilizador, apague um utilizador, atualize a palavra-passe de um utilizador e importe a granel.

### <a name="code-discussion"></a>Discussão de código

O código da amostra utiliza o [Microsoft Graph SDK,](https://docs.microsoft.com/graph/sdks/sdks-overview)projetado para simplificar a construção de aplicações de alta qualidade, eficientes e resilientes que acedem ao Microsoft Graph. Então, não precisas de fazer um direct all the Microsoft Graph API.

Qualquer pedido à API do Microsoft Graph requer um sinal de acesso para autenticação. A solução utiliza o pacote [Microsoft.Graph.Auth](https://www.nuget.org/packages/Microsoft.Graph.Auth/) NuGet que fornece um invólucro baseado em cenários de autenticação da Microsoft Authentication Library (MSAL) para utilização com o Microsoft Graph SDK.

O `RunAsync` método no ficheiro _Program.cs:_

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

[Faça chamadas API utilizando os SDKs](https://docs.microsoft.com/graph/sdks/create-requests) do Microsoft Graph incluem informações sobre como ler e escrever informações a partir do Microsoft Graph, usar `$select` para controlar as propriedades devolvidas, fornecer parâmetros de consulta personalizados e usar os `$filter` parâmetros de consulta e `$orderBy` consulta.

## <a name="next-steps"></a>Passos seguintes

Para obter um índice completo das operações da Microsoft Graph API suportadas pelos recursos Do Azure AD B2C, consulte as operações do [Microsoft Graph disponíveis para o Azure AD B2C](microsoft-graph-operations.md).

<!-- LINK -->

[graph-objectIdentity]: https://docs.microsoft.com/graph/api/resources/objectidentity
[graph-user]: (https://docs.microsoft.com/graph/api/resources/user)
