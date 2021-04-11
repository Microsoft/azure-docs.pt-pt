---
title: Compreender definições de função Azure - Azure RBAC
description: Conheça as definições de funções Azure no controlo de acesso baseado em funções Azure (Azure RBAC) para uma gestão de acesso de grãos finos dos recursos Azure.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: conceptual
ms.workload: identity
ms.date: 03/22/2021
ms.author: rolyon
ms.custom: ''
ms.openlocfilehash: 5b2ec3289d187997763ee0d9280a777d4fa1f396
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104801762"
---
# <a name="understand-azure-role-definitions"></a>Compreender definições de função Azure

Se está a tentar perceber como funciona um papel Azure ou se está a criar o seu próprio [papel personalizado Azure,](custom-roles.md)é útil compreender como os papéis são definidos. Este artigo descreve os detalhes das definições de funções e fornece alguns exemplos.

## <a name="role-definition"></a>Definição de função

Uma *definição de função* é uma coleção de permissões. Às vezes chama-se apenas um *papel.* Uma definição de função lista as operações que podem ser efetuadas, por exemplo, ler, escrever e eliminar. Pode ainda enumerar as operações excluídas das operações ou operações permitidas relacionadas com os dados subjacentes.

O seguinte mostra um exemplo das propriedades numa definição de função quando exibidas utilizando a Azure PowerShell:

```
Name
Id
IsCustom
Description
Actions []
NotActions []
DataActions []
NotDataActions []
AssignableScopes []
```

O seguinte mostra um exemplo das propriedades numa definição de função quando exibidas utilizando o portal Azure CLI ou a API REST:

```
roleName
name
type
description
actions []
notActions []
dataActions []
notDataActions []
assignableScopes []
```

A tabela seguinte descreve o que as propriedades de função significam.

| Propriedade | Descrição |
| --- | --- |
| `Name`</br>`roleName` | O nome de exibição do papel. |
| `Id`</br>`name` | A identificação única do papel. Os papéis incorporados têm o mesmo papel de ID através das nuvens. |
| `IsCustom`</br>`roleType` | Indica se este é um papel personalizado. Definir para `true` ou `CustomRole` para papéis personalizados. Definir para `false` ou `BuiltInRole` para papéis embutidos. |
| `Description`</br>`description` | A descrição do papel. |
| `Actions`</br>`actions` | Um conjunto de cordas que especifica as operações de gestão que o papel permite ser executado. |
| `NotActions`</br>`notActions` | Um conjunto de cordas que especifica as operações de gestão que são excluídas do permitido `Actions` . |
| `DataActions`</br>`dataActions` | Um conjunto de cordas que especifica as operações de dados que a função permite ser realizada aos seus dados dentro desse objeto. |
| `NotDataActions`</br>`notDataActions` | Um conjunto de cordas que especifica as operações de dados que são excluídas do permitido `DataActions` . |
| `AssignableScopes`</br>`assignableScopes` | Uma série de cordas que especifica os âmbitos que o papel está disponível para a atribuição. |

### <a name="operations-format"></a>Formato de operações

As operações são especificadas com cordas que têm o seguinte formato:

- `{Company}.{ProviderName}/{resourceType}/{action}`

A `{action}` parte de uma cadeia de operação especifica o tipo de operações que pode executar num tipo de recurso. Por exemplo, verá os seguintes sublpementos em `{action}` :

| Substring de ação    | Description         |
| ------------------- | ------------------- |
| `*` | O personagem wildcard dá acesso a todas as operações que correspondam à corda. |
| `read` | Permite operações de leitura (GET). |
| `write` | Permite a escrita de operações (PUT ou PATCH). |
| `action` | Permite operações personalizadas como reiniciar máquinas virtuais (POST). |
| `delete` | Ativa a eliminação de operações (DELETE). |

### <a name="role-definition-example"></a>Exemplo de definição de função

