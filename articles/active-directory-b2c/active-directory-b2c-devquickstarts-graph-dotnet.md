---
title: Usar o API do Graph no Azure Active Directory B2C
description: Como gerenciar usuários em um locatário Azure AD B2C chamando o API do Graph do Azure AD e usando uma identidade de aplicativo para automatizar o processo.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: c4c8f123eb8c32362219f21dc70d137f2cc9b4b1
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74078827"
---
# <a name="azure-ad-b2c-use-the-azure-ad-graph-api"></a>Azure AD B2C: usar o API do Graph do Azure AD

Os locatários de Azure Active Directory B2C (Azure AD B2C) podem ter milhares ou milhões de usuários. Isso significa que muitas tarefas comuns de gerenciamento de locatários precisam ser executadas programaticamente. Um exemplo principal é o gerenciamento de usuários.

Talvez seja necessário migrar um repositório de usuários existente para um locatário B2C. Talvez você queira hospedar o registro de usuário em sua própria página e criar contas de usuário em seu diretório Azure AD B2C nos bastidores. Esses tipos de tarefas exigem a capacidade de criar, ler, atualizar e excluir contas de usuário. Você pode executar essas tarefas usando o API do Graph do Azure AD.

Para locatários B2C, há dois modos principais de se comunicar com o API do Graph:

* Para tarefas **interativas**e de execução única, você deve atuar como uma conta de administrador no locatário B2C ao executar as tarefas. Esse modo requer que um administrador entre com credenciais antes que o administrador possa executar qualquer chamada para o API do Graph.
* Para tarefas **automatizadas**e contínuas, você deve usar algum tipo de conta de serviço fornecido com os privilégios necessários para executar tarefas de gerenciamento. No Azure AD, você pode fazer isso registrando um aplicativo e Autenticando no Azure AD. Isso é feito usando uma *ID de aplicativo* que usa a [concessão de credenciais de cliente OAuth 2,0](../active-directory/develop/service-to-service.md). Nesse caso, o aplicativo atua como ele mesmo, não como um usuário, para chamar o API do Graph.

Neste artigo, você aprenderá a executar o caso de uso automatizado. Você criará um `B2CGraphClient` .NET 4,5 que executa operações CRUD (criação, leitura, atualização e exclusão) de usuário. O cliente terá uma CLI (interface de linha de comando) do Windows que permite invocar vários métodos. No entanto, o código é escrito para se comportar de maneira não interativa e automatizada.

