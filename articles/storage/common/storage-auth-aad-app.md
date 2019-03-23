---
title: Autenticar com o Azure Active Directory para aceder a dados de BLOBs e filas de seus aplicativos | Documentos da Microsoft
description: Utilize o Azure Active Directory para autenticar a partir de dentro de um aplicativo e, em seguida, autorizar pedidos para blobs e filas.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 03/21/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: a313061f89d33ee2bf5379dbd37495db06b64440
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58369518"
---
# <a name="authenticate-with-azure-active-directory-from-an-application-for-access-to-blobs-and-queues"></a>Autenticar com o Azure Active Directory a partir de uma aplicação para o acesso para blobs e filas

Das principais vantagens da utilização do Azure Active Directory (Azure AD) com o armazenamento do Azure é que as suas credenciais não precisam mais ser armazenada no seu código. Em vez disso, pode solicitar um token de acesso de OAuth 2.0 do Azure AD. O Azure AD processa a autenticação do principal de segurança (um utilizador, grupo ou principal de serviço) de execução do aplicativo. Se a autenticação for bem-sucedida, do Azure AD devolve o token de acesso à aplicação e a aplicação, em seguida, pode utilizar o token de acesso para autorizar os pedidos ao armazenamento do Azure.

Este artigo mostra como configurar a sua aplicação para a autenticação com o Azure AD. O .NET de recursos de exemplo de código, mas outras linguagens usam uma abordagem semelhante.

Antes de pode autenticar-se uma entidade de segurança da sua aplicação de armazenamento do Azure, configure definições de controlo (RBAC) de acesso baseado em funções para aquela entidade de segurança. O armazenamento do Azure define as funções RBAC que abranger permissões para contentores e filas. Quando a função RBAC é atribuída a uma entidade de segurança, essa entidade de segurança é concedido acesso a esse recurso. Para obter mais informações, consulte [gerir direitos de acesso aos dados de armazenamento com o RBAC](storage-auth-aad-rbac.md).

Para uma descrição geral do fluxo de concessão de código do OAuth 2.0, consulte [fluxo de concessão de acesso de autorizar a aplicações de web do Azure Active Directory usando o código de OAuth 2.0](../../active-directory/develop/v1-protocols-oauth-code.md).

[!INCLUDE [storage-auth-aad-note-include](../../../includes/storage-auth-aad-note-include.md)]

## <a name="assign-an-rbac-role-to-an-azure-ad-security-principal"></a>Atribuir uma função RBAC para um principal de segurança do Azure AD

Para autenticar uma entidade de segurança da sua aplicação de armazenamento do Azure, primeiro de configurar as definições de controlo (RBAC) de acesso baseado em funções para aquela entidade de segurança. O armazenamento do Azure define as funções RBAC que abranger permissões para contentores e filas. Quando a função RBAC é atribuída a uma entidade de segurança, essa entidade de segurança é concedido acesso a esse recurso. Para obter mais informações, consulte [gerir direitos de acesso aos dados de Blobs do Azure e a fila com o RBAC](storage-auth-aad-rbac.md).

## <a name="register-your-application-with-an-azure-ad-tenant"></a>Registar a aplicação com um inquilino do Azure AD

O primeiro passo para utilizar o Azure AD para autorizar o acesso aos recursos de armazenamento está a registar a aplicação cliente no inquilino do Azure AD. Registar a aplicação permite-lhe chamar do Azure [Active Directory Authentication Library](../../active-directory/active-directory-authentication-libraries.md) (ADAL) a partir do código. A ADAL fornece uma API para autenticar com o Azure AD a partir da sua aplicação. Registar a aplicação também permite-lhe autorizar chamadas desse aplicativo para APIs de armazenamento do Azure com um token de acesso.

Quando registar a sua aplicação, fornecer informações sobre a sua aplicação para o Azure AD. O Azure AD, em seguida, fornece um ID de cliente (também chamado de um *ID da aplicação*) que utilizou para associar a sua aplicação com o Azure AD em tempo de execução. Para saber mais sobre o ID de cliente, veja [aplicativos e objetos de principal de serviço no Azure Active Directory](../../active-directory/develop/app-objects-and-service-principals.md).

