---
title: Serviços authenticate Azure Batch com Diretório Ativo Azure
description: O lote suporta a AD Azure para autenticação do serviço Batch. Aprenda a autenticar de uma de duas maneiras.
ms.topic: article
ms.date: 01/28/2020
ms.custom: has-adal-ref
ms.openlocfilehash: 3fa1aa2bb7389200fe5e5a80598686143344d636
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2020
ms.locfileid: "82608477"
---
# <a name="authenticate-batch-service-solutions-with-active-directory"></a>Soluções de serviço authenticate Batch com Diretório Ativo

O Azure Batch suporta a autenticação com [o Azure Ative Directory][aad_about] (Azure AD). A Azure AD é o serviço de diretório e gestão de identidade multi-inquilinos da Microsoft. A própria Azure utiliza a Azure AD para autenticar os seus clientes, administradores de serviços e utilizadores organizacionais.

Ao utilizar a autenticação Azure AD com o Lote Azure, pode autenticar de uma de duas formas:

- Utilizando **a autenticação integrada** para autenticar um utilizador que esteja a interagir com a aplicação. Uma aplicação que utiliza a autenticação integrada reúne as credenciais de um utilizador e utiliza essas credenciais para autenticar o acesso aos recursos do Lote.
- Utilizando um diretor de **serviço** para autenticar uma aplicação sem supervisão. Um diretor de serviço define a política e as permissões para uma aplicação de forma a representar a aplicação no acesso aos recursos em tempo de execução.