Aqui está a definição de função [do Contribuinte,](built-in-roles.md#contributor) tal como exibida no Azure PowerShell e no Azure CLI. A operação de caráter universal (`*`) em `Actions` indica que o principal atribuído a esta função pode efetuar todas as ações ou, por outras palavras, pode gerir tudo. Isto inclui ações definidas no futuro, uma vez que o Azure adiciona novos tipos de recursos. As operações em `NotActions` são subtraídas de `Actions`. No caso do [papel de Contribuinte,](built-in-roles.md#contributor) elimina a capacidade desta função de gerir o `NotActions` acesso aos recursos e também gerir as atribuições do Azure Blueprint.

Função de contribuinte como mostrado no Azure PowerShell:

```json
{
  "Name": "Contributor",
  "Id": "b24988ac-6180-42a0-ab88-20f7382dd24c",
  "IsCustom": false,
  "Description": "Lets you manage everything except access to resources.",
  "Actions": [
    "*"
  ],
  "NotActions": [
    "Microsoft.Authorization/*/Delete",
    "Microsoft.Authorization/*/Write",
    "Microsoft.Authorization/elevateAccess/Action",
    "Microsoft.Blueprint/blueprintAssignments/write",
    "Microsoft.Blueprint/blueprintAssignments/delete"
  ],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": [
    "/"
  ]
}
```

Função contributiva como mostrado no Azure CLI:

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage everything except access to resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
  "name": "b24988ac-6180-42a0-ab88-20f7382dd24c",
  "permissions": [
    {
      "actions": [
        "*"
      ],
      "notActions": [
        "Microsoft.Authorization/*/Delete",
        "Microsoft.Authorization/*/Write",
        "Microsoft.Authorization/elevateAccess/Action",
        "Microsoft.Blueprint/blueprintAssignments/write",
        "Microsoft.Blueprint/blueprintAssignments/delete"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="management-and-data-operations"></a>Operações de gestão e dados

O controlo de acesso baseado em funções para operações de gestão é especificado na `Actions` e propriedades de uma `NotActions` definição de papel. Aqui estão alguns exemplos de operações de gestão em Azure:

- Gerir o acesso a uma conta de armazenamento
- Criar, atualizar ou eliminar um recipiente blob
- Eliminar um grupo de recursos e todos os seus recursos

O acesso à gestão não é herdado aos seus dados desde que o método de autenticação do contentor esteja definido para "Conta de Utilizador AD Azure" e não "Chave de Acesso". Esta separação impede que os papéis com wildcards `*` () tenham acesso ilimitado aos seus dados. Por exemplo, se um utilizador tiver uma função [Reader](built-in-roles.md#reader) numa subscrição, então pode ver a conta de armazenamento, mas por padrão não consegue ver os dados subjacentes.

Anteriormente, o controlo de acesso baseado em funções não era utilizado para operações de dados. A autorização para operações de dados variava entre fornecedores de recursos. O mesmo modelo de autorização de acesso baseado em funções utilizado para operações de gestão foi alargado às operações de dados.

Para apoiar as operações de dados, novas propriedades de dados foram adicionadas à definição de função. As operações de dados são especificadas nas propriedades `DataActions` e `NotDataActions`. Ao adicionar estas propriedades de dados, mantém-se a separação entre gestão e dados. Tal impede que as atribuições de função atuais com carateres universais (`*`) tenham, de repente, acesso aos dados. Aqui estão algumas operações de dados que podem ser especificadas em `DataActions` e `NotDataActions`:

- Ler uma lista de blobs num contentor
- Escrever um blob de armazenamento num contentor
- Eliminar uma mensagem numa fila

Aqui está a definição de função de leitor de [dados de armazenamento Blob,](built-in-roles.md#storage-blob-data-reader) que inclui operações tanto nas propriedades como nas `Actions` `DataActions` propriedades. Esta função permite-lhe ler o recipiente blob e também os dados subjacentes à bolha.

Função de leitor de dados blob de armazenamento, tal como mostrado no Azure PowerShell:

```json
{
  "Name": "Storage Blob Data Reader",
  "Id": "2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
  "IsCustom": false,
  "Description": "Allows for read access to Azure Storage blob containers and data",
  "Actions": [
    "Microsoft.Storage/storageAccounts/blobServices/containers/read",
    "Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action"
  ],
  "NotActions": [],
  "DataActions": [
    "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read"
  ],
  "NotDataActions": [],
  "AssignableScopes": [
    "/"
  ]
}
```

Função de Leitor de Dados Blob de Armazenamento, tal como mostrado no Azure CLI:

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read access to Azure Storage blob containers and data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
  "name": "2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/read",
        "Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Blob Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

Apenas as operações de dados podem ser adicionadas às propriedades `DataActions` e `NotDataActions`. Os fornecedores de recursos identificam quais as operações que são operações de dados, definindo a `isDataAction` propriedade para `true` . Para ver uma lista das operações onde `isDataAction` `true` está, consulte [as operações do fornecedor de recursos.](resource-provider-operations.md) As funções que não têm operações de dados não são obrigadas a ter `DataActions` e propriedades dentro da `NotDataActions` definição de função.

A autorização para todas as chamadas de gestão da API é tratada pelo Azure Resource Manager. A autorização para a operação de dados As chamadas API são tratadas por um fornecedor de recursos ou pelo Azure Resource Manager.

### <a name="data-operations-example"></a>Exemplo de operações de dados

Para entender melhor como funcionam as operações de gestão e dados, vamos considerar um exemplo específico. Alice foi atribuída a função [de Proprietário](built-in-roles.md#owner) no âmbito de subscrição. Foi atribuída ao Bob a função [de Contribuinte de Dados blob de armazenamento](built-in-roles.md#storage-blob-data-contributor) num âmbito de conta de armazenamento. O diagrama que se segue mostra este exemplo.

![O controlo de acesso baseado em funções foi alargado para apoiar tanto as operações de gestão como as operações de dados](./media/role-definitions/rbac-management-data.png)

O papel [de Proprietário](built-in-roles.md#owner) para Alice e o papel de colaborador [de dados blob de armazenamento](built-in-roles.md#storage-blob-data-contributor) para Bob têm as seguintes ações:

Proprietário

&nbsp;&nbsp;&nbsp;&nbsp;Ações<br>
&nbsp;&nbsp;&nbsp;&nbsp;`*`

Contribuinte de Dados do Armazenamento de Blobs

&nbsp;&nbsp;&nbsp;&nbsp;Ações<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/delete`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/read`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/write`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action`<br>
&nbsp;&nbsp;&nbsp;&nbsp;DataActions<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/move/action`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write`

Uma vez que Alice tem uma ação wildcard `*` no âmbito de subscrição, as suas permissões herdam para que eles realizem todas as ações de gestão. Alice pode ler, escrever e apagar recipientes. No entanto, Alice não pode realizar operações de dados sem tomar medidas adicionais. Por exemplo, por defeito, Alice não consegue ler as bolhas dentro de um recipiente. Para ler as bolhas, Alice teria que recuperar as chaves de acesso ao armazenamento e usá-las para aceder às bolhas.

As permissões do Bob limitam-se apenas ao `Actions` e `DataActions` especificado na função de Contribuinte de [Dados blob de armazenamento.](built-in-roles.md#storage-blob-data-contributor) Com base no papel, o Bob pode realizar operações de gestão e dados. Por exemplo, Bob pode ler, escrever e apagar recipientes na conta de armazenamento especificada e também pode ler, escrever e apagar as bolhas.

Para obter mais informações sobre a gestão e segurança do plano de dados para armazenamento, consulte o [guia de segurança do Azure Storage](../storage/blobs/security-recommendations.md).

### <a name="what-tools-support-using-azure-roles-for-data-operations"></a>Que ferramentas suportam a utilização de funções Azure para operações de dados?

Para visualizar e trabalhar com operações de dados, tem de ter as versões corretas das ferramentas ou dos SDKs:

| Ferramenta  | Versão  |
|---------|---------|
| [Azure PowerShell](/powershell/azure/install-az-ps) | 1.1.0 ou mais tarde |
| [CLI do Azure](/cli/azure/install-azure-cli) | 2.0.30 ou mais tarde |
| [Azure para .NET](/dotnet/azure/) | 2.8.0 pré-visualização ou mais tarde |
| [SDK do Azure para Go](/azure/go/azure-sdk-go-install) | 15.0.0 ou mais tarde |
| [Azul para Java](/java/azure/) | 1.9.0 ou mais tarde |
| [Azul para Python](/azure/python/) | 0.40.0 ou mais tarde |
| [SDK do Azure para Ruby](https://rubygems.org/gems/azure_sdk) | 0.17.1 ou mais tarde |

Para visualizar e utilizar as operações de dados na API REST, tem de definir o parâmetro **da versão API** para a seguinte versão ou posterior:

- 2018-07-01

## <a name="actions"></a>Ações

A `Actions` permissão especifica as operações de gestão que o papel permite ser desempenhado. Trata-se de uma coleção de cadeias de operação que identificam operações securáveis de fornecedores de recursos Azure. Aqui estão alguns exemplos de operações de gestão que podem ser usadas em `Actions` .

> [!div class="mx-tableFixed"]
> | Cadeia de operação    | Description         |
> | ------------------- | ------------------- |
> | `*/read` | Concede acesso a operações de leitura para todos os tipos de recursos de todos os fornecedores de recursos Azure.|
> | `Microsoft.Compute/*` | Concede acesso a todas as operações para todos os tipos de recursos no fornecedor de recursos Microsoft.Compute.|
> | `Microsoft.Network/*/read` | Concede acesso a operações de leitura para todos os tipos de recursos no fornecedor de recursos Microsoft.Network.|
> | `Microsoft.Compute/virtualMachines/*` | Concede acesso a todas as operações de máquinas virtuais e seus tipos de recursos infantis.|
> | `microsoft.web/sites/restart/Action` | Concede acesso para reiniciar uma aplicação web.|

## <a name="notactions"></a>NotActions

A `NotActions` permissão especifica as operações de gestão que são subtraídas ou excluídas do permitido `Actions` que têm um wildcard `*` (). Utilize a `NotActions` permissão se o conjunto de operações que pretende permitir for mais facilmente definido subtraindo a partir do `Actions` qual tem um wildcard `*` (). O acesso concedido por uma função (permissões efetivas) é calculado subtraindo as `NotActions` operações das `Actions` operações.

`Actions - NotActions = Effective management permissions`

A tabela a seguir mostra dois exemplos das permissões eficazes para uma operação [wildcard Microsoft.CostManagement:](resource-provider-operations.md#microsoftcostmanagement)

> [!div class="mx-tableFixed"]
> | Actions | NotActions | Permissões de gestão eficazes |
> | --- | --- | --- |
> | `Microsoft.CostManagement/exports/*` | *nenhum* | `Microsoft.CostManagement/exports/action`</br>`Microsoft.CostManagement/exports/read`</br>`Microsoft.CostManagement/exports/write`</br>`Microsoft.CostManagement/exports/delete`</br>`Microsoft.CostManagement/exports/run/action` |
> | `Microsoft.CostManagement/exports/*` | `Microsoft.CostManagement/exports/delete` | `Microsoft.CostManagement/exports/action`</br>`Microsoft.CostManagement/exports/read`</br>`Microsoft.CostManagement/exports/write`</br>`Microsoft.CostManagement/exports/run/action` |

> [!NOTE]
> Se um utilizador tiver uma função que exclua uma operação em `NotActions` , e lhe for atribuída uma segunda função que garanta o acesso à mesma operação, o utilizador pode realizar essa operação. `NotActions` não é uma regra de negação – é simplesmente uma forma conveniente de criar um conjunto de operações permitidas quando as operações específicas precisam de ser excluídas.
>

### <a name="differences-between-notactions-and-deny-assignments"></a>Diferenças entre NotActions e negar atribuições

`NotActions` e negar atribuições não são as mesmas e servir propósitos diferentes. `NotActions` são uma forma conveniente de subtrair ações específicas de uma operação wildcard `*` ()

Negar que as atribuições bloqueiam os utilizadores de realizarem ações específicas, mesmo que uma atribuição de funções lhes conceda acesso. Para obter mais informações, consulte [Understand Azure negar atribuições.](deny-assignments.md)

## <a name="dataactions"></a>DataActions

A `DataActions` permissão especifica as operações de dados que a função permite ser realizada aos seus dados dentro desse objeto. Por exemplo, se um utilizador tiver lido o acesso de dados blob a uma conta de armazenamento, então pode ler as bolhas dentro dessa conta de armazenamento. Aqui estão alguns exemplos de operações de dados que podem ser usadas em `DataActions` .

> [!div class="mx-tableFixed"]
> | Cadeia de operação    | Description         |
> | ------------------- | ------------------- |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read` | Devolve uma bolha ou uma lista de bolhas. |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write` | Devolve o resultado de escrever uma bolha. |
> | `Microsoft.Storage/storageAccounts/queueServices/queues/messages/read` | Devolve uma mensagem. |
> | `Microsoft.Storage/storageAccounts/queueServices/queues/messages/*` | Devolve uma mensagem ou o resultado de escrever ou apagar uma mensagem. |

## <a name="notdataactions"></a>NotDataActions

A `NotDataActions` permissão especifica as operações de dados que são subtraídas ou excluídas do permitido `DataActions` que têm um wildcard `*` (). Utilize a `NotDataActions` permissão se o conjunto de operações que pretende permitir for mais facilmente definido subtraindo a partir do `DataActions` qual tem um wildcard `*` (). O acesso concedido por uma função (permissões efetivas) é calculado subtraindo as `NotDataActions` operações das `DataActions` operações. Cada fornecedor de recursos fornece o seu respetivo conjunto de APIs para realizar operações de dados.

`DataActions - NotDataActions = Effective data permissions`

A tabela a seguir mostra dois exemplos das permissões eficazes para uma operação [wildcard microsoft.storage:](resource-provider-operations.md#microsoftstorage)

> [!div class="mx-tableFixed"]
> | DataActions | NotDataActions | Permissões de dados eficazes |
> | --- | --- | --- |
> | `Microsoft.Storage/storageAccounts/queueServices/queues/messages/*` | *nenhum* | `Microsoft.Storage/storageAccounts/queueServices/queues/messages/read`</br>`Microsoft.Storage/storageAccounts/queueServices/queues/messages/write`</br>`Microsoft.Storage/storageAccounts/queueServices/queues/messages/delete`</br>`Microsoft.Storage/storageAccounts/queueServices/queues/messages/add/action`</br>`Microsoft.Storage/storageAccounts/queueServices/queues/messages/process/action` |
> | `Microsoft.Storage/storageAccounts/queueServices/queues/messages/*` | `Microsoft.Storage/storageAccounts/queueServices/queues/messages/delete`</br> | `Microsoft.Storage/storageAccounts/queueServices/queues/messages/read`</br>`Microsoft.Storage/storageAccounts/queueServices/queues/messages/write`</br>`Microsoft.Storage/storageAccounts/queueServices/queues/messages/add/action`</br>`Microsoft.Storage/storageAccounts/queueServices/queues/messages/process/action` |

> [!NOTE]
> Se um utilizador tiver uma função que exclua uma operação de dados em `NotDataActions` , e lhe for atribuída uma segunda função que concede acesso à mesma operação de dados, o utilizador pode realizar essa operação de dados. `NotDataActions` não é uma regra de negação – é simplesmente uma forma conveniente de criar um conjunto de operações de dados permitidas quando as operações específicas de dados precisam de ser excluídas.
>

## <a name="assignablescopes"></a>Scopes atribuíveis

A `AssignableScopes` propriedade especifica os âmbitos (grupos de gestão, subscrições ou grupos de recursos) que têm esta definição de função disponível. Pode disponibilizar o papel para atribuição apenas nos grupos de gestão, subscrições ou grupos de recursos que o exijam. Deve utilizar pelo menos um grupo de gestão, subscrição ou grupo de recursos.

As funções incorporadas `AssignableScopes` definiram-se no âmbito da raiz ( `"/"` ). O âmbito da raiz indica que a função está disponível para atribuição em todos os âmbitos. Exemplos de âmbitos validáveis atribuíveis incluem:

> [!div class="mx-tableFixed"]
> | A função está disponível para a atribuição | Exemplo |
> |----------|---------|
> | Uma subscrição | `"/subscriptions/{subscriptionId1}"` |
> | Duas subscrições | `"/subscriptions/{subscriptionId1}", "/subscriptions/{subscriptionId2}"` |
> | Grupo de recursos de rede | `"/subscriptions/{subscriptionId1}/resourceGroups/Network"` |
> | Um grupo de gestão | `"/providers/Microsoft.Management/managementGroups/{groupId1}"` |
> | Grupo de gestão e uma subscrição | `"/providers/Microsoft.Management/managementGroups/{groupId1}", /subscriptions/{subscriptionId1}",` |
> | Todos os âmbitos (aplica-se apenas a funções incorporadas) | `"/"` |

Para obter informações sobre `AssignableScopes` funções personalizadas, consulte [as funções personalizadas Azure](custom-roles.md).

## <a name="next-steps"></a>Passos seguintes

* [Funções incorporadas do Azure](built-in-roles.md)
* [Funções personalizadas do Azure](custom-roles.md)
* [Operações de fornecedor de recursos Azure](resource-provider-operations.md)
