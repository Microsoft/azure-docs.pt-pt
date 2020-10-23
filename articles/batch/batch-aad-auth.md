---
title: Autenticar serviços Azure Batch com Diretório Ativo Azure
description: O lote suporta o Azure AD para autenticação a partir do serviço Batch. Aprenda a autenticar de duas maneiras.
ms.topic: how-to
ms.date: 10/20/2020
ms.custom: has-adal-ref
ms.openlocfilehash: cb8306da4022ea1819e2da32a2f513c83bed309f
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92309378"
---
# <a name="authenticate-batch-service-solutions-with-active-directory"></a>Autenticar soluções de serviço batch com Diretório Ativo

O Azure Batch suporta a autenticação com [o Azure Ative Directory](../active-directory/fundamentals/active-directory-whatis.md) (Azure AD). Azure AD é o serviço de gestão de identidade e diretório multi-inquilino da Microsoft. O próprio Azure utiliza a Azure AD para autenticar os seus clientes, administradores de serviços e utilizadores organizacionais.

Ao utilizar a autenticação AZure AD com Azure Batch, pode autenticar de duas formas:

- Utilizando **a autenticação integrada** para autenticar um utilizador que interage com a aplicação. Uma aplicação que utiliza a autenticação integrada reúne as credenciais de um utilizador e utiliza essas credenciais para autenticar o acesso aos recursos do Batch.
- Utilizando um **principal de serviço** para autenticar uma aplicação sem supervisão. Um diretor de serviço define a política e permissões para um pedido de forma a representar a aplicação ao aceder aos recursos em tempo de execução.

Para saber mais sobre a Azure AD, consulte a [Documentação do Diretório Ativo Azure](../active-directory/index.yml).

## <a name="endpoints-for-authentication"></a>Pontos finais para autenticação

Para autenticar aplicações de Lote com Azure AD, tem de incluir alguns pontos finais bem conhecidos no seu código.

### <a name="azure-ad-endpoint"></a>Ponto final Azure AD

O ponto final da autoridade Azure AD base é:

`https://login.microsoftonline.com/`

Para autenticar com Azure AD, você usa este ponto final juntamente com o ID do inquilino (ID de diretório). A identificação do inquilino identifica o inquilino da Ad Azure para usar para autenticação. Para recuperar a ID do inquilino, siga os passos delineados em [Obter a ID do inquilino para o seu Diretório Ativo Azure:](#get-the-tenant-id-for-your-active-directory)

`https://login.microsoftonline.com/<tenant-id>`

> [!NOTE]
> O ponto final específico do inquilino é necessário quando autenticar usando um principal de serviço.
>
> O ponto final específico do arrendatário é opcional quando autentica usando a autenticação integrada, mas recomendado. No entanto, também pode utilizar o ponto final comum Azure AD. O ponto final comum fornece uma interface de recolha de credenciais genéricas quando um inquilino específico não é fornecido. O ponto final comum `https://login.microsoftonline.com/common` é.

Para obter mais informações sobre os pontos finais da Azure AD, consulte [autenticação vs. autorização]().. /active-directório/desenvolvimento/autenticação-vs-autorização.md).

### <a name="batch-resource-endpoint"></a>Ponto final de recursos de lote

Utilize o **ponto final de recurso Azure Batch** para adquirir um símbolo para autenticar pedidos ao serviço Batch:

`https://batch.core.windows.net/`

## <a name="register-your-application-with-a-tenant"></a>Registe o seu pedido junto de um inquilino

O primeiro passo para a utilização da Azure AD para autenticar é registar a sua candidatura num inquilino da AD Azure. Registar a sua aplicação permite-lhe ligar para a [Azure Ative Directory Authentication Library](../active-directory/azuread-dev/active-directory-authentication-libraries.md) (ADAL) a partir do seu código. A ADAL fornece uma API para autenticação com Azure AD a partir da sua aplicação. O registo da sua candidatura é obrigatório quer planeie utilizar a autenticação integrada ou um principal de serviço.

