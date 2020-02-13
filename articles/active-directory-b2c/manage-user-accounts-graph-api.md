---
title: Utilize o Gráfico API no Diretório Ativo Azure B2C
description: Como gerir os utilizadores num inquilino Azure AD B2C, ligando para a APi do Gráfico AD Azure e utilizando uma identidade de aplicação para automatizar o processo.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 71b437f57f9d9e6e18af88d6413269cac6f66c47
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77161669"
---
# <a name="azure-ad-b2c-use-the-azure-ad-graph-api"></a>Azure AD B2C: Utilize a API do gráfico azure AD

Os inquilinos do Azure Ative Directory B2C (Azure AD B2C) podem ter milhares ou milhões de utilizadores. Isto significa que muitas tarefas comuns de gestão dos inquilinos precisam de ser executadas programáticamente. Um exemplo primário é a gestão do utilizador.

Pode precisar migrar uma loja de utilizadores existente para um inquilino B2C. Pode querer hospedar o registo do utilizador na sua própria página e criar contas de utilizador no seu diretório Azure AD B2C nos bastidores. Este tipo de tarefas requerem a capacidade de criar, ler, atualizar e eliminar as contas dos utilizadores. Pode executar tais tarefas utilizando a API do Gráfico AD Azure.

Para os inquilinos B2C, existem dois modos primários de comunicação com a API graph:

* Para tarefas **interativas,** executadas, deve atuar como uma conta de administrador no inquilino B2C quando executar as tarefas. Este modo requer que um administrador assine com credenciais antes que o administrador possa efetuar quaisquer chamadas para a API do Gráfico.
* Para tarefas **automatizadas**e contínuas, deve utilizar algum tipo de conta de serviço que fornece os privilégios necessários para executar tarefas de gestão. Em Azure AD, pode fazê-lo registando uma aplicação e autenticando a Azure AD. Isto é feito através de um ID de *aplicação* que utiliza a concessão de credenciais de [cliente OAuth 2.0](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md). Neste caso, a aplicação funciona por si só, não como utilizador, para chamar a API do Gráfico.

Neste artigo, aprende-se a executar o caso de utilização automatizada. Irá construir uma `B2CGraphClient` .NET 4.5 que executa operações de criação, leitura, atualização e exclusão (CRUD) do utilizador. O cliente terá uma interface de linha de comando windows (CLI) que lhe permite invocar vários métodos. No entanto, o código é escrito para se comportar de forma não interativa e automatizada.

## <a name="prerequisites"></a>Pré-requisitos

Antes de poder criar aplicações ou utilizadores, precisa de um inquilino Azure AD B2C. Se ainda não tem um, [crie um inquilino Azure Ative Directory B2C.](tutorial-create-tenant.md)

## <a name="register-an-application"></a>Registar uma aplicação

