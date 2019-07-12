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
ms.openlocfilehash: da10b70b85e284173abbd1779fb1d39f477ca0cd
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/10/2019
ms.locfileid: "67723228"
---
# <a name="authenticate-with-azure-active-directory-from-an-application-for-access-to-blobs-and-queues"></a>Autenticar com o Azure Active Directory a partir de uma aplicação para o acesso para blobs e filas

Das principais vantagens da utilização do Azure Active Directory (Azure AD) com o armazenamento de Blobs do Azure ou o armazenamento de filas é que as suas credenciais não precisam mais ser armazenada no seu código. Em vez disso, pode pedir um token de acesso de OAuth 2.0 da plataforma de identidades da Microsoft (anteriormente conhecido como o Azure AD). O Azure AD autentica o principal de segurança (um utilizador, grupo ou principal de serviço) que executa a aplicação. Se a autenticação for bem-sucedida, do Azure AD devolve o token de acesso à aplicação e a aplicação, em seguida, pode utilizar o token de acesso para autorizar os pedidos para o armazenamento de Blobs do Azure ou o armazenamento de filas.

Este artigo mostra como configurar o seu aplicativo nativo ou aplicativo web para a autenticação com a plataforma de identidade do Microsoft 2.0. O .NET de recursos de exemplo de código, mas outras linguagens usam uma abordagem semelhante. Para obter mais informações sobre a plataforma de identidade do Microsoft 2.0, consulte [visão geral de plataforma (v2.0) do Microsoft identity](../../active-directory/develop/v2-overview.md).

Para uma descrição geral do fluxo de concessão de código do OAuth 2.0, consulte [fluxo de concessão de acesso de autorizar a aplicações de web do Azure Active Directory usando o código de OAuth 2.0](../../active-directory/develop/v2-oauth2-auth-code-flow.md).

## <a name="assign-a-role-to-an-azure-ad-security-principal"></a>Atribuir uma função a uma entidade de segurança do Azure AD

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
1. Na **permissões de API** secção, selecione **adicionar uma permissão** e escolha **APIs da Microsoft**.
1. Selecione **armazenamento do Azure** na lista de resultados a apresentar a **permissões de pedido de API** painel.
1. Sob **o tipo de permissões de seu aplicativo requer?** , verifique se o tipo de permissão disponíveis **permissões delegadas**. Por predefinição, esta opção está selecionada para.
1. No **selecionar permissões** secção a **permissões de API do pedido** painel, selecione a caixa de verificação junto a **user_impersonation**, em seguida, clique em **adicionar permissões**.

    ![Captura de ecrã que mostra permissões para o armazenamento](media/storage-auth-aad-app/registered-app-permissions-1.png)

O **permissões API** painel mostra, agora que sua registados aplicação do Azure AD tem acesso ao Microsoft Graph e o armazenamento do Azure. São concedidas permissões para o Microsoft Graph automaticamente quando primeiro registar a aplicação com o Azure AD.

![Captura de ecrã que mostra registar permissões de aplicações](media/storage-auth-aad-app/registered-app-permissions-2.png)

## <a name="create-a-client-secret"></a>Criar um segredo do cliente

A aplicação tem um segredo do cliente para provar a identidade ao pedir um token. Para adicionar o segredo do cliente, siga estes passos:

1. Navegue para o registo de aplicação no portal do Azure.
1. Selecione o **certificados e segredos** definição.
1. Sob **segredos de cliente**, clique em **novo segredo do cliente** para criar um novo segredo.
1. Forneça uma descrição para o segredo e escolha o intervalo de expiração pretendido.
1. Imediatamente, copie o valor do segredo do novo para uma localização segura. O valor total é apresentado a apenas uma vez.

    ![Captura de ecrã que mostra segredo do cliente](media/storage-auth-aad-app/client-secret.png)

## <a name="client-libraries-for-token-acquisition"></a>Bibliotecas de cliente para a aquisição do token

Assim que tiver registado a sua aplicação e ele concedidas permissões para aceder aos dados no armazenamento de Blobs do Azure ou no armazenamento de filas, é possível adicionar código à sua aplicação para autenticar uma entidade de segurança e adquirir um token de OAuth 2.0. Para autenticar e adquirir o token, pode utilizar uma da [bibliotecas de autenticação de plataforma de identidade do Microsoft](../../active-directory/develop/reference-v2-libraries.md) ou outra biblioteca de código-fonte aberto que suporta o OpenID Connect 1.0. Seu aplicativo, em seguida, pode utilizar o token de acesso para autorizar um pedido com o armazenamento de Blobs do Azure ou o armazenamento de filas.

