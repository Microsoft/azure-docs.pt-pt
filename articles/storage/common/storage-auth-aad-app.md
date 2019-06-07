---
title: Autenticar com o Azure Active Directory para aceder a dados de BLOBs e filas a partir da sua aplicação de cliente
description: Utilize o Azure Active Directory para autenticar a partir de dentro de um aplicativo de cliente, adquirir um token de OAuth 2.0 e autorizar pedidos ao armazenamento de Blobs do Azure e o armazenamento de filas.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 06/05/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: e45fe20e93d81c1cfd1f868b40f76743558758bb
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/07/2019
ms.locfileid: "66754921"
---
# <a name="authenticate-with-azure-active-directory-from-an-application-for-access-to-blobs-and-queues"></a>Autenticar com o Azure Active Directory a partir de uma aplicação para o acesso para blobs e filas

Das principais vantagens da utilização do Azure Active Directory (Azure AD) com o armazenamento de Blobs do Azure ou o armazenamento de filas é que as suas credenciais não precisam mais ser armazenada no seu código. Em vez disso, pode pedir um token de acesso de OAuth 2.0 da plataforma de identidades da Microsoft (anteriormente conhecido como o Azure AD). O Azure AD autentica o principal de segurança (um utilizador, grupo ou principal de serviço) que executa a aplicação. Se a autenticação for bem-sucedida, do Azure AD devolve o token de acesso à aplicação e a aplicação, em seguida, pode utilizar o token de acesso para autorizar os pedidos para o armazenamento de Blobs do Azure ou o armazenamento de filas.

Este artigo mostra como configurar seu aplicativo nativo ou aplicativo web para a autenticação com o Azure AD. O .NET de recursos de exemplo de código, mas outras linguagens usam uma abordagem semelhante.

Para uma descrição geral do fluxo de concessão de código do OAuth 2.0, consulte [fluxo de concessão de acesso de autorizar a aplicações de web do Azure Active Directory usando o código de OAuth 2.0](../../active-directory/develop/v2-oauth2-auth-code-flow.md).

## <a name="assign-an-rbac-role-to-an-azure-ad-security-principal"></a>Atribuir uma função RBAC para um principal de segurança do Azure AD

Para autenticar uma entidade de segurança da sua aplicação de armazenamento do Azure, primeiro de configurar as definições de controlo (RBAC) de acesso baseado em funções para aquela entidade de segurança. O armazenamento do Azure define funções RBAC incorporadas que abrangem as permissões para contentores e filas. Quando a função RBAC é atribuída a uma entidade de segurança, essa entidade de segurança é concedido acesso a esse recurso. Para obter mais informações, consulte [gerir direitos de acesso aos dados de Blobs do Azure e a fila com o RBAC](storage-auth-aad-rbac.md).

## <a name="register-your-application-with-an-azure-ad-tenant"></a>Registar a aplicação com um inquilino do Azure AD

