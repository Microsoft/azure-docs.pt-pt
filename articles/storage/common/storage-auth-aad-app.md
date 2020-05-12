---
title: Adquirir um símbolo da Azure AD para autorizar pedidos de um pedido de cliente
titleSuffix: Azure Storage
description: Utilize o Diretório Ativo Azure para autenticar a partir de uma aplicação de cliente, adquirir um token OAuth 2.0 e autorizar pedidos para armazenamento e armazenamento de fila da Blob Azure.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2019
ms.author: tamram
ms.subservice: common
ms.custom: has-adal-ref
ms.openlocfilehash: 0cda75469edaa183ed6553a431b9ad13b611db7d
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83201076"
---
# <a name="acquire-a-token-from-azure-ad-for-authorizing-requests-from-a-client-application"></a>Adquirir um símbolo da Azure AD para autorizar pedidos de um pedido de cliente

Uma vantagem fundamental de utilizar o Azure Ative Directory (Azure AD) com armazenamento Azure Blob ou armazenamento de fila é que as suas credenciais já não precisam de ser armazenadas no seu código. Em vez disso, pode solicitar um sinal de acesso OAuth 2.0 da plataforma de identidade da Microsoft (anteriormente Azure AD). A Azure AD autentica o diretor de segurança (utilizador, grupo ou diretor de serviço) que executa a aplicação. Se a autenticação for bem sucedida, a Azure AD devolve o sinal de acesso à aplicação, podendo então utilizar o sinal de acesso para autorizar pedidos de armazenamento ou armazenamento de fila da Azure Blob.

Este artigo mostra como configurar a sua aplicação nativa ou aplicação web para autenticação com a plataforma de identidade Microsoft 2.0. O exemplo de código apresenta .NET, mas outras línguas usam uma abordagem semelhante. Para obter mais informações sobre a plataforma de identidade da Microsoft 2.0, consulte a visão geral da [plataforma de identidade da Microsoft (v2.0).](../../active-directory/develop/v2-overview.md)

Para uma visão geral do fluxo de concessão de código OAuth 2.0, consulte [Autorizar o acesso às aplicações web do Azure Ative Directory utilizando o fluxo de subvenção de código OAuth 2.0](../../active-directory/develop/v2-oauth2-auth-code-flow.md).

## <a name="assign-a-role-to-an-azure-ad-security-principal"></a>Atribuir um papel a um diretor de segurança da AD Azure

Para autenticar um diretor de segurança da sua aplicação De armazenamento Azure, configure primeiro as definições de controlo de acesso baseado em funções (RBAC) para esse diretor de segurança. O Azure Storage define funções RBAC incorporadas que englobam permissões para contentores e filas. Quando a função RBAC é atribuída a um diretor de segurança, esse diretor de segurança tem acesso a esse recurso. Para mais informações, consulte Gerir os direitos de [acesso aos dados de Azure Blob e fila com RBAC](storage-auth-aad-rbac.md).

## <a name="register-your-application-with-an-azure-ad-tenant"></a>Registe a sua candidatura com um inquilino da AD Azure