Para obter uma lista de cenários para os quais aquisição de tokens é suportado, consulte a [cenários](https://aka.ms/msal-net-scenarios) secção a [Microsoft Authentication Library (MSAL) para .NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) repositório do GitHub.

## <a name="net-code-example-create-a-block-blob"></a>Exemplo de código do .NET: Criar um blob de blocos

O exemplo de código mostra como obter um acesso de token do Azure AD. O token de acesso é utilizado para autenticar o utilizador especificado e, em seguida, autorizar um pedido para criar um blob de blocos. Para que isso funcione de exemplo, primeiro siga os passos descritos nas secções anteriores.

Para pedir o token, terá os seguintes valores do registo da sua aplicação:

- O nome do seu domínio do Azure AD. Obter este valor a partir da **descrição geral** página do seu Azure Active Directory.
- O ID de inquilino (ou diretório). Obter este valor a partir da **descrição geral** página do seu registo de aplicações.
- O ID de cliente (ou aplicação). Obter este valor a partir da **descrição geral** página do seu registo de aplicações.
- O cliente URI de redirecionamento. Obter este valor a partir da **autenticação** definições para o registo de aplicação.
- O valor do segredo do cliente. Obter este valor a partir da localização para o qual anteriormente copiado.

### <a name="well-known-values-for-authentication-with-azure-ad"></a>Valores conhecidas para autenticação com o Azure AD

Para autenticar uma entidade de segurança com o Azure AD, tem de incluir alguns valores conhecidos em seu código.

#### <a name="azure-ad-authority"></a>Autoridade do Azure AD

Para a nuvem pública da Microsoft, o Azure base autoridade AD é o seguinte, onde *id de inquilino* é o seu ID de inquilino do Active Directory (ou o ID de diretório):

`https://login.microsoftonline.com/<tenant-id>/`

O ID de inquilino identifica o inquilino do Azure AD para utilizar para autenticação. Ele também é referido como o ID do diretório. Para obter o ID de inquilino, navegue para o **descrição geral** página para o seu registo de aplicação no portal do Azure e copie o valor a partir daí.

#### <a name="storage-resource-id"></a>ID de recurso de armazenamento

Utilize o ID de recurso de armazenamento do Azure para adquirir um token de autorização de solicitações para o armazenamento do Azure:

`https://storage.azure.com/`

### <a name="create-a-storage-account-and-container"></a>Criar uma conta de armazenamento e um contentor

Para executar o código de exemplo, crie uma conta de armazenamento na mesma subscrição, como o Azure Active Directory. Em seguida, crie um contentor dentro dessa conta de armazenamento. O código de exemplo irá criar um blob de blocos neste contentor.

Em seguida, atribuir explicitamente a **contribuinte de dados de Blob de armazenamento** função para a conta de utilizador sob a qual executará o código de exemplo. Para obter instruções sobre como atribuir esta função no portal do Azure, consulte [conceder acesso a dados BLOBs e filas do Azure com o RBAC no portal do Azure](storage-auth-aad-rbac-portal.md).

> [!NOTE]
> Quando cria uma conta de armazenamento do Azure, não é atribuídos automaticamente permissões para aceder aos dados através do Azure AD. Tem explicitamente de atribuir-se uma função RBAC do armazenamento do Azure. Pode atribuí-la no nível da sua subscrição, grupo de recursos, conta de armazenamento ou contentor ou fila.

### <a name="create-a-web-application-that-authorizes-access-to-blob-storage-with-azure-ad"></a>Criar uma aplicação web que autoriza o acesso ao armazenamento de Blobs com o Azure AD

Quando a aplicação acede ao armazenamento do Azure, ele assim por diante nome do usuário, que significa que os recursos de BLOBs ou filas são acedidos utilizando as permissões do utilizador que tem sessão iniciada. Para experimentar este exemplo de código, precisa de uma aplicação web que pede ao utilizador para iniciar sessão com uma identidade do Azure AD. Pode criar o seu próprio, ou utilizar a aplicação de exemplo fornecida pela Microsoft.

Um aplicativo de web de exemplo completo que recebe um token e a utiliza para criar um blob no armazenamento do Azure está disponível no [GitHub](https://aka.ms/aadstorage). Rever e executar o exemplo concluído pode ser útil para compreender os exemplos de código. Para obter instruções sobre como executar o exemplo concluído, consulte a secção intitulada [modo de exibição e execute o exemplo concluído](#view-and-run-the-completed-sample).

#### <a name="add-references-and-using-statements"></a>Adicionar referências e instruções "using"  

No Visual Studio, instale a biblioteca de cliente de armazenamento do Azure. Do **ferramentas** menu, selecione **Gestor de pacotes Nuget**, em seguida, **Package Manager Console**. Escreva os seguintes comandos para a janela de consola para instalar os pacotes necessários a partir da biblioteca de cliente de armazenamento do Azure para .NET:

```console
Install-Package Microsoft.Azure.Storage.Blob
Install-Package Microsoft.Azure.Storage.Common
```

Em seguida, adicione as seguintes instruções "using" para o ficheiro HomeController.cs:

```csharp
using Microsoft.Identity.Client; //MSAL library for getting the access token
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.WindowsAzure.Storage.Blob;
```

#### <a name="create-a-block-blob"></a>Criar um blob de blocos

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
            new Uri("https://<storage-account>.blob.core.windows.net/<container>/Blob1.txt"),
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

#### <a name="get-an-oauth-token-from-azure-ad"></a>Obter um OAuth token do Azure AD

Em seguida, adicione um método que solicita um token do Azure AD. O token de que solicitar estará em nome do utilizador e utilizamos o método GetTokenOnBehalfOfUser.

Lembre-se de que se iniciou recentemente e que está a solicitar um token para o `storage.azure.com` recurso, precisará apresentar ao usuário uma interface do Usuário em que o utilizador pode consentir essa ação em seu nome. Para facilitar que precisa capturar o `MsalUiRequiredException` e adicionar a funcionalidade para pedir consentimento do utilizador, conforme mostrado no exemplo a seguir:

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

Consentimento é o processo de um concessão de autorização para uma aplicação para aceder a recursos protegidos em seu nome de utilizador. A plataforma de identidade do Microsoft 2.0 oferece suporte a consentimento incremental, que significa que uma entidade de segurança pode pedir um conjunto mínimo de permissões inicialmente e adicionar permissões ao longo do tempo, conforme necessário. Quando seu código solicita um token de acesso, especifique o âmbito de permissões que a sua aplicação precisa num determinado momento no `scope` parâmetro. Para obter mais informações sobre o consentimento incremental, consulte a secção intitulada **consentimento de Incremental e dinâmico** na [porquê atualizar à plataforma de identidades da Microsoft (v2.0)?](../../active-directory/develop/azure-ad-endpoint-comparison.md#incremental-and-dynamic-consent).

O seguinte método constrói as propriedades de autenticação para pedir consentimento incremental:

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

## <a name="view-and-run-the-completed-sample"></a>Ver e executar o exemplo concluído

Para executar o aplicativo de exemplo, primeiro clonar ou transferi-lo a partir [GitHub](https://aka.ms/aadstorage). Em seguida, atualize a aplicação conforme descrito nas seções a seguir.

### <a name="provide-values-in-the-settings-file"></a>Forneça os valores no ficheiro de definições

Em seguida, atualize o *appSettings* ficheiro pelos seus próprios valores, da seguinte forma:

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "<azure-ad-domain-name>.onmicrosoft.com",
    "TenantId": "<tenant-id>",
    "ClientId": "<client-id>",
    "CallbackPath": "/signin-oidc",
    "SignedOutCallbackPath ": "/signout-callback-oidc",

    // To call an API
    "ClientSecret": "<client-secret>"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Warning"
    }
  },
  "AllowedHosts": "*"
}
```

### <a name="update-the-storage-account-and-container-name"></a>Atualizar o nome de conta e contentor de armazenamento

Na *HomeController.cs* de ficheiros, atualize o URI que referencia o blob de blocos para utilizar o nome da sua conta de armazenamento e o contentor:

```csharp
CloudBlockBlob blob = new CloudBlockBlob(
                      new Uri("https://<storage-account>.blob.core.windows.net/<container>/Blob1.txt"),
                      storageCredentials);
```

### <a name="enable-implicit-grant-flow"></a>Habilitar o fluxo de concessão implícita

Para executar o exemplo, se pretender configurar o fluxo de concessão implícita para o seu registo de aplicação. Siga estes passos.

1. Navegue para o registo de aplicação no portal do Azure.
1. Na secção Gerir, selecione o **autenticação** definição.
1. Sob **definições avançadas**, na **concessão implícita** secção, selecione as caixas de verificação para permitir que os tokens de acesso e tokens de ID, conforme mostrado na imagem seguinte:

    ![Captura de ecrã que mostra como ativar as definições de fluxo de concessão implícita](media/storage-auth-aad-app/enable-implicit-grant-flow.png)

### <a name="update-the-port-used-by-localhost"></a>Atualizar a porta usada pelo localhost

Quando executar o exemplo, pode achar que tem de atualizar o redirecionamento URI especificado no registo da aplicação para utilizar o *localhost* porta atribuída no tempo de execução. Para atualizar o URI de redirecionamento a utilizar a porta atribuída, siga estes passos:

1. Navegue para o registo de aplicação no portal do Azure.
1. Na secção Gerir, selecione o **autenticação** definição.
1. Sob **URIs de redirecionamento**, edite a porta para fazer a correspondência usado pelo aplicativo de exemplo, conforme mostrado na imagem seguinte:

    ![Captura de ecrã que mostra URIs de redirecionamento para o registo de aplicação](media/storage-auth-aad-app/redirect-uri.png)

## <a name="next-steps"></a>Passos Seguintes

- Para saber mais sobre a plataforma de identidade da Microsoft, consulte [plataforma de identidade do Microsoft](https://docs.microsoft.com/azure/active-directory/develop/).
- Para saber mais sobre as funções do RBAC para o armazenamento do Azure, veja [gerir direitos de acesso aos dados de armazenamento com o RBAC](storage-auth-aad-rbac.md).
- Para saber mais sobre a utilização de identidades geridas para recursos do Azure com o armazenamento do Azure, veja [autenticar o acesso a blobs e filas com o Azure Active Directory e de identidades geridas para recursos do Azure](storage-auth-aad-msi.md).
