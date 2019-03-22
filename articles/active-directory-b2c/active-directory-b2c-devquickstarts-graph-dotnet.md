---
title: Utilizar a Graph API no Azure Active Directory B2C | Documentos da Microsoft
description: Como chamar a Graph API para um inquilino B2C ao utilizar uma identidade de aplicativo para automatizar o processo.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/07/2017
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: a516f99af05ba3f3bb7ab98d3def123a488e0d9d
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58075906"
---
# <a name="azure-ad-b2c-use-the-azure-ad-graph-api"></a>Azure AD B2C: Utilizar a Graph API do Azure AD

>[!NOTE]
> Tem de utilizar o [Azure AD Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-operations-overview?f=255&MSPPError=-2147217396) para gerir utilizadores num diretório do Azure AD B2C. Isto é diferente do Microsoft Graph API. Sabia mais [aqui](https://blogs.msdn.microsoft.com/aadgraphteam/2016/07/08/microsoft-graph-or-azure-ad-graph/).

Inquilinos do Azure Active Directory (Azure AD) B2C tendem a ser muito grandes. Isso significa que muitas tarefas de gestão comuns do inquilino precisam ser executadas por meio de programação. Um principal exemplo é a gestão de utilizadores. Poderá ter de migrar um arquivo de utilizador existente a um inquilino do B2C. Pode querer alojar o registo de utilizador na sua própria página e criar contas de utilizador no seu diretório do Azure AD B2C em segundo plano. Esses tipos de tarefas exigem a capacidade de criar, ler, atualizar e eliminar contas de utilizador. Pode realizar estas tarefas, utilizando o Azure AD Graph API.

Para inquilinos de B2C, existem dois modos principais de comunicação com a Graph API.

* Para tarefas interativas e de execução única, deve agir como uma conta de administrador no inquilino do B2C ao executar as tarefas. Este modo requer um administrador iniciar sessão com as credenciais para que o administrador pode realizar todas as chamadas para a Graph API.
* Para tarefas automatizadas, contínuas, deve usar algum tipo de conta de serviço que forneça com os privilégios necessários para efetuar tarefas de gestão. No Azure AD, pode fazê-ao registar uma aplicação e autenticar para o Azure AD. Isso é feito usando uma **ID da aplicação** que utiliza o [concessão de credenciais de cliente OAuth 2.0](../active-directory/develop/service-to-service.md). Neste caso, o aplicativo age em nome próprio, não como um utilizador, para chamar a API de gráfico.

Neste artigo, aprenderá a efetuar o caso de uso automatizado. Irá criar um .NET 4.5 `B2CGraphClient` que realiza o usuário criar, ler, atualizar e eliminar (CRUD) de operações. O cliente terá uma interface de linha de comandos do Windows (CLI) que permite invocar vários métodos. No entanto, o código é escrito para se comportar de forma noninteractive e automatizada.

## <a name="get-an-azure-ad-b2c-tenant"></a>Obter um inquilino do Azure AD B2C
Antes de poder criar aplicativos ou usuários, precisa de um inquilino do Azure AD B2C. Se ainda não tiver um inquilino, [introdução ao Azure AD B2C](active-directory-b2c-get-started.md).

## <a name="register-your-application-in-your-tenant"></a>Registar a sua aplicação no seu inquilino
Depois de ter um inquilino de B2C, tem de registar a sua aplicação utilizando o [portal do Azure](https://portal.azure.com).

> [!IMPORTANT]
> Para utilizar a Graph API com o seu inquilino do B2C, terá de registar uma aplicação com o *registos de aplicações* no portal do Azure, serviço **não** do Azure AD B2C *aplicativos*menu. As instruções seguintes levá-lo ao menu apropriado. Não é possível reutilizar os aplicativos existentes do B2C que registou do Azure AD B2C *aplicativos* menu.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Escolha o seu inquilino do Azure AD B2C ao selecionar a sua conta no canto superior direito da página.
3. No painel de navegação do lado esquerdo, escolha **todos os serviços**, clique em **registos das aplicações**e clique em **Add**.
4. Siga os avisos e crie uma nova aplicação. 
    1. Selecione **aplicação Web / API** como o tipo de aplicação.    
    2. Fornecer **qualquer URL de início de sessão** (por exemplo, `https://B2CGraphAPI`) porque não é relevante para este exemplo.  
5. O aplicação irá agora apresentados na lista de aplicações, clique no mesmo para obter o **ID da aplicação** (também conhecido como ID de cliente). Copie-o à medida que vai precisar numa seção posterior.
6. No menu de definições, clique em **chaves**.
7. Na **palavras-passe** secção, introduza a descrição da chave e selecione uma duração e, em seguida, clique em **guardar**. Copie o valor da chave (também conhecida como segredo do cliente) para utilização numa secção posterior.

## <a name="configure-create-read-and-update-permissions-for-your-application"></a>Configurar a criar, ler e atualizar as permissões para a sua aplicação
Agora precisa de configurar a sua aplicação para obter todas as permissões necessárias para criar, ler, atualizar e eliminar os utilizadores.

1. Continuando no menu de registos de aplicações do portal do Azure, selecione a sua aplicação.
2. No menu de definições, clique em **permissões obrigatórias**.
3. No menu de permissões necessárias, clique em **Windows Azure Active Directory**.
4. No menu de ativar o acesso, selecione o **dados de diretório de leitura e escrita** permissão do **permissões de aplicação** e clique em **guardar**.
5. Por fim, no menu de permissões necessárias, clique nas **conceder permissões** botão.

Tem agora uma aplicação que tenha permissão para criar, ler e atualizar utilizadores do seu inquilino do B2C.

> [!NOTE]
> Concessão de permissões pode demorar alguns minutos para processar totalmente.
> 
> 

## <a name="configure-delete-or-update-password-permissions-for-your-application"></a>Configurar a eliminação ou atualizar as permissões de palavra-passe para a sua aplicação
Atualmente, o *dados de diretório de leitura e escrita* permissão faz **não** incluem a capacidade de eliminar utilizadores ou atualizar as palavras-passe do utilizador. Se pretende conceder a capacidade de seu aplicativo para usuários de eliminação ou palavras-passe de atualização, terá de executar estes passos Extras que envolvem o PowerShell, caso contrário, pode avançar para a secção seguinte.

Em primeiro lugar, se ainda não tiver instalado, instale o [módulo do Azure AD PowerShell v1 (MSOnline)](https://docs.microsoft.com/powershell/azure/active-directory/install-msonlinev1?view=azureadps-1.0):

```powershell
Install-Module MSOnline
```

Depois de instalar o módulo do PowerShell ligar ao seu inquilino do Azure AD B2C.

> [!IMPORTANT]
> Tem de utilizar uma conta de administrador de inquilino de B2C que seja **local** para o inquilino do B2C. Estas contas ter este aspeto: myusername@myb2ctenant.onmicrosoft.com.

```powershell
Connect-MsolService
```

Agora, vamos utilizar o **ID da aplicação** no script abaixo para atribuir a aplicação a função de administrador de conta de utilizador. Estas funções têm identificadores bem conhecidos, portanto, tudo o que precisa fazer é a entrada sua **ID da aplicação** no script abaixo.

```powershell
$applicationId = "<YOUR_APPLICATION_ID>"
$sp = Get-MsolServicePrincipal -AppPrincipalId $applicationId
Add-MsolRoleMember -RoleObjectId fe930be7-5e62-47db-91af-98c3a49a38b1 -RoleMemberObjectId $sp.ObjectId -RoleMemberType servicePrincipal
```

Agora seu aplicativo também tem permissões para eliminar os utilizadores ou atualizar as palavras-passe do seu inquilino do B2C.

## <a name="download-configure-and-build-the-sample-code"></a>Transferir, configurar e criar o código de exemplo
Em primeiro lugar, Baixe o código de exemplo e executá-lo. Em seguida, podemos irá demorar uma análise detalhada-lo.  Pode [baixar o código de exemplo como um ficheiro. zip](https://github.com/AzureADQuickStarts/B2C-GraphAPI-DotNet/archive/master.zip). Também pode cloná-lo num diretório à sua escolha:

```cmd
git clone https://github.com/AzureADQuickStarts/B2C-GraphAPI-DotNet.git
```

Abra o `B2CGraphClient\B2CGraphClient.sln` solução do Visual Studio no Visual Studio. Na `B2CGraphClient` do projeto, abra o arquivo `App.config`. Substitua as definições da aplicação de três pelos seus próprios valores:

```xml
<appSettings>
    <add key="b2c:Tenant" value="{Your Tenant Name}" />
    <add key="b2c:ClientId" value="{The ApplicationID from above}" />
    <add key="b2c:ClientSecret" value="{The Key from above}" />
</appSettings>
```

[!INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

Em seguida, clique com botão direito no `B2CGraphClient` solução e o exemplo de reconstrução. Se for bem-sucedida, já deve ter uma `B2C.exe` ficheiro executável, localizado na `B2CGraphClient\bin\Debug`.

## <a name="build-user-crud-operations-by-using-the-graph-api"></a>Criar as operações de CRUD do usuário com a Graph API
Para usar o B2CGraphClient, abra um `cmd` Windows comando linha de comandos e altere o diretório para o `Debug` diretório. Em seguida, execute o `B2C Help` comando.

```cmd
cd B2CGraphClient\bin\Debug
B2C Help
```

Esta ação apresenta uma breve descrição de cada comando. Sempre que invoca um desses comandos, `B2CGraphClient` faz um pedido para o Azure AD Graph API.

### <a name="get-an-access-token"></a>Obter um token de acesso
Qualquer pedido para a Graph API exige um token de acesso para a autenticação. `B2CGraphClient` utiliza o Active Directory Authentication Library (ADAL) aberto para adquirir os tokens de acesso. ADAL facilita a aquisição do token, fornecendo uma API simples e responsável pelo alguns detalhes importantes, como colocação em cache de tokens de acesso. Não tem de utilizar a ADAL para obter os tokens, no entanto. Também pode obter os tokens ao criar pedidos de HTTP.

> [!NOTE]
> Este exemplo de código utiliza a ADAL v2 para se comunicar com a Graph API.  Tem de utilizar da ADAL v2 ou v3 para obter os tokens de acesso que podem ser utilizados com o Azure AD Graph API.
> 
> 

Quando `B2CGraphClient` é executado, ele cria uma instância do `B2CGraphClient` classe. O construtor para essa classe define uma estrutura de autenticação da ADAL:

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

Vamos utilizar o `B2C Get-User` comando como exemplo. Quando `B2C Get-User` for chamado sem quaisquer entradas adicionais, as chamadas CLI o `B2CGraphClient.GetAllUsers(...)` método. Esse método chama `B2CGraphClient.SendGraphGetRequest(...)`, que envia um pedido HTTP GET para a Graph API. Antes de `B2CGraphClient.SendGraphGetRequest(...)` envia pedidos de GET, primeiro obtém um acesso token ao utilizar a ADAL:

```csharp
public async Task<string> SendGraphGetRequest(string api, string query)
{
    // First, use ADAL to acquire a token by using the app's identity (the credential)
    // The first parameter is the resource we want an access_token for; in this case, the Graph API.
    AuthenticationResult result = authContext.AcquireToken("https://graph.windows.net", credential);

    ...

```

Pode obter um token de acesso para a Graph API, chamando a ADAL `AuthenticationContext.AcquireToken(...)` método. ADAL, em seguida, devolve um `access_token` que representa a identidade da aplicação.

### <a name="read-users"></a>Utilizadores de leitura
Quando quiser obter uma lista de utilizadores ou obter um utilizador específico do Graph API, pode enviar um HTTP `GET` pedido para o `/users` ponto final. Um pedido para todos os utilizadores num inquilino tem esta aparência:

```
GET https://graph.windows.net/contosob2c.onmicrosoft.com/users?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```

Para ver este pedido, execute:

 ```cmd
 B2C Get-User
 ```

Existem duas coisas importantes a ter em conta:

* O token de acesso que adquiriu através da ADAL é adicionado à `Authorization` cabeçalho utilizando o `Bearer` esquema.
* Para inquilinos de B2C, tem de utilizar o parâmetro de consulta `api-version=1.6`.

Ambos esses detalhes são processados no `B2CGraphClient.SendGraphGetRequest(...)` método:

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

### <a name="create-consumer-user-accounts"></a>Criar contas de utilizador do consumidor
Ao criar contas de utilizador no seu inquilino do B2C, pode enviar um HTTP `POST` pedido para o `/users` ponto final:

```
POST https://graph.windows.net/contosob2c.onmicrosoft.com/users?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
Content-Type: application/json
Content-Length: 338

{
    // All of these properties are required to create consumer users.

    "accountEnabled": true,
    "signInNames": [                            // controls which identifier the user uses to sign in to the account
        {
            "type": "emailAddress",             // can be 'emailAddress' or 'userName'
            "value": "joeconsumer@gmail.com"
        }
    ],
    "creationType": "LocalAccount",            // always set to 'LocalAccount'
    "displayName": "Joe Consumer",                // a value that can be used for displaying to the end user
    "mailNickname": "joec",                        // an email alias for the user
    "passwordProfile": {
        "password": "P@ssword!",
        "forceChangePasswordNextLogin": false   // always set to false
    },
    "passwordPolicies": "DisablePasswordExpiration"
}
```

A maioria dessas propriedades neste pedido é necessários para criar utilizadores de consumidor. Para obter mais informações, clique em [aqui](https://msdn.microsoft.com/library/azure/ad/graph/api/users-operations#CreateLocalAccountUser). Tenha em atenção que o `//` comentários foram incluídos para fins de ilustração. Não incluí-los numa solicitação real.

Para ver o pedido, execute um dos seguintes comandos:

```cmd
B2C Create-User ..\..\..\usertemplate-email.json
B2C Create-User ..\..\..\usertemplate-username.json
```

O `Create-User` comando recebe um ficheiro. JSON como um parâmetro de entrada. Contém uma representação JSON de um objeto de usuário. Existem dois ficheiros. JSON de exemplo no código de exemplo: `usertemplate-email.json` e `usertemplate-username.json`. É possível modificar esses arquivos para se adequar às suas necessidades. Além dos campos necessários acima, estão incluídos vários campos opcionais que pode utilizar estes ficheiros. Podem encontrar detalhes sobre os campos opcionais na [referência de entidade do Azure AD Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#user-entity).

Pode ver como o pedido POST é construído em `B2CGraphClient.SendGraphPostRequest(...)`.

* Ele anexa um token de acesso para o `Authorization` cabeçalho do pedido.
* Ele define `api-version=1.6`.
* Ele inclui o objeto de utilizador JSON no corpo do pedido.

> [!NOTE]
> Se as contas que pretende migrar a partir de um arquivo de utilizador existente tem menos fortes de palavra-passe que o [força da senha forte imposta pelo Azure AD B2C](https://msdn.microsoft.com/library/azure/jj943764.aspx), pode desativar o requisito de palavra-passe segura utilizando o `DisableStrongPassword` valor no `passwordPolicies` propriedade. Por exemplo, pode modificar o pedido de utilizador de criar fornecido acima da seguinte forma: `"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"`.
> 
> 

### <a name="update-consumer-user-accounts"></a>Atualizar as contas de utilizador do consumidor
Quando atualizar objetos do usuário, o processo é semelhante ao que utilizar para criar objetos de utilizador. Mas este processo utiliza HTTP `PATCH` método:

```
PATCH https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
Content-Type: application/json
Content-Length: 37

{
    "displayName": "Joe Consumer"                // this request updates only the user's displayName
}
```

Tente atualizar um utilizador, atualizando os seus ficheiros JSON com novos dados. Em seguida, pode utilizar `B2CGraphClient` para executar um dos seguintes comandos:

```cmd
B2C Update-User <user-object-id> ..\..\..\usertemplate-email.json
B2C Update-User <user-object-id> ..\..\..\usertemplate-username.json
```

Inspecione o `B2CGraphClient.SendGraphPatchRequest(...)` método para obter detalhes sobre como enviar este pedido.

### <a name="search-users"></a>Procurar utilizadores
Pode procurar utilizadores no seu inquilino de B2C de duas formas. Um, o utilizador a utilizar dois, usando o identificador de início de sessão do utilizador ou ID de objeto (ou seja, o `signInNames` propriedade).

Execute um dos seguintes comandos para procurar um utilizador específico:

```cmd
B2C Get-User <user-object-id>
B2C Get-User <filter-query-expression>
```

Aqui estão alguns exemplos:

```cmd
B2C Get-User 2bcf1067-90b6-4253-9991-7f16449c2d91
B2C Get-User $filter=signInNames/any(x:x/value%20eq%20%27joeconsumer@gmail.com%27)
```

### <a name="delete-users"></a>Eliminar utilizadores
O processo para eliminar um utilizador é simples. Utilizar o HTTP `DELETE` método e a construção o URL correto com o ID de objeto:

```
DELETE https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```

Para ver um exemplo, introduzir este comando e ver o pedido de eliminação é impresso no console:

```cmd
B2C Delete-User <object-id-of-user>
```

Inspecione o `B2CGraphClient.SendGraphDeleteRequest(...)` método para obter detalhes sobre como enviar este pedido.

Pode realizar muitas outras ações com o Azure AD Graph API para além da gestão de utilizadores. O [referência da API do Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog) fornece detalhes sobre cada ação, juntamente com pedidos de exemplo.

## <a name="use-custom-attributes"></a>Utilizar atributos personalizados
A maioria dos aplicativos de consumidor tem de armazenar algum tipo de informações de perfil do usuário personalizada. Uma forma que pode fazer isso é definir um atributo personalizado no seu inquilino do B2C. Em seguida, pode tratar esse atributo da mesma forma que tratar qualquer outra propriedade num objeto de utilizador. Pode atualizar o atributo, elimine o atributo, consultar o atributo, envie o atributo como uma afirmação no início de sessão de tokens e muito mais.

Para definir um atributo personalizado no seu inquilino do B2C, consulte a [referência de atributo personalizado do B2C](active-directory-b2c-reference-custom-attr.md).

Pode ver os atributos personalizados definidos no seu inquilino B2C ao utilizar `B2CGraphClient`:

```cmd
B2C Get-B2C-Application
B2C Get-Extension-Attribute <object-id-in-the-output-of-the-above-command>
```

O resultado dessas funções revela os detalhes de cada atributo personalizado, tais como:

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

Pode utilizar o nome completo, como `extension_55dc0861f9a44eb999e0a8a872204adb_Jersey_Number`, como uma propriedade em seus objetos de utilizador.  Atualize o ficheiro. JSON com a nova propriedade e um valor para a propriedade e, em seguida, execute:

```cmd
B2C Update-User <object-id-of-user> <path-to-json-file>
```

Ao utilizar `B2CGraphClient`, terá uma aplicação de serviço que pode gerir os utilizadores de inquilino do B2C através de programação. `B2CGraphClient` utiliza a sua própria identidade de aplicação para autenticar para o Azure AD Graph API. Ele também adquire tokens utilizando um segredo do cliente. Como incorporar essa funcionalidade em seu aplicativo, lembre-se alguns pontos fundamentais para as aplicações B2C:

* Tem de conceder a aplicação as permissões adequadas no inquilino.
* Por enquanto, terá de utilizar a ADAL (não MSAL) para obter os tokens de acesso. (Também pode enviar mensagens de protocolo diretamente, sem utilizar uma biblioteca.)
* Quando chamar a Graph API, utilize `api-version=1.6`.
* Quando cria e atualizar utilizadores de consumidor, algumas propriedades são necessárias, conforme descrito acima.

Se tiver quaisquer perguntas ou pedidos para as ações que pretende efetuar ao utilizar a Graph API no seu inquilino do B2C, deixe um comentário sobre este artigo ou um problema de ficheiros no repositório de exemplo de código do GitHub.