O primeiro passo na utilização da Azure AD para autorizar o acesso aos recursos de armazenamento é registar a sua aplicação de cliente com um inquilino Azure AD do [portal Azure.](https://portal.azure.com) Ao registar a sua aplicação de cliente, fornece informações sobre a aplicação à Azure AD. A Azure AD fornece então um ID do cliente (também chamado de ID de *aplicação)* que utiliza para associar a sua aplicação ao Azure AD em tempo de execução. Para saber mais sobre o ID do cliente, consulte [os objetos principais de aplicação e serviço no Diretório Ativo Azure](../../active-directory/develop/app-objects-and-service-principals.md).

Para registar a sua aplicação De armazenamento Azure, siga os passos apresentados no [Quickstart: Registe uma aplicação com a plataforma de identidade Microsoft](../../active-directory/develop/quickstart-configure-app-access-web-apis.md). A imagem seguinte mostra configurações comuns para registar uma aplicação web:

![Screenshot mostrando como registar o seu pedido de armazenamento com AD Azure](./media/storage-auth-aad-app/app-registration.png)

> [!NOTE]
> Se registar a sua candidatura como uma aplicação nativa, pode especificar qualquer URI válido para o **Redirect URI**. Para aplicações nativas, este valor não tem de ser um URL real. Para aplicações web, o URI redirecionado deve ser um URI válido, porque especifica o URL a que são fornecidos tokens.

Depois de ter registado o seu pedido, verá o ID da aplicação (ou ID do cliente) em **Definições:**

![Screenshot mostrando o ID do cliente](./media/storage-auth-aad-app/app-registration-client-id.png)

Para obter mais informações sobre o registo de uma aplicação com a Azure AD, consulte a Integração de [aplicações com o Diretório Ativo Azure](../../active-directory/develop/quickstart-v2-register-an-app.md).

## <a name="grant-your-registered-app-permissions-to-azure-storage"></a>Conceda as suas permissões de aplicações registadas ao Armazenamento Azure

Em seguida, conceda as suas permissões de pedido para ligar para a Azure Storage APIs. Este passo permite que a sua aplicação autorize pedidos de Armazenamento Azure com a Azure AD.

1. Na página **'Visão Geral'** da sua aplicação registada, selecione **Ver Permissões API**.
1. Na secção de **permissões DaPI,** selecione **Adicionar uma permissão** e escolha **APIs microsoft**.
1. Selecione **Armazenamento Azure** na lista de resultados para exibir o painel de **permissões Request API.**
1. Sob **que tipo de permissões o seu pedido requer?** **Delegated permissions** Esta opção é selecionada por padrão.
1. Na secção **de permissões Select** do painel de **permissões Request API,** selecione a caixa de verificação ao lado de **user_impersonation**e, em seguida, clique em **Adicionar permissões**.

    ![Screenshot mostrando permissões para armazenamento](media/storage-auth-aad-app/registered-app-permissions-1.png)

O painel de **permissões DaPI** mostra agora que a sua aplicação Azure AD registada tem acesso tanto ao Microsoft Graph como ao Azure Storage. As permissões são concedidas automaticamente ao Microsoft Graph quando regista a sua aplicação pela primeira vez com o Azure AD.

![Screenshot mostrando permissões de aplicativos de registo](media/storage-auth-aad-app/registered-app-permissions-2.png)

## <a name="create-a-client-secret"></a>Criar um segredo de cliente

O pedido precisa de um segredo de cliente para provar a sua identidade ao solicitar um símbolo. Para adicionar o segredo do cliente, siga estes passos:

1. Navegue para o registo da sua aplicação no portal Azure.
1. Selecione a definição **de segredos & Certificados.**
1. Sob **os segredos do Cliente,** clique em **novo segredo de cliente** para criar um novo segredo.
1. Forneça uma descrição para o segredo e escolha o intervalo de validade desejado.
1. Copie imediatamente o valor do novo segredo para um local seguro. O valor total é-lhe apresentado apenas uma vez.

    ![Screenshot mostrando o segredo do cliente](media/storage-auth-aad-app/client-secret.png)

## <a name="client-libraries-for-token-acquisition"></a>Bibliotecas de clientes para aquisição simbólica

Depois de ter registado a sua aplicação e lhe ter concedido permissões para aceder a dados no armazenamento de Azure Blob ou no armazenamento de fila, pode adicionar código à sua aplicação para autenticar um diretor de segurança e adquirir um token OAuth 2.0. Para autenticar e adquirir o símbolo, pode utilizar uma das bibliotecas de [autenticação](../../active-directory/develop/reference-v2-libraries.md) da plataforma de identidade microsoft ou outra biblioteca de código aberto que suporta o OpenID Connect 1.0. A sua aplicação pode então utilizar o sinal de acesso para autorizar um pedido contra o armazenamento de Azure Blob ou armazenamento de fila.

Para obter uma lista de cenários para os quais é suportado o suporte da aquisição de fichas, consulte a secção de fluxos de [autenticação](/en-us/azure/active-directory/develop/msal-authentication-flows) do conteúdo da Biblioteca de [Autenticação](/azure/active-directory/develop/msal-overview)da Microsoft .

## <a name="well-known-values-for-authentication-with-azure-ad"></a>Valores bem conhecidos para autenticação com Azure AD

Para autenticar um diretor de segurança com a AD Azure, precisa de incluir alguns valores bem conhecidos no seu código.

### <a name="azure-ad-authority"></a>Autoridade da AD Azure

Para a nuvem pública da Microsoft, a base da autoridade da AD Azure é a seguinte, onde o *inquilino-id* é o seu ID de inquilino de Diretório Ativo (ou ID de diretório):

`https://login.microsoftonline.com/<tenant-id>/`

O ID do inquilino identifica o inquilino da AD Azure para usar para autenticação. É também referido como id do diretório. Para recuperar o ID do inquilino, navegue para a página **'Overview'** para o registo da sua aplicação no portal Azure e copie o valor a partir daí.

### <a name="azure-storage-resource-id"></a>ID de recurso de armazenamento azure

[!INCLUDE [storage-resource-id-include](../../../includes/storage-resource-id-include.md)]

## <a name="net-code-example-create-a-block-blob"></a>Exemplo de código .NET: Criar uma bolha de bloco

O exemplo do código mostra como obter um sinal de acesso da Azure AD. O sinal de acesso é usado para autenticar o utilizador especificado e, em seguida, autorizar um pedido para criar uma bolha de bloco. Para que esta amostra funcione, siga primeiro os passos descritos nas secções anteriores.

Para solicitar o símbolo, necessitará dos seguintes valores do registo da sua aplicação:

- O nome do seu domínio Azure AD. Recupere este valor a partir da página **de visão geral** do seu Diretório Ativo Azure.
- O id do inquilino (ou diretório). Recupere este valor a partir da página **de visão geral** do registo da sua aplicação.
- O ID do cliente (ou aplicação). Recupere este valor a partir da página **de visão geral** do registo da sua aplicação.
- A reorientação do cliente URI. Recupere este valor das definições de **Autenticação** para o registo da sua aplicação.
- O valor do segredo do cliente. Recupere este valor a partir do local para onde o copiou anteriormente.

### <a name="create-a-storage-account-and-container"></a>Criar uma conta de armazenamento e um recipiente

Para executar a amostra de código, crie uma conta de armazenamento dentro da mesma subscrição que o seu Diretório Ativo Azure. Em seguida, crie um recipiente dentro dessa conta de armazenamento. O código da amostra criará uma bolha de bloco neste recipiente.

Em seguida, atribuir explicitamente a função de Contribuinte de **Dados blob** de armazenamento à conta de utilizador sob a qual executará o código da amostra. Para obter instruções sobre como atribuir este papel no portal Azure, consulte [o acesso do Grant à blob Azure e](storage-auth-aad-rbac-portal.md)os dados de fila com rBAC no portal Azure .

> [!NOTE]
> Quando cria uma conta de Armazenamento Azure, não é atribuída automaticamente permissões para aceder a dados via Azure AD. Deve atribuir-se explicitamente uma função RBAC para o Armazenamento Azure. Pode atribuí-lo ao nível da sua subscrição, grupo de recursos, conta de armazenamento ou contentor ou fila.

### <a name="create-a-web-application-that-authorizes-access-to-blob-storage-with-azure-ad"></a>Criar uma aplicação web que autorize o acesso ao armazenamento blob com a Azure AD

Quando a sua aplicação acede ao Armazenamento Azure, fá-lo em nome do utilizador, o que significa que os recursos blob ou fila são acedidos usando as permissões do utilizador que está a iniciar sessão. Para experimentar este exemplo de código, precisa de uma aplicação web que leve o utilizador a iniciar sessão utilizando uma identidade Azure AD. Pode criar o seu próprio ou utilizar a aplicação de amostra fornecida pela Microsoft.

Uma aplicação web de amostra completa que adquire um símbolo e a utiliza para criar uma bolha no Armazenamento Azure está disponível no [GitHub](https://aka.ms/aadstorage). Rever e executar a amostra completa pode ser útil para entender os exemplos de código. Para obter instruções sobre como executar a amostra completa, consulte a secção intitulada [Ver e executar a amostra completa](#view-and-run-the-completed-sample).

#### <a name="add-references-and-using-statements"></a>Adicione referências e usando declarações  

A partir do Estúdio Visual, instale a biblioteca de clientes Azure Storage. No menu **Ferramentas**, selecione **Gestor de Pacotes NuGet** e, em seguida, **Consola do Gestor de Pacotes**. Digite os seguintes comandos na janela da consola para instalar os pacotes necessários da biblioteca de clientes do Azure Storage para .NET:

```console
Install-Package Microsoft.Azure.Storage.Blob
Install-Package Microsoft.Azure.Storage.Common
```

Em seguida, adicione as seguintes declarações utilizando o ficheiro HomeController.cs:

```csharp
using Microsoft.Identity.Client; //MSAL library for getting the access token
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.WindowsAzure.Storage.Blob;
```

#### <a name="create-a-block-blob"></a>Criar uma bolha de bloco

Adicione o seguinte fragmento de código para criar uma bolha de bloco:

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
> Para autorizar operações de blob e fila com um token OAuth 2.0, deve utilizar HTTPS.

No exemplo acima, a biblioteca de clientes .NET trata da autorização do pedido de criação da bolha do bloco. As bibliotecas de clientes do Azure Storage para outras línguas também tratam da autorização do pedido para si. No entanto, se estiver a ligar para uma operação de armazenamento azure com um token OAuth usando a API REST, então terá de autorizar o pedido usando o símbolo OAuth.

Para ligar para as operações de serviço Blob e Queue utilizando fichas de acesso OAuth, passe o sinal de acesso no cabeçalho de **autorização** utilizando o esquema **Bearer,** e especifique uma versão de serviço de 2017-11-09 ou superior, como mostra o seguinte exemplo:

```https
GET /container/file.txt HTTP/1.1
Host: mystorageaccount.blob.core.windows.net
x-ms-version: 2017-11-09
Authorization: Bearer eyJ0eXAiOnJKV1...Xd6j
```

#### <a name="get-an-oauth-token-from-azure-ad"></a>Obtenha um token OAuth da Azure AD

Em seguida, adicione um método que solicita um token da AD Azure em nome do utilizador. Este método define o âmbito para o qual as permissões devem ser concedidas. Para obter mais informações sobre permissões e âmbitos, consulte [Permissões e consentimento no ponto final da plataforma de identidade da Microsoft](../../active-directory/develop/v2-permissions-and-consent.md).

Utilize o ID de recurso para construir o âmbito para obter o símbolo. O exemplo constrói o âmbito utilizando o ID do recurso juntamente com o `user_impersonation` âmbito incorporado, o que indica que o símbolo está a ser solicitado em nome do utilizador.

Tenha em mente que poderá ser necessário apresentar ao utilizador uma interface que permita ao utilizador consentir em solicitar o seu nome. Quando o consentimento é necessário, o exemplo captura a **MsalUiRequiredException** e chama outro método para facilitar o pedido de consentimento:

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

O consentimento é o processo de um utilizador que concede autorização a uma aplicação de acesso a recursos protegidos em seu nome. A plataforma de identidade Microsoft 2.0 suporta o consentimento incremental, o que significa que um diretor de segurança pode solicitar um conjunto mínimo de permissões inicialmente e adicionar permissões ao longo do tempo, conforme necessário. Quando o seu código solicitar um sinal de acesso, especifique o âmbito de permissões de que a sua aplicação necessita a qualquer momento do `scope` parâmetro. Para obter mais informações sobre o consentimento incremental, consulte a secção intitulada **Incremental e consentimento dinâmico** em Porquê atualizar para a plataforma de identidade da Microsoft [(v2.0)?](../../active-directory/azuread-dev/azure-ad-endpoint-comparison.md#incremental-and-dynamic-consent)

O seguinte método constrói as propriedades de autenticação para solicitar o consentimento incremental:

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

## <a name="view-and-run-the-completed-sample"></a>Ver e executar a amostra completa

Para executar a aplicação da amostra, primeiro clone ou descarregue-a a partir do [GitHub](https://github.com/Azure-Samples/storage-dotnet-azure-ad-msal). Em seguida, atualize a aplicação conforme descrito nas seguintes secções.

### <a name="provide-values-in-the-settings-file"></a>Fornecer valores no ficheiro definições

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

### <a name="update-the-storage-account-and-container-name"></a>Atualizar a conta de armazenamento e o nome do contentor

No ficheiro *HomeController.cs,* atualize o URI que faz referência à bolha do bloco para utilizar o nome da sua conta de armazenamento e do seu recipiente:

```csharp
CloudBlockBlob blob = new CloudBlockBlob(
                      new Uri("https://<storage-account>.blob.core.windows.net/<container>/Blob1.txt"),
                      storageCredentials);
```

### <a name="enable-implicit-grant-flow"></a>Ativar fluxo implícito de subvenção

Para executar a amostra, poderá ser necessário configurar o fluxo implícito de subvenção para o registo da sua aplicação. Siga estes passos.

1. Navegue para o registo da sua aplicação no portal Azure.
1. Na secção Gerir, selecione a definição de **Autenticação.**
1. Em **definições avançadas,** na secção **de subvenção implícita,** selecione as caixas de verificação para permitir fichas de acesso e fichas de identificação, como mostra a seguinte imagem:

    ![Screenshot mostrando como ativar configurações para fluxo de subvenção implícito](media/storage-auth-aad-app/enable-implicit-grant-flow.png)

### <a name="update-the-port-used-by-localhost"></a>Atualize o porto utilizado pelo local

Quando executar a amostra, poderá descobrir que necessita de atualizar o URI redirecionado especificado no registo da sua aplicação para utilizar a porta local de *acolhimento* atribuída no tempo de execução. Para atualizar o redirecionamento do URI para utilizar a porta atribuída, siga estes passos:

1. Navegue para o registo da sua aplicação no portal Azure.
1. Na secção Gerir, selecione a definição de **Autenticação.**
1. Em **Redirecionamento de URIs,** edite a porta para corresponder à utilizada pela aplicação da amostra, como mostra a seguinte imagem:

    ![Screenshot mostrando REdirecionar URIs para registo de aplicações](media/storage-auth-aad-app/redirect-uri.png)

## <a name="next-steps"></a>Passos seguintes

- Para saber mais sobre a plataforma de identidade da Microsoft, consulte a [plataforma de identidade da Microsoft.](https://docs.microsoft.com/azure/active-directory/develop/)
- Para saber mais sobre as funções RBAC para armazenamento Azure, consulte Gerir os direitos de acesso aos dados de [armazenamento com rBAC](storage-auth-aad-rbac.md).
- Para aprender sobre a utilização de identidades geridas para recursos Azure com armazenamento Azure, consulte o [acesso authenticado a blobs e filas com o Azure Ative Directory e identidades geridas para os Recursos Azure.](storage-auth-aad-msi.md)