>[!IMPORTANT]
> Você **deve** usar o [API do Graph do Azure ad](../active-directory/develop/active-directory-graph-api-quickstart.md) para gerenciar usuários em um diretório Azure ad B2C. O API do Graph do Azure AD é diferente da API do Microsoft Graph. Saiba mais nesta postagem no blog do MSDN: [Microsoft Graph ou Azure ad Graph](https://blogs.msdn.microsoft.com/aadgraphteam/2016/07/08/microsoft-graph-or-azure-ad-graph/).

## <a name="prerequisites"></a>Pré-requisitos

Antes de poder criar aplicativos ou usuários, você precisa de um locatário Azure AD B2C. Se você ainda não tiver uma, [crie um locatário Azure Active Directory B2C](tutorial-create-tenant.md).

## <a name="register-an-application"></a>Registar uma aplicação

Depois de ter um locatário Azure AD B2C, você precisa registrar seu aplicativo de gerenciamento usando o [portal do Azure](https://portal.azure.com). Você também precisa conceder a ele as permissões necessárias para executar tarefas de gerenciamento em nome do seu aplicativo de gerenciamento ou de script automatizado.

### <a name="register-application-in-azure-active-directory"></a>Registrar aplicativo no Azure Active Directory

Para usar o API do Graph do Azure AD com seu locatário B2C, você precisa registrar um aplicativo usando o fluxo de trabalho de registro do aplicativo Azure Active Directory.

[!INCLUDE [active-directory-b2c-appreg-mgmt](../../includes/active-directory-b2c-appreg-mgmt.md)]

### <a name="assign-api-access-permissions"></a>Atribuir permissões de acesso à API

[!INCLUDE [active-directory-b2c-permissions-directory](../../includes/active-directory-b2c-permissions-directory.md)]

### <a name="create-client-secret"></a>Criar segredo do cliente

[!INCLUDE [active-directory-b2c-client-secret](../../includes/active-directory-b2c-client-secret.md)]

Agora você tem um aplicativo que tem permissão para *criar*, *ler*e *atualizar* usuários em seu locatário Azure ad B2C. Continue na próxima seção para adicionar permissões de *exclusão* de usuário e *atualização de senha* .

## <a name="add-user-delete-and-password-update-permissions"></a>Adicionar permissões de exclusão de usuário e atualização de senha

A permissão *ler e gravar dados do diretório* que você concedeu anteriormente **não** inclui a capacidade de excluir usuários ou atualizar suas senhas.

Se você quiser dar ao aplicativo a capacidade de excluir usuários ou atualizar senhas, será necessário conceder a ela a função de *administrador de usuários* .

1. Entre no [portal do Azure](https://portal.azure.com) e alterne para o diretório que contém o locatário Azure ad B2C.
1. Selecione **Azure ad B2C** no menu à esquerda. Ou então, selecione **todos os serviços** e, em seguida, pesquise e selecione **Azure ad B2C**.
1. Em **gerenciar**, selecione **funções e administradores**.
1. Selecione a função de **administrador de usuários** .
1. Selecione **Adicionar atribuição**.
1. Na caixa de texto **selecionar** , digite o nome do aplicativo que você registrou anteriormente, por exemplo, *managementapp1*. Selecione seu aplicativo quando ele aparecer nos resultados da pesquisa.
1. Selecione **Adicionar**. Pode levar alguns minutos para que as permissões se propaguem totalmente.

Seu aplicativo Azure AD B2C agora tem as permissões adicionais necessárias para excluir usuários ou atualizar suas senhas em seu locatário B2C.

## <a name="get-the-sample-code"></a>Obter o código de exemplo

O exemplo de código é um aplicativo de console .NET que usa o [biblioteca de autenticação do Active Directory (Adal)](../active-directory/develop/active-directory-authentication-libraries.md) para interagir com o API do Graph do Azure AD. Seu código demonstra como chamar a API para gerenciar programaticamente os usuários em um locatário Azure AD B2C.

Você pode [baixar o arquivo de exemplo](https://github.com/AzureADQuickStarts/B2C-GraphAPI-DotNet/archive/master.zip) (\*. zip) ou clonar o repositório github:

```cmd
git clone https://github.com/AzureADQuickStarts/B2C-GraphAPI-DotNet.git
```

Depois de obter o exemplo de código, configure-o para o seu ambiente e, em seguida, compile o projeto:

1. Abra a solução `B2CGraphClient\B2CGraphClient.sln` no Visual Studio.
1. No projeto **B2CGraphClient** , abra o arquivo *app. config* .
1. Substitua a seção `<appSettings>` pelo seguinte XML. Em seguida, substitua `{your-b2c-tenant}` pelo nome do seu locatário e `{Application ID}` e `{Client secret}` pelos valores que você registrou anteriormente.

    ```xml
    <appSettings>
        <add key="b2c:Tenant" value="{your-b2c-tenant}.onmicrosoft.com" />
        <add key="b2c:ClientId" value="{Application ID}" />
        <add key="b2c:ClientSecret" value="{Client secret}" />
    </appSettings>
    ```

1. Compile a solução. Clique com o botão direito do mouse na solução **B2CGraphClient** no Gerenciador de soluções e selecione **Recompilar solução**.

Se a compilação for bem-sucedida, o aplicativo de console `B2C.exe` pode ser encontrado em `B2CGraphClient\bin\Debug`.

## <a name="review-the-sample-code"></a>Reveja o código de exemplo

Para usar o B2CGraphClient, abra um prompt de comando (`cmd.exe`) e altere para o diretório `Debug` do projeto. Em seguida, execute o comando `B2C Help`.

```cmd
cd B2CGraphClient\bin\Debug
B2C Help
```

O comando `B2C Help` exibe uma breve descrição dos subcomandos disponíveis. Cada vez que você invoca um de seus subcomandos, `B2CGraphClient` envia uma solicitação para o API do Graph do Azure AD.

As seções a seguir discutem como o código do aplicativo faz chamadas para o API do Graph do Azure AD.

### <a name="get-an-access-token"></a>Obter um token de acesso

Qualquer solicitação para o API do Graph do Azure AD requer um token de acesso para autenticação. `B2CGraphClient` usa o ADAL (Biblioteca de Autenticação do Active Directory de software livre) para auxiliar na obtenção de tokens de acesso. A ADAL facilita a aquisição de token ao fornecer uma API auxiliar e cuidar de alguns detalhes importantes, como o cache de tokens de acesso. No entanto, você não precisa usar a ADAL para obter tokens. Em vez disso, você pode obter tokens criando manualmente solicitações HTTP.

> [!NOTE]
> Você deve usar a ADAL v2 ou superior para obter tokens de acesso que podem ser usados com o API do Graph do Azure AD. Você não pode usar o ADAL v1.

Quando `B2CGraphClient` é executado, ele cria uma instância da classe `B2CGraphClient`. O construtor dessa classe configura o scaffolding de autenticação ADAL:

```csharp
public B2CGraphClient(string clientId, string clientSecret, string tenant)
{
    // The client_id, client_secret, and tenant are provided in Program.cs, which pulls the values from App.config
    this.clientId = clientId;
    this.clientSecret = clientSecret;
    this.tenant = tenant;

    // The AuthenticationContext is ADAL's primary class, in which you indicate the tenant to use.
    this.authContext = new AuthenticationContext("https://login.microsoftonline.com/" + tenant);

    // The ClientCredential is where you pass in your client_id and client_secret, which are
    // provided to Azure AD in order to receive an access_token by using the app's identity.
    this.credential = new ClientCredential(clientId, clientSecret);
}
```

Vamos usar o comando `B2C Get-User` como um exemplo.

Quando `B2C Get-User` é invocado sem argumentos adicionais, o aplicativo chama o método `B2CGraphClient.GetAllUsers()`. em seguida, `GetAllUsers()` chama `B2CGraphClient.SendGraphGetRequest()`, que envia uma solicitação HTTP GET para o API do Graph do Azure AD. Antes que `B2CGraphClient.SendGraphGetRequest()` envie a solicitação GET, ele primeiro obtém um token de acesso usando a ADAL:

```csharp
public async Task<string> SendGraphGetRequest(string api, string query)
{
    // First, use ADAL to acquire a token by using the app's identity (the credential)
    // The first parameter is the resource we want an access_token for; in this case, the Graph API.
    AuthenticationResult result = authContext.AcquireToken("https://graph.windows.net", credential);
    ...
```

Você pode obter um token de acesso para o API do Graph chamando o método ADAL `AuthenticationContext.AcquireToken()`. Em seguida, a ADAL retorna um `access_token` que representa a identidade do aplicativo.

### <a name="read-users"></a>Ler usuários

Quando desejar obter uma lista de usuários ou obter um usuário específico do API do Graph do Azure AD, você poderá enviar uma solicitação HTTP `GET` para o ponto de extremidade `/users`. Uma solicitação para todos os usuários em um locatário é parecida com esta:

```HTTP
GET https://graph.windows.net/contosob2c.onmicrosoft.com/users?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```

Para ver essa solicitação, execute:

 ```cmd
 B2C Get-User
 ```

Há duas coisas importantes a serem observadas:

* O token de acesso obtido usando a ADAL é adicionado ao cabeçalho de `Authorization` usando o esquema de `Bearer`.
* Para locatários B2C, você deve usar o parâmetro de consulta `api-version=1.6`.

Esses dois detalhes são tratados no método `B2CGraphClient.SendGraphGetRequest()`:

```csharp
public async Task<string> SendGraphGetRequest(string api, string query)
{
    ...

    // For B2C user management, be sure to use the 1.6 Graph API version.
    HttpClient http = new HttpClient();
    string url = "https://graph.windows.net/" + tenant + api + "?" + "api-version=1.6";
    if (!string.IsNullOrEmpty(query))
    {
        url += "&" + query;
    }

    // Append the access token for the Graph API to the Authorization header of the request by using the Bearer scheme.
    HttpRequestMessage request = new HttpRequestMessage(HttpMethod.Get, url);
    request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);
    HttpResponseMessage response = await http.SendAsync(request);

    ...
```

### <a name="create-consumer-user-accounts"></a>Criar contas de usuário do consumidor

Ao criar contas de usuário em seu locatário B2C, você pode enviar uma solicitação HTTP `POST` para o ponto de extremidade `/users`. A solicitação HTTP `POST` a seguir mostra um exemplo de usuário a ser criado no locatário.

A maioria das propriedades na solicitação a seguir é necessária para criar usuários do consumidor. Os comentários `//` foram incluídos para ilustração--não os incluam em uma solicitação real.

```HTTP
POST https://graph.windows.net/contosob2c.onmicrosoft.com/users?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
Content-Type: application/json
Content-Length: 338

{
    // All of these properties are required to create consumer users.

    "accountEnabled": true,
    "signInNames": [                           // controls which identifier the user uses to sign in to the account
        {
            "type": "emailAddress",            // can be 'emailAddress' or 'userName'
            "value": "consumer@fabrikam.com"
        }
    ],
    "creationType": "LocalAccount",            // always set to 'LocalAccount'
    "displayName": "Consumer User",            // a value that can be used for displaying to the end user
    "mailNickname": "cuser",                   // an email alias for the user
    "passwordProfile": {
        "password": "P@ssword!",
        "forceChangePasswordNextLogin": false  // always set to false
    },
    "passwordPolicies": "DisablePasswordExpiration"
}
```

Para ver a solicitação, execute um dos seguintes comandos:

```cmd
B2C Create-User ..\..\..\usertemplate-email.json
B2C Create-User ..\..\..\usertemplate-username.json
```

O comando `Create-User` usa como um parâmetro de entrada um arquivo JSON que contém uma representação JSON de um objeto de usuário. Há dois arquivos JSON de exemplo no exemplo de código: `usertemplate-email.json` e `usertemplate-username.json`. Você pode modificar esses arquivos para atender às suas necessidades. Além dos campos obrigatórios acima, vários campos opcionais são incluídos nos arquivos.

Para obter mais informações sobre os campos obrigatórios e opcionais, consulte a [entidade e a referência de tipo complexo | Referência de API do Graph](/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference).

Você pode ver como a solicitação POST é construída no `B2CGraphClient.SendGraphPostRequest()`:

* Ele anexa um token de acesso ao cabeçalho `Authorization` da solicitação.
* Ele define `api-version=1.6`.
* Ele inclui o objeto de usuário JSON no corpo da solicitação.

> [!NOTE]
> Se as contas que você deseja migrar de um repositório de usuários existente tiverem uma intensidade de senha menor do que a [força de senha forte imposta por Azure ad B2C](active-directory-b2c-reference-password-complexity.md), você poderá desabilitar o requisito de senha forte usando o valor `DisableStrongPassword` na propriedade `passwordPolicies`. Por exemplo, você pode modificar a solicitação de criação de usuário anterior da seguinte maneira: `"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"`.

### <a name="update-consumer-user-accounts"></a>Atualizar contas de usuário do consumidor

Quando você atualiza objetos de usuário, o processo é semelhante ao que você usa para criar objetos de usuário, mas usa o método de `PATCH` HTTP:

```HTTP
PATCH https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
Content-Type: application/json
Content-Length: 37

{
    "displayName": "Joe Consumer"    // this request updates only the user's displayName
}
```

Tente atualizar um usuário modificando alguns valores em seus arquivos JSON e, em seguida, use o `B2CGraphClient` para executar um destes comandos:

```cmd
B2C Update-User <user-object-id> ..\..\..\usertemplate-email.json
B2C Update-User <user-object-id> ..\..\..\usertemplate-username.json
```

Inspecione o método `B2CGraphClient.SendGraphPatchRequest()` para obter detalhes sobre como enviar essa solicitação.

### <a name="search-users"></a>Procurar utilizadores

Você pode pesquisar usuários em seu locatário B2C das seguintes maneiras:

* Referencie a ID de **objeto**do usuário.
* Referencie seu identificador de entrada, a propriedade `signInNames`.
* Faça referência a qualquer um dos parâmetros de OData válidos, por exemplo, excertoname, sobrenome, displayName, etc.

Execute um dos seguintes comandos para procurar um usuário:

```cmd
B2C Get-User <user-object-id>
B2C Get-User <filter-query-expression>
```

Por exemplo:

```cmd
B2C Get-User 2bcf1067-90b6-4253-9991-7f16449c2d91
B2C Get-User $filter=signInNames/any(x:x/value%20eq%20%27consumer@fabrikam.com%27)
B2C get-user $filter=givenName%20eq%20%27John%27
B2C get-user $filter=surname%20eq%20%27Doe%27
B2C get-user $filter=displayName%20eq%20%27John%20Doe%27
```

### <a name="delete-users"></a>Excluir usuários

Para excluir usuários, use o método HTTP `DELETE` e construa a URL com a ID de objeto do usuário:

```HTTP
DELETE https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```

Para ver um exemplo, digite este comando e exiba a solicitação de exclusão que é impressa no console:

```cmd
B2C Delete-User <object-id-of-user>
```

Inspecione o método `B2CGraphClient.SendGraphDeleteRequest()` para obter detalhes sobre como enviar essa solicitação.

Você pode executar muitas outras ações com o API do Graph do Azure AD, além do gerenciamento de usuários. A [referência de API do Graph do Azure ad](/previous-versions/azure/ad/graph/api/api-catalog) fornece detalhes sobre cada ação, juntamente com as solicitações de exemplo.

## <a name="use-custom-attributes"></a>Utilizar atributos personalizados

A maioria dos aplicativos de consumidor precisa armazenar algum tipo de informação de perfil de usuário personalizado. Uma maneira de fazer isso é definir um atributo personalizado em seu locatário B2C. Em seguida, você pode tratar esse atributo da mesma maneira que trata qualquer outra propriedade em um objeto de usuário. Você pode atualizar o atributo, excluir o atributo, consultar pelo atributo, enviar o atributo como uma declaração em tokens de entrada e muito mais.

Para definir um atributo personalizado em seu locatário B2C, consulte a [referência de atributo personalizado B2C](active-directory-b2c-reference-custom-attr.md).

Você pode exibir os atributos personalizados definidos em seu locatário B2C usando os seguintes comandos de `B2CGraphClient`:

```cmd
B2C Get-B2C-Application
B2C Get-Extension-Attribute <object-id-in-the-output-of-the-above-command>
```

A saída revela os detalhes de cada atributo personalizado. Por exemplo:

```json
{
      "odata.type": "Microsoft.DirectoryServices.ExtensionProperty",
      "objectType": "ExtensionProperty",
      "objectId": "cec6391b-204d-42fe-8f7c-89c2b1964fca",
      "deletionTimestamp": null,
      "appDisplayName": "",
      "name": "extension_55dc0861f9a44eb999e0a8a872204adb_Jersey_Number",
      "dataType": "Integer",
      "isSyncedFromOnPremises": false,
      "targetObjects": [
        "User"
      ]
}
```

Você pode usar o nome completo, como `extension_55dc0861f9a44eb999e0a8a872204adb_Jersey_Number`, como uma propriedade em seus objetos de usuário. Atualize o arquivo JSON com a nova propriedade e um valor para a propriedade e, em seguida, execute:

```cmd
B2C Update-User <object-id-of-user> <path-to-json-file>
```

## <a name="next-steps"></a>Passos seguintes

Usando `B2CGraphClient`, você tem um aplicativo de serviço que pode gerenciar seus usuários de locatário do B2C de forma programática. `B2CGraphClient` usa sua própria identidade de aplicativo para autenticar para o API do Graph do Azure AD. Ele também adquire tokens usando um segredo do cliente.

Ao incorporar essa funcionalidade em seu próprio aplicativo, lembre-se de alguns pontos-chave para aplicativos B2C:

* Conceda ao aplicativo as permissões necessárias no locatário.
* Por enquanto, você precisa usar a ADAL (não MSAL) para obter tokens de acesso. (Você também pode enviar mensagens de protocolo diretamente, sem usar uma biblioteca.)
* Ao chamar o API do Graph, use `api-version=1.6`.
* Quando você cria e atualiza os usuários do consumidor, algumas propriedades são necessárias, conforme descrito acima.