Quando regista a sua candidatura, fornece informações sobre a sua candidatura à Azure AD. A Azure AD fornece então um ID de aplicação (também chamado *de ID do cliente)* que utiliza para associar a sua aplicação à Azure AD no tempo de execução. Para saber mais sobre o ID da aplicação, consulte [os objetos principais de aplicação e serviço no Diretório Ativo Azure.](../active-directory/develop/app-objects-and-service-principals.md)

Para registar a sua aplicação Batch, siga os passos no Registo de uma secção **de aplicação** em [Quickstart: Registe uma aplicação com a plataforma de identidade Microsoft](../active-directory/develop/quickstart-register-app.md). Se registar a sua candidatura como Uma Aplicação Nativa, pode especificar qualquer URI válido para o **URI de redirecionamento.** Não precisa de ser um verdadeiro ponto final.

Depois de ter registado o seu pedido, verá o ID da aplicação:

![Screenshot do ID da aplicação mostrado no portal Azure.](./media/batch-aad-auth/app-registration-data-plane.png)

## <a name="get-the-tenant-id-for-your-active-directory"></a>Obtenha a iD do inquilino para o seu Diretório Ativo

O ID do inquilino identifica o inquilino da Azure AD que presta serviços de autenticação à sua aplicação. Para obter a iM do inquilino, siga estes passos:

1. No portal Azure, selecione o seu Diretório Ativo.
1. Selecione **Propriedades**.
1. Copiar o valor GUID previsto para o ID do **Diretório**. Este valor também é chamado de identificação do inquilino.

![Screenshot do ID do Diretório no portal Azure.](./media/batch-aad-auth/aad-directory-id.png)

## <a name="use-integrated-authentication"></a>Utilizar a autenticação integrada

Para autenticar com autenticação integrada, tem de conceder permissões de candidatura para se ligar à API do serviço Batch. Este passo permite que a sua aplicação autureça chamadas para o serviço Batch API com Azure AD.

Depois de registar a sua candidatura, siga estes passos no portal Azure para lhe dar acesso ao serviço Batch:

1. No portal Azure, escolha **Todos os serviços,** em seguida, selecione **Registos de Aplicações**.
1. Procure o nome da sua aplicação na lista de registos de aplicações.
1. Selecione a aplicação e selecione **permissões API**.
1. Na secção **permissões API,** selecione **Adicionar uma permissão**.
1. Em **Selecionar uma API,** procure a API do Lote. Procure para cada uma destas cadeias até encontrar a API:
    1. **Microsoft Azure Batch**
    1. **ddbf3205-c6bd-46ae-8127-60eb93363864** é o ID para a API do Batch.
1. Assim que encontrar a API do Lote, selecione-a e, em seguida, escolha **Selecione**.
1. Nas **permissões Select**, selecione a caixa de verificação ao lado **do Access Azure Batch Service** e, em seguida, selecione **Permissões de adicionar**.

A secção **de permissões API** mostra agora que a sua aplicação AD Azure tem acesso tanto ao Microsoft Graph como ao Serviço API do serviço Batch. As permissões são concedidas automaticamente ao Microsoft Graph quando regista a sua aplicação pela primeira vez com a Azure AD.

## <a name="use-a-service-principal"></a>Use um principal de serviço

Para autenticar uma aplicação que é executado sem vigilância, utilize um principal de serviço. Depois de registar a sua candidatura, siga estes passos no portal Azure para configurar um diretor de serviço:

1. Solicite um segredo para a sua candidatura.
1. Atribua o controlo de acesso baseado em funções (Azure RBAC) à sua aplicação.

### <a name="request-a-secret-for-your-application"></a>Solicite um segredo para a sua candidatura

Quando a sua aplicação autentica com um diretor de serviço, envia o ID da aplicação e um segredo para a Azure AD. Terá de criar e copiar a chave secreta para usar a partir do seu código.

Siga os passos abaixo no portal do Azure:

1. No portal Azure, escolha **Todos os serviços.** Selecione **Registos de Aplicações**.
1. Selecione a sua candidatura a partir da lista de registos de aplicações.
1. Selecione a aplicação e, em seguida, **selecione Certificados & segredos**. Na secção **de segredos** do Cliente, selecione **Novo segredo de cliente.**
1. Para criar um segredo, insira uma descrição para o segredo. Em seguida, selecione uma expiração para o segredo de um ano, dois anos, ou sem expiração.
1. **Selecione Adicionar** para criar e exibir o segredo. Copie o valor secreto para um local seguro, uma vez que não poderá aceder-lhe novamente depois de sair da página.

### <a name="assign-azure-rbac-to-your-application"></a>Atribua a Azure RBAC à sua aplicação

Para autenticar com um diretor de serviço, precisa de atribuir o Azure RBAC à sua aplicação. Siga estes passos:

1. No portal Azure, navegue para a conta Batch utilizada pela sua aplicação.
1. Na secção **Definições** da conta Lote, selecione **Access Control (IAM)**.
1. Selecione o **separador Funções.**
1. Selecione **Adicionar atribuição de função**.
1. A partir **do** drop-down Role, escolha a função *Contribuinte* ou *Leitor* para a sua aplicação. Para obter mais informações sobre estas funções, consulte [Começar com o controlo de acesso baseado em funções do Azure no portal Azure](../role-based-access-control/overview.md).
1. No campo **Seleção,** insira o nome da sua candidatura. Selecione a sua candidatura da lista e, em seguida, **selecione Guardar**.

A sua aplicação deve agora aparecer nas definições do controlo de acesso com uma função Azure atribuída.

![Atribua um papel Azure à sua candidatura](./media/batch-aad-auth/app-rbac-role.png)

### <a name="assign-a-custom-role"></a>Atribuir uma função personalizada

Uma função personalizada concede permissão granular a um utilizador para a apresentação de empregos, tarefas e muito mais. Isto proporciona a capacidade de impedir que os utilizadores realizem operações que afetem o custo, como criar piscinas ou modificar nós.

Pode utilizar uma função personalizada para conceder permissões a um utilizador, grupo ou principal de serviço da Azure para as seguintes operações do Azure RBAC:

- Microsoft.Batch/batchAcounts/pools/write
- Microsoft.Batch/batchAcounts/pools/delete
- Microsoft.Batch/batchAcounts/pools/read
- Microsoft.Batch/batchAcounts/jobSchedules/write
- Microsoft.Batch/batchAcounts/jobSchedules/delete
- Microsoft.Batch/batchAcounts/jobSchedules/read
- Microsoft.Batch/batchAcounts/jobs/write
- Microsoft.Batch/batchAcounts/jobs/delete
- Microsoft.Batch/batchAcounts/jobs/read
- Microsoft.Batch/batchAcounts/certificados/write
- Microsoft.Batch/batchAcounts/certificados/delete
- Microsoft.Batch/batchAcounts/certificados/read
- Microsoft.Batch/batchAcounts/read (para qualquer operação de leitura)
- Microsoft.Batch/batchAccounts/listKeys/action (para qualquer operação)

As funções personalizadas são para utilizadores autenticados pelo Azure AD, e não para as credenciais de conta Batch (chave partilhada). Note que as credenciais da conta Batch dão permissão total à conta Batch. Note também que os trabalhos que utilizam a autopool requerem permissões ao nível da piscina.

