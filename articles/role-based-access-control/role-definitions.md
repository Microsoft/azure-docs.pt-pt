---
title: Compreender as definições de funções no RBAC para recursos do Azure | Documentos da Microsoft
description: Conheça a definições de funções no controlo de acesso baseado em funções (RBAC) para gestão de acessos detalhada dos recursos do Azure.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: ''
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/09/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: ''
ms.openlocfilehash: 7855c2bd45ba35ecb0ede5c60268e6446f37ed5a
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2019
ms.locfileid: "58804535"
---
# <a name="understand-role-definitions-for-azure-resources"></a>Compreender as definições de funções para recursos do Azure

Se estiver a tentar entender como funciona uma função ou se estiver a criar sua própria [função personalizada para recursos do Azure](custom-roles.md), é útil compreender como as funções são definidas. Este artigo descreve os detalhes das definições de funções e fornece alguns exemplos.

## <a name="role-definition-structure"></a>Estrutura de definição de função

Uma *definição de função* é uma coleção de permissões. Por vezes é denominada apenas *função*. Uma definição de função lista as operações que podem ser efetuadas, por exemplo, ler, escrever e eliminar. Também pode listar as operações que não podem ser executadas ou operações relacionadas aos dados subjacentes. Uma definição de função tem a seguinte estrutura:

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

Operações são especificadas com cadeias de caracteres que tem o seguinte formato:

- `{Company}.{ProviderName}/{resourceType}/{action}`

O `{action}` parte de uma cadeia de operação Especifica o tipo de operações que pode executar num tipo de recurso. Por exemplo, verá as seguintes subcadeias de carateres no `{action}`:

| Subcadeia de ação    | Descrição         |
| ------------------- | ------------------- |
| `*` | O caráter universal concede acesso a todas as operações que corresponde à cadeia. |
| `read` | Permite ler operações (GET). |
| `write` | Permite escrever operações (PUT, POST e PATCH). |
| `delete` | Permite elimina operações (eliminar). |