Uma vez que tenha um inquilino Azure AD B2C, precisa de registar a sua aplicação de gestão utilizando o [portal Azure](https://portal.azure.com). Também precisa conceder-lhe as permissões necessárias para executar tarefas de gestão em nome do seu script automatizado ou aplicação de gestão.

### <a name="register-application-in-azure-active-directory"></a>Inscrição no Diretório Ativo do Azure

Para utilizar o Azure AD Graph API com o seu inquilino B2C, é necessário registar uma candidatura utilizando o fluxo de trabalho de registo de inscrição de inscrição de inscrição de candidatura do Azure Ative Diretório.

[!INCLUDE [active-directory-b2c-appreg-mgmt](../../includes/active-directory-b2c-appreg-mgmt.md)]

### <a name="assign-api-access-permissions"></a>Atribuir permissões de acesso a API

[!INCLUDE [active-directory-b2c-permissions-directory](../../includes/active-directory-b2c-permissions-directory.md)]

### <a name="create-client-secret"></a>Criar o segredo do cliente

[!INCLUDE [active-directory-b2c-client-secret](../../includes/active-directory-b2c-client-secret.md)]

Tem agora uma aplicação que tem permissão para *criar,* *ler*e *atualizar* utilizadores no seu inquilino Azure AD B2C. Continue na secção seguinte para adicionar permissões de *eliminação* de utilizador e *atualização* de passwords.

## <a name="add-user-delete-and-password-update-permissions"></a>Adicionar autorizações de eliminação do utilizador e atualização de passwords

A permissão de dados de leitura e escrita de dados de *diretório* que concedeu anteriormente **NÃO** inclui a capacidade de eliminar utilizadores ou atualizar as suas palavras-passe.

Se pretender dar à sua aplicação a capacidade de eliminar utilizadores ou atualizar palavras-passe, tem de lhe conceder a função de administrador do *Utilizador.*

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
1. Selecione o ícone **de Diretório + Subscrição** na barra de ferramentas do portal e, em seguida, selecione o diretório que contém o seu inquilino Azure AD AD B2C.
1. No portal Azure, procure e selecione **Azure AD B2C**.
1. Em **Gerir,** selecione **Funções e administradores.**
1. Selecione a função de administrador do **Utilizador.**
1. Selecione **Adicionar tarefa**.
1. Na caixa de texto **Select,** insira o nome da aplicação que registou anteriormente, por exemplo, *o managementapp1*. Selecione a sua aplicação quando aparecer nos resultados da pesquisa.
1. Selecione **Adicionar**. Pode levar alguns minutos para as permissões se propagarem completamente.

A sua aplicação Azure AD B2C tem agora as permissões adicionais necessárias para eliminar utilizadores ou atualizar as suas palavras-passe no seu inquilino B2C.

## <a name="get-the-sample-code"></a>Obter o código de exemplo

A amostra de código é uma aplicação de consola .NET que utiliza a Biblioteca de Autenticação do [Diretório Ativo (ADAL)](../active-directory/azuread-dev/active-directory-authentication-libraries.md) para interagir com a API do Gráfico AD Azure. O seu código demonstra como chamar a API para gerir programáticamente os utilizadores num inquilino Azure AD B2C.

Pode [descarregar o arquivo de amostras](https://github.com/AzureADQuickStarts/B2C-GraphAPI-DotNet/archive/master.zip) (\*.zip) ou clonar o repositório GitHub:

```cmd
git clone https://github.com/AzureADQuickStarts/B2C-GraphAPI-DotNet.git
```

Depois de obter a amostra de código, configure-a para o seu ambiente e, em seguida, construa o projeto:

1. Abra a solução `B2CGraphClient\B2CGraphClient.sln` no Visual Studio.
1. No projeto **B2CGraphClient,** abra o ficheiro *App.config.*
1. Substitua a secção `<appSettings>` pelo xML seguinte. Em seguida, substitua `{your-b2c-tenant}` pelo nome do seu inquilino, e `{Application ID}` e `{Client secret}` com os valores que gravou anteriormente.

    ```xml
    <appSettings>
        <add key="b2c:Tenant" value="{your-b2c-tenant}.onmicrosoft.com" />
        <add key="b2c:ClientId" value="{Application ID}" />
        <add key="b2c:ClientSecret" value="{Client secret}" />
    </appSettings>
    ```

1. Compilar a solução. Clique à direita na solução **B2CGraphClient** no Solution Explorer e, em seguida, **selecione Rebuild Solution**.

Se a construção for bem sucedida, a aplicação de consola `B2C.exe` pode ser encontrada em `B2CGraphClient\bin\Debug`.

## <a name="review-the-sample-code"></a>Reveja o código de exemplo

Para utilizar o B2CGraphClient, abra um Pedido de Comando (`cmd.exe`) e mude para o diretório `Debug` do projeto. Então, executar o comando `B2C Help`.

```cmd
cd B2CGraphClient\bin\Debug
B2C Help
```

O comando `B2C Help` apresenta uma breve descrição dos subcomandos disponíveis. Cada vez que invoca um dos seus subcomandos, `B2CGraphClient` envia um pedido à API do Gráfico AD Azure.

As seguintes secções discutem como o código da aplicação faz chamadas para a API do Gráfico AD Azure.

### <a name="get-an-access-token"></a>Obter um token de acesso

Qualquer pedido à AD Graph API do Azure requer um sinal de acesso para autenticação. `B2CGraphClient` utiliza a Biblioteca de Autenticação de DirectórioActivo (ADAL) de código aberto para ajudar na obtenção de fichas de acesso. A ADAL facilita a aquisição de fichas fornecendo uma API auxiliar e cuidando de alguns detalhes importantes, como fichas de acesso ao cache. No entanto, não é preciso usar a ADAL para obter fichas. Em vez disso, poderia obter fichas através da elaboração manual de pedidos HTTP.

> [!NOTE]
> Deve utilizar ADAL v2 ou superior para obter fichas de acesso que possam ser utilizadas com o API do Gráfico AD Azure. Não pode usar ADAL v1.

Quando `B2CGraphClient` executa, cria um exemplo da classe `B2CGraphClient`. O construtor desta classe configura o andaime de autenticação ADAL:

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

Vamos usar o comando `B2C Get-User` como exemplo.

Quando `B2C Get-User` é invocada sem argumentos adicionais, a aplicação chama o método `B2CGraphClient.GetAllUsers()`. `GetAllUsers()` então chama `B2CGraphClient.SendGraphGetRequest()`, que submete um pedido HTTP GET à API do Gráfico AD Azure. Antes de `B2CGraphClient.SendGraphGetRequest()` envia o pedido GET, obtém primeiro um sinal de acesso utilizando a ADAL:

```csharp
public async Task<string> SendGraphGetRequest(string api, string query)
{
    // First, use ADAL to acquire a token by using the app's identity (the credential)
    // The first parameter is the resource we want an access_token for; in this case, the Graph API.
    AuthenticationResult result = authContext.AcquireToken("https://graph.windows.net", credential);
    ...
```

Você pode obter um sinal de acesso para a API do gráfico, chamando o método de `AuthenticationContext.AcquireToken()` ADAL. A ADAL devolve então um `access_token` que representa a identidade da aplicação.

### <a name="read-users"></a>Ler utilizadores

Quando pretender obter uma lista de utilizadores ou obter um utilizador específico da APi do Gráfico AD Azure, pode enviar um pedido de `GET` HTTP para o `/users` ponto final. Um pedido para todos os utilizadores de um inquilino é assim:

```HTTP
GET https://graph.windows.net/contosob2c.onmicrosoft.com/users?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```

Para ver este pedido, corra:

 ```cmd
 B2C Get-User
 ```

Há duas coisas importantes a notar:

* O símbolo de acesso obtido através da utilização do ADAL é adicionado ao cabeçalho `Authorization` utilizando o esquema `Bearer`.
* Para os inquilinos B2C, você deve usar o parâmetro de consulta `api-version=1.6`.

Ambos os detalhes são tratados no método `B2CGraphClient.SendGraphGetRequest()`:

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

### <a name="create-consumer-user-accounts"></a>Criar contas de utilizador de consumo

Quando criar contas de utilizador no seu inquilino B2C, pode enviar um pedido de `POST` HTTP para o `/users` ponto final. O seguinte pedido de `POST` HTTP mostra um utilizador exemplo a ser criado no inquilino.

A maioria dos imóveis no seguinte pedido são necessários para criar utilizadores de consumo. Os comentários `//` foram incluídos para ilustração -- não incluem-nos num pedido real.

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

Para ver o pedido, executar um dos seguintes comandos:

```cmd
B2C Create-User ..\..\..\usertemplate-email.json
B2C Create-User ..\..\..\usertemplate-username.json
```

O comando `Create-User` toma como parâmetro de entrada um ficheiro JSON que contém uma representação JSON de um objeto de utilizador. Existem dois ficheiros JSON da amostra na amostra de código: `usertemplate-email.json` e `usertemplate-username.json`. Pode modificar estes ficheiros de acordo com as suas necessidades. Além dos campos acima necessários, vários campos opcionais estão incluídos nos ficheiros.

Para mais informações sobre os campos necessários e opcionais, consulte a Entidade e referência [de tipo complexo  Referência aGrafia API.](/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference)

Pode ver como o pedido do POST é construído em `B2CGraphClient.SendGraphPostRequest()`:

* Anexa um sinal de acesso ao cabeçalho `Authorization` do pedido.
* Define `api-version=1.6`.
* Inclui o objeto de utilizador JSON no corpo do pedido.

> [!NOTE]
> Se as contas que pretende migrar de uma loja de utilizadores existente supor uma força de senha mais baixa do que a forte força de [senha aplicada pelo Azure AD B2C,](user-flow-password-complexity.md)pode desativar o forte requisito de senha utilizando o valor `DisableStrongPassword` na propriedade `passwordPolicies`. Por exemplo, pode modificar o pedido de criação anterior do utilizador da seguinte forma: `"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"`.

### <a name="update-consumer-user-accounts"></a>Atualizar as contas dos utilizadores do consumidor

Quando atualiza os objetos do utilizador, o processo é semelhante ao que utiliza para criar objetos de utilizador, mas utiliza o método HTTP `PATCH`:

```HTTP
PATCH https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
Content-Type: application/json
Content-Length: 37

{
    "displayName": "Joe Consumer"    // this request updates only the user's displayName
}
```

Tente atualizar um utilizador modificando alguns valores nos seus ficheiros JSON e, em seguida, use o `B2CGraphClient` para executar um destes comandos:

```cmd
B2C Update-User <user-object-id> ..\..\..\usertemplate-email.json
B2C Update-User <user-object-id> ..\..\..\usertemplate-username.json
```

Inspecione o método `B2CGraphClient.SendGraphPatchRequest()` para obter informações sobre como enviar este pedido.

### <a name="search-users"></a>Procurar utilizadores

Pode pesquisar utilizadores no seu inquilino B2C das seguintes formas:

* Consulte o ID do **objeto**do utilizador .
* Referenciar o seu identificador de inscrição, o `signInNames` propriedade.
* Referência a qualquer um dos parâmetros OData válidos. Por exemplo, 'nome dado', 'apelido', 'displayName' etc.

Executar um dos seguintes comandos para procurar um utilizador:

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

### <a name="delete-users"></a>Excluir utilizadores

Para eliminar os utilizadores, utilize o método HTTP `DELETE` e construa o URL com o ID do objeto do utilizador:

```HTTP
DELETE https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```

Para ver um exemplo, introduza este comando e veja o pedido de exclusão que está impresso na consola:

```cmd
B2C Delete-User <object-id-of-user>
```

Inspecione o método `B2CGraphClient.SendGraphDeleteRequest()` para obter informações sobre como enviar este pedido.

Pode realizar muitas outras ações com a APi de Gráfico AD Azure, além da gestão do utilizador. A [referência Azure AD Graph API](/previous-versions/azure/ad/graph/api/api-catalog) fornece detalhes sobre cada ação, juntamente com pedidos de amostra.

## <a name="use-custom-attributes"></a>Utilizar atributos personalizados

A maioria das aplicações de consumo precisa de armazenar algum tipo de informação personalizada sobre o perfil do utilizador. Uma maneira de o fazer é definir um atributo personalizado no seu inquilino B2C. Em seguida, pode tratar esse atributo da mesma forma que trata qualquer outra propriedade num objeto de utilizador. Pode atualizar o atributo, eliminar o atributo, consulta pelo atributo, enviar o atributo como uma reclamação em fichas de entrada e muito mais.

Para definir um atributo personalizado no seu inquilino B2C, consulte a [referência personalizada do atributo B2C](user-flow-custom-attributes.md).

Pode ver os atributos personalizados definidos no seu inquilino B2C utilizando os seguintes comandos `B2CGraphClient`:

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

Pode utilizar o nome completo, como `extension_55dc0861f9a44eb999e0a8a872204adb_Jersey_Number`, como propriedade nos objetos do utilizador. Atualize o seu ficheiro JSON com a nova propriedade e um valor para a propriedade, e depois executar:

```cmd
B2C Update-User <object-id-of-user> <path-to-json-file>
```

## <a name="next-steps"></a>Passos seguintes

Ao utilizar `B2CGraphClient`, tem uma aplicação de serviço que pode gerir os seus utilizadores de inquilinos B2C programáticamente. `B2CGraphClient` usa a sua própria identidade de aplicação para autenticar a API do Gráfico AD Azure. Também adquire fichas usando um segredo de cliente.

Ao incorporar esta funcionalidade na sua própria aplicação, lembre-se de alguns pontos-chave para aplicações B2C:

* Conceda ao pedido as permissões necessárias no inquilino.
* Quando ligar para a API do gráfico, use `api-version=1.6`.
* Quando cria e atualiza os utilizadores de consumo, são necessárias algumas propriedades, como descrito acima.
