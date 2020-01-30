---
title: Usar Azure Active Directory para autenticar soluções de serviço do lote do Azure | Microsoft Docs
description: O lote dá suporte ao Azure AD para autenticação do serviço de lote.
services: batch
documentationcenter: .net
author: ju-shim
manager: gwallace
editor: ''
tags: ''
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 01/28/2020
ms.author: jushiman
ms.openlocfilehash: ce3582539d6130e13ef205806d780164ba70c4fe
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76842542"
---
# <a name="authenticate-batch-service-solutions-with-active-directory"></a>Autenticar soluções de serviço de lote com o Active Directory

O lote do Azure dá suporte à autenticação com o [Azure Active Directory][aad_about] (Azure AD). O Azure AD é o serviço de gerenciamento de identidade e diretório multilocatário baseado em nuvem da Microsoft. O próprio Azure usa o Azure AD para autenticar seus clientes, administradores de serviço e usuários organizacionais.

Ao usar a autenticação do Azure AD com o lote do Azure, você pode autenticar de uma destas duas maneiras:

- Usando a **autenticação integrada** para autenticar um usuário que está interagindo com o aplicativo. Um aplicativo que usa a autenticação integrada reúne as credenciais de um usuário e usa essas credenciais para autenticar o acesso aos recursos do lote.
- Usando uma **entidade de serviço** para autenticar um aplicativo autônomo. Uma entidade de serviço define a política e as permissões para um aplicativo a fim de representar o aplicativo ao acessar recursos em tempo de execução.

Para saber mais sobre o Azure AD, consulte a [documentação do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/).

## <a name="endpoints-for-authentication"></a>Pontos de extremidade para autenticação

Para autenticar aplicativos do lote com o Azure AD, você precisa incluir alguns pontos de extremidade conhecidos em seu código.

### <a name="azure-ad-endpoint"></a>Ponto de extremidade do Azure AD

O ponto de extremidade de autoridade de base do Azure AD é:

`https://login.microsoftonline.com/`

