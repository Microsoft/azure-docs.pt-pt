---
title: Usar o API do Graph no Azure Active Directory B2C | Microsoft Docs
description: Como chamar o API do Graph para um locatário B2C usando uma identidade de aplicativo para automatizar o processo.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/07/2017
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 11a9fc521a7b17ae0ff2f579f173f4d43383bdd5
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70880083"
---
# <a name="azure-ad-b2c-use-the-azure-ad-graph-api"></a>Azure AD B2C: Utilizar a Graph API do Azure AD

>[!NOTE]
> Você deve usar o [API do Graph do Azure ad](/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-operations-overview) para gerenciar usuários em um diretório Azure ad B2C. Isso é diferente da API Microsoft Graph. Sabia mais [aqui](https://blogs.msdn.microsoft.com/aadgraphteam/2016/07/08/microsoft-graph-or-azure-ad-graph/).

Os locatários do Azure Active Directory (Azure AD) B2C tendem a ser muito grandes. Isso significa que muitas tarefas comuns de gerenciamento de locatários precisam ser executadas programaticamente. Um exemplo principal é o gerenciamento de usuários. Talvez seja necessário migrar um repositório de usuários existente para um locatário B2C. Talvez você queira hospedar o registro de usuário em sua própria página e criar contas de usuário em seu diretório Azure AD B2C nos bastidores. Esses tipos de tarefas exigem a capacidade de criar, ler, atualizar e excluir contas de usuário. Você pode executar essas tarefas usando o API do Graph do Azure AD.

Para locatários B2C, há dois modos principais de se comunicar com o API do Graph.

* Para tarefas interativas e de execução única, você deve atuar como uma conta de administrador no locatário B2C ao executar as tarefas. Esse modo requer que um administrador entre com credenciais antes que o administrador possa executar qualquer chamada para o API do Graph.
* Para tarefas automatizadas e contínuas, você deve usar algum tipo de conta de serviço fornecido com os privilégios necessários para executar tarefas de gerenciamento. No Azure AD, você pode fazer isso registrando um aplicativo e Autenticando no Azure AD. Isso é feito usando uma **ID de aplicativo** que usa a [concessão de credenciais de cliente OAuth 2,0](../active-directory/develop/service-to-service.md). Nesse caso, o aplicativo atua como ele mesmo, não como um usuário, para chamar o API do Graph.

Neste artigo, você aprenderá a executar o caso de uso automatizado. Você criará um .NET 4,5 `B2CGraphClient` que executa operações CRUD (criação, leitura, atualização e exclusão) de usuário. O cliente terá uma CLI (interface de linha de comando) do Windows que permite invocar vários métodos. No entanto, o código é escrito para se comportar de maneira não interativa e automatizada.

## <a name="get-an-azure-ad-b2c-tenant"></a>Obter um locatário Azure AD B2C
Antes de poder criar aplicativos ou usuários, você precisa de um locatário Azure AD B2C. Se você ainda não tiver um locatário, comece [com Azure ad B2C](active-directory-b2c-get-started.md).

## <a name="register-your-application-in-your-tenant"></a>Registrar seu aplicativo em seu locatário
Depois de ter um locatário B2C, você precisa registrar seu aplicativo usando o [portal do Azure](https://portal.azure.com).

> [!IMPORTANT]
> Para usar o API do Graph com seu locatário B2C, você precisa registrar um aplicativo usando o serviço de *registros de aplicativo* no menu portal do Azure, **não** no Azure ad B2C *aplicativos* . As instruções a seguir levam você ao menu apropriado. Não é possível reutilizar os aplicativos B2C existentes que você registrou no menu *aplicativos* B2C do Azure AD.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Escolha seu locatário de Azure AD B2C selecionando sua conta no canto superior direito da página.
3. No painel de navegação à esquerda, escolha **todos os serviços**, clique em **registros de aplicativo**e clique em **novo registro**.
4. Siga os avisos e crie uma nova aplicação. 
    1. Adicionar um nome apropriado
    2. Selecionar **contas somente neste diretório organizacional**
    3. Selecione **Web** como o tipo de aplicativo e forneça **qualquer URL de logon** `https://B2CGraphAPI`(por exemplo,), pois ele não é relevante para este exemplo.  
    4. Clique em registrar.
5. O aplicativo agora aparecerá na lista de aplicativos, clique nele para obter a **ID do aplicativo** (também conhecida como ID do cliente). Copie-o, pois você precisará dele em uma seção posterior.
6. No menu configurações, clique em **certificados & segredos**.
7. Na seção **segredos do cliente** , clique em **novo segredo do cliente**, forneça uma descrição para o segredo e selecione uma duração e clique em **Adicionar**. Copie o valor do segredo (também conhecido como segredo do cliente) para uso em uma seção posterior.

## <a name="configure-create-read-and-update-permissions-for-your-application"></a>Configurar permissões de criação, leitura e atualização para seu aplicativo
Agora você precisa configurar seu aplicativo para obter todas as permissões necessárias para criar, ler, atualizar e excluir usuários.

1. Continuando no menu de registros do aplicativo do portal do Azure, selecione seu aplicativo.
2. No menu configurações, clique em **permissões necessárias**.
3. No menu permissões necessárias, clique em **Azure Active Directory do Windows**.
4. No menu habilitar acesso, selecione a permissão **ler e gravar dados de diretório** em **permissões de aplicativo** e clique em **salvar**.
5. Por fim, de volta ao menu permissões necessárias, clique no botão **conceder permissões** .

Agora você tem um aplicativo que tem permissão para criar, ler e atualizar usuários de seu locatário B2C.

> [!NOTE]
> A concessão de permissões pode levar alguns minutos para ser totalmente processada.
> 
> 

## <a name="configure-delete-or-update-password-permissions-for-your-application"></a>Configurar permissões de exclusão ou atualização de senha para seu aplicativo
Atualmente, a permissão *ler e gravar dados do diretório* **não** inclui a capacidade de excluir usuários ou atualizar senhas de usuário. Se desejar dar ao seu aplicativo a capacidade de excluir usuários ou atualizar senhas, você precisará executar estas etapas adicionais que envolvem o PowerShell. caso contrário, você poderá pular para a próxima seção.

Primeiro, se você ainda não o tiver instalado, instale o [módulo do PowerShell do Azure ad V1 (MSOnline)](https://docs.microsoft.com/powershell/azure/active-directory/install-msonlinev1?view=azureadps-1.0):

```powershell
Install-Module MSOnline
```

Depois de instalar o módulo do PowerShell, conecte-se ao seu locatário do Azure AD B2C.

> [!IMPORTANT]
> Você precisa usar uma conta de administrador de locatário B2C que seja **local** para o locatário B2C. Essas contas têm a seguinte aparência myusername@myb2ctenant.onmicrosoft.com:.

```powershell
Connect-MsolService
```

Agora, usaremos a **ID do aplicativo** no script abaixo para atribuir o aplicativo à função de administrador da conta de usuário. Essas funções têm identificadores bem conhecidos, portanto, tudo o que você precisa fazer é inserir a **ID do aplicativo** no script abaixo.

```powershell
$applicationId = "<YOUR_APPLICATION_ID>"
$sp = Get-MsolServicePrincipal -AppPrincipalId $applicationId
Add-MsolRoleMember -RoleObjectId fe930be7-5e62-47db-91af-98c3a49a38b1 -RoleMemberObjectId $sp.ObjectId -RoleMemberType servicePrincipal
```

Seu aplicativo agora também tem permissões para excluir usuários ou atualizar senhas de seu locatário B2C.

## <a name="download-configure-and-build-the-sample-code"></a>Baixar, configurar e compilar o código de exemplo
Primeiro, baixe o código de exemplo e obtenha-o em execução. Em seguida, vamos dar uma olhada mais detalhada.  Você pode [baixar o código de exemplo como um arquivo. zip](https://github.com/AzureADQuickStarts/B2C-GraphAPI-DotNet/archive/master.zip). Você também pode cloná-lo em um diretório de sua escolha:

```cmd
git clone https://github.com/AzureADQuickStarts/B2C-GraphAPI-DotNet.git
```

Abra a `B2CGraphClient\B2CGraphClient.sln` solução do Visual Studio no Visual Studio. No projeto, abra o arquivo `App.config`. `B2CGraphClient` Substitua as três configurações de aplicativo pelos seus próprios valores:

```xml
<appSettings>
    <add key="b2c:Tenant" value="{Your Tenant Name}" />
    <add key="b2c:ClientId" value="{The ApplicationID from above}" />
    <add key="b2c:ClientSecret" value="{The Key from above}" />
</appSettings>
```

[!INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

Em seguida, clique com o `B2CGraphClient` botão direito do mouse na solução e recompile o exemplo. Se tiver êxito, agora você deve ter um `B2C.exe` arquivo executável localizado em. `B2CGraphClient\bin\Debug`

## <a name="build-user-crud-operations-by-using-the-graph-api"></a>Criar operações CRUD do usuário usando o API do Graph
Para usar o B2CGraphClient, abra um `cmd` prompt de comando do Windows e altere o diretório `Debug` para o diretório. Em seguida, `B2C Help` execute o comando.

```cmd
cd B2CGraphClient\bin\Debug
B2C Help
```

Isso exibirá uma breve descrição de cada comando. Cada vez que você invoca um desses comandos, `B2CGraphClient` o faz uma solicitação para o API do Graph do Azure AD.

### <a name="get-an-access-token"></a>Obter um token de acesso
Qualquer solicitação para o API do Graph requer um token de acesso para autenticação. `B2CGraphClient`usa o ADAL (Biblioteca de Autenticação do Active Directory de software livre) para ajudar a adquirir tokens de acesso. A ADAL facilita a aquisição de tokens fornecendo uma API simples e cuida de alguns detalhes importantes, como o cache de tokens de acesso. No entanto, você não precisa usar a ADAL para obter tokens. Você também pode obter tokens ao criar solicitações HTTP.

> [!NOTE]
> Este exemplo de código usa a ADAL v2 para se comunicar com o API do Graph.  Você deve usar a ADAL v2 ou V3 para obter tokens de acesso que podem ser usados com o API do Graph do Azure AD.
> 
> 

Quando `B2CGraphClient` é executado, ele cria uma instância `B2CGraphClient` da classe. O construtor dessa classe configura um scaffolding de autenticação ADAL:

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

Vamos usar o `B2C Get-User` comando como exemplo. Quando `B2C Get-User` é invocado sem nenhuma entrada adicional, a CLI chama `B2CGraphClient.GetAllUsers(...)` o método. Esse método chama `B2CGraphClient.SendGraphGetRequest(...)`, que envia uma solicitação HTTP Get para o API do Graph. Antes `B2CGraphClient.SendGraphGetRequest(...)` de enviar a solicitação GET, ele recebe primeiro um token de acesso usando a Adal:

```csharp
public async Task<string> SendGraphGetRequest(string api, string query)
{
    // First, use ADAL to acquire a token by using the app's identity (the credential)
    // The first parameter is the resource we want an access_token for; in this case, the Graph API.
    AuthenticationResult result = authContext.AcquireToken("https://graph.windows.net", credential);

    ...

```

Você pode obter um token de acesso para o API do Graph chamando o método `AuthenticationContext.AcquireToken(...)` Adal. Em seguida, a `access_token` Adal retorna um que representa a identidade do aplicativo.

### <a name="read-users"></a>Ler usuários
Quando desejar obter uma lista de usuários ou obter um usuário específico da API do Graph, você poderá enviar uma solicitação HTTP `GET` para o `/users` ponto de extremidade. Uma solicitação para todos os usuários em um locatário é parecida com esta:

```
GET https://graph.windows.net/contosob2c.onmicrosoft.com/users?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```

Para ver essa solicitação, execute:

 ```cmd
 B2C Get-User
 ```

Há duas coisas importantes a serem observadas:

* O token de acesso adquirido via Adal é adicionado `Authorization` ao cabeçalho usando o `Bearer` esquema.
* Para locatários B2C, você deve usar o parâmetro `api-version=1.6`de consulta.

Esses dois detalhes são tratados no `B2CGraphClient.SendGraphGetRequest(...)` método:

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
Ao criar contas de usuário em seu locatário B2C, você pode enviar uma solicitação `POST` http para o `/users` ponto de extremidade:

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

A maioria dessas propriedades nessa solicitação é necessária para criar usuários do consumidor. Para saber mais, clique [aqui](/previous-versions/azure/ad/graph/api/users-operations#CreateLocalAccountUser). Observe que os `//` comentários foram incluídos para ilustração. Não os inclua em uma solicitação real.

Para ver a solicitação, execute um dos seguintes comandos:

```cmd
B2C Create-User ..\..\..\usertemplate-email.json
B2C Create-User ..\..\..\usertemplate-username.json
```

O `Create-User` comando usa um arquivo. JSON como um parâmetro de entrada. Contém uma representação JSON de um objeto de usuário. Há dois arquivos. JSON de exemplo no código de exemplo: `usertemplate-email.json` e `usertemplate-username.json`. Você pode modificar esses arquivos para atender às suas necessidades. Além dos campos necessários acima, vários campos opcionais que você pode usar são incluídos nesses arquivos. Detalhes sobre os campos opcionais podem ser encontrados na [referência de entidade do Azure AD API do Graph](/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference#user-entity).

Você pode ver como a solicitação POST é construída no `B2CGraphClient.SendGraphPostRequest(...)`.

* Ele anexa um token de acesso ao `Authorization` cabeçalho da solicitação.
* Ele define `api-version=1.6`.
* Ele inclui o objeto de usuário JSON no corpo da solicitação.

> [!NOTE]
> Se as contas que você deseja migrar de um repositório de usuários existente tiverem uma intensidade de senha menor do que a [força de senha forte imposta por Azure ad B2C](/previous-versions/azure/jj943764(v=azure.100)), você poderá desabilitar o requisito de `DisableStrongPassword` senha forte usando o valor no `passwordPolicies`propriedade. Por exemplo, você pode modificar a solicitação de criação de usuário fornecida acima da `"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"`seguinte maneira:.
> 
> 

### <a name="update-consumer-user-accounts"></a>Atualizar contas de usuário do consumidor
Quando você atualiza objetos de usuário, o processo é semelhante ao que você usa para criar objetos de usuário. Mas esse processo usa o método `PATCH` http:

```
PATCH https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
Content-Type: application/json
Content-Length: 37

{
    "displayName": "Joe Consumer"                // this request updates only the user's displayName
}
```

Tente atualizar um usuário atualizando seus arquivos JSON com novos dados. Em seguida, você `B2CGraphClient` pode usar para executar um destes comandos:

```cmd
B2C Update-User <user-object-id> ..\..\..\usertemplate-email.json
B2C Update-User <user-object-id> ..\..\..\usertemplate-username.json
```

Inspecione o `B2CGraphClient.SendGraphPatchRequest(...)` método para obter detalhes sobre como enviar essa solicitação.

### <a name="search-users"></a>Procurar utilizadores
Você pode pesquisar usuários em seu locatário B2C de duas maneiras. Um, usando a ID de objeto do usuário ou dois, usando o identificador de entrada do usuário (ou seja, a `signInNames` Propriedade).

Execute um dos seguintes comandos para procurar um usuário específico:

```cmd
B2C Get-User <user-object-id>
B2C Get-User <filter-query-expression>
```

Aqui estão alguns exemplos:

```cmd
B2C Get-User 2bcf1067-90b6-4253-9991-7f16449c2d91
B2C Get-User $filter=signInNames/any(x:x/value%20eq%20%27joeconsumer@gmail.com%27)
```

### <a name="delete-users"></a>Excluir usuários
O processo de exclusão de um usuário é simples. Use o método `DELETE` http e construa a URL com a ID de objeto correta:

```
DELETE https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```

Para ver um exemplo, digite este comando e exiba a solicitação de exclusão que é impressa no console:

```cmd
B2C Delete-User <object-id-of-user>
```

Inspecione o `B2CGraphClient.SendGraphDeleteRequest(...)` método para obter detalhes sobre como enviar essa solicitação.

Você pode executar muitas outras ações com o API do Graph do Azure AD, além do gerenciamento de usuários. A [referência de API do Graph do Azure ad](/previous-versions/azure/ad/graph/api/api-catalog) fornece detalhes sobre cada ação, juntamente com as solicitações de exemplo.

## <a name="use-custom-attributes"></a>Utilizar atributos personalizados
A maioria dos aplicativos de consumidor precisa armazenar algum tipo de informação de perfil de usuário personalizado. Uma maneira de fazer isso é definir um atributo personalizado em seu locatário B2C. Em seguida, você pode tratar esse atributo da mesma forma que trata qualquer outra propriedade em um objeto de usuário. Você pode atualizar o atributo, excluir o atributo, consultar pelo atributo, enviar o atributo como uma declaração em tokens de entrada e muito mais.

Para definir um atributo personalizado em seu locatário B2C, consulte a [referência de atributo personalizado B2C](active-directory-b2c-reference-custom-attr.md).

Você pode exibir os atributos personalizados definidos em seu locatário B2C usando `B2CGraphClient`:

```cmd
B2C Get-B2C-Application
B2C Get-Extension-Attribute <object-id-in-the-output-of-the-above-command>
```

A saída dessas funções revela os detalhes de cada atributo personalizado, como:

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

Você pode usar o nome completo, `extension_55dc0861f9a44eb999e0a8a872204adb_Jersey_Number`como, como uma propriedade em seus objetos de usuário.  Atualize o arquivo. JSON com a nova propriedade e um valor para a propriedade e, em seguida, execute:

```cmd
B2C Update-User <object-id-of-user> <path-to-json-file>
```

Usando `B2CGraphClient`o, você tem um aplicativo de serviço que pode gerenciar seus usuários de locatário do B2C de forma programática. `B2CGraphClient`usa sua própria identidade de aplicativo para autenticar para o API do Graph do Azure AD. Ele também adquire tokens usando um segredo do cliente. Ao incorporar essa funcionalidade em seu aplicativo, lembre-se de alguns pontos-chave para aplicativos B2C:

* Você precisa conceder ao aplicativo as permissões apropriadas no locatário.
* Por enquanto, você precisa usar a ADAL (não MSAL) para obter tokens de acesso. (Você também pode enviar mensagens de protocolo diretamente, sem usar uma biblioteca.)
* Ao chamar o API do Graph, use `api-version=1.6`.
* Quando você cria e atualiza os usuários do consumidor, algumas propriedades são necessárias, conforme descrito acima.

Se você tiver dúvidas ou solicitações de ações que deseja executar usando o API do Graph em seu locatário B2C, deixe um comentário neste artigo ou arquivo um problema no repositório de exemplo de código do GitHub.