O primeiro passo para utilizar o Azure AD para autorizar o acesso aos recursos de armazenamento está a registar a aplicação de cliente com o inquilino do Azure AD a partir da [portal do Azure](https://portal.azure.com). Ao registar a aplicação de cliente, fornece informações sobre a aplicação para o Azure AD. O Azure AD, em seguida, fornece um ID de cliente (também chamado de um *ID da aplicação*) que utilizou para associar a sua aplicação com o Azure AD em tempo de execução. Para saber mais sobre o ID de cliente, veja [aplicativos e objetos de principal de serviço no Azure Active Directory](../../active-directory/develop/app-objects-and-service-principals.md).

Para registar a aplicação de armazenamento do Azure, siga os passos apresentados no [início rápido: Registar uma aplicação com a plataforma de identidade do Microsoft](../../active-directory/develop/quickstart-configure-app-access-web-apis.md). A imagem seguinte mostra as configurações comuns para registar uma aplicação web:

![Captura de ecrã que mostra como registar a sua aplicação de armazenamento com o Azure AD](./media/storage-auth-aad-app/app-registration.png)

> [!NOTE]
> Se registar a sua aplicação como um aplicativo nativo, pode especificar qualquer URI válida para o **URI de redirecionamento**. Para aplicativos nativos, este valor não tem de ser um URL real. Para aplicações web, o URI de redirecionamento tem de ser um URI válido, porque Especifica o URL para os quais tokens são fornecidos.

Depois de registar a aplicação, verá o ID da aplicação (ou o ID de cliente) em **definições**:

![Captura de ecrã que mostra o ID de cliente](./media/storage-auth-aad-app/app-registration-client-id.png)

Para obter mais informações sobre como registar uma aplicação com o Azure AD, consulte [integrar aplicações com o Azure Active Directory](../../active-directory/develop/quickstart-v2-register-an-app.md).

## <a name="grant-your-registered-app-permissions-to-azure-storage"></a>Conceder as permissões de aplicação registada para o armazenamento do Azure

Em seguida, conceda as permissões de aplicação para chamar as APIs de armazenamento do Azure. Este passo permite que a sua aplicação autorizar os pedidos ao armazenamento do Azure com o Azure AD.

1. Sobre o **descrição geral** página para a aplicação registada, selecione **permissões de API de visualização**.
1. Na **permissões de API** secção, selecione **adicionar uma permissão** e escolha **APIs minha organização utiliza**.
1. Sob o **APIs utiliza a minha organização** secção, procure "Azure Storage" e selecione **armazenamento do Azure** da lista de resultados a apresentar o **permissões de pedido de API** painel.

    ![Captura de ecrã que mostra permissões para o armazenamento](media/storage-auth-aad-app/registered-app-permissions-1.png)

1. Sob **o tipo de permissões de seu aplicativo requer?** , tenha em atenção que o tipo de permissão disponíveis é **permissões delegadas**. Por predefinição, esta opção está selecionada para.
1. No **selecionar permissões** secção a **permissões de API do pedido** painel, selecione a caixa de verificação junto a **user_impersonation**, em seguida, clique em **adicionar permissões**.
1. O **permissões API** painel mostra, agora, se a sua aplicação do Azure AD tem acesso ao Microsoft Graph e o armazenamento do Azure. São concedidas permissões para o Microsoft Graph automaticamente quando primeiro registar a aplicação com o Azure AD.

    ![Captura de ecrã que mostra registar permissões de aplicações](media/storage-auth-aad-app/registered-app-permissions-2.png)

## <a name="libraries-for-token-acquisition"></a>Bibliotecas para a aquisição do token

Assim que tiver registado a sua aplicação e ele concedidas permissões para aceder aos dados no armazenamento de Blobs do Azure ou no armazenamento de filas, é possível adicionar código à sua aplicação para autenticar uma entidade de segurança e adquirir um token de OAuth 2.0. Para autenticar e adquirir o token, pode utilizar uma da [bibliotecas de autenticação de plataforma de identidade do Microsoft](../../active-directory/develop/reference-v2-libraries.md) ou outra biblioteca de código-fonte aberto que suporta o OpenID Connect 1.0. Seu aplicativo, em seguida, pode utilizar o token de acesso para autorizar um pedido com o armazenamento de Blobs do Azure ou o armazenamento de filas.

Para obter uma lista de cenários para os quais aquisição de tokens é suportado, consulte a [cenários](https://aka.ms/msal-net-scenarios) secção a [Microsoft Authentication Library (MSAL) para .NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) repositório do GitHub.

## <a name="net-code-example-create-a-block-blob"></a>Exemplo de código do .NET: Criar um blob de blocos

O exemplo de código mostra como obter um acesso de token do Azure AD. O token de acesso é utilizado para autenticar o utilizador especificado e, em seguida, autorizar um pedido para criar um blob de blocos. Para que isso funcione de exemplo, primeiro siga os passos descritos nas secções anteriores.

> [!NOTE]
> Como proprietário da conta de armazenamento do Azure, não é atribuídos automaticamente permissões para aceder aos dados. Tem explicitamente de atribuir-se uma função RBAC do armazenamento do Azure. Pode atribuí-la no nível da sua subscrição, grupo de recursos, conta de armazenamento ou contentor ou fila.
>
> Por exemplo, para executar o código de exemplo numa conta de armazenamento na qual seja proprietário e em sua própria identidade de utilizador, tem de atribuir a função RBAC para contribuinte de dados do Blob a próprio. Caso contrário, a chamada para criar o blob irá falhar com o código de estado HTTP 403 (proibido). Para obter mais informações, consulte [gerir direitos de acesso aos dados de armazenamento com o RBAC](storage-auth-aad-rbac.md).

### <a name="well-known-values-for-authentication-with-azure-ad"></a>Valores conhecidas para autenticação com o Azure AD

Para autenticar uma entidade de segurança com o Azure AD, tem de incluir alguns valores conhecidos em seu código.

#### <a name="azure-ad-authority"></a>Autoridade do Azure AD

Para a nuvem pública da Microsoft, o Azure base autoridade AD é o seguinte, onde *id de inquilino* é o seu ID de inquilino do Active Directory (ou o ID de diretório):

`https://login.microsoftonline.com/<tenant-id>/`

O ID de inquilino identifica o inquilino do Azure AD para utilizar para autenticação. Para obter o ID de inquilino, siga os passos descritos na seção intitulada **obter o ID de inquilino do Azure Active Directory**.

#### <a name="storage-resource-id"></a>ID de recurso de armazenamento

Utilize o ID de recurso de armazenamento do Azure para adquirir um token de autorização de solicitações para o armazenamento do Azure:

`https://storage.azure.com/`

### <a name="get-the-tenant-id-for-your-azure-active-directory"></a>Obter o ID de inquilino do Azure Active Directory

Para obter o ID de inquilino, siga estes passos:

1. No portal do Azure, selecione o seu Active Directory.
2. Clique em **Propriedades**.
3. Copie o valor GUID fornecido para o **ID de diretório**. Este valor também é chamado de ID do inquilino.

![Captura de ecrã que mostra como copiar o ID de inquilino](./media/storage-auth-aad-app/aad-tenant-id.png)

## <a name="set-up-a-basic-web-app-to-authenticate-to-azure-ad"></a>Configurar uma aplicação web básica para autenticar para o Azure AD

Quando o aplicativo acessa o armazenamento do Azure, ele assim por diante nome do usuário. Para experimentar este exemplo de código, precisa de uma web aplicativo que solicita ao usuário pode iniciar sessão com uma identidade do Azure AD. É possível transferir este [exemplo de código](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2) para testar uma aplicação web básica que autentique com a sua conta do Azure AD.

### <a name="completed-sample"></a>Exemplo concluído

Uma versão de trabalho completa do código de exemplo mostrado neste artigo pode ser transferida a partir [GitHub](http://aka.ms/aadstorage). Rever e executar o exemplo completo pode ser útil para compreender os exemplos de código.

### <a name="add-references-and-using-statements"></a>Adicionar referências e instruções "using"  

No Visual Studio, instale a biblioteca de cliente de armazenamento do Azure. Do **ferramentas** menu, selecione **Gestor de pacotes Nuget**, em seguida, **Package Manager Console**. Escreva os seguintes comandos para a janela de consola para instalar os pacotes necessários a partir da biblioteca de cliente de armazenamento do Azure para .NET:

```console
Install-Package Microsoft.Azure.Storage.Blob
Install-Package Microsoft.Azure.Storage.Common
```

Em seguida, adicione as seguintes instruções "using" para o ficheiro HomeController.cs:

```csharp
using System;
using Microsoft.Identity.Client; //MSAL library for getting the access token
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.WindowsAzure.Storage.Blob;
```

### <a name="create-a-block-blob"></a>Criar um blob de blocos

Adicione o seguinte fragmento de código para criar um blob de blocos:

```csharp
private static async Task<string> CreateBlob(string accessToken)
{
    // Create a blob on behalf of the user
    TokenCredential tokenCredential = new TokenCredential(accessToken);
    StorageCredentials storageCredentials = new StorageCredentials(tokenCredential);

    // Replace the URL below with your storage account URL
    CloudBlockBlob blob =
        new CloudBlockBlob(
            new Uri("https://<storage-account>.blob.core.windows.net/sample-container/Blob1.txt"),
            storageCredentials);
    await blob.UploadTextAsync("Blob created by Azure AD authenticated user.");
    return "Blob successfully created";
}
```

> [!NOTE]
> Para autorizar as operações de BLOBs e filas com um token de OAuth 2.0, tem de utilizar HTTPS.

No exemplo acima, a biblioteca de cliente .NET processa a autorização do pedido para criar o blob de blocos. Bibliotecas de cliente de armazenamento do Azure para outros idiomas também lidar com a autorização do pedido para. No entanto, se estiver a chamar uma operação de armazenamento do Azure com um token de OAuth utilizando a API REST, em seguida, terá de autorizar a solicitação usando o token OAuth.

Para chamar operações de serviço de BLOBs e filas com tokens de acesso de OAuth, passar o token de acesso a **autorização** usando o cabeçalho a **portador** esquema e especifique uma versão de serviço de 2017-11-09 ou superior, como mostrado no exemplo a seguir:

```https
GET /container/file.txt HTTP/1.1
Host: mystorageaccount.blob.core.windows.net
x-ms-version: 2017-11-09
Authorization: Bearer eyJ0eXAiOnJKV1...Xd6j
```

### <a name="get-an-oauth-token-from-azure-ad"></a>Obter um OAuth token do Azure AD

Em seguida, adicione um método que solicita um token do Azure AD. O token de que solicitar estará em nome do utilizador e utilizamos o método GetTokenOnBehalfOfUser.

Para pedir o token, terá dos seguintes valores do registo da sua aplicação,

- ID de inquilino (ou diretório)
- ID de cliente (ou aplicação)
- URI de redirecionamento do cliente

Lembre-se de que se apenas iniciou sessão e que está a solicitar um token para o `storage.azure.com` recurso, precisará apresentar ao usuário uma interface do Usuário em que o utilizador pode consentir essa ação em seu nome. Para facilitar que precisa capturar o `MsalUiRequiredException` e adicionar a funcionalidade para pedir consentimento do utilizador, conforme mostrado no exemplo a seguir:

```csharp
public async Task<IActionResult> Blob()
{
    var scopes = new string[] { "https://storage.azure.com/user_impersonation" };
    try
    {
        var accessToken =
            await _tokenAcquisition.GetAccessTokenOnBehalfOfUser(HttpContext, scopes);
        ViewData["Message"] = await CreateBlob(accessToken);
        return View();
    }
    catch (MsalUiRequiredException ex)
    {
        AuthenticationProperties properties = BuildAuthenticationPropertiesForIncrementalConsent(scopes, ex);
        return Challenge(properties);
    }
}
```

Consentimento é o processo de um concessão de autorização para uma aplicação para aceder a recursos protegidos em seu nome de utilizador. A plataforma de identidade do Microsoft 2.0 oferece suporte a consentimento incremental, que significa que uma entidade de segurança pode pedir um conjunto mínimo de permissões inicialmente e adicionar permissões ao longo do tempo, conforme necessário. Quando seu código solicita um token de acesso, especifique o âmbito de permissões que a sua aplicação precisa num determinado momento no `scope` parâmetro. O seguinte método constrói as propriedades de autenticação para pedir consentimento incremental:

```csharp
private AuthenticationProperties BuildAuthenticationPropertiesForIncrementalConsent(string[] scopes, MsalUiRequiredException ex)
{
    AuthenticationProperties properties = new AuthenticationProperties();

    // Set the scopes, including the scopes that ADAL.NET / MSAL.NET need for the Token cache.
    string[] additionalBuildInScopes = new string[] { "openid", "offline_access", "profile" };
    properties.SetParameter<ICollection<string>>(OpenIdConnectParameterNames.Scope, scopes.Union(additionalBuildInScopes).ToList());

    // Attempt to set the login_hint so that the logged-in user is not presented with an account selection dialog.
    string loginHint = HttpContext.User.GetLoginHint();
    if (!string.IsNullOrWhiteSpace(loginHint))
    {
        properties.SetParameter<string>(OpenIdConnectParameterNames.LoginHint, loginHint);

        string domainHint = HttpContext.User.GetDomainHint();
        properties.SetParameter<string>(OpenIdConnectParameterNames.DomainHint, domainHint);
    }

    // Specify any additional claims that are required (for instance, MFA).
    if (!string.IsNullOrEmpty(ex.Claims))
    {
        properties.Items.Add("claims", ex.Claims);
    }

    return properties;
}
```

## <a name="next-steps"></a>Passos Seguintes

- Para saber mais sobre a plataforma de identidade da Microsoft, consulte [plataforma de identidade do Microsoft](https://docs.microsoft.com/azure/active-directory/develop/).
- Para saber mais sobre as funções do RBAC para o armazenamento do Azure, veja [gerir direitos de acesso aos dados de armazenamento com o RBAC](storage-auth-aad-rbac.md).
- Para saber mais sobre a utilização de identidades geridas para recursos do Azure com o armazenamento do Azure, veja [autenticar o acesso a blobs e filas com o Azure Active Directory e de identidades geridas para recursos do Azure](storage-auth-aad-msi.md).