Para autenticar com o Azure AD, use esse ponto de extremidade junto com a ID do locatário (ID do diretório). O ID de inquilino identifica o inquilino do Azure AD para utilizar para autenticação. Para recuperar a ID do locatário, siga as etapas descritas em [obter a ID do locatário para seu Azure Active Directory](#get-the-tenant-id-for-your-active-directory):

`https://login.microsoftonline.com/<tenant-id>`

> [!NOTE] 
> O ponto de extremidade específico do locatário é necessário quando você autentica usando uma entidade de serviço. 
> 
> O ponto de extremidade específico do locatário é opcional quando você autentica usando a autenticação integrada, mas recomendado. No entanto, você também pode usar o ponto de extremidade comum do Azure AD. O ponto de extremidade comum fornece uma interface de coleta de credenciais genérica quando um locatário específico não é fornecido. O ponto de extremidade comum é `https://login.microsoftonline.com/common`.
>
>

Para obter mais informações sobre pontos de extremidade do Azure AD, consulte [cenários de autenticação do Azure ad][aad_auth_scenarios].

### <a name="batch-resource-endpoint"></a>Ponto de extremidade de recurso do lote

Use o **ponto de extremidade de recurso do lote do Azure** para adquirir um token para autenticar solicitações para o serviço de lote:

`https://batch.core.windows.net/`

## <a name="register-your-application-with-a-tenant"></a>Registrar seu aplicativo com um locatário

A primeira etapa para usar o Azure AD para autenticar está registrando seu aplicativo em um locatário do Azure AD. Registar a aplicação permite-lhe chamar do Azure [Active Directory Authentication Library][aad_adal] (ADAL) a partir do código. A ADAL fornece uma API para autenticar com o Azure AD a partir da sua aplicação. O registro do aplicativo é necessário se você planeja usar a autenticação integrada ou uma entidade de serviço.

Quando registar a sua aplicação, fornecer informações sobre a sua aplicação para o Azure AD. Em seguida, o Azure AD fornece uma ID do aplicativo (também chamada de *ID do cliente*) que você usa para associar seu aplicativo ao Azure AD em tempo de execução. Para saber mais sobre a ID do aplicativo, consulte [objetos de aplicativo e entidade de serviço no Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md).

Para registrar seu aplicativo do lote, siga as etapas na seção [adicionando um aplicativo](../active-directory/develop/quickstart-register-app.md) em [integrando aplicativos com o Azure Active Directory][aad_integrate]. Se você registrar seu aplicativo como um aplicativo nativo, poderá especificar qualquer URI válido para o **URI de redirecionamento**. Ele não precisa ser um ponto de extremidade real.

Depois de registrar seu aplicativo, você verá a ID do aplicativo:

![Registrar seu aplicativo do lote com o Azure AD](./media/batch-aad-auth/app-registration-data-plane.png)

Para obter mais informações sobre como registrar um aplicativo com o Azure AD, consulte [cenários de autenticação do Azure ad](../active-directory/develop/authentication-scenarios.md).

## <a name="get-the-tenant-id-for-your-active-directory"></a>Obter a ID do locatário para seu Active Directory

A ID de locatário identifica o locatário do Azure AD que fornece serviços de autenticação para seu aplicativo. Para obter o ID de inquilino, siga estes passos:

1. No portal do Azure, selecione o seu Active Directory.
1. Selecione **Propriedades**.
1. Copie o valor GUID fornecido para o **ID de diretório**. Este valor também é chamado de ID do inquilino.

![Copiar a ID do diretório](./media/batch-aad-auth/aad-directory-id.png)

## <a name="use-integrated-authentication"></a>Usar autenticação integrada

Para autenticar com a autenticação integrada, você precisa conceder permissões de aplicativo para se conectar à API do serviço de lote. Esta etapa permite que seu aplicativo autentique chamadas para a API de serviço do lote com o Azure AD.

Depois de registrar seu aplicativo, siga estas etapas na portal do Azure para conceder acesso ao serviço de lote:

1. No painel de navegação à esquerda da portal do Azure, escolha **todos os serviços**. Selecione **registros do aplicativo**.
1. Procure o nome do seu aplicativo na lista de registros do aplicativo:

    ![Pesquisar o nome do aplicativo](./media/batch-aad-auth/search-app-registration.png)

1. Selecione o aplicativo e selecione **permissões de API**.
1. Na seção **permissões de API** , selecione **Adicionar uma permissão**.
1. Em **selecionar uma API**, procure a API do lote. Procure para cada uma destas cadeias até encontrar a API:
    1. **Lote do Microsoft Azure**
    1. **ddbf3205-c6bd-46ae-8127-60eb93363864** é o ID para a API do Batch.
1. Depois de encontrar a API do lote, selecione-a e selecione **selecionar**.
1. Em **selecionar permissões**, marque a caixa de seleção ao lado de **acessar serviço do lote do Azure** e, em seguida, selecione **adicionar permissões**.

A seção **permissões de API** agora mostra que o aplicativo do Azure ad tem acesso ao Microsoft Graph e à API do serviço de lote. As permissões são concedidas para Microsoft Graph automaticamente quando você registra seu aplicativo pela primeira vez com o Azure AD.

![Conceder permissões de API](./media/batch-aad-auth/required-permissions-data-plane.png)

## <a name="use-a-service-principal"></a>Usar uma entidade de serviço

Para autenticar um aplicativo que é executado de forma autônoma, você usa uma entidade de serviço. Depois de registrar seu aplicativo, siga estas etapas na portal do Azure para configurar uma entidade de serviço:

1. Solicite um segredo para seu aplicativo.
1. Atribua o RBAC (controle de acesso baseado em função) ao seu aplicativo.

### <a name="request-a-secret-for-your-application"></a>Solicitar um segredo para seu aplicativo

Quando seu aplicativo é autenticado com uma entidade de serviço, ele envia a ID do aplicativo e um segredo para o Azure AD. Você precisará criar e copiar a chave secreta para usar do seu código.

Siga estes passos no portal Azure:

1. No painel de navegação à esquerda da portal do Azure, escolha **todos os serviços**. Selecione **registros do aplicativo**.
1. Selecione seu aplicativo na lista de registros do aplicativo.
1. Selecione o aplicativo e, em seguida, selecione **certificados & segredos**. Na seção **segredos do cliente** , selecione **novo segredo do cliente**.
1. Para criar um segredo, insira uma descrição para o segredo. Em seguida, selecione uma expiração para o segredo de um ano, dois anos ou sem expiração..
1. Selecione **Adicionar** para criar e exibir o segredo. Copie o valor secreto para um local seguro, pois você não poderá acessá-lo novamente depois de sair da página.

    ![Criar uma chave secreta](./media/batch-aad-auth/secret-key.png)

### <a name="assign-rbac-to-your-application"></a>Atribuir RBAC ao seu aplicativo

Para autenticar com uma entidade de serviço, você precisa atribuir o RBAC ao seu aplicativo. Siga estes passos.

1. Na portal do Azure, navegue até a conta do lote usada pelo seu aplicativo.
1. Na seção **configurações** da conta do lote, selecione **controle de acesso (iam)** .
1. Selecione a guia **atribuições de função** .
1. Selecione **adicionar atribuição de função**.
1. Na lista suspensa **função** , escolha a função *colaborador* ou *leitor* para seu aplicativo. Para obter mais informações sobre essas funções, consulte Introdução [ao controle de acesso baseado em função no portal do Azure](../role-based-access-control/overview.md).  
1. No campo **selecionar** , insira o nome do seu aplicativo. Selecione o aplicativo na lista e, em seguida, selecione **salvar**.

Seu aplicativo agora deve aparecer em suas configurações de controle de acesso com uma função RBAC atribuída.

![Atribuir uma função RBAC ao seu aplicativo](./media/batch-aad-auth/app-rbac-role.png)

### <a name="assign-a-custom-role"></a>Atribuir uma função personalizada

Um papel personalizado concede permissão granular a um utilizador para a apresentação de empregos, tarefas e muito mais. Isto fornece a capacidade de impedir que os utilizadores realizem operações que afetam os custos, tais como a criação de piscinas ou a modificação de nós.

Pode utilizar uma função personalizada para conceder permissões a um utilizador, grupo ou diretor de serviço da Azure AD para as seguintes operações rBAC:

- Microsoft.Batch/batchAccounts/pools/write
- Microsoft.Batch/batchAccounts/pools/delete
- Microsoft.Batch/batchAccounts/pools/read
- Microsoft. batch/batchAccounts/jobSchedules/Write
- Microsoft. batch/batchAccounts/jobSchedules/Delete
- Microsoft. batch/batchAccounts/jobSchedules/Read
- Microsoft. batch/batchAccounts/Jobs/Write
- Microsoft. batch/batchAccounts/trabalhos/excluir
- Microsoft. batch/batchAccounts/trabalhos/ler
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

### <a name="get-the-tenant-id-for-your-azure-active-directory"></a>Obter o ID de inquilino do Azure Active Directory

A ID de locatário identifica o locatário do Azure AD que fornece serviços de autenticação para seu aplicativo. Para obter o ID de inquilino, siga estes passos:

1. No portal do Azure, selecione o seu Active Directory.
1. Selecione **Propriedades**.
1. Copie o valor GUID fornecido para o **ID de diretório**. Este valor também é chamado de ID do inquilino.

![Copiar a ID do diretório](./media/batch-aad-auth/aad-directory-id.png)

## <a name="code-examples"></a>Exemplos de código

Os exemplos de código nesta seção mostram como autenticar com o Azure AD usando a autenticação integrada e com uma entidade de serviço. A maioria desses exemplos de código usa o .NET, mas os conceitos são semelhantes para outras linguagens.

> [!NOTE]
> Um token de autenticação do Azure AD expira após uma hora. Ao usar um objeto **BatchClient** de vida útil longa, é recomendável recuperar um token do Adal em cada solicitação para garantir que você sempre tenha um token válido. 
>
>
> Para conseguir isso no .NET, escreva um método que recupere o token do Azure AD e passe esse método para um objeto **BatchTokenCredentials** como um delegado. O método delegado é chamado em cada solicitação para o serviço de lote para garantir que um token válido seja fornecido. Por padrão, a ADAL armazena em cache tokens, portanto, um novo token é recuperado do Azure AD somente quando necessário. Para obter mais informações sobre tokens no Azure AD, consulte [cenários de autenticação do Azure ad][aad_auth_scenarios].
>
>

### <a name="code-example-using-azure-ad-integrated-authentication-with-batch-net"></a>Exemplo de código: usando a autenticação integrada do Azure AD com o .NET do lote

Para autenticar com a autenticação integrada do .NET do lote, referencie o pacote [.net do lote do Azure](https://www.nuget.org/packages/Microsoft.Azure.Batch/) e o pacote [Adal](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) .

Inclua as seguintes instruções de `using` em seu código:

```csharp
using Microsoft.Azure.Batch;
using Microsoft.Azure.Batch.Auth;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Referencie o ponto de extremidade do Azure AD em seu código, incluindo a ID do locatário. Para recuperar a ID do locatário, siga as etapas descritas em [obter a ID do locatário para seu Azure Active Directory](#get-the-tenant-id-for-your-active-directory):

```csharp
private const string AuthorityUri = "https://login.microsoftonline.com/<tenant-id>";
```

Referencie o ponto de extremidade de recurso do serviço de lote:

```csharp
private const string BatchResourceUri = "https://batch.core.windows.net/";
```

Faça referência à sua conta do lote:

```csharp
private const string BatchAccountUrl = "https://myaccount.mylocation.batch.azure.com";
```

Especifique a ID do aplicativo (ID do cliente) para seu aplicativo. A ID do aplicativo está disponível no registro do aplicativo no portal do Azure:

```csharp
private const string ClientId = "<application-id>";
```

Copie também o URI de redirecionamento que você especificou, se você registrou seu aplicativo como um aplicativo nativo. O URI de redirecionamento especificado no seu código deve corresponder ao URI de redirecionamento que você forneceu quando registrou o aplicativo:

```csharp
private const string RedirectUri = "http://mybatchdatasample";
```

Escreva um método de retorno de chamada para adquirir o token de autenticação do Azure AD. O método de retorno de chamada **GetAuthenticationTokenAsync** mostrado aqui chama o Adal para autenticar um usuário que está interagindo com o aplicativo. O método **AcquireTokenAsync** fornecido pela Adal solicita ao usuário suas credenciais e o aplicativo continua quando o usuário as fornece (a menos que já tenha credenciais em cache):

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

Construa um objeto **BatchTokenCredentials** que usa o delegado como um parâmetro. Use essas credenciais para abrir um objeto **BatchClient** . Você pode usar esse objeto **BatchClient** para operações subsequentes no serviço de lote:

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

### <a name="code-example-using-an-azure-ad-service-principal-with-batch-net"></a>Exemplo de código: usando uma entidade de serviço do Azure AD com o .NET do lote

Para autenticar com uma entidade de serviço do .NET do lote, referencie o pacote [.net do lote do Azure](https://www.nuget.org/packages/Azure.Batch/) e o pacote [Adal](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) .

Inclua as seguintes instruções de `using` em seu código:

```csharp
using Microsoft.Azure.Batch;
using Microsoft.Azure.Batch.Auth;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Referencie o ponto de extremidade do Azure AD em seu código, incluindo a ID do locatário. Ao usar uma entidade de serviço, você deve fornecer um ponto de extremidade específico do locatário. Para recuperar a ID do locatário, siga as etapas descritas em [obter a ID do locatário para seu Azure Active Directory](#get-the-tenant-id-for-your-active-directory):

```csharp
private const string AuthorityUri = "https://login.microsoftonline.com/<tenant-id>";
```

Referencie o ponto de extremidade de recurso do serviço de lote:  

```csharp
private const string BatchResourceUri = "https://batch.core.windows.net/";
```

Faça referência à sua conta do lote:

```csharp
private const string BatchAccountUrl = "https://myaccount.mylocation.batch.azure.com";
```

Especifique a ID do aplicativo (ID do cliente) para seu aplicativo. A ID do aplicativo está disponível no registro do aplicativo no portal do Azure:

```csharp
private const string ClientId = "<application-id>";
```

Especifique a chave secreta que você copiou do portal do Azure:

```csharp
private const string ClientKey = "<secret-key>";
```

Escreva um método de retorno de chamada para adquirir o token de autenticação do Azure AD. O método de retorno de chamada **GetAuthenticationTokenAsync** mostrado aqui chama Adal para autenticação autônoma:

```csharp
public static async Task<string> GetAuthenticationTokenAsync()
{
    AuthenticationContext authContext = new AuthenticationContext(AuthorityUri);
    AuthenticationResult authResult = await authContext.AcquireTokenAsync(BatchResourceUri, new ClientCredential(ClientId, ClientKey));

    return authResult.AccessToken;
}
```

Construa um objeto **BatchTokenCredentials** que usa o delegado como um parâmetro. Use essas credenciais para abrir um objeto **BatchClient** . Em seguida, use esse objeto **BatchClient** para operações subsequentes no serviço de lote:

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

### <a name="code-example-using-an-azure-ad-service-principal-with-batch-python"></a>Exemplo de código: usando uma entidade de serviço do Azure AD com Python do lote

Para autenticar com uma entidade de serviço do Python do lote, instale e referencie os módulos [Azure-batch](https://pypi.org/project/azure-batch/) e [Azure-Common](https://pypi.org/project/azure-common/) .

```python
from azure.batch import BatchServiceClient
from azure.common.credentials import ServicePrincipalCredentials
```

Ao usar uma entidade de serviço, você deve fornecer a ID do locatário. Para recuperar a ID do locatário, siga as etapas descritas em [obter a ID do locatário para seu Azure Active Directory](#get-the-tenant-id-for-your-active-directory):

```python
TENANT_ID = "<tenant-id>"
```

Referencie o ponto de extremidade de recurso do serviço de lote:  

```python
RESOURCE = "https://batch.core.windows.net/"
```

Faça referência à sua conta do lote:

```python
BATCH_ACCOUNT_URL = "https://myaccount.mylocation.batch.azure.com"
```

Especifique a ID do aplicativo (ID do cliente) para seu aplicativo. A ID do aplicativo está disponível no registro do aplicativo no portal do Azure:

```python
CLIENT_ID = "<application-id>"
```

Especifique a chave secreta que você copiou do portal do Azure:

```python
SECRET = "<secret-key>"
```

Criar um objeto **ServicePrincipalCredentials** :

```python
credentials = ServicePrincipalCredentials(
    client_id=CLIENT_ID,
    secret=SECRET,
    tenant=TENANT_ID,
    resource=RESOURCE
)
```

Use as credenciais da entidade de serviço para abrir um objeto **BatchServiceClient** . Em seguida, use esse objeto **BatchServiceClient** para operações subsequentes no serviço de lote.

```python
    batch_client = BatchServiceClient(
    credentials,
    base_url=BATCH_ACCOUNT_URL
)
```

## <a name="next-steps"></a>Passos seguintes

- Para saber mais sobre o Azure AD, consulte a [documentação do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/). Exemplos detalhados que mostram como usar a ADAL estão disponíveis na biblioteca de [exemplos de código do Azure](https://azure.microsoft.com/resources/samples/?service=active-directory) .

- Para saber mais sobre entidades de serviço, consulte [objetos de aplicativo e entidade de serviço no Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md). Para criar uma entidade de serviço usando o portal do Azure, consulte [usar o portal para criar Active Directory aplicativo e entidade de serviço que possa acessar recursos](../active-directory/develop/howto-create-service-principal-portal.md). Você também pode criar uma entidade de serviço com o PowerShell ou CLI do Azure.

- Para autenticar aplicativos de gerenciamento de lote usando o Azure AD, consulte [autenticar soluções de gerenciamento de lote com Active Directory](batch-aad-auth-management.md).

- Para obter um exemplo de Python de como criar um cliente do lote autenticado usando um token do Azure AD, consulte a exemplo [implantando a imagem personalizada do lote do Azure com um script Python](https://github.com/azurebigcompute/recipes/blob/master/Azure%20Batch/CustomImages/CustomImagePython.md) .

[aad_about]:../active-directory/fundamentals/active-directory-whatis.md "O que é Azure Active Directory?"
[aad_adal]: ../active-directory/active-directory-authentication-libraries.md
[aad_auth_scenarios]: ../active-directory/active-directory-authentication-scenarios.md "Cenários de autenticação do Azure AD"
[aad_integrate]: ../active-directory/active-directory-integrating-applications.md "Integrando aplicativos com o Azure Active Directory"
[azure_portal]: https://portal.azure.com
