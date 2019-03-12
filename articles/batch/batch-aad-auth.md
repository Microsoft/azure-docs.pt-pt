---
title: Utilizar o Azure Active Directory para autenticar soluções de serviços do Azure Batch | Documentos da Microsoft
description: O batch suporta o Azure AD para autenticação do serviço Batch.
services: batch
documentationcenter: .net
author: laurenhughes
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/18/2018
ms.author: lahugh
ms.openlocfilehash: 4ecee0e4f01da329e44c0f65b2c204520f2da87f
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57548341"
---
# <a name="authenticate-batch-service-solutions-with-active-directory"></a>Autenticar soluções de serviço do Batch com o Active Directory

O Azure Batch suporta a autenticação com [do Azure Active Directory] [ aad_about] (Azure AD). O Azure AD é o diretório de multi-inquilino com base na cloud da Microsoft e o serviço de gestão de identidade. Azure próprio utiliza o Azure AD para autenticar os seus clientes, os administradores de serviços e utilizadores organizacionais.

Ao utilizar a autenticação do Azure AD com o Azure Batch, pode autenticar-se de uma de duas formas:

- Usando **autenticação integrada do** para autenticar um usuário que está a interagir com a aplicação. Uma aplicação utilizando a autenticação integrada reúne as credenciais de um utilizador e utiliza essas credenciais para autenticar o acesso a recursos do Batch.
- Ao utilizar um **principal de serviço** para autenticar uma aplicação autónoma. Um principal de serviço define a política e as permissões para uma aplicação para poder representar a aplicação ao aceder a recursos em tempo de execução.

