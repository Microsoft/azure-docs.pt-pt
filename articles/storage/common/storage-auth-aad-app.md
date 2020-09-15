---
title: Adquirir um token da Azure AD para autorizar pedidos de um pedido de cliente
titleSuffix: Azure Storage
description: Utilize o Azure Ative Directory para autenticar a partir de uma aplicação do cliente, adquira um token OAuth 2.0 e autorize pedidos para armazenamento e armazenamento de fila da Azure Blob.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 09/14/2020
ms.author: tamram
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: b5a39b08f34bec5ee1db42cde1fb171452d0efd3
ms.sourcegitcommit: 1fe5127fb5c3f43761f479078251242ae5688386
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/14/2020
ms.locfileid: "90069820"
---
# <a name="acquire-a-token-from-azure-ad-for-authorizing-requests-from-a-client-application"></a>Adquirir um token da Azure AD para autorizar pedidos de um pedido de cliente

Uma vantagem fundamental da utilização do Azure Ative Directory (Azure AD) com o armazenamento Azure Blob ou o armazenamento da fila é que as suas credenciais já não precisam de ser armazenadas no seu código. Em vez disso, pode solicitar um token de acesso OAuth 2.0 a partir da plataforma de identidade da Microsoft. A Azure AD autentica o principal de segurança (um utilizador, grupo ou principal de serviço) que executa a aplicação. Se a autenticação for bem sucedida, a Azure AD devolve o sinal de acesso à aplicação, e a aplicação pode então utilizar o token de acesso para autorizar pedidos para armazenamento de Azure Blob ou armazenamento de fila.

Este artigo mostra como configurar a sua aplicação nativa ou aplicação web para autenticação com a plataforma de identidade microsoft 2.0. O exemplo de código apresenta .NET, mas outras línguas usam uma abordagem semelhante. Para obter mais informações sobre a plataforma de identidade 2.0 da Microsoft, consulte a [plataforma de identidade da Microsoft (v2.0) visão geral](../../active-directory/develop/v2-overview.md).

Para obter uma visão geral do fluxo de concessão de código OAuth 2.0, consulte [o acesso autorizado às aplicações web do Azure Ative Directory utilizando o fluxo de concessão de código OAuth 2.0](../../active-directory/develop/v2-oauth2-auth-code-flow.md).

## <a name="assign-a-role-to-an-azure-ad-security-principal"></a>Atribuir um papel a um diretor de segurança da AD Azure

Para autenticar um princípio de segurança da sua aplicação Azure Storage, configurar primeiro as definições de controlo de acesso baseado em funções (RBAC) para esse diretor de segurança. O Azure Storage define funções incorporadas que englobam permissões para contentores e filas. Quando o papel do RBAC é atribuído a um diretor de segurança, esse diretor de segurança tem acesso a esse recurso. Para obter mais informações, consulte [Gerir os direitos de acesso aos dados de Azure Blob e Queue com o RBAC.](storage-auth-aad-rbac.md)

## <a name="register-your-application-with-an-azure-ad-tenant"></a>Registe o seu pedido junto de um inquilino da AD Azure

