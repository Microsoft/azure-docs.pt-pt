---
title: Adquirir um token do Azure AD para autorizar solicitações de um aplicativo cliente
titleSuffix: Azure Storage
description: Use Azure Active Directory para autenticar de dentro de um aplicativo cliente, adquirir um token OAuth 2,0 e autorizar solicitações para armazenamento de BLOBs do Azure e armazenamento de filas.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: d3ee211298598d78f423d88fd4df1c58ed4bfa29
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77157453"
---
# <a name="acquire-a-token-from-azure-ad-for-authorizing-requests-from-a-client-application"></a>Adquirir um token do Azure AD para autorizar solicitações de um aplicativo cliente

Uma vantagem importante de usar o Azure Active Directory (Azure AD) com o armazenamento de BLOBs do Azure ou o armazenamento de filas é que suas credenciais não precisam mais ser armazenadas em seu código. Em vez disso, você pode solicitar um token de acesso OAuth 2,0 da plataforma de identidade da Microsoft (anteriormente Azure AD). O Azure AD autentica a entidade de segurança (um usuário, grupo ou entidade de serviço) que executa o aplicativo. Se a autenticação for bem sucedido, o Azure AD retornará o token de acesso para o aplicativo e o aplicativo poderá usar o token de acesso para autorizar solicitações ao armazenamento de BLOBs do Azure ou armazenamento de filas.

Este artigo mostra como configurar seu aplicativo nativo ou aplicativo Web para autenticação com a plataforma de identidade da Microsoft 2,0. O .NET de recursos de exemplo de código, mas outras linguagens usam uma abordagem semelhante. Para obter mais informações sobre a plataforma de identidade da Microsoft 2.0, consulte a visão geral da [plataforma de identidade da Microsoft (v2.0).](../../active-directory/develop/v2-overview.md)

Para uma visão geral do fluxo de concessão de código OAuth 2.0, consulte [Autorizar o acesso às aplicações web do Azure Ative Directory utilizando o fluxo de subvenção de código OAuth 2.0](../../active-directory/develop/v2-oauth2-auth-code-flow.md).

## <a name="assign-a-role-to-an-azure-ad-security-principal"></a>Atribuir uma função a uma entidade de segurança do Azure AD

Para autenticar uma entidade de segurança da sua aplicação de armazenamento do Azure, primeiro de configurar as definições de controlo (RBAC) de acesso baseado em funções para aquela entidade de segurança. O armazenamento do Azure define funções RBAC internas que abrangem permissões para contêineres e filas. Quando a função RBAC é atribuída a uma entidade de segurança, essa entidade de segurança é concedido acesso a esse recurso. Para mais informações, consulte Gerir os direitos de [acesso aos dados de Azure Blob e fila com RBAC](storage-auth-aad-rbac.md).

## <a name="register-your-application-with-an-azure-ad-tenant"></a>Registar a aplicação com um inquilino do Azure AD