Para saber mais sobre o Azure AD, veja a [do Azure Active Directory Documentation](https://docs.microsoft.com/azure/active-directory/).

## <a name="endpoints-for-authentication"></a>Pontos finais para autenticação

Para autenticar as aplicações do Batch com o Azure AD, tem de incluir alguns pontos de extremidade bem conhecidos no seu código.

### <a name="azure-ad-endpoint"></a>Ponto final de AD do Azure

A base de ponto final da autoridade do Azure AD é:

`https://login.microsoftonline.com/`

Para autenticar com o Azure AD, utilize este ponto final em conjunto com o ID de inquilino (ID de diretório). O ID de inquilino identifica o inquilino do Azure AD para utilizar para autenticação. Para obter o ID de inquilino, siga os passos descritos em [obter o ID de inquilino do Azure Active Directory](#get-the-tenant-id-for-your-active-directory):

`https://login.microsoftonline.com/<tenant-id>`

> [!NOTE] 
> O ponto de extremidade específico de inquilino é necessário quando se autentica com um principal de serviço. 
> 
> O ponto de extremidade específico de inquilino é opcional quando se autentica utilizando a autenticação integrada, mas recomendado. No entanto, também pode utilizar o ponto de extremidade comum do Azure AD. O ponto de extremidade comum fornece uma credencial genérica recolha interface quando não for fornecido um inquilino específico. O ponto de extremidade comum é `https://login.microsoftonline.com/common`.
>
>

Para obter mais informações sobre os pontos finais do Azure AD, consulte [cenários de autenticação do Azure AD][aad_auth_scenarios].

### <a name="batch-resource-endpoint"></a>Ponto final de recurso do batch

Utilize o **ponto final de recurso do Azure Batch** para adquirir um token para autenticar pedidos para o serviço Batch:

`https://batch.core.windows.net/`

## <a name="register-your-application-with-a-tenant"></a>Registar a aplicação com um inquilino

O primeiro passo para utilizar o Azure AD para autenticar está a registar a aplicação no inquilino do Azure AD. Registar a aplicação permite-lhe chamar do Azure [Active Directory Authentication Library] [ aad_adal] (ADAL) a partir do código. A ADAL fornece uma API para autenticar com o Azure AD a partir da sua aplicação. Registar a aplicação é necessário se planeia utilizar a autenticação integrada ou um principal de serviço.

Quando registar a sua aplicação, fornecer informações sobre a sua aplicação para o Azure AD. Em seguida, do Azure AD fornece um ID de aplicação (também chamado de um *ID de cliente*) que utilizou para associar a sua aplicação com o Azure AD em tempo de execução. Para saber mais sobre o ID da aplicação, veja [aplicativos e objetos de principal de serviço no Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md).

Para registar a aplicação do Batch, siga os passos a [adicionar uma aplicação](../active-directory/develop/quickstart-v1-add-azure-ad-app.md) secção [integrar aplicações com o Azure Active Directory][aad_integrate]. Se registar a sua aplicação como um aplicativo nativo, pode especificar qualquer URI válida para o **URI de redirecionamento**. Não é necessário ser um ponto final real.

Depois de registar a aplicação, verá o ID da aplicação:

![Registar a sua aplicação do Batch com o Azure AD](./media/batch-aad-auth/app-registration-data-plane.png)

Para obter mais informações sobre como registar uma aplicação com o Azure AD, consulte [cenários de autenticação do Azure AD](../active-directory/develop/authentication-scenarios.md).

## <a name="get-the-tenant-id-for-your-active-directory"></a>Obter o ID de inquilino do seu Active Directory

O ID de inquilino identifica o inquilino do Azure AD, que fornece serviços de autenticação à sua aplicação. Para obter o ID de inquilino, siga estes passos:

1. No portal do Azure, selecione o seu Active Directory.
2. Clique em **Propriedades**.
3. Copie o valor GUID fornecido para o **ID de diretório**. Este valor também é chamado de ID do inquilino.

![Copie o ID de diretório](./media/batch-aad-auth/aad-directory-id.png)


## <a name="use-integrated-authentication"></a>Utilizar a autenticação integrada

Para autenticar com a autenticação integrada, tem de conceder as permissões de aplicação para ligar à API de serviço do Batch. Este passo ativa a sua aplicação autenticar as chamadas à API de serviço do Batch com o Azure AD.

Assim que tiver registado a sua aplicação, siga estes passos no portal do Azure para conceder acesso para o serviço Batch:

1. No painel de navegação do lado esquerdo do portal do Azure, escolha **todos os serviços**. Clique em **registos das aplicações**.
2. Procure o nome da sua aplicação na lista de registos de aplicações:

    ![Procurar nome da sua aplicação](./media/batch-aad-auth/search-app-registration.png)

3. Clique na aplicação e clique em **definições**. Na **acesso à API** secção, selecione **permissões obrigatórias**.
4. Na **permissões obrigatórias** painel, clique nas **Add** botão.
5. Na **selecionar uma API**, procure a API do Batch. Procure para cada uma destas cadeias até encontrar a API:
    1. **MicrosoftAzureBatch**.
    2. **Batch do Microsoft Azure**. Os inquilinos mais recentes podem utilizar este nome.
    3. **ddbf3205-c6bd-46ae-8127-60eb93363864** é o ID para a API do Batch. 
6. Depois de localizar a API do Batch, selecione-o e clique em **selecione**.
7. Na **selecionar permissões**, selecione a caixa de verificação junto a **acesso do Azure Batch Service** e clique em **selecionar**.
8. Clique em **Concluído**.

O **permissões obrigatórias** windows agora mostra que a sua aplicação do Azure AD tem acesso à ADAL e o Batch serviço API. As permissões são concedidas a ADAL automaticamente quando primeiro registar a aplicação com o Azure AD.

![Permissões de concessão de API](./media/batch-aad-auth/required-permissions-data-plane.png)

## <a name="use-a-service-principal"></a>Utilizar um principal de serviço 

Para autenticar uma aplicação que é executada autónoma, utilize um principal de serviço. Depois de registar a aplicação, siga estes passos no portal do Azure para configurar um principal de serviço:

1. Pedir uma chave secreta para a sua aplicação.
2. Atribua uma função RBAC para a sua aplicação.

### <a name="request-a-secret-key-for-your-application"></a>Pedir uma chave secreta para a sua aplicação

Quando a aplicação autentica-se com um principal de serviço, envia o ID da aplicação e uma chave secreta para o Azure AD. Terá de criar e copie a chave secreta para utilizar a partir do código.

Siga estes passos no portal do Azure:

1. No painel de navegação do lado esquerdo do portal do Azure, escolha **todos os serviços**. Clique em **registos das aplicações**.
2. Procure o nome da sua aplicação na lista de registos de aplicações.
3. Clique na aplicação e clique em **definições**. Na **acesso à API** secção, selecione **chaves**.
4. Para criar uma chave, introduza uma descrição para a chave. Em seguida, selecione uma duração para a chave de um ou dois anos. 
5. Clique nas **guardar** botão para criar e apresentar a chave. Copie o valor da chave para um local seguro, pois não será capaz de acessá-lo novamente depois de sair do painel. 

    ![Criar uma chave secreta](./media/batch-aad-auth/secret-key.png)

### <a name="assign-an-rbac-role-to-your-application"></a>Atribuir uma função RBAC para a sua aplicação

Para se autenticar com um principal de serviço, terá de atribuir uma função RBAC para a sua aplicação. Siga estes passos.

1. No portal do Azure, navegue para a conta do Batch utilizada pela sua aplicação.
2. Na **configurações** painel para a conta do Batch, selecione **controlo de acesso (IAM)**.
3. Clique nas **atribuições de funções** separador.
4. Clique nas **adicionar atribuição de função** botão. 
5. Do **função** menu pendente, escolha o o _contribuinte_ ou _leitor_ função para a sua aplicação. Para obter mais informações sobre estas funções, consulte [introdução ao controlo de acesso baseado em funções no portal do Azure](../role-based-access-control/overview.md).  
6. Na **selecione** , insira o nome da sua aplicação. Selecione a aplicação a partir da lista e clique em **guardar**.

A aplicação deverá agora aparecer nas definições de controlo do acesso com uma função RBAC atribuída. 

![Atribuir uma função RBAC para a sua aplicação](./media/batch-aad-auth/app-rbac-role.png)

### <a name="get-the-tenant-id-for-your-azure-active-directory"></a>Obter o ID de inquilino do Azure Active Directory

O ID de inquilino identifica o inquilino do Azure AD, que fornece serviços de autenticação à sua aplicação. Para obter o ID de inquilino, siga estes passos:

1. No portal do Azure, selecione o seu Active Directory.
2. Clique em **Propriedades**.
3. Copie o valor GUID fornecido para o **ID de diretório**. Este valor também é chamado de ID do inquilino.

![Copie o ID de diretório](./media/batch-aad-auth/aad-directory-id.png)


## <a name="code-examples"></a>Exemplos de código

Os exemplos de código nesta secção mostram como autenticar com o Azure AD utilizando a autenticação integrada e com um principal de serviço. A maioria destes exemplos de código usa o .NET, mas os conceitos são semelhantes para outros idiomas.

> [!NOTE]
> Um token de autenticação do Azure AD expira após uma hora. Quando utilizar uma vida longa **BatchClient** de objeto, recomendamos que obter um token da ADAL em cada pedido para confirmar que tem sempre um token válido. 
>
>
> Para conseguir isso no .NET, escrever um método que obtém o token do Azure AD e passar esse método para um **BatchTokenCredentials** objeto como um delegado. O método de delegado é chamado em cada solicitação para o serviço Batch para se certificar de que é fornecido um token válido. Por predefinição ADAL coloca em cache tokens, para que um novo token é obtido a partir do Azure AD apenas quando necessário. Para obter mais informações sobre tokens no Azure AD, consulte [cenários de autenticação do Azure AD][aad_auth_scenarios].
>
>

### <a name="code-example-using-azure-ad-integrated-authentication-with-batch-net"></a>Exemplo de código: Utilizar o Azure AD autenticação integrada com o Batch .NET

Para autenticar com a autenticação integrada do .NET do Batch, fazer referência a [do Azure Batch .NET](https://www.nuget.org/packages/Microsoft.Azure.Batch/) pacote e o [ADAL](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) pacote.

Incluem o seguinte `using` instruções no seu código:

```csharp
using Microsoft.Azure.Batch;
using Microsoft.Azure.Batch.Auth;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Ponto final de referência do Azure AD no seu código, incluindo o ID do inquilino. Para obter o ID de inquilino, siga os passos descritos em [obter o ID de inquilino do Azure Active Directory](#get-the-tenant-id-for-your-active-directory):

```csharp
private const string AuthorityUri = "https://login.microsoftonline.com/<tenant-id>";
```

Referenciar o ponto de final do recurso de serviço do Batch:

```csharp
private const string BatchResourceUri = "https://batch.core.windows.net/";
```

Fazer referência a sua conta do Batch:

```csharp
private const string BatchAccountUrl = "https://myaccount.mylocation.batch.azure.com";
```

Especifica o ID de aplicação (ID de cliente) para a sua aplicação. O ID da aplicação está disponível no seu registo de aplicações no portal do Azure:

```csharp
private const string ClientId = "<application-id>";
```

Também copie o URI de redirecionamento que especificou, se tiver registado a sua aplicação como um aplicativo nativo. O URI especificado no seu código de redirecionamento tem de corresponder ao URI de redirecionamento que que forneceu quando se registou a aplicação:

```csharp
private const string RedirectUri = "http://mybatchdatasample";
```

Escreva um método de retorno de chamada para adquirir o token de autenticação do Azure AD. O **GetAuthenticationTokenAsync** método de retorno de chamada mostrado aqui chamadas da ADAL para autenticar um utilizador que está a interagir com a aplicação. O **AcquireTokenAsync** método fornecido pelo ADAL pede ao utilizador para as suas credenciais e a receita do aplicativo, assim que o utilizador fornece-los (a menos que já tem em cache as credenciais):

```csharp
public static async Task<string> GetAuthenticationTokenAsync()
{
    var authContext = new AuthenticationContext(AuthorityUri);

    // Acquire the authentication token from Azure AD.
    var authResult = await authContext.AcquireTokenAsync(BatchResourceUri, 
                                                        ClientId, 
                                                        new Uri(RedirectUri), 
                                                        new PlatformParameters(PromptBehavior.Auto));

    return authResult.AccessToken;
}
```

Construir uma **BatchTokenCredentials** objeto que usa o delegado como parâmetro. Utilize as credenciais para abrir um **BatchClient** objeto. Pode utilizá-la **BatchClient** objeto para operações subsequentes com o serviço do Batch:

```csharp
public static async Task PerformBatchOperations()
{
    Func<Task<string>> tokenProvider = () => GetAuthenticationTokenAsync();

    using (var client = await BatchClient.OpenAsync(new BatchTokenCredentials(BatchAccountUrl, tokenProvider)))
    {
        await client.JobOperations.ListJobs().ToListAsync();
    }
}
```

### <a name="code-example-using-an-azure-ad-service-principal-with-batch-net"></a>Exemplo de código: Utilizar um principal de serviço do Azure AD com o .NET do Batch

Para autenticar com um principal de serviço do Batch .NET, fazer referência a [do Azure Batch .NET](https://www.nuget.org/packages/Azure.Batch/) pacote e o [ADAL](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) pacote.

Incluem o seguinte `using` instruções no seu código:

```csharp
using Microsoft.Azure.Batch;
using Microsoft.Azure.Batch.Auth;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Ponto final de referência do Azure AD no seu código, incluindo o ID do inquilino. Quando utilizar um principal de serviço, tem de fornecer um ponto de extremidade específico de inquilino. Para obter o ID de inquilino, siga os passos descritos em [obter o ID de inquilino do Azure Active Directory](#get-the-tenant-id-for-your-active-directory):

```csharp
private const string AuthorityUri = "https://login.microsoftonline.com/<tenant-id>";
```

Referenciar o ponto de final do recurso de serviço do Batch:  

```csharp
private const string BatchResourceUri = "https://batch.core.windows.net/";
```

Fazer referência a sua conta do Batch:

```csharp
private const string BatchAccountUrl = "https://myaccount.mylocation.batch.azure.com";
```

Especifica o ID de aplicação (ID de cliente) para a sua aplicação. O ID da aplicação está disponível no seu registo de aplicações no portal do Azure:

```csharp
private const string ClientId = "<application-id>";
```

Especifique a chave secreta que copiou do portal do Azure:

```csharp
private const string ClientKey = "<secret-key>";
```

Escreva um método de retorno de chamada para adquirir o token de autenticação do Azure AD. O **GetAuthenticationTokenAsync** método de retorno de chamada mostrado aqui chamadas da ADAL para autenticação automática:

```csharp
public static async Task<string> GetAuthenticationTokenAsync()
{
    AuthenticationContext authContext = new AuthenticationContext(AuthorityUri);
    AuthenticationResult authResult = await authContext.AcquireTokenAsync(BatchResourceUri, new ClientCredential(ClientId, ClientKey));

    return authResult.AccessToken;
}
```

Construir uma **BatchTokenCredentials** objeto que usa o delegado como parâmetro. Utilize as credenciais para abrir um **BatchClient** objeto. Em seguida, utilizá-la **BatchClient** objeto para operações subsequentes com o serviço do Batch:

```csharp
public static async Task PerformBatchOperations()
{
    Func<Task<string>> tokenProvider = () => GetAuthenticationTokenAsync();

    using (var client = await BatchClient.OpenAsync(new BatchTokenCredentials(BatchAccountUrl, tokenProvider)))
    {
        await client.JobOperations.ListJobs().ToListAsync();
    }
}
```
### <a name="code-example-using-an-azure-ad-service-principal-with-batch-python"></a>Exemplo de código: Utilizar um principal de serviço do Azure AD com o Batch Python

Para se autenticar com um principal de serviço do Batch Python, instale e fazer referência a [azure-batch](https://pypi.org/project/azure-batch/) e [comuns do azure](https://pypi.org/project/azure-common/) módulos.


```python
from azure.batch import BatchServiceClient
from azure.common.credentials import ServicePrincipalCredentials
```

Quando utilizar um principal de serviço, tem de fornecer o ID do inquilino. Para obter o ID de inquilino, siga os passos descritos em [obter o ID de inquilino do Azure Active Directory](#get-the-tenant-id-for-your-active-directory):

```python
TENANT_ID = "<tenant-id>";
```

Referenciar o ponto de final do recurso de serviço do Batch:  

```python
RESOURCE = "https://batch.core.windows.net/";
```

Fazer referência a sua conta do Batch:

```python
BATCH_ACCOUNT_URL = "https://myaccount.mylocation.batch.azure.com";
```

Especifica o ID de aplicação (ID de cliente) para a sua aplicação. O ID da aplicação está disponível no seu registo de aplicações no portal do Azure:

```python
CLIENT_ID = "<application-id>";
```

Especifique a chave secreta que copiou do portal do Azure:

```python
SECRET = "<secret-key>";
```

Criar uma **ServicePrincipalCredentials** objeto:

```python
credentials = ServicePrincipalCredentials(
    client_id=CLIENT_ID,
    secret=SECRET,
    tenant=TENANT_ID,
    resource=RESOURCE
)
```

Utilize as credenciais do principal de serviço para abrir um **BatchServiceClient** objeto. Em seguida, utilizá-la **BatchServiceClient** objeto para operações subsequentes com o serviço do Batch.

```python
    batch_client = BatchServiceClient(
    credentials,
    base_url=BATCH_ACCOUNT_URL
)
```

## <a name="next-steps"></a>Passos Seguintes

* Para saber mais sobre o Azure AD, veja a [do Azure Active Directory Documentation](https://docs.microsoft.com/azure/active-directory/). Exemplos detalhados que mostram como utilizar a ADAL estão disponíveis no [exemplos de código do Azure](https://azure.microsoft.com/resources/samples/?service=active-directory) biblioteca.

* Para obter mais informações sobre principais de serviço, consulte [aplicativos e objetos de principal de serviço no Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md). Para criar um principal de serviço com o portal do Azure, veja [utilize o portal para criar o principal de aplicações e serviço que pode aceder aos recursos do Active Directory](../active-directory/develop/howto-create-service-principal-portal.md). Também pode criar um principal de serviço com o PowerShell ou da CLI do Azure.

* Para autenticar aplicações de gestão do Batch com o Azure AD, consulte [soluções de gestão de Batch de autenticar com o Active Directory](batch-aad-auth-management.md).

* Para obter um exemplo de Python de como criar um cliente do Batch autenticado utilizando um token do Azure AD, consulte a [implementar o Azure Batch Custom Image com um Script de Python](https://github.com/azurebigcompute/recipes/blob/master/Azure%20Batch/CustomImages/CustomImagePython.md) exemplo.

[aad_about]:../active-directory/fundamentals/active-directory-whatis.md "O que é o Azure Active Directory?"
[aad_adal]: ../active-directory/active-directory-authentication-libraries.md
[aad_auth_scenarios]: ../active-directory/active-directory-authentication-scenarios.md "Cenários de autenticação do Azure AD"
[aad_integrate]: ../active-directory/active-directory-integrating-applications.md "Integrar aplicações com o Azure Active Directory"
[azure_portal]: https://portal.azure.com