Para saber mais sobre o Azure AD, consulte a Documentação de [Diretório Ativo Azure.](https://docs.microsoft.com/azure/active-directory/)

## <a name="endpoints-for-authentication"></a>Pontos finais para autenticação

Para autenticar aplicações do Lote com a AD Azure, precisa de incluir alguns pontos finais bem conhecidos no seu código.

### <a name="azure-ad-endpoint"></a>Ponto final da AD Azure

O ponto final da autoridade da adi azure base é:

`https://login.microsoftonline.com/`

Para autenticar com a Azure AD, utiliza este ponto final juntamente com o ID do inquilino (ID do diretório). O ID do inquilino identifica o inquilino da AD Azure para usar para autenticação. Para recuperar a identificação do inquilino, siga os passos delineados no [Get the Tenant ID para o seu Diretório Ativo Azure:](#get-the-tenant-id-for-your-active-directory)

`https://login.microsoftonline.com/<tenant-id>`

> [!NOTE]
> O ponto final específico do inquilino é necessário quando autentica o recurso a um diretor de serviço.
>
> O ponto final específico do arrendatário é opcional quando autentica com a autenticação integrada, mas recomendado. No entanto, também pode utilizar o ponto final comum da AD Azure. O ponto final comum fornece uma interface de reunião de credencial genérica quando um inquilino específico não é fornecido. O ponto final `https://login.microsoftonline.com/common`comum é.
>
>

Para obter mais informações sobre os pontos finais da AD Azure, consulte Cenários de [Autenticação para AD Azure][aad_auth_scenarios].

### <a name="batch-resource-endpoint"></a>Ponto final do recurso do lote

Utilize o ponto final do **recurso Azure Batch** para adquirir um símbolo para autenticação de pedidos ao serviço Batch:

`https://batch.core.windows.net/`

## <a name="register-your-application-with-a-tenant"></a>Registe o seu pedido com um inquilino

O primeiro passo para a utilização da AD Azure para autenticar é registar a sua candidatura num inquilino da Azure AD. Registar a sua aplicação permite-lhe ligar para a Biblioteca de [Autenticação de Diretórios Ativos][aad_adal] Azure (ADAL) a partir do seu código. A ADAL fornece uma API para autenticar com a Azure AD a partir da sua aplicação. O registo da sua candidatura é necessário quer pretenda utilizar a autenticação integrada ou um diretor de serviço.

Ao registar a sua candidatura, fornece informações sobre a sua candidatura à Azure AD. A Azure AD fornece então um ID de aplicação (também chamado de ID do *cliente)* que utiliza para associar a sua aplicação ao Azure AD em tempo de execução. Para saber mais sobre o ID da aplicação, consulte [os objetos principais de aplicação e serviço no Diretório Ativo azure](../active-directory/develop/app-objects-and-service-principals.md).

Para registar a sua aplicação Batch, siga os passos na secção [adicionar uma aplicação](../active-directory/develop/quickstart-register-app.md) na integração de [aplicações com o Diretório Ativo Azure][aad_integrate]. Se registar a sua candidatura como Pedido Nativo, pode especificar qualquer URI válido para o **Redirect URI**. Não precisa de ser um verdadeiro ponto final.

Depois de ter registado o seu pedido, verá o ID da inscrição:

![Registe a sua aplicação de Lote com a Azure AD](./media/batch-aad-auth/app-registration-data-plane.png)

Para obter mais informações sobre o registo de uma aplicação com a AD Azure, consulte Cenários de [Autenticação para AD Azure](../active-directory/develop/authentication-scenarios.md).

## <a name="get-the-tenant-id-for-your-active-directory"></a>Obtenha a identificação do inquilino para o seu Diretório Ativo

O ID do inquilino identifica o inquilino da AD Azure que presta serviços de autenticação à sua aplicação. Para obter a identificação do inquilino, siga estes passos:

1. No portal Azure, selecione o seu Diretório Ativo.
1. Selecione **Propriedades**.
1. Copiar o valor GUID fornecido para o **ID do Diretório**. Este valor também é chamado de identificação do inquilino.

![Copiar o ID do diretório](./media/batch-aad-auth/aad-directory-id.png)

## <a name="use-integrated-authentication"></a>Utilizar autenticação integrada

Para autenticar com autenticação integrada, é necessário conceder permissões de pedido para ligar à API do serviço Delote. Este passo permite que a sua aplicação autentique chamadas para o serviço de lote API com AD Azure.

Depois de ter registado a sua candidatura, siga estes passos no portal Azure para lhe conceder acesso ao serviço Batch:

1. No painel de navegação à esquerda do portal Azure, escolha **Todos os serviços.** Selecione **Registos de Aplicações**.
1. Procure o nome da sua aplicação na lista de registos de aplicações:

    ![Procure o seu nome de candidatura](./media/batch-aad-auth/search-app-registration.png)

1. Selecione a aplicação e selecione **permissões API**.
1. Na secção de **permissões DaPI,** selecione **Adicionar uma permissão**.
1. Em **Selecionar uma API,** procure a API do lote. Procure para cada uma destas cadeias até encontrar a API:
    1. **Lote Microsoft Azure**
    1. **ddbf3205-c6bd-46ae-8127-60eb93363864** é o ID para a API do Batch.
1. Assim que encontrar o Lote API, selecione-o e **selecione Selecione**.
1. Em **'Selecionar permissões',** selecione a caixa de verificação ao lado do **Access Azure Batch Service** e, em seguida, selecione Adicionar **permissões**.

A secção de **permissões DaPI** mostra agora que a sua aplicação Azure AD tem acesso tanto ao Microsoft Graph como à API do serviço De batch. As permissões são concedidas automaticamente ao Microsoft Graph quando regista a sua aplicação pela primeira vez com o Azure AD.

![Permissões da API de concessão](./media/batch-aad-auth/required-permissions-data-plane.png)

## <a name="use-a-service-principal"></a>Use um diretor de serviço

Para autenticar uma aplicação que funciona sem supervisão, utiliza um diretor de serviço. Depois de ter registado a sua candidatura, siga estes passos no portal Azure para configurar um diretor de serviço:

1. Solicite um segredo para o seu pedido.
1. Atribuir o controlo de acesso baseado em funções (RBAC) à sua aplicação.

### <a name="request-a-secret-for-your-application"></a>Solicite um segredo para o seu pedido

Quando a sua aplicação autentica com um diretor de serviço, envia tanto o ID da aplicação como um segredo para a AD Azure. Terá de criar e copiar a chave secreta para usar a partir do seu código.

Siga os passos abaixo no portal do Azure:

1. No painel de navegação à esquerda do portal Azure, escolha **Todos os serviços.** Selecione **Registos de Aplicações**.
1. Selecione a sua aplicação na lista de registos de aplicações.
1. Selecione a aplicação e, em seguida, selecione **Certificados & segredos**. Na secção **de segredos do Cliente,** selecione **novo segredo de cliente.**
1. Para criar um segredo, insira uma descrição para o segredo. Em seguida, selecione uma expiração para o segredo de um ano, dois anos, ou nenhuma expiração..
1. Selecione **Adicionar** para criar e exibir o segredo. Copie o valor secreto para um lugar seguro, já que não poderá aceder novamente depois de deixar a página.

    ![Criar uma chave secreta](./media/batch-aad-auth/secret-key.png)

### <a name="assign-rbac-to-your-application"></a>Atribuir RBAC à sua candidatura

Para autenticar com um diretor de serviço, tem de atribuir rBAC à sua aplicação. Siga estes passos.

1. No portal Azure, navegue para a conta Lote utilizada pela sua aplicação.
1. Na secção **Definições** da conta 'Lote', selecione **Controlo de Acesso (IAM)**.
1. Selecione o separador de **tarefas role.**
1. Selecione **Adicionar atribuição de função**.
1. A partir do **role** drop-down, escolha o papel *de Contribuinte* ou *Leitor* para a sua aplicação. Para obter mais informações sobre estas funções, consulte [Iniciar-se com o Controlo de Acesso baseado em Funções no portal Azure](../role-based-access-control/overview.md).
1. No campo **Select,** introduza o nome da sua aplicação. Selecione a sua aplicação na lista e, em seguida, selecione **Guardar**.

A sua aplicação deve agora aparecer nas definições de controlo de acesso com uma função RBAC atribuída.

![Atribuir uma função RBAC à sua aplicação](./media/batch-aad-auth/app-rbac-role.png)

### <a name="assign-a-custom-role"></a>Atribuir uma função personalizada

Um papel personalizado concede permissão granular a um utilizador para a apresentação de empregos, tarefas e muito mais. Isto fornece a capacidade de impedir que os utilizadores realizem operações que afetam os custos, tais como a criação de piscinas ou a modificação de nós.

Pode utilizar uma função personalizada para conceder permissões a um utilizador, grupo ou diretor de serviço da Azure AD para as seguintes operações rBAC:

- Microsoft.Batch/batchAccounts/pools/write
- Microsoft.Batch/batchAccounts/pools/delete
- Microsoft.Batch/batchAccounts/pools/read
- Microsoft.Batch/batchAccounts/jobSchedules/write
- Microsoft.Batch/batchAccounts/jobSchedules/delete
- Microsoft.Batch/batchAccounts/jobSchedules/read
- Microsoft.Batch/batchAccounts/jobs/write
- Microsoft.Batch/batchAccounts/jobs/delete
- Microsoft.Batch/batchAccounts/jobs/read
- Microsoft.Batch/batchAccounts/certificates/write
- Microsoft.Batch/batchAccounts/certificates/delete
- Microsoft.Batch/batchAccounts/certificates/read
- Microsoft.Batch/batchAccounts/read (para qualquer operação de leitura)
- Microsoft.Batch/batchAccounts/listKeys/action (para qualquer operação)

As funções personalizadas são para utilizadores autenticados pela Azure AD, e não para as credenciais de conta Batch (chave partilhada). Note que as credenciais da conta Lote dão permissão total à conta Do Lote. Note também que os trabalhos que utilizam a autopool requerem permissões ao nível da piscina.

Aqui está um exemplo de uma definição de papel personalizado:

```json
{
 "properties":{
    "roleName":"Azure Batch Custom Job Submitter",
    "type":"CustomRole",
    "description":"Allows a user to submit jobs to Azure Batch but not manage pools",
    "assignableScopes":[
      "/subscriptions/88888888-8888-8888-8888-888888888888"
    ],
    "permissions":[
      {
        "actions":[
          "Microsoft.Batch/*/read",
          "Microsoft.Authorization/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Support/*",
          "Microsoft.Insights/alertRules/*"
        ],
        "notActions":[

        ],
        "dataActions":[
          "Microsoft.Batch/batchAccounts/jobs/*",
          "Microsoft.Batch/batchAccounts/jobSchedules/*"
        ],
        "notDataActions":[

        ]
      }
    ]
  }
}
```

Para obter informações mais gerais sobre a criação de um papel personalizado, consulte [papéis personalizados para os recursos Do Azure.](../role-based-access-control/custom-roles.md)

### <a name="get-the-tenant-id-for-your-azure-active-directory"></a>Obtenha a identificação do inquilino para o seu Diretório Ativo Azure

O ID do inquilino identifica o inquilino da AD Azure que presta serviços de autenticação à sua aplicação. Para obter a identificação do inquilino, siga estes passos:

1. No portal Azure, selecione o seu Diretório Ativo.
1. Selecione **Propriedades**.
1. Copiar o valor GUID fornecido para o **ID do Diretório**. Este valor também é chamado de identificação do inquilino.

![Copiar o ID do diretório](./media/batch-aad-auth/aad-directory-id.png)

## <a name="code-examples"></a>Exemplos de código

Os exemplos de código nesta secção mostram como autenticar com a AD Azure utilizando a autenticação integrada e com um diretor de serviço. A maioria destes exemplos de código usam .NET, mas os conceitos são semelhantes para outras línguas.

> [!NOTE]
> Um símbolo de autenticação da AD Azure expira após uma hora. Ao utilizar um objeto **BatchClient** de longa duração, recomendamos que recupere um símbolo da ADAL em cada pedido para garantir que tem sempre um token válido.
>
>
> Para o conseguir em .NET, escreva um método que recupere o símbolo da AD Azure e passe esse método a um objeto **BatchTokenCredentials** como delegado. O método delegado é solicitado em todos os pedidos ao serviço Batch para garantir que um token válido é fornecido. Por padrão, os caches ADAL tokens, por isso um novo símbolo só é recuperado da AD Azure quando necessário. Para obter mais informações sobre fichas em Azure AD, consulte Cenários de [Autenticação para AD Azure][aad_auth_scenarios].
>
>

### <a name="code-example-using-azure-ad-integrated-authentication-with-batch-net"></a>Exemplo de código: Utilização da autenticação integrada da AD Azure com lote .NET

Para autenticar com autenticação integrada do Batch .NET, consulte o pacote [Azure Batch .NET](https://www.nuget.org/packages/Microsoft.Azure.Batch/) e o pacote [ADAL.](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/)

Inclua `using` as seguintes declarações no seu código:

```csharp
using Microsoft.Azure.Batch;
using Microsoft.Azure.Batch.Auth;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Consulte o ponto final do Azure AD no seu código, incluindo a identificação do inquilino. Para recuperar a identificação do inquilino, siga os passos delineados no [Get the Tenant ID para o seu Diretório Ativo Azure:](#get-the-tenant-id-for-your-active-directory)

```csharp
private const string AuthorityUri = "https://login.microsoftonline.com/<tenant-id>";
```

Referência ao ponto final do recurso do serviço lote:

```csharp
private const string BatchResourceUri = "https://batch.core.windows.net/";
```

Consulte a sua conta Lote:

```csharp
private const string BatchAccountUrl = "https://myaccount.mylocation.batch.azure.com";
```

Especifique o ID da aplicação (ID do cliente) para a sua aplicação. O ID da aplicação está disponível a partir do registo da sua aplicação no portal Azure:

```csharp
private const string ClientId = "<application-id>";
```

Copie também o URI redirecionado que especificou, caso tenha registado a sua candidatura como Pedido Nativo. O URI redirecionado especificado no seu código deve corresponder ao URI redireccional que forneceu quando registou a aplicação:

```csharp
private const string RedirectUri = "http://mybatchdatasample";
```

Escreva um método de chamada para adquirir o símbolo de autenticação da Azure AD. O método de chamada **GetAuthenticationTokenAsync** mostrado aqui chama a ADAL para autenticar um utilizador que esteja a interagir com a aplicação. O método **AcquireTokenAsync** fornecido pela ADAL solicita ao utilizador as suas credenciais, e a aplicação prossegue assim que o utilizador as fornece (a menos que já tenha credenciais em cache):

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

Construa um objeto **BatchTokenCredentials** que tome o delegado como parâmetro. Use essas credenciais para abrir um objeto **BatchClient.** Pode utilizar esse objeto **BatchClient** para operações subsequentes contra o serviço Batch:

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

### <a name="code-example-using-an-azure-ad-service-principal-with-batch-net"></a>Exemplo de código: Utilização de um diretor de serviço Azure AD com Lote .NET

Para autenticar com um diretor de serviço do Batch .NET, consulte o pacote [Azure Batch .NET](https://www.nuget.org/packages/Azure.Batch/) e o pacote [ADAL.](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/)

Inclua `using` as seguintes declarações no seu código:

```csharp
using Microsoft.Azure.Batch;
using Microsoft.Azure.Batch.Auth;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Consulte o ponto final do Azure AD no seu código, incluindo a identificação do inquilino. Ao utilizar um diretor de serviço, deve fornecer um ponto final específico do inquilino. Para recuperar a identificação do inquilino, siga os passos delineados no [Get the Tenant ID para o seu Diretório Ativo Azure:](#get-the-tenant-id-for-your-active-directory)

```csharp
private const string AuthorityUri = "https://login.microsoftonline.com/<tenant-id>";
```

Referência ao ponto final do recurso do serviço lote:

```csharp
private const string BatchResourceUri = "https://batch.core.windows.net/";
```

Consulte a sua conta Lote:

```csharp
private const string BatchAccountUrl = "https://myaccount.mylocation.batch.azure.com";
```

Especifique o ID da aplicação (ID do cliente) para a sua aplicação. O ID da aplicação está disponível a partir do registo da sua aplicação no portal Azure:

```csharp
private const string ClientId = "<application-id>";
```

Especifique a chave secreta que copiou do portal Azure:

```csharp
private const string ClientKey = "<secret-key>";
```

Escreva um método de chamada para adquirir o símbolo de autenticação da Azure AD. O método de chamada **GetAuthenticationTokenAsync** mostrado aqui chama ADAL para autenticação sem supervisão:

```csharp
public static async Task<string> GetAuthenticationTokenAsync()
{
    AuthenticationContext authContext = new AuthenticationContext(AuthorityUri);
    AuthenticationResult authResult = await authContext.AcquireTokenAsync(BatchResourceUri, new ClientCredential(ClientId, ClientKey));

    return authResult.AccessToken;
}
```

Construa um objeto **BatchTokenCredentials** que tome o delegado como parâmetro. Use essas credenciais para abrir um objeto **BatchClient.** Em seguida, utilize o objeto **BatchClient** para operações subsequentes contra o serviço Batch:

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

### <a name="code-example-using-an-azure-ad-service-principal-with-batch-python"></a>Exemplo de código: Utilização de um diretor de serviço azure AD com Lote Python

Para autenticar com um diretor de serviço do Batch Python, instale e refira os módulos [azul-lote](https://pypi.org/project/azure-batch/) e [azul-comum.](https://pypi.org/project/azure-common/)

```python
from azure.batch import BatchServiceClient
from azure.common.credentials import ServicePrincipalCredentials
```

Ao utilizar um diretor de serviço, deve fornecer a identificação do inquilino. Para recuperar a identificação do inquilino, siga os passos delineados no [Get the Tenant ID para o seu Diretório Ativo Azure:](#get-the-tenant-id-for-your-active-directory)

```python
TENANT_ID = "<tenant-id>"
```

Referência ao ponto final do recurso do serviço lote:

```python
RESOURCE = "https://batch.core.windows.net/"
```

Consulte a sua conta Lote:

```python
BATCH_ACCOUNT_URL = "https://myaccount.mylocation.batch.azure.com"
```

Especifique o ID da aplicação (ID do cliente) para a sua aplicação. O ID da aplicação está disponível a partir do registo da sua aplicação no portal Azure:

```python
CLIENT_ID = "<application-id>"
```

Especifique a chave secreta que copiou do portal Azure:

```python
SECRET = "<secret-key>"
```

Criar um objeto **ServicePrincipalCredenciais:**

```python
credentials = ServicePrincipalCredentials(
    client_id=CLIENT_ID,
    secret=SECRET,
    tenant=TENANT_ID,
    resource=RESOURCE
)
```

Utilize as credenciais principais do serviço para abrir um objeto **BatchServiceClient.** Em seguida, utilize o objeto **BatchServiceClient** para operações subsequentes contra o serviço Batch.

```python
    batch_client = BatchServiceClient(
    credentials,
    base_url=BATCH_ACCOUNT_URL
)
```

## <a name="next-steps"></a>Passos seguintes

- Para saber mais sobre o Azure AD, consulte a Documentação de [Diretório Ativo Azure.](https://docs.microsoft.com/azure/active-directory/) Exemplos aprofundados que mostram como utilizar o ADAL estão disponíveis na biblioteca De amostras de [código azul.](https://azure.microsoft.com/resources/samples/?service=active-directory)

- Para saber mais sobre os principais de serviços, consulte [os objetos principais de aplicação e serviço no Diretório Ativo azure](../active-directory/develop/app-objects-and-service-principals.md). Para criar um serviço principal utilizando o portal Azure, consulte [o portal Use para criar aplicação de Diretório Ativo e diretor](../active-directory/develop/howto-create-service-principal-portal.md)de serviço que possa aceder a recursos. Também pode criar um diretor de serviço com powerShell ou Azure CLI.

- Para autenticar aplicações de Gestão de Lotes utilizando a AD Azure, consulte [soluções authenticate de gestão de lotes com Diretório Ativo](batch-aad-auth-management.md).

- Para um exemplo python de como criar um cliente Batch autenticado usando um token Azure AD, consulte a imagem personalizada do [lote azure de implantação com uma](https://github.com/azurebigcompute/recipes/blob/master/Azure%20Batch/CustomImages/CustomImagePython.md) amostra de Script Python.

[aad_about]:../active-directory/fundamentals/active-directory-whatis.md "O que é o Azure Ative Directory?"
[aad_adal]: ../active-directory/active-directory-authentication-libraries.md
[aad_auth_scenarios]: ../active-directory/active-directory-authentication-scenarios.md "Cenários de autenticação para AD Azure"
[aad_integrate]: ../active-directory/active-directory-integrating-applications.md "Integração de Aplicações com Diretório Ativo Azure"
[azure_portal]: https://portal.azure.com