> [!NOTE]
> Determinadas atribuições de funções devem ser especificadas no campo ação, enquanto outras devem ser especificadas no campo DataAction. Para obter mais informações, consulte [as operações do fornecedor de recursos Azure.](../role-based-access-control/resource-provider-operations.md#microsoftbatch)

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

Para obter mais informações sobre a criação de um papel personalizado, consulte [os papéis personalizados da Azure.](../role-based-access-control/custom-roles.md)

### <a name="get-the-tenant-id-for-your-azure-active-directory"></a>Obtenha a iD do inquilino para o seu Diretório Ativo Azure

O ID do inquilino identifica o inquilino da Azure AD que presta serviços de autenticação à sua aplicação. Para obter a iM do inquilino, siga estes passos:

1. No portal Azure, selecione o seu Diretório Ativo.
1. Selecione **Propriedades**.
1. Copiar o valor GUID previsto para o ID do **Diretório**. Este valor também é chamado de identificação do inquilino.

![Copiar o iD do diretório](./media/batch-aad-auth/aad-directory-id.png)

## <a name="code-examples"></a>Exemplos de código

Os exemplos de código nesta secção mostram como autenticar com Azure AD utilizando a autenticação integrada e com um principal de serviço. A maioria destes exemplos de código usam .NET, mas os conceitos são semelhantes para outras línguas.

> [!NOTE]
> Um sinal de autenticação AD Azure expira após uma hora. Ao utilizar um objeto **BatchClient** de longa duração, recomendamos que recupere um símbolo da ADAL em cada pedido para garantir que tem sempre um token válido.
>
> Para o conseguir em .NET, escreva um método que recupere o símbolo do AZure AD e passe esse método para um objeto **BatchTokenCredentials** como delegado. O método de delegado é solicitado a todos os pedidos ao serviço Batch para garantir que um token válido seja fornecido. Por defeito, os símbolos de cache adal, por isso um novo token é recuperado do Azure AD apenas quando necessário. Para obter mais informações sobre fichas em Azure AD, consulte [fichas de segurança.](../active-directory/develop/security-tokens.md)

### <a name="code-example-using-azure-ad-integrated-authentication-with-batch-net"></a>Exemplo de código: Utilização da autenticação integrada Azure AD com Lote .NET

Para autenticar com autenticação integrada do Lote .NET, consulte o pacote [Azure Batch .NET](https://www.nuget.org/packages/Microsoft.Azure.Batch/) e o pacote [ADAL.](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/)

Inclua as `using` seguintes declarações no seu código:

```csharp
using Microsoft.Azure.Batch;
using Microsoft.Azure.Batch.Auth;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Consulte o ponto final da Azure AD no seu código, incluindo a identificação do inquilino. Para recuperar a ID do inquilino, siga os passos delineados em [Obter a ID do inquilino para o seu Diretório Ativo Azure:](#get-the-tenant-id-for-your-active-directory)

```csharp
private const string AuthorityUri = "https://login.microsoftonline.com/<tenant-id>";
```

Referenciar o ponto final do recurso de serviço do Lote:

```csharp
private const string BatchResourceUri = "https://batch.core.windows.net/";
```

Referenciar a sua conta Batch:

```csharp
private const string BatchAccountUrl = "https://myaccount.mylocation.batch.azure.com";
```

Especifique o ID da aplicação (ID do cliente) para a sua aplicação. O ID da aplicação está disponível a partir do registo da sua aplicação no portal Azure:

```csharp
private const string ClientId = "<application-id>";
```

Copie também o URI de redirecionamento que especificou, se registou a sua candidatura como Uma Aplicação Nativa. O URI de redirecionamento especificado no seu código deve coincidir com o URI de redirecionamento que forneceu quando registou a aplicação:

```csharp
private const string RedirectUri = "http://mybatchdatasample";
```

Escreva um método de retorno para adquirir o token de autenticação da Azure AD. O método **getAuthenticationTokenAsync** call here chama a ADAL para autenticar um utilizador que está a interagir com a aplicação. O método **AcquireTokenAsync** fornecido pela ADAL solicita ao utilizador as suas credenciais, e a aplicação prossegue assim que o utilizador as fornecer (a menos que já tenha credenciais em cache):

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

Construa um objeto **BatchTokenCredentials** que toma o delegado como parâmetro. Use estas credenciais para abrir um objeto **BatchClient.** Pode utilizar o objeto **BatchClient** para operações subsequentes contra o serviço Batch:

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

### <a name="code-example-using-an-azure-ad-service-principal-with-batch-net"></a>Exemplo de código: Utilização de um principal de serviço AZure AD com Batch .NET

Para autenticar com um resmíssoador de serviço do Batch .NET, consulte o pacote [Azure Batch .NET](https://www.nuget.org/packages/Azure.Batch/) e o pacote [ADAL.](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/)

Inclua as `using` seguintes declarações no seu código:

```csharp
using Microsoft.Azure.Batch;
using Microsoft.Azure.Batch.Auth;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Consulte o ponto final da Azure AD no seu código, incluindo a identificação do inquilino. Ao utilizar um principal de serviço, deve fornecer um ponto final específico para o inquilino. Para recuperar a ID do inquilino, siga os passos delineados em [Obter a ID do inquilino para o seu Diretório Ativo Azure:](#get-the-tenant-id-for-your-active-directory)

```csharp
private const string AuthorityUri = "https://login.microsoftonline.com/<tenant-id>";
```

Referenciar o ponto final do recurso de serviço do Lote:

```csharp
private const string BatchResourceUri = "https://batch.core.windows.net/";
```

Referenciar a sua conta Batch:

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

Escreva um método de retorno para adquirir o token de autenticação da Azure AD. O método **getAuthenticationTokenAsync** call here chama a ADAL para autenticação não atendida:

```csharp
public static async Task<string> GetAuthenticationTokenAsync()
{
    AuthenticationContext authContext = new AuthenticationContext(AuthorityUri);
    AuthenticationResult authResult = await authContext.AcquireTokenAsync(BatchResourceUri, new ClientCredential(ClientId, ClientKey));

    return authResult.AccessToken;
}
```

Construa um objeto **BatchTokenCredentials** que toma o delegado como parâmetro. Use estas credenciais para abrir um objeto **BatchClient.** Em seguida, utilize o objeto **BatchClient** para operações subsequentes contra o serviço Batch:

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

### <a name="code-example-using-an-azure-ad-service-principal-with-batch-python"></a>Exemplo de código: Utilização de um principal de serviço Azure AD com Batch Python

Para autenticar com um principal de serviço do Batch Python, instale e refira os [módulos azure-lot](https://pypi.org/project/azure-batch/) e [azul-comum.](https://pypi.org/project/azure-common/)

```python
from azure.batch import BatchServiceClient
from azure.common.credentials import ServicePrincipalCredentials
```

Ao utilizar um diretor de serviço, deve fornecer a identificação do inquilino. Para recuperar a ID do inquilino, siga os passos delineados em [Obter a ID do inquilino para o seu Diretório Ativo Azure:](#get-the-tenant-id-for-your-active-directory)

```python
TENANT_ID = "<tenant-id>"
```

Referenciar o ponto final do recurso de serviço do Lote:

```python
RESOURCE = "https://batch.core.windows.net/"
```

Referenciar a sua conta Batch:

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

Criar um objeto **ServicePrincipalCredentials:**

```python
credentials = ServicePrincipalCredentials(
    client_id=CLIENT_ID,
    secret=SECRET,
    tenant=TENANT_ID,
    resource=RESOURCE
)
```

Utilize as credenciais principais de serviço para abrir um objeto **BatchServiceClient.** Em seguida, utilize o objeto **BatchServiceClient** para operações subsequentes contra o serviço Batch.

```python
    batch_client = BatchServiceClient(
    credentials,
    batch_url=BATCH_ACCOUNT_URL
)
```

## <a name="next-steps"></a>Passos seguintes

- Reveja a [Documentação do Diretório Ativo Azure](../active-directory/index.yml). Exemplos aprofundados que mostram como utilizar o ADAL estão disponíveis na biblioteca [Azure Code Samples.](https://azure.microsoft.com/resources/samples/?service=active-directory)
- Conheça os [objetos principais de aplicação e serviço no Azure Ative Directory](../active-directory/develop/app-objects-and-service-principals.md) e [como criar uma aplicação AD AD Azure que possa aceder a recursos.](../active-directory/develop/howto-create-service-principal-portal.md)
- Saiba a [autenticação de soluções de Gestão de Lotes com Diretório Ativo.](batch-aad-auth-management.md)
- Para um exemplo python de como criar um cliente Batch autenticado usando um token AD Azure, consulte a Imagem Personalizada do [Lote de Azure implantando com uma](https://github.com/azurebigcompute/recipes/blob/master/Azure%20Batch/CustomImages/CustomImagePython.md) amostra de Script Python.