O primeiro passo para a utilização da Azure AD para autorizar o acesso aos recursos de armazenamento é registar a sua aplicação de cliente com um inquilino AZure AD do [portal Azure.](https://portal.azure.com) Quando regista a sua candidatura ao cliente, fornece informações sobre o pedido à Azure AD. A Azure AD fornece então um ID do cliente (também chamado *de ID de aplicação)* que utiliza para associar a sua aplicação à Azure AD no tempo de execução. Para saber mais sobre o ID do cliente, consulte [os objetos principais de aplicação e serviço no Azure Ative Directory.](../../active-directory/develop/app-objects-and-service-principals.md)

Para registar a sua aplicação Azure Storage, siga os passos mostrados no [Quickstart: Registe uma aplicação com a plataforma de identidade microsoft](../../active-directory/develop/quickstart-configure-app-access-web-apis.md). A imagem a seguir mostra configurações comuns para registar uma aplicação web:

![Screenshot mostrando como registar a sua aplicação de armazenamento com Azure AD](./media/storage-auth-aad-app/app-registration.png)

> [!NOTE]
> Se registar a sua candidatura como uma aplicação nativa, pode especificar qualquer URI válido para o **URI de redirecionamento.** Para aplicações nativas, este valor não tem de ser um URL real. Para aplicações web, o URI de redirecionamento deve ser um URI válido, pois especifica o URL para o qual são fornecidos tokens.

Depois de ter registado a sua candidatura, verá o ID da aplicação (ou ID do cliente) em **Definições:**

![Screenshot mostrando o ID do cliente](./media/storage-auth-aad-app/app-registration-client-id.png)

Para obter mais informações sobre o registo de uma candidatura com a Azure AD, consulte [integrar aplicações com o Azure Ative Directory](../../active-directory/develop/quickstart-v2-register-an-app.md).

## <a name="grant-your-registered-app-permissions-to-azure-storage"></a>Conceda as permissões da sua aplicação registada ao Azure Storage

Em seguida, conceda permissões à sua candidatura para ligar para as APIs de Armazenamento Azure. Este passo permite que o seu pedido autorize pedidos para a Azure Storage com Azure AD.

1. Na página **'Vista Geral'** para a sua aplicação registada, selecione **Ver Permissões API**.
1. Na secção **de permissões API,** selecione **Adicionar uma permissão** e escolha **APIs da Microsoft**.
1. Selecione **Azure Storage** da lista de resultados para exibir o painel de **permissões da API do Pedido.**
1. Em **Que tipo de permissões a sua aplicação requer?** **Delegated permissions** Esta opção é selecionada por padrão.
1. Na secção **de permissões Select** do painel de **permissões da API do Pedido,** selecione a caixa de verificação ao lado **de user_impersonation,** em seguida, clique em  **Adicionar permissões**.

    ![Screenshot mostrando permissões para armazenamento](media/storage-auth-aad-app/registered-app-permissions-1.png)

O painel **de permissões da API** mostra agora que a sua aplicação AD Azure registada tem acesso tanto ao Microsoft Graph como ao Azure Storage APIs. As permissões são concedidas automaticamente ao Microsoft Graph quando regista a sua aplicação pela primeira vez com a Azure AD.

![Screenshot mostrando permissões de aplicações de registo](media/storage-auth-aad-app/registered-app-permissions-2.png)

## <a name="create-a-client-secret"></a>Criar um segredo de cliente

O pedido precisa de um segredo de cliente para provar a sua identidade ao pedir um token. Para adicionar o segredo do cliente, siga estes passos:

1. Navegue para o registo da sua aplicação no portal Azure.
1. Selecione a definição **de segredos & certificados.**
1. Sob **os segredos do Cliente,** clique em **Novo segredo de cliente** para criar um novo segredo.
1. Forneça uma descrição para o segredo e escolha o intervalo de validade pretendido.
1. Copie imediatamente o valor do novo segredo para um local seguro. O valor total é apresentado apenas uma vez.

    ![Screenshot mostrando o segredo do cliente](media/storage-auth-aad-app/client-secret.png)

## <a name="client-libraries-for-token-acquisition"></a>Bibliotecas de clientes para aquisição simbólica

Uma vez registado o seu pedido e concedido permissões de acesso a dados no armazenamento de Azure Blob ou no armazenamento da fila, pode adicionar código à sua aplicação para autenticar um principal de segurança e adquirir um token OAuth 2.0. Para autenticar e adquirir o token, pode utilizar uma das bibliotecas de [autenticação](../../active-directory/develop/reference-v2-libraries.md) da plataforma de identidade da Microsoft ou outra biblioteca de código aberto que suporte o OpenID Connect 1.0. A sua aplicação pode então utilizar o token de acesso para autorizar um pedido contra o armazenamento da Azure Blob ou o armazenamento da fila.

Para obter uma lista de cenários para os quais a aquisição de fichas é suportada, consulte a secção de fluxos de [autenticação](/en-us/azure/active-directory/develop/msal-authentication-flows) do conteúdo da Biblioteca de [Autenticação](/azure/active-directory/develop/msal-overview)do Microsoft .

## <a name="well-known-values-for-authentication-with-azure-ad"></a>Valores bem conhecidos para autenticação com Azure AD

Para autenticar um principal de segurança com Azure AD, tem de incluir alguns valores bem conhecidos no seu código.

### <a name="azure-ad-authority"></a>Autoridade AD de Azure

Para a nuvem pública da Microsoft, a base Azure AD authority é a seguinte, onde *o id do inquilino* é o seu ID do inquilino ative (ou iD do diretório):

`https://login.microsoftonline.com/<tenant-id>/`

A identificação do inquilino identifica o inquilino da Ad Azure para usar para autenticação. É também referido como o ID do diretório. Para recuperar o ID do inquilino, navegue na página **Geral** para o registo da sua aplicação no portal Azure e copie o valor daí a partir daí.

### <a name="azure-storage-resource-id"></a>ID de recurso de armazenamento Azure

[!INCLUDE [storage-resource-id-include](../../../includes/storage-resource-id-include.md)]

## <a name="net-code-example-create-a-block-blob"></a>.NET código exemplo: Criar uma bolha de bloco

O exemplo de código mostra como obter um token de acesso a partir de Azure AD. O token de acesso é utilizado para autenticar o utilizador especificado e, em seguida, autorizar um pedido para criar uma bolha de bloco. Para pôr esta amostra a funcionar, siga primeiro os passos descritos nas secções anteriores.

Para solicitar o token, necessitará dos seguintes valores a partir do registo da sua aplicação:

- O nome do seu domínio Azure AD. Recupere este valor a partir da página **geral** do seu Diretório Ativo Azure.
- O iD do inquilino (ou diretório). Recupere este valor a partir da página **geral** do registo da sua aplicação.
- O ID do cliente (ou aplicação). Recupere este valor a partir da página **geral** do registo da sua aplicação.
- O URI de redirecionamento de clientes. Recupere este valor das definições de **Autenticação** para o registo da sua aplicação.
- O valor do segredo do cliente. Recupere este valor a partir do local para o qual o copiou anteriormente.

### <a name="create-a-storage-account-and-container"></a>Criar uma conta de armazenamento e um recipiente

Para executar a amostra de código, crie uma conta de armazenamento dentro da mesma subscrição que o seu Diretório Ativo Azure. Em seguida, crie um recipiente dentro dessa conta de armazenamento. O código de amostra criará uma bolha de bloco neste recipiente.

Em seguida, atribua explicitamente a **função de Contribuinte de Dados blob de armazenamento** à conta de utilizador sob a qual irá executar o código de amostra. Para obter instruções sobre como atribuir esta função no portal Azure, consulte [o Acesso ao Bloco de Contas e dados de fila com o RBAC no portal Azure](storage-auth-aad-rbac-portal.md).

> [!NOTE]
> Quando cria uma conta de Armazenamento Azure, não lhe são atribuídas automaticamente permissões de acesso aos dados através do Azure AD. Deve atribuir-se explicitamente um papel Azure para o Azure Storage. Pode atribuí-lo ao nível da sua subscrição, grupo de recursos, conta de armazenamento ou contentor ou fila.

### <a name="create-a-web-application-that-authorizes-access-to-blob-storage-with-azure-ad"></a>Crie uma aplicação web que autorize o acesso ao armazenamento blob com Azure AD

Quando a sua aplicação acede ao Azure Storage, fá-lo em nome do utilizador, o que significa que os recursos de blob ou fila são acedidos usando as permissões do utilizador que está iniciado. Para experimentar este exemplo de código, precisa de uma aplicação web que indique ao utilizador que insinue-se utilizando uma identidade AD AZure. Pode criar a sua própria, ou utilizar a aplicação de amostra fornecida pela Microsoft.

Uma aplicação web de amostra concluída que adquire um token e a usa para criar uma bolha no Azure Storage está disponível no [GitHub](https://aka.ms/aadstorage). Rever e executar a amostra completa pode ser útil para entender os exemplos de código. Para obter instruções sobre como executar a amostra completa, consulte a secção intitulada [Ver e executar a amostra completa](#view-and-run-the-completed-sample).

#### <a name="add-references-and-using-statements"></a>Adicionar referências e usar declarações  

A partir do Visual Studio, instale a biblioteca de clientes Azure Storage. No menu **Ferramentas**, selecione **Gestor de Pacotes NuGet** e, em seguida, **Consola do Gestor de Pacotes**. Digite os seguintes comandos na janela da consola para instalar os pacotes necessários na biblioteca do cliente do Azure Storage para .NET:

# <a name="net-v12-sdk"></a>[.NET v12 SDK](#tab/dotnet)

```console
Install-Package Azure.Storage.Blobs
Install-Package Microsoft.Identity.Web -Version 0.4.0-preview
```

Em seguida, adicione as seguintes declarações usando o ficheiro HomeController.cs:

```csharp
using Microsoft.Identity.Web; //MSAL library for getting the access token
using Azure.Storage.Blobs;
```

# <a name="net-v11-sdk"></a>[.NET v11 SDK](#tab/dotnet11)

```console
Install-Package Microsoft.Azure.Storage.Blob
Install-Package Microsoft.Identity.Web -Version 0.4.0-preview
```

Em seguida, adicione as seguintes declarações usando o ficheiro HomeController.cs:

```csharp
using Microsoft.Identity.Client; //MSAL library for getting the access token
using Microsoft.Azure.Storage.Auth;
using Microsoft.Azure.Storage.Blob;
```

---

#### <a name="create-a-block-blob"></a>Criar uma bolha de bloco

Adicione o seguinte corte de código para criar uma bolha de bloco:

# <a name="net-v12-sdk"></a>[.NET v12 SDK](#tab/dotnet)

```csharp
private static async Task<string> CreateBlob(TokenAcquisitionTokenCredential tokenCredential)
{
    Uri blobUri = new Uri("https://<storage-account>.blob.core.windows.net/<container>/Blob1.txt");
    BlobClient blobClient = new BlobClient(blobUri, tokenCredential);

    string blobContents = "Blob created by Azure AD authenticated user.";
    byte[] byteArray = Encoding.ASCII.GetBytes(blobContents);

    using (MemoryStream stream = new MemoryStream(byteArray))
    {
        await blobClient.UploadAsync(stream);
    }
    return "Blob successfully created";
}
```

# <a name="net-v11-sdk"></a>[.NET v11 SDK](#tab/dotnet11)

```csharp
private static async Task<string> CreateBlob(string accessToken)
{
    // Create a blob on behalf of the user.
    TokenCredential tokenCredential = new TokenCredential(accessToken);
    StorageCredentials storageCredentials = new StorageCredentials(tokenCredential);

    // Replace the URL below with the URL to your blob.
    Uri blobUri = new Uri("https://<storage-account>.blob.core.windows.net/<container>/Blob1.txt");
    CloudBlockBlob blob = new CloudBlockBlob(blobUri, storageCredentials);
    await blob.UploadTextAsync("Blob created by Azure AD authenticated user.");
    return "Blob successfully created";
}
```

---

> [!NOTE]
> Para autorizar operações de bolhas e filas com um token OAuth 2.0, tem de utilizar HTTPS.

No exemplo acima, a biblioteca de clientes .NET trata da autorização do pedido de criação do bloco blob. As bibliotecas de clientes Azure Storage para outros idiomas também tratam da autorização do pedido para si. No entanto, se estiver a ligar para uma operação de Armazenamento Azure com um token OAuth utilizando a API REST, então terá de autorizar o pedido utilizando o token OAuth.

Para ligar para as operações de serviço Blob e Queue utilizando fichas de acesso OAuth, passe o token de acesso no cabeçalho **de autorização** utilizando o esquema **Bearer,** e especifique uma versão de serviço de 2017-11-09 ou superior, como mostra o seguinte exemplo:

```https
GET /container/file.txt HTTP/1.1
Host: mystorageaccount.blob.core.windows.net
x-ms-version: 2017-11-09
Authorization: Bearer eyJ0eXAiOnJKV1...Xd6j
```

#### <a name="get-an-access-token-from-azure-ad"></a>Obter um token de acesso do Azure AD

Em seguida, adicione um método que solicita um token da Azure AD em nome do utilizador. Este método define o âmbito para o qual devem ser concedidas permissões. Para obter mais informações sobre permissões e âmbitos, consulte [permissões e consentimento no ponto final da plataforma de identidade da Microsoft.](../../active-directory/develop/v2-permissions-and-consent.md)

Utilize o ID do recurso para construir o âmbito para a aquisição do token. O exemplo constrói o âmbito utilizando o ID do recurso juntamente com o âmbito incorporado, o `user_impersonation` que indica que o token está a ser solicitado em nome do utilizador.

Tenha em mente que poderá ter de apresentar ao utilizador uma interface que permita ao utilizador consentir em solicitar o sinal em seu nome:

```csharp
[AuthorizeForScopes(Scopes = new string[] { "https://storage.azure.com/user_impersonation" })]
public async Task<IActionResult> Blob()
{
    string message = await CreateBlob(new TokenAcquisitionTokenCredential(_tokenAcquisition));
    ViewData["Message"] = message;
    return View();
}
```

Consentimento é o processo de um utilizador que concede autorização a um pedido de acesso a recursos protegidos em seu nome. A plataforma de identidade 2.0 da Microsoft suporta o consentimento incremental, o que significa que um principal de segurança pode solicitar um conjunto mínimo de permissões inicialmente e adicionar permissões ao longo do tempo, conforme necessário. Quando o seu código solicitar um token de acesso, especifique o âmbito das permissões de que a sua aplicação necessita a qualquer momento no `scope` parâmetro. Para obter mais informações sobre o consentimento incremental, consulte [o consentimento incremental e dinâmico.](../../active-directory/azuread-dev/azure-ad-endpoint-comparison.md#incremental-and-dynamic-consent)

## <a name="view-and-run-the-completed-sample"></a>Ver e executar a amostra completa

Para executar a aplicação da amostra, primeiro clone ou descarregue-o a partir do [GitHub](https://github.com/Azure-Samples/storage-dotnet-azure-ad-msal). Em seguida, atualize a aplicação como descrito nas seguintes secções.

### <a name="provide-values-in-the-settings-file"></a>Fornecer valores no ficheiro de definições

Em seguida, atualize o *appsettings.jsficheiro* com os seus próprios valores, da seguinte forma:

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

### <a name="update-the-storage-account-and-container-name"></a>Atualizar a conta de armazenamento e o nome do recipiente

No ficheiro *HomeController.cs,* atualize o URI que faz referência à bolha de bloco para utilizar o nome da sua conta de armazenamento e do seu contentor, substituindo os valores em suportes angulares pelos seus próprios valores:

```html
https://<storage-account>.blob.core.windows.net/<container>/Blob1.txt
```

### <a name="enable-implicit-grant-flow"></a>Permitir o fluxo de subvenção implícita

Para executar a amostra, poderá ser necessário configurar o fluxo implícito de subvenção para o registo da sua aplicação. Siga estes passos:

1. Navegue para o registo da sua aplicação no portal Azure.
1. Na secção **Gerir,** selecione a **definição de Autenticação.**
1. Na secção **de concessão implícita,** selecione a caixa de verificação para permitir fichas de ID, como mostra a seguinte imagem:

    ![Screenshot mostrando como ativar configurações para fluxo de subvenção implícito](media/storage-auth-aad-app/enable-implicit-grant-flow.png)

### <a name="update-the-port-used-by-localhost"></a>Atualizar o porto utilizado pela localidade

Ao executar a amostra, poderá descobrir que precisa de atualizar o URI de redirecionamento especificado no registo da sua aplicação para utilizar a porta *local* atribuída no tempo de execução. Para atualizar o URI de redirecionamento para utilizar a porta atribuída, siga estes passos:

1. Navegue para o registo da sua aplicação no portal Azure.
1. Na secção **Gerir,** selecione a **definição de Autenticação.**
1. Em **IUR redirect**, edite a porta para corresponder à utilizada pela aplicação da amostra, como mostra a seguinte imagem:

    ![Screenshot mostrando URIs redirecionando para registo de aplicações](media/storage-auth-aad-app/redirect-uri.png)

## <a name="next-steps"></a>Passos seguintes

- [Plataforma de identidades da Microsoft](https://docs.microsoft.com/azure/active-directory/develop/)
- [Gerir direitos de acesso aos dados de armazenamento com o RBAC](storage-auth-aad-rbac.md)
- [Autenticar acesso a bolhas e filas com diretório ativo Azure e identidades geridas para recursos Azure](storage-auth-aad-msi.md)