Aqui está o [contribuinte](built-in-roles.md#contributor) definição de função no formato JSON. O caráter universal (`*`) uma operação em `Actions` indica que o principal atribuído a esta função pode efetuar todas as ações ou em outras palavras, ele pode gerir tudo. Isso inclui ações definidas no futuro, o Azure adiciona novos tipos de recursos. As operações sob `NotActions` são subtraído `Actions`. No caso do [contribuinte](built-in-roles.md#contributor) função, `NotActions` remove a capacidade desta função para gerir o acesso aos recursos e também atribuir acesso a recursos.

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
    "Microsoft.Authorization/elevateAccess/Action"
  ],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": [
    "/"
  ]
}
```

## <a name="management-and-data-operations-preview"></a>Operações de gestão e de dados (pré-visualização)

Controlo de acesso baseado em funções para operações de gestão é especificado na `Actions` e `NotActions` propriedades de uma definição de função. Aqui estão alguns exemplos de operações de gestão no Azure:

- Gerir o acesso a uma conta de armazenamento
- Criar, atualizar ou eliminar um contentor de BLOBs
- Eliminar um grupo de recursos e todos os respetivos recursos

Acesso de gestão não é herdado aos seus dados. Essa separação impede que funções com carateres universais (`*`) de ter acesso ilimitado aos seus dados. Por exemplo, se um utilizador tem um [leitor](built-in-roles.md#reader) função numa assinatura, em seguida, podem visualizar a conta de armazenamento, mas por padrão não é possível ver os dados subjacentes.

Anteriormente, o controlo de acesso baseado em funções não foi utilizado para operações de dados. Autorização para operações de dados diversificados em fornecedores de recursos. O mesmo modelo de autorização de controle com base em função de acesso utilizado para operações de gestão tiver sido expandido para operações de dados (atualmente em pré-visualização).

Para suportar operações de dados, foram adicionadas novas propriedades de dados para a estrutura de definição de função. Operações de dados estão especificadas na `DataActions` e `NotDataActions` propriedades. Ao adicionar essas propriedades de dados, a separação entre a gestão e de dados é mantida. Isto impede que as atribuições de funções atual com carateres universais (`*`), de repente, tenha acesso a dados. Aqui estão algumas operações de dados que podem ser especificadas em `DataActions` e `NotDataActions`:

- Ler uma lista de blobs num contentor
- Escrever um blob de armazenamento num contentor
- Eliminar uma mensagem numa fila

Aqui está o [leitor de dados de Blob de armazenamento](built-in-roles.md#storage-blob-data-reader) definição de função, que inclui operações em ambos os `Actions` e `DataActions` propriedades. Esta função permite-lhe ler o contentor de BLOBs e também os dados de blob subjacente.

```json
{
  "Name": "Storage Blob Data Reader",
  "Id": "2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
  "IsCustom": false,
  "Description": "Allows for read access to Azure Storage blob containers and data",
  "Actions": [
    "Microsoft.Storage/storageAccounts/blobServices/containers/read"
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

Apenas as operações de dados podem ser adicionadas para o `DataActions` e `NotDataActions` propriedades. Fornecedores de recursos identificar as operações são operações de dados, definindo a `isDataAction` propriedade `true`. Para ver uma lista das operações em que `isDataAction` é `true`, consulte [operações de fornecedor de recursos](resource-provider-operations.md). Funções que não têm as operações de dados não devem ter `DataActions` e `NotDataActions` propriedades dentro da definição de função.

Autorização para todas as chamadas de API de operação de gestão é processada pelo Azure Resource Manager. Autorização para chamadas de API de operação de dados é processada por um fornecedor de recursos ou o Azure Resource Manager.

### <a name="data-operations-example"></a>Exemplo de operações de dados

Para compreender melhor como funcionam as operações de gestão e os dados, vamos considerar um exemplo específico. Alice foi atribuída a [proprietário](built-in-roles.md#owner) função no âmbito da subscrição. BOB foi atribuído a [contribuinte de dados de Blob de armazenamento](built-in-roles.md#storage-blob-data-contributor) função num âmbito de conta de armazenamento. O diagrama seguinte mostra este exemplo.

![Controlo de acesso baseado em funções foi expandido para suportar a gestão e operações de dados](./media/role-definitions/rbac-management-data.png)

O [proprietário](built-in-roles.md#owner) função para Alice e o [contribuinte de dados de Blob de armazenamento](built-in-roles.md#storage-blob-data-contributor) função para o Bernardo tem as seguintes ações:

Proprietário

&nbsp;&nbsp;&nbsp;&nbsp;Ações<br>
&nbsp;&nbsp;&nbsp;&nbsp;`*`

Contribuinte de Dados do Armazenamento de Blobs

&nbsp;&nbsp;&nbsp;&nbsp;Ações<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/delete`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/read`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/write`<br>
&nbsp;&nbsp;&nbsp;&nbsp;DataActions<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write`

Uma vez que a Alice tem um caráter universal (`*`) ação num âmbito de subscrição, suas permissões herdam para permitir-lhe efetuar todas as ações de gestão. Alice pode ler, escrever e eliminar contentores. No entanto, Alice não é possível efetuar operações de dados sem colocar os passos adicionais. Por exemplo, por predefinição, Alice não é possível ler os blobs num contentor. Para ler os blobs, Alice teria-se obter as chaves de acesso de armazenamento e usá-los para aceder aos blobs.

Permissões de Bob estão limitadas a apenas o `Actions` e `DataActions` especificado na [contribuinte de dados de Blob de armazenamento](built-in-roles.md#storage-blob-data-contributor) função. Com base na função, Bob pode executar gerenciamento e operações de dados. Por exemplo, Bob poderá ler, escrever e eliminar os contentores na conta de armazenamento especificada e também ele pode ler, escrever e eliminar os blobs.

Para obter mais informações sobre a gestão e segurança de plano de dados de armazenamento, consulte a [guia de segurança do armazenamento do Azure](../storage/common/storage-security-guide.md).

### <a name="what-tools-support-using-rbac-for-data-operations"></a>Que ferramentas de suporte através do RBAC para operações de dados?

Para visualizar e trabalhar com operações de dados, tem de ter as versões corretas do ferramentas ou SDKs:

| Ferramenta  | Versão  |
|---------|---------|
| [Azure PowerShell](/powershell/azure/install-az-ps) | 1.1.0 ou posterior |
| [CLI do Azure](/cli/azure/install-azure-cli) | 2.0.30 ou posterior |
| [Azure para .NET](/dotnet/azure/) | 2.8.0-Preview ou posterior |
| [Azure SDK para Go](/go/azure/azure-sdk-go-install) | 15.0.0 ou posterior |
| [Azure para Java](/java/azure/) | 1.9.0 ou posterior |
| [Azure para Python](/python/azure) | 0.40.0 ou posterior |
| [Azure SDK for Ruby](https://rubygems.org/gems/azure_sdk) (Azure SDK para Ruby) | 0.17.1 ou posterior |

Para ver e utilizar as operações de dados na REST API, tem de definir o **versão de api** parâmetro para a versão seguinte ou posterior:

- 2018-01-01-pré-visualização

O portal do Azure também permite aos utilizadores procurar e gerir o conteúdo das filas e BLOBs experiência de pré-visualização de contentores através do Azure AD. Ver e gerir o conteúdo de um clique de contentor de BLOBs ou filas a **explorar dados com o Azure AD preview** na descrição geral da conta de armazenamento.

![Explore as filas e BLOBs contentores com a pré-visualização do Azure AD](./media/role-definitions/rbac-dataactions-browsing.png)

## <a name="actions"></a>Ações

O `Actions` permissão Especifica as operações de gestão que permite que a função a ser executada. É uma coleção de cadeias de caracteres de operação que identificar as operações com capacidade de segurança de fornecedores de recursos do Azure. Aqui estão alguns exemplos de operações de gestão que podem ser utilizados em `Actions`.

| Cadeia de caracteres de operação    | Descrição         |
| ------------------- | ------------------- |
| `*/read` | Concede acesso para operações de leitura para todos os tipos de recursos de todos os fornecedores de recursos do Azure.|
| `Microsoft.Compute/*` | Concede acesso a todas as operações para todos os tipos de recurso no fornecedor de recursos Microsoft. Compute.|
| `Microsoft.Network/*/read` | Concede acesso para operações de leitura para todos os tipos de recursos de fornecedor de recursos de Network.|
| `Microsoft.Compute/virtualMachines/*` | Concede acesso a todas as operações de máquinas virtuais e respetivos subordinados tipos de recursos.|
| `microsoft.web/sites/restart/Action` | Concede acesso para reiniciar uma aplicação web.|

## <a name="notactions"></a>NotActions

O `NotActions` permissão Especifica as operações de gestão que são excluídas da permitidos `Actions`. Utilize o `NotActions` permissão se o conjunto de operações que pretende permitir que mais facilmente é definido por excluindo restritas de operações. O acesso concedido por uma função (permissões efetivas) é calculado ao subtrair a `NotActions` operações a partir da `Actions` operações.

> [!NOTE]
> Se um utilizador é atribuído uma função que exclui uma operação em `NotActions`e é atribuído uma segunda função concede acesso à mesma operação, o utilizador tem permissão para executar essa operação. `NotActions` Não é uma negação de regra – é simplesmente uma forma conveniente para criar um conjunto de operações permitidas quando precisam de operações específicas a serem excluídos.
>

## <a name="dataactions-preview"></a>dataActions (pré-visualização)

O `DataActions` permissão Especifica as operações de dados que permite que a função a ser executada aos seus dados dentro desse objeto. Por exemplo, se um utilizador tem de ler BLOBs acesso a dados para uma conta de armazenamento, em seguida, podem ler os blobs dentro dessa conta de armazenamento. Aqui estão alguns exemplos de operações de dados que podem ser utilizados em `DataActions`.

| Cadeia de caracteres de operação    | Descrição         |
| ------------------- | ------------------- |
| `Microsoft.Storage/storageAccounts/ blobServices/containers/blobs/read` | Devolve um blob ou uma lista de blobs. |
| `Microsoft.Storage/storageAccounts/ blobServices/containers/blobs/write` | Devolve o resultado de escrever um blob. |
| `Microsoft.Storage/storageAccounts/ queueServices/queues/messages/read` | Devolve uma mensagem. |
| `Microsoft.Storage/storageAccounts/ queueServices/queues/messages/*` | Devolve uma mensagem ou o resultado de escrever ou eliminar uma mensagem. |

## <a name="notdataactions-preview"></a>notDataActions (pré-visualização)

O `NotDataActions` permissão Especifica as operações de dados que são excluídas da permitidos `DataActions`. O acesso concedido por uma função (permissões efetivas) é calculado ao subtrair a `NotDataActions` operações a partir da `DataActions` operações. Cada fornecedor de recursos fornece seu respectivo conjunto de APIs para realizar operações de dados.

> [!NOTE]
> Se um utilizador é atribuído uma função que exclui uma operação de dados no `NotDataActions`e é atribuído uma segunda função concede acesso à mesma operação de dados, o utilizador tem permissão para executar essa operação de dados. `NotDataActions` Não é uma negação de regra – é simplesmente uma forma conveniente para criar um conjunto de operações de dados permitido quando precisam de operações de dados específicas a serem excluídos.
>

## <a name="assignablescopes"></a>AssignableScopes

O `AssignableScopes` propriedade especifica os âmbitos (subscrições, grupos de recursos ou recursos) que a função está disponível para atribuição. Pode disponibilizar a função para atribuição apenas a subscrições ou grupos de recursos que exigem e não pelo usuário desordem experiência para o restante do subscrições ou grupos de recursos. Tem de utilizar, pelo menos, uma subscrição, grupo de recursos ou ID de recurso.

Funções incorporadas têm `AssignableScopes` definido como o âmbito de raiz (`"/"`). O âmbito de raiz indica que a função está disponível para atribuição em todos os âmbitos. Exemplos de âmbitos atribuíveis válidos incluem:

| Cenário | Exemplo |
|----------|---------|
| Função está disponível para atribuição numa única subscrição | `"/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"` |
| Função está disponível para atribuição em duas subscrições | `"/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e", "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624"` |
| Função está disponível para atribuição apenas no grupo de recursos de rede | `"/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network"` |
| Função está disponível para atribuição em todos os âmbitos | `"/"` |

Para obter informações sobre `AssignableScopes` para funções personalizadas, consulte [funções personalizadas para recursos do Azure](custom-roles.md).

## <a name="next-steps"></a>Passos Seguintes

* [Built-in roles for Azure resources](built-in-roles.md) (Funções incorporadas para recursos do Azure)
* [Custom roles for Azure resources](custom-roles.md) (Funções personalizadas para recursos do Azure)
* [Operações de fornecedor de recursos do Azure Resource Manager](resource-provider-operations.md)