Para registar a aplicação de armazenamento do Azure, siga os passos a [adicionar uma aplicação](../../active-directory/develop/quickstart-v1-add-azure-ad-app.md) secção [integrar aplicações com o Azure Active Directory](../../active-directory/active-directory-integrating-applications.md). Se registar a sua aplicação como um aplicativo nativo, pode especificar qualquer URI válida para o **URI de redirecionamento**. O valor não precisa de ser um ponto final real.

![Captura de ecrã que mostra como registar a sua aplicação de armazenamento com o Azure AD](./media/storage-auth-aad-app/app-registration.png)

Depois de registar a aplicação, verá o ID da aplicação (ou o ID de cliente) em **definições**:

![Captura de ecrã que mostra o ID de cliente](./media/storage-auth-aad-app/app-registration-client-id.png)

Para obter mais informações sobre como registar uma aplicação com o Azure AD, consulte [integrar aplicações com o Azure Active Directory](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md). 

## <a name="grant-your-registered-app-permissions-to-azure-storage"></a>Conceder as permissões de aplicação registada para o armazenamento do Azure

Em seguida, tem de conceder as permissões de aplicação para chamar as APIs de armazenamento do Azure. Este passo permite que a sua aplicação autorizar chamadas para o armazenamento do Azure com o Azure AD.

1. No painel de navegação do lado esquerdo do portal do Azure, escolha **todos os serviços**e procure **registos das aplicações**.
2. Procure o nome do aplicativo registrado que criou no passo anterior.
3. Selecione a sua aplicação registada e clique em **definições**. Na **acesso à API** secção, selecione **permissões obrigatórias**.
4. Na **permissões obrigatórias** painel, clique nas **Add** botão.
5. Sob **selecionar uma API**, procure "Azure Storage" e selecione **armazenamento do Azure** na lista de resultados.

    ![Captura de ecrã que mostra permissões para o armazenamento](media/storage-auth-aad-app/registered-app-permissions-1.png)

6. Sob **selecionar permissões**, a caixa de verificação junto a **armazenamento de acesso do Azure**e clique em **selecionar**.
7. Clique em **Concluído**.

O **permissões obrigatórias** agora o windows mostra que a sua aplicação do Azure AD tem acesso ao Azure Active Directory e o armazenamento do Azure. São concedidas permissões para o Azure AD automaticamente quando primeiro registar a sua aplicação com o Azure AD.

![Captura de ecrã que mostra registar permissões de aplicações](media/storage-auth-aad-app/registered-app-permissions-2.png)

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

O ID de inquilino identifica o inquilino do Azure AD para utilizar para autenticação. Para obter o ID de inquilino, siga os passos descritos em **obter o ID de inquilino do Azure Active Directory**.

#### <a name="storage-resource-id"></a>ID de recurso de armazenamento

Utilize o ID de recurso de armazenamento do Azure para adquirir um token para autenticar pedidos no armazenamento do Azure:

`https://storage.azure.com/`

### <a name="get-the-tenant-id-for-your-azure-active-directory"></a>Obter o ID de inquilino do Azure Active Directory

Para obter o ID de inquilino, siga estes passos:

1. No portal do Azure, selecione o seu Active Directory.
2. Clique em **Propriedades**.
3. Copie o valor GUID fornecido para o **ID de diretório**. Este valor também é chamado de ID do inquilino.

![Captura de ecrã que mostra como copiar o ID de inquilino](./media/storage-auth-aad-app/aad-tenant-id.png)

### <a name="add-references-and-using-statements"></a>Adicionar referências e instruções "using"  

No Visual Studio, instale a biblioteca de cliente de armazenamento do Azure. Do **ferramentas** menu, selecione **Gestor de pacotes Nuget**, em seguida, **Package Manager Console**. Escreva o seguinte comando na consola para instalar a versão mais recente da biblioteca de clientes para o .NET:

```
Install-Package WindowsAzure.Storage
```

Também instale a versão mais recente da ADAL:

```
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
```

Em seguida, adicione o seguinte através de instruções para o seu código:

```dotnet
using System.Globalization;
using Microsoft.IdentityModel.Clients.ActiveDirectory; //ADAL client library for getting the access token
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.WindowsAzure.Storage.Blob;
```

### <a name="get-an-oauth-token-from-azure-ad"></a>Obter um OAuth token do Azure AD

Em seguida, adicione um método que solicita um token do Azure AD. Para pedir o token, chamar o [AuthenticationContext.AcquireTokenAsync](https://docs.microsoft.com/dotnet/api/microsoft.identitymodel.clients.activedirectory.authenticationcontext.acquiretokenasync) método. Certifique-se de que tem os seguintes valores nos passos que seguiu anteriormente:

- ID de inquilino (diretório)
- ID de cliente (aplicação)
- URI de redirecionamento do cliente

```dotnet
static string GetUserOAuthToken()
{
    const string ResourceId = "https://storage.azure.com/";
    const string AuthInstance = "https://login.microsoftonline.com/{0}/";
    const string TenantId = "<tenant-id>"; // Tenant or directory ID

    // Construct the authority string from the Azure AD OAuth endpoint and the tenant ID. 
    string authority = string.Format(CultureInfo.InvariantCulture, AuthInstance, TenantId);
    AuthenticationContext authContext = new AuthenticationContext(authority);

    // Acquire an access token from Azure AD. 
    AuthenticationResult result = authContext.AcquireTokenAsync(ResourceId, 
                                                                "<client-id>", 
                                                                new Uri(@"<client-redirect-uri>"), 
                                                                new PlatformParameters(PromptBehavior.Auto)).Result;

    return result.AccessToken;
}
```

### <a name="create-the-block-blob"></a>Criar o blob de blocos

Por fim, use o token de acesso para criar novas credenciais de armazenamento e usar as credencias para criar o blob:

```dotnet
// Get the access token.
string accessToken = GetUserOAuthToken();

// Use the access token to create the storage credentials.
TokenCredential tokenCredential = new TokenCredential(accessToken);
StorageCredentials storageCredentials = new StorageCredentials(tokenCredential);

// Create a block blob using those credentials
CloudBlockBlob blob = new CloudBlockBlob(new Uri("https://storagesamples.blob.core.windows.net/sample-container/Blob1.txt"), storageCredentials);

blob.UploadTextAsync("Blob created by Azure AD authenticated user.");
```

> [!NOTE]
> A integração do Azure AD com o armazenamento do Azure requer a utilização do HTTPS para operações de armazenamento do Azure.

No exemplo acima, a biblioteca de cliente .NET processa a autorização do pedido para criar o blob de blocos. Outras bibliotecas de cliente de armazenamento também lidar com a autorização do pedido para. No entanto, se estiver a chamar uma operação de armazenamento do Azure com um token de OAuth utilizando a API REST, em seguida, terá de autorizar a solicitação usando o token OAuth.   

Para chamar operações de serviço de BLOBs e filas com tokens de acesso de OAuth, passar o token de acesso a **autorização** usando o cabeçalho a **portador** esquema e especifique uma versão de serviço de 2017-11-09 ou superior, como mostrado no exemplo a seguir:

```
GET /container/file.txt HTTP/1.1
Host: mystorageaccount.blob.core.windows.net
x-ms-version: 2017-11-09
Authorization: Bearer eyJ0eXAiOnJKV1...Xd6j
```

Para obter mais informações sobre a autorização de operações de armazenamento do Azure a partir de REST, consulte [autenticar com o Azure Active Directory](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory).

## <a name="next-steps"></a>Passos Seguintes

- Para saber mais sobre as funções do RBAC para o armazenamento do Azure, veja [gerir direitos de acesso aos dados de armazenamento com o RBAC](storage-auth-aad-rbac.md).
- Para saber mais sobre a utilização de identidades geridas para recursos do Azure com o armazenamento do Azure, veja [Authenticate acesso para blobs e filas com o Azure managed identidades para recursos do Azure](storage-auth-aad-msi.md).
- Para saber como iniciar sessão CLI do Azure e o PowerShell com uma identidade do Azure AD, veja [utilizar uma identidade do Azure AD para aceder ao armazenamento do Azure com a CLI ou o PowerShell](storage-auth-aad-script.md).
