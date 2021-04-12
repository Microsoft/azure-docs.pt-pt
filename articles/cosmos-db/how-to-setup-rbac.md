---
title: Configure o controlo de acesso baseado em funções para a sua conta DB Azure Cosmos com Azure AD
description: Saiba como configurar o controlo de acesso baseado em funções com o Azure Ative Directory para a sua conta DB Azure Cosmos
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: how-to
ms.date: 03/30/2021
ms.author: thweiss
ms.openlocfilehash: 1a6bdf55e52a7060423d2a016f07eee3608f50d4
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106063479"
---
# <a name="configure-role-based-access-control-with-azure-active-directory-for-your-azure-cosmos-db-account-preview"></a>Configure o controlo de acesso baseado em funções com o Azure Ative Directory para a sua conta DB Azure Cosmos (Preview)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!IMPORTANT]
> O controlo de acesso baseado em funções da Azure Cosmos está atualmente em pré-visualização. Esta versão de pré-visualização é fornecida sem um Acordo de Nível de Serviço e não é recomendada para cargas de trabalho de produção. Para obter mais informações, consulte [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

> [!NOTE]
> Este artigo é sobre controlo de acesso baseado em funções para operações de plano de dados em Azure Cosmos DB. Se estiver a utilizar operações de avião de gestão, consulte [o controlo de acesso baseado em funções](role-based-access-control.md) aplicado ao artigo de operações do seu avião de gestão.

A Azure Cosmos DB expõe um sistema de controlo de acesso baseado em funções incorporada (RBAC) que permite:

- Autenticar os seus pedidos de dados com uma identidade do Azure Ative Directory (Azure AD).
- Autorize os seus pedidos de dados com um modelo de permissão baseado em papel.

## <a name="concepts"></a>Conceitos

O plano de dados Azure Cosmos DB RBAC é construído com base em conceitos que são geralmente encontrados em outros sistemas RBAC como [Azure RBAC](../role-based-access-control/overview.md):

- O modelo de [permissão](#permission-model) é composto por um conjunto de **ações;** cada uma destas ações mapeia para uma ou múltiplas operações de base de dados. Alguns exemplos de ações incluem ler um item, escrever um item ou executar uma consulta.
- Os utilizadores de DB da Azure Cosmos criam definições de **[papéis](#role-definitions)** que contêm uma lista de ações permitidas.
- Definições de função são atribuídas a identidades específicas da AD Azure através de **[atribuições de funções](#role-assignments)**. Uma atribuição de funções também define o âmbito a que a definição de função se aplica; atualmente, três âmbitos são atualmente:
    - Uma conta DB Azure Cosmos,
    - Uma base de dados DB Azure Cosmos,
    - Um contentor DB Azure Cosmos.

  :::image type="content" source="./media/how-to-setup-rbac/concepts.png" alt-text="Conceitos RBAC":::

> [!NOTE]
> O Azure Cosmos DB RBAC não expõe atualmente quaisquer definições de papel incorporadas.

## <a name="permission-model"></a><a id="permission-model"></a> Modelo de permissão

A tabela abaixo lista todas as ações expostas pelo modelo de permissão.

| Name | Operação de base de dados correspondente |
|---|---|
| `Microsoft.DocumentDB/databaseAccounts/readMetadata` | Leia metadados de conta. Consulte [os pedidos de metadados](#metadata-requests) para mais detalhes. |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/create` | Crie um novo item. |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/read` | Leia um item individual pela sua chave de ID e partição (leitura de ponto). |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/replace` | Substitua um item existente. |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/upsert` | "Upsert" um item, o que significa criá-lo se não existir, ou substituí-lo se existir. |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/delete` | Apagar um item. |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/executeQuery` | Execute uma [consulta SQL](sql-query-getting-started.md). |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/readChangeFeed` | Leia a partir da [alimentação](read-change-feed.md)de mudança do recipiente . |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/executeStoredProcedure` | Execute um [procedimento armazenado](stored-procedures-triggers-udfs.md). |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/manageConflicts` | Gerir [conflitos](conflict-resolution-policies.md) para contas de região multi-escrita (isto é, listar e apagar itens do feed de conflito). |

Os wildcards são suportados em ambos os *níveis de contentores* e *itens:*

- `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/*`
- `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/*`

> [!IMPORTANT]
> Este modelo de permissão cobre apenas operações de base de dados que lhe permitem ler e escrever dados. **Não** abrange qualquer tipo de operações de gestão, como a criação de contentores ou a alteração da sua produção. Para autenticar operações de gestão com identidade AAD, utilize [o Azure RBAC.](role-based-access-control.md)

### <a name="metadata-requests"></a><a id="metadata-requests"></a> Pedidos de metadados

Ao utilizar em Azure Cosmos DB SDKs, estes SDKs emitem pedidos de metadados apenas de leitura durante a inicialização e para atender pedidos de dados específicos. Estes pedidos de metadados recolhem vários detalhes de configuração, tais como: 

- A configuração global da sua conta, que inclui as regiões Azure, a conta está disponível.
- A chave de partição dos seus contentores ou a sua política de indexação.
- A lista de divisórias físicas que fazem um contentor e seus endereços.

*Não* recolhem nenhum dos dados que guardou na sua conta.

Para garantir a melhor transparência do nosso modelo de permissão, estes pedidos de metadados são explicitamente cobertos pela `Microsoft.DocumentDB/databaseAccounts/readMetadata` ação. Esta ação deve ser permitida em todas as situações em que a sua conta DB Azure Cosmos é acedida através de um dos SDKs Azure Cosmos. Pode ser atribuído (através de uma atribuição de funções) a qualquer nível da hierarquia DB do Azure Cosmos (isto é, conta, base de dados ou contentor).

Os pedidos reais de metadados permitidos pela ação `Microsoft.DocumentDB/databaseAccounts/readMetadata` dependem do âmbito a que a ação é atribuída:

| Âmbito | Pedidos permitidos pela ação |
|---|---|
| Conta | - Listagem das bases de dados na conta<br>- Para cada base de dados na conta, as ações permitidas no âmbito da base de dados |
| Base de Dados | - Leitura de metadados de base de dados<br>- Listagem dos contentores debaixo da base de dados<br>- Para cada contentor sob a base de dados, as ações permitidas no âmbito do contentor |
| Contentor | - Metadados de contentores de leitura<br>- Listagem de divisórias físicas debaixo do recipiente<br>- Resolução do endereço de cada divisória física |

## <a name="create-role-definitions"></a><a id="role-definitions"></a> Criar definições de função

Ao criar uma definição de papel, você precisa fornecer:

- O nome da sua conta DB Azure Cosmos.
- O grupo de recursos que contém a sua conta.
- O tipo de definição de função; apenas `CustomRole` é atualmente suportado.
- O nome da definição de papel.
- Uma lista de [ações](#permission-model) que quer que o papel permita.
- Um ou vários âmbitos a que a definição de função pode ser atribuída; Os âmbitos suportados são:
    - `/` (nível de conta),
    - `/dbs/<database-name>` (nível de base de dados),
    - `/dbs/<database-name>/colls/<container-name>` (nível de contentor).

> [!NOTE]
> As operações descritas abaixo estão atualmente disponíveis em:
> - Azure PowerShell: [Az.CosmosDB versão 2.0.1-pré-visualização](https://www.powershellgallery.com/packages/Az.CosmosDB/2.0.1-preview)
> - Azure CLI: [versão de extensão 'cosmosdb-preview' 0.4.0](https://github.com/Azure/azure-cli-extensions/tree/master/src/cosmosdb-preview)

### <a name="using-azure-powershell"></a>Utilizar o Azure PowerShell

Crie uma função chamada *MyReadOnlyRole* que contenha apenas ações de leitura:

```powershell
$resourceGroupName = "<myResourceGroup>"
$accountName = "<myCosmosAccount>"
New-AzCosmosDBSqlRoleDefinition -AccountName $accountName `
    -ResourceGroupName $resourceGroupName `
    -Type CustomRole -RoleName MyReadOnlyRole `
    -DataAction @( `
        'Microsoft.DocumentDB/databaseAccounts/readMetadata',
        'Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/read', `
        'Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/executeQuery', `
        'Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/readChangeFeed') `
    -AssignableScope "/"
```

Crie um papel chamado *MyReadWriteRole* que contenha todas as ações:

```powershell
New-AzCosmosDBSqlRoleDefinition -AccountName $accountName `
    -ResourceGroupName $resourceGroupName `
    -Type CustomRole -RoleName MyReadWriteRole `
    -DataAction @( `
        'Microsoft.DocumentDB/databaseAccounts/readMetadata',
        'Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/*', `
        'Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/*') `
    -AssignableScope "/"
```

Enuste as definições de função que criou para obter as suas IDs:

```powershell
Get-AzCosmosDBSqlRoleDefinition -AccountName $accountName `
    -ResourceGroupName $resourceGroupName
```

```
RoleName         : MyReadWriteRole
Id               : /subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDB/databaseAcc
                   ounts/<myCosmosAccount>/sqlRoleDefinitions/<roleDefinitionId>
Type             : CustomRole
Permissions      : {Microsoft.Azure.Management.CosmosDB.Models.Permission}
AssignableScopes : {/subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDB/databaseAc
                   counts/<myCosmosAccount>}

RoleName         : MyReadOnlyRole
Id               : /subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDB/databaseAcc
                   ounts/<myCosmosAccount>/sqlRoleDefinitions/<roleDefinitionId>
Type             : CustomRole
Permissions      : {Microsoft.Azure.Management.CosmosDB.Models.Permission}
AssignableScopes : {/subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDB/databaseAc
                   counts/<myCosmosAccount>}
```

### <a name="using-the-azure-cli"></a>Com a CLI do Azure

Crie uma função chamada *MyReadOnlyRole* que contenha apenas ações de leitura:

```json
// role-definition-ro.json
{
    "RoleName": "MyReadOnlyRole",
    "Type": "CustomRole",
    "AssignableScopes": ["/"],
    "Permissions": [{
        "DataActions": [
            "Microsoft.DocumentDB/databaseAccounts/readMetadata",
            "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/read",
            "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/executeQuery",
            "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/readChangeFeed"
        ]
    }]
}
```

```azurecli
resourceGroupName='<myResourceGroup>'
accountName='<myCosmosAccount>'
az cosmosdb sql role definition create --account-name $accountName --resource-group $resourceGroupName --body @role-definition-ro.json
```

Crie um papel chamado *MyReadWriteRole* que contenha todas as ações:

```json
// role-definition-rw.json
{
    "RoleName": "MyReadWriteRole",
    "Type": "CustomRole",
    "AssignableScopes": ["/"],
    "Permissions": [{
        "DataActions": [
            "Microsoft.DocumentDB/databaseAccounts/readMetadata",
            "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/*",
            "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/*"
        ]
    }]
}
```

```azurecli
az cosmosdb sql role definition create --account-name $accountName --resource-group $resourceGroupName --body @role-definition-rw.json
```

Enuste as definições de função que criou para obter as suas IDs:

```azurecli
az cosmosdb sql role definition list --account-name $accountName --resource-group $resourceGroupName
```

```
[
  {
    "assignableScopes": [
      "/subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDB/databaseAccounts/<myCosmosAccount>"
    ],
    "id": "/subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDB/databaseAccounts/<myCosmosAccount>/sqlRoleDefinitions/<roleDefinitionId>",
    "name": "<roleDefinitionId>",
    "permissions": [
      {
        "dataActions": [
          "Microsoft.DocumentDB/databaseAccounts/readMetadata",
          "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/*",
          "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/*"
        ],
        "notDataActions": []
      }
    ],
    "resourceGroup": "<myResourceGroup>",
    "roleName": "MyReadWriteRole",
    "sqlRoleDefinitionGetResultsType": "CustomRole",
    "type": "Microsoft.DocumentDB/databaseAccounts/sqlRoleDefinitions"
  },
  {
    "assignableScopes": [
      "/subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDB/databaseAccounts/<myCosmosAccount>"
    ],
    "id": "/subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDB/databaseAccounts/<myCosmosAccount>/sqlRoleDefinitions/<roleDefinitionId>",
    "name": "<roleDefinitionId>",
    "permissions": [
      {
        "dataActions": [
          "Microsoft.DocumentDB/databaseAccounts/readMetadata",
          "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/read",
          "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/executeQuery",
          "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/readChangeFeed"
        ],
        "notDataActions": []
      }
    ],
    "resourceGroup": "<myResourceGroup>",
    "roleName": "MyReadOnlyRole",
    "sqlRoleDefinitionGetResultsType": "CustomRole",
    "type": "Microsoft.DocumentDB/databaseAccounts/sqlRoleDefinitions"
  }
]
```

## <a name="create-role-assignments"></a><a id="role-assignments"></a> Criar atribuições de funções

Uma vez criadas as definições de papel, podes associá-las às tuas identidades de AAD. Ao criar uma atribuição de funções, você precisa fornecer:

- O nome da sua conta DB Azure Cosmos.
- O grupo de recursos que contém a sua conta.
- A identificação da definição de papel a atribuir.
- A identificação principal da identidade a que a definição de papel deve ser atribuída.
- O âmbito da atribuição de funções; Os âmbitos suportados são:
    - `/` (nível de conta)
    - `/dbs/<database-name>` (nível de base de dados)
    - `/dbs/<database-name>/colls/<container-name>` (nível de contentor)

  O âmbito deve coincidir ou ser um sub-âmbito de um dos âmbitos atribuíveis da definição de função.

> [!NOTE]
> Se pretender criar uma atribuição de funções para um diretor de serviço, certifique-se de que utiliza o seu **ID de Objeto,** tal como encontrado na secção de **aplicações da Enterprise** da lâmina do portal **Azure Ative.**

> [!NOTE]
> As operações descritas abaixo estão atualmente disponíveis em:
> - Azure PowerShell: [Az.CosmosDB versão 2.0.1-pré-visualização](https://www.powershellgallery.com/packages/Az.CosmosDB/2.0.1-preview)
> - Azure CLI: [versão de extensão 'cosmosdb-preview' 0.4.0](https://github.com/Azure/azure-cli-extensions/tree/master/src/cosmosdb-preview)

### <a name="using-azure-powershell"></a>Utilizar o Azure PowerShell

Atribuir um papel a uma identidade:

```powershell
$resourceGroupName = "<myResourceGroup>"
$accountName = "<myCosmosAccount>"
$readOnlyRoleDefinitionId = "<roleDefinitionId>" // as fetched above
$principalId = "<aadPrincipalId>"
New-AzCosmosDBSqlRoleAssignment -AccountName $accountName `
    -ResourceGroupName $resourceGroupName `
    -RoleDefinitionId $readOnlyRoleDefinitionId `
    -Scope $accountName `
    -PrincipalId $principalId
```

### <a name="using-the-azure-cli"></a>Com a CLI do Azure

Atribuir um papel a uma identidade:

```azurecli
resourceGroupName='<myResourceGroup>'
accountName='<myCosmosAccount>'
readOnlyRoleDefinitionId = '<roleDefinitionId>' // as fetched above
principalId = '<aadPrincipalId>'
az cosmosdb sql role assignment create --account-name $accountName --resource-group $resourceGroupName --scope "/" --principal-id $principalId --role-definition-id $readOnlyRoleDefinitionId
```

## <a name="initialize-the-sdk-with-azure-ad"></a>Inicializar o SDK com Azure AD

Para utilizar o Azure Cosmos DB RBAC na sua aplicação, tem de atualizar a forma como inicializa o Azure Cosmos DB SDK. Em vez de passar a chave primária da sua conta, tem que passar uma instância de uma `TokenCredential` classe. Este caso fornece ao Azure Cosmos DB SDK o contexto necessário para obter um token AAD em nome da identidade que deseja usar.

A forma como cria um `TokenCredential` caso está fora do âmbito deste artigo. Existem muitas formas de criar tal instância dependendo do tipo de identidade AAD que pretende usar (principal utilizador, diretor de serviço, grupo, etc.). O mais importante, o seu `TokenCredential` caso deve resolver a identidade (ID principal) a que atribuiu as suas funções. Pode encontrar exemplos de criação de uma `TokenCredential` classe:

- [em .NET](/dotnet/api/overview/azure/identity-readme#credential-classes)
- [em Java](/java/api/overview/azure/identity-readme#credential-classes)
- [em JavaScript](/javascript/api/overview/azure/identity-readme#credential-classes)

Os exemplos abaixo utilizam um principal de serviço com um `ClientSecretCredential` exemplo.

### <a name="in-net"></a>Em .NET

O Azure Cosmos DB RBAC é atualmente suportado na `preview` versão do [.NET SDK V3](sql-api-sdk-dotnet-standard.md).

```csharp
TokenCredential servicePrincipal = new ClientSecretCredential(
    "<azure-ad-tenant-id>",
    "<client-application-id>",
    "<client-application-secret>");
CosmosClient client = new CosmosClient("<account-endpoint>", servicePrincipal);
```

### <a name="in-java"></a>Em Java

O Azure Cosmos DB RBAC é atualmente suportado no [Java SDK V4](sql-api-sdk-java-v4.md).

```java
TokenCredential ServicePrincipal = new ClientSecretCredentialBuilder()
    .authorityHost("https://login.microsoftonline.com")
    .tenantId("<azure-ad-tenant-id>")
    .clientId("<client-application-id>")
    .clientSecret("<client-application-secret>")
    .build();
CosmosAsyncClient Client = new CosmosClientBuilder()
    .endpoint("<account-endpoint>")
    .credential(ServicePrincipal)
    .build();
```

### <a name="in-javascript"></a>Em JavaScript

O Azure Cosmos DB RBAC é atualmente suportado no [JavaScript SDK V3](sql-api-sdk-node.md).

```javascript
const servicePrincipal = new ClientSecretCredential(
    "<azure-ad-tenant-id>",
    "<client-application-id>",
    "<client-application-secret>");
const client = new CosmosClient({
    "<account-endpoint>",
    aadCredentials: servicePrincipal
});
```

## <a name="auditing-data-requests"></a>Pedidos de dados de auditoria

Ao utilizar o Azure Cosmos DB RBAC, [os registos de diagnóstico](cosmosdb-monitor-resource-logs.md) são aumentados com informações de identidade e autorização para cada operação de dados. Isto permite-lhe realizar auditorias detalhadas e recuperar a identidade AAD utilizada para cada pedido de dados enviado para a sua conta DB Azure Cosmos.

Esta informação adicional flui na categoria **dataPlaneRequests** e consiste em duas colunas extra:

- `aadPrincipalId_g` mostra o iD principal da identidade AAD que foi usado para autenticar o pedido.
- `aadAppliedRoleAssignmentId_g` mostra a [atribuição de papel](#role-assignments) que foi honrada ao autorizar o pedido.

## <a name="limits"></a>Limites

- Você pode criar até 100 definições de papel e 2.000 atribuições de papel por conta DB Azure Cosmos.
- Só pode atribuir definições de funções a identidades AD AZure pertencentes ao mesmo inquilino AD AZure que a sua conta DB Azure Cosmos.
- A resolução do grupo AZure AD não é atualmente apoiada para identidades que pertencem a mais de 200 grupos.
- O token AD AZure é atualmente passado como um cabeçalho com cada pedido individual enviado para o serviço DB Azure Cosmos, aumentando o tamanho total da carga útil.
- O acesso aos seus dados com a Azure AD através do [Azure Cosmos DB Explorer](data-explorer.md) ainda não está suportado. A utilização do Azure Cosmos DB Explorer ainda requer que o utilizador tenha acesso à chave principal da conta por enquanto.

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

### <a name="which-azure-cosmos-db-apis-are-supported-by-rbac"></a>Quais as APIs do Azure Cosmos DB suportadas pelo RBAC?

Atualmente, só é suportada a API SQL.

### <a name="is-it-possible-to-manage-role-definitions-and-role-assignments-from-the-azure-portal"></a>É possível gerir definições de funções e atribuições de funções no portal do Azure?

O suporte do portal do Azure para a gestão de funções ainda não está disponível.

### <a name="which-sdks-in-azure-cosmos-db-sql-api-support-rbac"></a>Quais SDKs em Azure Cosmos DB SQL API suportam o RBAC?

Os SDKs [.NET V3](sql-api-sdk-dotnet-standard.md) e [Java V4](sql-api-sdk-java-v4.md) estão atualmente suportados.

### <a name="is-the-azure-ad-token-automatically-refreshed-by-the-azure-cosmos-db-sdks-when-it-expires"></a>O token do Azure Active Directory é automaticamente atualizado pelos SDKs do Azure Cosmos DB quando expira?

Sim.

### <a name="is-it-possible-to-disable-the-usage-of-the-account-primary-key-when-using-rbac"></a>É possível desativar a utilização da chave primária da conta ao utilizar o RBAC?

De momento, não é possível desativar a chave primária da conta.

## <a name="next-steps"></a>Passos seguintes

- Obtenha uma visão geral do [acesso seguro aos dados em Cosmos DB](secure-access-to-data.md).
- Saiba mais sobre [a GESTÃO RBAC para gestão DB da Azure Cosmos](role-based-access-control.md).