O primeiro passo na utilização da Azure AD para autorizar o acesso aos recursos de armazenamento é registar a sua aplicação de cliente com um inquilino Azure AD do [portal Azure.](https://portal.azure.com) Ao registrar seu aplicativo cliente, você fornece informações sobre o aplicativo para o Azure AD. A Azure AD fornece então um ID do cliente (também chamado de ID de *aplicação)* que utiliza para associar a sua aplicação ao Azure AD em tempo de execução. Para saber mais sobre o ID do cliente, consulte [os objetos principais de aplicação e serviço no Diretório Ativo Azure](../../active-directory/develop/app-objects-and-service-principals.md).

Para registar a sua aplicação De armazenamento Azure, siga os passos apresentados no [Quickstart: Registe uma aplicação com a plataforma de identidade Microsoft](../../active-directory/develop/quickstart-configure-app-access-web-apis.md). A imagem a seguir mostra as configurações comuns para o registro de um aplicativo Web:

![Captura de tela mostrando como registrar seu aplicativo de armazenamento com o Azure AD](./media/storage-auth-aad-app/app-registration.png)

> [!NOTE]
> Se registar a sua candidatura como uma aplicação nativa, pode especificar qualquer URI válido para o **Redirect URI**. Para aplicativos nativos, esse valor não precisa ser uma URL real. Para aplicativos Web, o URI de redirecionamento deve ser um URI válido, pois ele especifica a URL para a qual os tokens são fornecidos.

Depois de ter registado o seu pedido, verá o ID da aplicação (ou ID do cliente) em **Definições:**

![Captura de tela mostrando a ID do cliente](./media/storage-auth-aad-app/app-registration-client-id.png)

Para obter mais informações sobre o registo de uma aplicação com a Azure AD, consulte a Integração de [aplicações com o Diretório Ativo Azure](../../active-directory/develop/quickstart-v2-register-an-app.md).

## <a name="grant-your-registered-app-permissions-to-azure-storage"></a>Conceder as permissões de aplicação registada para o armazenamento do Azure

Em seguida, conceda ao seu aplicativo permissões para chamar as APIs de armazenamento do Azure. Esta etapa permite que seu aplicativo autorize solicitações ao armazenamento do Azure com o Azure AD.

1. Na página **'Visão Geral'** da sua aplicação registada, selecione **Ver Permissões API**.
1. Na secção de **permissões DaPI,** selecione **Adicionar uma permissão** e escolha **APIs microsoft**.
1. Selecione **Armazenamento Azure** na lista de resultados para exibir o painel de **permissões Request API.**
1. Sob **que tipo de permissões o seu pedido requer?** Essa opção é selecionada por padrão.
1. Na secção **de permissões Select** do painel de **permissões Request API,** selecione a caixa de verificação ao lado de **user_impersonation**e, em seguida, clique em **Adicionar permissões**.

    ![Captura de tela mostrando permissões para armazenamento](media/storage-auth-aad-app/registered-app-permissions-1.png)

O painel de **permissões DaPI** mostra agora que a sua aplicação Azure AD registada tem acesso tanto ao Microsoft Graph como ao Azure Storage. As permissões são concedidas para Microsoft Graph automaticamente quando você registra seu aplicativo pela primeira vez com o Azure AD.

![Captura de tela mostrando permissões de aplicativo de registro](media/storage-auth-aad-app/registered-app-permissions-2.png)

## <a name="create-a-client-secret"></a>Criar um segredo de cliente

O aplicativo precisa de um segredo do cliente para provar sua identidade ao solicitar um token. Para adicionar o segredo do cliente, siga estas etapas:

1. Navegue até o registro do aplicativo no portal do Azure.
1. Selecione a definição de **Certificados e segredos.**
1. Sob **os segredos do Cliente,** clique em **novo segredo de cliente** para criar um novo segredo.
1. Forneça uma descrição para o segredo e escolha o intervalo de expiração desejado.
1. Copie imediatamente o valor do novo segredo para um local seguro. O valor completo é exibido apenas uma vez.

    ![Captura de tela mostrando o segredo do cliente](media/storage-auth-aad-app/client-secret.png)

## <a name="client-libraries-for-token-acquisition"></a>Bibliotecas de cliente para aquisição de token

Depois de registrar seu aplicativo e conceder permissões de ti para acessar dados no armazenamento de BLOBs do Azure ou armazenamento de filas, você pode adicionar código ao seu aplicativo para autenticar uma entidade de segurança e adquirir um token 2,0 do OAuth. Para autenticar e adquirir o símbolo, pode utilizar uma das bibliotecas de [autenticação](../../active-directory/develop/reference-v2-libraries.md) da plataforma de identidade microsoft ou outra biblioteca de código aberto que suporta o OpenID Connect 1.0. Seu aplicativo pode, então, usar o token de acesso para autorizar uma solicitação no armazenamento de BLOBs do Azure ou no armazenamento de filas.

Para obter uma lista de cenários para os quais é suportado o suporte da aquisição de fichas, consulte a secção de fluxos de [autenticação](/en-us/azure/active-directory/develop/msal-authentication-flows) do conteúdo da Biblioteca de [Autenticação](/azure/active-directory/develop/msal-overview)da Microsoft .

## <a name="well-known-values-for-authentication-with-azure-ad"></a>Valores conhecidas para autenticação com o Azure AD

Para autenticar uma entidade de segurança com o Azure AD, tem de incluir alguns valores conhecidos em seu código.

### <a name="azure-ad-authority"></a>Autoridade do Azure AD

Para a nuvem pública da Microsoft, a base da autoridade da AD Azure é a seguinte, onde o *inquilino-id* é o seu ID de inquilino de Diretório Ativo (ou ID de diretório):

`https://login.microsoftonline.com/<tenant-id>/`

O ID de inquilino identifica o inquilino do Azure AD para utilizar para autenticação. Ele também é conhecido como a ID do diretório. Para recuperar o ID do inquilino, navegue para a página **'Overview'** para o registo da sua aplicação no portal Azure e copie o valor a partir daí.

### <a name="azure-storage-resource-id"></a>ID de recurso de armazenamento do Azure

[!INCLUDE [storage-resource-id-include](../../../includes/storage-resource-id-include.md)]

## <a name="net-code-example-create-a-block-blob"></a>Exemplo de código do .NET: criar um blob de blocos

O exemplo de código mostra como obter um acesso de token do Azure AD. O token de acesso é utilizado para autenticar o utilizador especificado e, em seguida, autorizar um pedido para criar um blob de blocos. Para que isso funcione de exemplo, primeiro siga os passos descritos nas secções anteriores.

Para solicitar o token, você precisará dos seguintes valores do registro do aplicativo:

- O nome do seu domínio do Azure AD. Recupere este valor a partir da página **de visão geral** do seu Diretório Ativo Azure.
- A ID do locatário (ou diretório). Recupere este valor a partir da página **de visão geral** do registo da sua aplicação.
- A ID do cliente (ou aplicativo). Recupere este valor a partir da página **de visão geral** do registo da sua aplicação.
- O URI de redirecionamento de cliente. Recupere este valor das definições de **Autenticação** para o registo da sua aplicação.
- O valor do segredo do cliente. Recupere esse valor do local para o qual você o copiou anteriormente.

### <a name="create-a-storage-account-and-container"></a>Criar uma conta de armazenamento e um contentor

Para executar o exemplo de código, crie uma conta de armazenamento na mesma assinatura que o Azure Active Directory. Em seguida, crie um contêiner dentro dessa conta de armazenamento. O código de exemplo criará um blob de blocos neste contêiner.

Em seguida, atribuir explicitamente a função de Contribuinte de **Dados blob** de armazenamento à conta de utilizador sob a qual executará o código da amostra. Para obter instruções sobre como atribuir este papel no portal Azure, consulte [o acesso do Grant à blob Azure e](storage-auth-aad-rbac-portal.md)os dados de fila com rBAC no portal Azure .

> [!NOTE]
> Ao criar uma conta de armazenamento do Azure, você não recebe automaticamente permissões para acessar dados por meio do Azure AD. Tem explicitamente de atribuir-se uma função RBAC do armazenamento do Azure. Pode atribuí-la no nível da sua subscrição, grupo de recursos, conta de armazenamento ou contentor ou fila.

### <a name="create-a-web-application-that-authorizes-access-to-blob-storage-with-azure-ad"></a>Criar um aplicativo Web que autorize o acesso ao armazenamento de BLOBs com o Azure AD

Quando seu aplicativo acessa o armazenamento do Azure, ele faz isso em nome do usuário, o que significa que os recursos de BLOB ou fila são acessados usando as permissões do usuário que está conectado. Para testar este exemplo de código, você precisa de um aplicativo Web que solicita ao usuário para entrar usando uma identidade do Azure AD. Você pode criar seu próprio ou usar o aplicativo de exemplo fornecido pela Microsoft.

Uma aplicação web de amostra completa que adquire um símbolo e a utiliza para criar uma bolha no Armazenamento Azure está disponível no [GitHub](https://aka.ms/aadstorage). Revisar e executar o exemplo completo pode ser útil para entender os exemplos de código. Para obter instruções sobre como executar a amostra completa, consulte a secção intitulada [Ver e executar a amostra completa](#view-and-run-the-completed-sample).

#### <a name="add-references-and-using-statements"></a>Adicionar referências e instruções "using"  

No Visual Studio, instale a biblioteca de cliente do armazenamento do Azure. No menu **Ferramentas**, selecione **Gestor de Pacotes NuGet** e, em seguida, **Consola do Gestor de Pacotes**. Digite os seguintes comandos na janela do console para instalar os pacotes necessários da biblioteca de cliente do armazenamento do Azure para .NET:

```console
Install-Package Microsoft.Azure.Storage.Blob
Install-Package Microsoft.Azure.Storage.Common
```

Em seguida, adicione as seguintes instruções using ao arquivo HomeController.cs:

```csharp
using Microsoft.Identity.Client; //MSAL library for getting the access token
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.WindowsAzure.Storage.Blob;
```

#### <a name="create-a-block-blob"></a>Criar um blob de blocos

Adicione o seguinte trecho de código para criar um blob de blocos:

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
> Para autorizar operações de BLOB e de fila com um token OAuth 2,0, você deve usar HTTPS.

No exemplo acima, a biblioteca de cliente .NET processa a autorização do pedido para criar o blob de blocos. As bibliotecas de cliente de armazenamento do Azure para outras linguagens também lidam com a autorização da solicitação para você. No entanto, se estiver a chamar uma operação de armazenamento do Azure com um token de OAuth utilizando a API REST, em seguida, terá de autorizar a solicitação usando o token OAuth.

Para ligar para as operações de serviço Blob e Queue utilizando fichas de acesso OAuth, passe o sinal de acesso no cabeçalho de **autorização** utilizando o esquema **Bearer,** e especifique uma versão de serviço de 2017-11-09 ou superior, como mostra o seguinte exemplo:

```https
GET /container/file.txt HTTP/1.1
Host: mystorageaccount.blob.core.windows.net
x-ms-version: 2017-11-09
Authorization: Bearer eyJ0eXAiOnJKV1...Xd6j
```

#### <a name="get-an-oauth-token-from-azure-ad"></a>Obter um OAuth token do Azure AD

Em seguida, adicione um método que solicite um token do Azure AD em nome do usuário. Esse método define o escopo para o qual as permissões devem ser concedidas. Para obter mais informações sobre permissões e âmbitos, consulte [Permissões e consentimento no ponto final da plataforma de identidade da Microsoft](../../active-directory/develop/v2-permissions-and-consent.md).

Use a ID do recurso para construir o escopo para o qual adquirir o token. O exemplo constrói o âmbito utilizando o ID do recurso juntamente com o âmbito de `user_impersonation` incorporado, o que indica que o símbolo está a ser solicitado em nome do utilizador.

Tenha em mente que talvez seja necessário apresentar ao usuário uma interface que permita ao usuário consentir que solicite o token de seu nome. Quando o consentimento é necessário, o exemplo captura a **MsalUiRequiredException** e chama outro método para facilitar o pedido de consentimento:

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
        AuthenticationProperties properties =
            BuildAuthenticationPropertiesForIncrementalConsent(scopes, ex);
        return Challenge(properties);
    }
}
```

O consentimento é o processo de um usuário que concede autorização a um aplicativo para acessar recursos protegidos em seu nome. A plataforma de identidade da Microsoft 2,0 dá suporte ao consentimento incremental, o que significa que uma entidade de segurança pode solicitar um conjunto mínimo de permissões inicialmente e adicionar permissões ao longo do tempo, conforme necessário. Quando o seu código solicitar um sinal de acesso, especifique o âmbito de permissões de que a sua aplicação necessita a qualquer momento no parâmetro `scope`. Para obter mais informações sobre o consentimento incremental, consulte a secção intitulada **Incremental e consentimento dinâmico** em Porquê atualizar para a plataforma de identidade da Microsoft [(v2.0)?](../../active-directory/azuread-dev/azure-ad-endpoint-comparison.md#incremental-and-dynamic-consent)

O método a seguir constrói as propriedades de autenticação para solicitar o consentimento incremental:

```csharp
private AuthenticationProperties BuildAuthenticationPropertiesForIncrementalConsent(string[] scopes,
                                                                                    MsalUiRequiredException ex)
{
    AuthenticationProperties properties = new AuthenticationProperties();

    // Set the scopes, including the scopes that ADAL.NET or MSAL.NET need for the Token cache.
    string[] additionalBuildInScopes = new string[] { "openid", "offline_access", "profile" };
    properties.SetParameter<ICollection<string>>(OpenIdConnectParameterNames.Scope,
                                                 scopes.Union(additionalBuildInScopes).ToList());

    // Attempt to set the login_hint so that the logged-in user is not presented
    // with an account selection dialog.
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

## <a name="view-and-run-the-completed-sample"></a>Exibir e executar o exemplo concluído

Para executar a aplicação da amostra, primeiro clone ou descarregue-a a partir do [GitHub](https://github.com/Azure-Samples/storage-dotnet-azure-ad-msal). Em seguida, atualize o aplicativo conforme descrito nas seções a seguir.

### <a name="provide-values-in-the-settings-file"></a>Forneça valores no arquivo de configurações

Em seguida, atualize o ficheiro *appsettings.json* com os seus próprios valores, da seguinte forma:

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

### <a name="update-the-storage-account-and-container-name"></a>Atualizar a conta de armazenamento e o nome do contêiner

No ficheiro *HomeController.cs,* atualize o URI que faz referência à bolha do bloco para utilizar o nome da sua conta de armazenamento e do seu recipiente:

```csharp
CloudBlockBlob blob = new CloudBlockBlob(
                      new Uri("https://<storage-account>.blob.core.windows.net/<container>/Blob1.txt"),
                      storageCredentials);
```

### <a name="enable-implicit-grant-flow"></a>Habilitar fluxo de concessão implícito

Para executar o exemplo, talvez seja necessário configurar o fluxo de concessão implícita para o registro do aplicativo. Siga estes passos.

1. Navegue até o registro do aplicativo no portal do Azure.
1. Na secção Gerir, selecione a definição de **Autenticação.**
1. Em **definições avançadas,** na secção **de subvenção implícita,** selecione as caixas de verificação para permitir fichas de acesso e fichas de identificação, como mostra a seguinte imagem:

    ![Captura de tela mostrando como habilitar as configurações para o fluxo de concessão implícita](media/storage-auth-aad-app/enable-implicit-grant-flow.png)

### <a name="update-the-port-used-by-localhost"></a>Atualizar a porta usada pelo localhost

Quando executar a amostra, poderá descobrir que necessita de atualizar o URI redirecionado especificado no registo da sua aplicação para utilizar a porta local de *acolhimento* atribuída no tempo de execução. Para atualizar o URI de redirecionamento para usar a porta atribuída, siga estas etapas:

1. Navegue até o registro do aplicativo no portal do Azure.
1. Na secção Gerir, selecione a definição de **Autenticação.**
1. Em **Redirecionamento de URIs,** edite a porta para corresponder à utilizada pela aplicação da amostra, como mostra a seguinte imagem:

    ![Captura de tela mostrando URIs de redirecionamento para registro de aplicativo](media/storage-auth-aad-app/redirect-uri.png)

## <a name="next-steps"></a>Passos seguintes

- Para saber mais sobre a plataforma de identidade da Microsoft, consulte a [plataforma de identidade da Microsoft.](https://docs.microsoft.com/azure/active-directory/develop/)
- Para saber mais sobre as funções RBAC para armazenamento Azure, consulte Gerir os direitos de acesso aos dados de [armazenamento com rBAC](storage-auth-aad-rbac.md).
- Para aprender sobre a utilização de identidades geridas para recursos Azure com armazenamento Azure, consulte o [acesso authenticado a blobs e filas com o Azure Ative Directory e identidades geridas para os Recursos Azure.](storage-auth-aad-msi.md)
