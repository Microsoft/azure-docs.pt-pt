---
title: Compreender definições de papéis azure - Azure RBAC
description: Conheça as definições de papel azure no controlo de acesso baseado em funções azure (Azure RBAC) para gestão de acesso de grãos finos de recursos Azure.
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
ms.date: 04/17/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: ''
ms.openlocfilehash: 03edb8e5c58f0fe746921d50ab3f657f291d16da
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/03/2020
ms.locfileid: "82735543"
---
# <a name="understand-azure-role-definitions"></a>Compreender definições de papéis de Azure

Se está a tentar perceber como funciona um papel azure ou se está a criar o seu próprio [papel personalizado azure,](custom-roles.md)é útil perceber como os papéis são definidos. Este artigo descreve os detalhes das definições de papéis e fornece alguns exemplos.

## <a name="role-definition"></a>Definição de função

Uma *definição de função* é uma coleção de permissões. Por vezes é denominada apenas *função*. Uma definição de função lista as operações que podem ser efetuadas, por exemplo, ler, escrever e eliminar. Pode também listar as operações que não podem ser efetuadas ou operações relacionadas a dados subjacentes. Uma definição de função tem as seguintes propriedades:

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

| Propriedade | Descrição |
| --- | --- |
| `Name` | O nome da exibição do papel. |
| `Id` | A identificação única do papel. |
| `IsCustom` | Indica se este é um papel personalizado. Pronto `true` para papéis personalizados. |
| `Description` | A descrição do papel. |
| `Actions` | Uma série de cordas que especifica as operações de gestão que a função permite ser executadas. |
| `NotActions` | Um conjunto de cordas que especifica as operações de `Actions`gestão que estão excluídas das permitidas . |
| `DataActions` | Um conjunto de cordas que especifica as operações de dados que a função permite ser executada aos seus dados dentro desse objeto. |
| `NotDataActions` | Um conjunto de cordas que especifica as operações de `DataActions`dados que estão excluídas das permitidas . |
| `AssignableScopes` | Uma série de cordas que especifica os âmbitos que a função está disponível para atribuição. |

### <a name="operations-format"></a>Formato de operações

As operações são especificadas com cordas que têm o seguinte formato:

- `{Company}.{ProviderName}/{resourceType}/{action}`

A `{action}` parte de uma cadeia de operação especifica o tipo de operações que pode executar num tipo de recurso. Por exemplo, verá as seguintes `{action}`subcordas em:

| Substring de ação    | Descrição         |
| ------------------- | ------------------- |
| `*` | O personagem wildcard dá acesso a todas as operações que correspondam à corda. |
| `read` | Permite a leitura de operações (GET). |
| `write` | Ativa as operações de escrita (PUT ou PATCH). |
| `action` | Permite operações personalizadas como reiniciar máquinas virtuais (POST). |
| `delete` | Ativa eliminar operações (DELETE). |

### <a name="role-definition-example"></a>Exemplo de definição de papel

Aqui está a definição de papel [do Colaborador](built-in-roles.md#contributor) no formato JSON. A operação de caráter universal (`*`) em `Actions` indica que o principal atribuído a esta função pode efetuar todas as ações ou, por outras palavras, pode gerir tudo. Isto inclui ações definidas no futuro, uma vez que o Azure adiciona novos tipos de recursos. As operações em `NotActions` são subtraídas de `Actions`. No caso da função [Contribuidor](built-in-roles.md#contributor), `NotActions` remove a capacidade desta função para gerir o acesso aos recursos e também atribuir acesso aos recursos.

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

## <a name="management-and-data-operations"></a>Gestão e operações de dados

O controlo de acesso baseado em funções para operações de gestão é especificado nas `Actions` propriedades e `NotActions` propriedades de uma definição de função. Aqui estão alguns exemplos de operações de gestão em Azure:

- Gerir o acesso a uma conta de armazenamento
- Criar, atualizar ou eliminar um recipiente de bolhas
- Eliminar um grupo de recursos e todos os seus recursos

O acesso à gestão não é herdado dos seus dados desde que o método de autenticação do contentor esteja definido como "Conta de Utilizador Azure AD" e não "Chave de Acesso". Esta separação impede que`*`as funções com wildcards () tenham acesso ilimitado aos seus dados. Por exemplo, se um utilizador tem uma função [de Leitor](built-in-roles.md#reader) numa subscrição, então pode ver a conta de armazenamento, mas por padrão não podem ver os dados subjacentes.

Anteriormente, o controlo de acesso baseado em funções não era utilizado para operações de dados. A autorização para operações de dados variou entre os fornecedores de recursos. O mesmo modelo de autorização de controlo de acesso baseado em funções utilizado para operações de gestão foi alargado às operações de dados.

Para apoiar as operações de dados, foram adicionadas novas propriedades de dados à definição de funções. As operações de dados são especificadas nas propriedades `DataActions` e `NotDataActions`. Ao adicionar estas propriedades de dados, mantém-se a separação entre gestão e dados. Tal impede que as atribuições de função atuais com carateres universais (`*`) tenham, de repente, acesso aos dados. Aqui estão algumas operações de dados que podem ser especificadas em `DataActions` e `NotDataActions`:

- Ler uma lista de blobs num contentor
- Escrever um blob de armazenamento num contentor
- Eliminar uma mensagem numa fila

Aqui está a definição de função de Leitor `Actions` de `DataActions` Dados blob de [armazenamento,](built-in-roles.md#storage-blob-data-reader) que inclui operações tanto nas propriedades como nas propriedades. Esta função permite-lhe ler o recipiente blob e também os dados de blob subjacentes.

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

Apenas as operações de dados podem ser adicionadas às propriedades `DataActions` e `NotDataActions`. Os fornecedores de recursos identificam quais `isDataAction` as `true`operações de dados, fixando o imóvel para . Para ver uma lista `isDataAction` das `true`operações onde está, consulte [as operações](resource-provider-operations.md)do fornecedor de recursos. As funções que não possuem operações de dados não são obrigadas a ter `DataActions` e `NotDataActions` propriedades dentro da definição de funções.

A autorização para toda a operação de gestão As chamadas API são tratadas pelo Gestor de Recursos azure. A autorização para a operação de dados as chamadas API são tratadas por um fornecedor de recursos ou por um Gestor de Recursos Azure.

### <a name="data-operations-example"></a>Exemplo de operações de dados

Para entender melhor como funcionam as operações de gestão e dados, vamos considerar um exemplo específico. Alice foi designada para o papel de [Proprietário](built-in-roles.md#owner) no âmbito da subscrição. Bob foi designado o papel de Contribuinte de [Dados blob de armazenamento](built-in-roles.md#storage-blob-data-contributor) em um âmbito de conta de armazenamento. O diagrama que se segue mostra este exemplo.

![O controlo de acesso baseado em funções foi alargado para apoiar tanto as operações de gestão como de dados](./media/role-definitions/rbac-management-data.png)

O papel [de Proprietário](built-in-roles.md#owner) para Alice e o papel de Colaborador de Dados de [Armazenamento Blob](built-in-roles.md#storage-blob-data-contributor) para Bob têm as seguintes ações:

Proprietário

&nbsp;&nbsp;&nbsp;&nbsp;Ações<br>
&nbsp;&nbsp;&nbsp;&nbsp;`*`

Contribuinte de dados blob de armazenamento

&nbsp;&nbsp;&nbsp;&nbsp;Ações<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/delete`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/read`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/write`<br>
&nbsp;&nbsp;&nbsp;&nbsp;Ações de Dados<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write`

Uma vez que`*`Alice tem uma ação wildcard ( ) em um âmbito de subscrição, suas permissões herdam para baixo para permitir-lhes executar todas as ações de gestão. Alice pode ler, escrever e apagar recipientes. No entanto, Alice não pode realizar operações de dados sem tomar medidas adicionais. Por exemplo, por defeito, Alice não consegue ler as bolhas dentro de um recipiente. Para ler as bolhas, Alice teria que recuperar as chaves de acesso ao armazenamento e usá-las para aceder às bolhas.

As permissões do Bob limitam-se apenas ao `Actions` e `DataActions` especificado na função de Contribuinte de Dados blob de [armazenamento.](built-in-roles.md#storage-blob-data-contributor) Com base no papel, bob pode executar operações de gestão e dados. Por exemplo, Bob pode ler, escrever e apagar recipientes na conta de armazenamento especificada e também pode ler, escrever e apagar as bolhas.

Para obter mais informações sobre gestão e segurança de aviões de dados para armazenamento, consulte o guia de [segurança azure Storage](../storage/blobs/security-recommendations.md).

### <a name="what-tools-support-using-azure-roles-for-data-operations"></a>Que ferramentas suportam a utilização de funções Azure para operações de dados?

Para visualizar e trabalhar com operações de dados, deve ter as versões corretas das ferramentas ou SDKs:

| Ferramenta  | Versão  |
|---------|---------|
| [Azure PowerShell](/powershell/azure/install-az-ps) | 1.1.0 ou mais tarde |
| [CLI do Azure](/cli/azure/install-azure-cli) | 2.0.30 ou mais tarde |
| [Azure para .NET](/dotnet/azure/) | 2.8.0 pré-visualização ou posterior |
| [SDK do Azure para Go](/azure/go/azure-sdk-go-install) | 15.0.0 ou mais tarde |
| [Azure para Java](/java/azure/) | 1.9.0 ou mais tarde |
| [Azure para Python](/azure/python/) | 0.40.0 ou mais tarde |
| [SDK do Azure para Ruby](https://rubygems.org/gems/azure_sdk) | 0.17.1 ou mais tarde |

Para visualizar e utilizar as operações de dados na API REST, deve definir o parâmetro **da versão api** para a seguinte versão ou posteriormente:

- 2018-07-01

## <a name="actions"></a>Actions (Ações)

A `Actions` permissão especifica as operações de gestão que a função permite ser executada. Trata-se de uma coleção de cadeias de operação que identificam operações de segurança dos fornecedores de recursos azure. Aqui estão alguns exemplos de `Actions`operações de gestão que podem ser usadas em .

> [!div class="mx-tableFixed"]
> | Cadeia de operação    | Descrição         |
> | ------------------- | ------------------- |
> | `*/read` | Concede acesso a operações de leitura para todos os tipos de recursos de todos os fornecedores de recursos Azure.|
> | `Microsoft.Compute/*` | Concede acesso a todas as operações para todos os tipos de recursos do fornecedor de recursos Microsoft.Compute.|
> | `Microsoft.Network/*/read` | Concede acesso a operações de leitura para todos os tipos de recursos no fornecedor de recursos Microsoft.Network.|
> | `Microsoft.Compute/virtualMachines/*` | Concede acesso a todas as operações de máquinas virtuais e aos seus tipos de recursos infantis.|
> | `microsoft.web/sites/restart/Action` | Concede acesso a reiniciar uma aplicação web.|

## <a name="notactions"></a>NotActions

A `NotActions` permissão especifica as operações de `Actions`gestão que estão excluídas das permitidas . Utilize `NotActions` a permissão se o conjunto de operações que pretende permitir for mais facilmente definido excluindo operações restritas. O acesso concedido por uma função (permissões eficazes) é calculado subtraindo as `NotActions` operações das `Actions` operações.

> [!NOTE]
> Se um utilizador for atribuído a uma `NotActions`função que exclua uma operação em , e lhe for atribuída uma segunda função que conceda acesso à mesma operação, o utilizador é autorizado a realizar essa operação. `NotActions`não é uma regra de negação – é simplesmente uma forma conveniente de criar um conjunto de operações permitidas quando é necessário excluir operações específicas.
>

## <a name="dataactions"></a>Ações de Dados

A `DataActions` permissão especifica as operações de dados que a função permite ser executada aos seus dados dentro desse objeto. Por exemplo, se um utilizador tiver lido o acesso dos dados blob a uma conta de armazenamento, então pode ler as bolhas dentro dessa conta de armazenamento. Aqui estão alguns exemplos de `DataActions`operações de dados que podem ser usadas em .

> [!div class="mx-tableFixed"]
> | Cadeia de operação    | Descrição         |
> | ------------------- | ------------------- |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read` | Devolve uma bolha ou uma lista de bolhas. |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write` | Devolve o resultado de escrever uma bolha. |
> | `Microsoft.Storage/storageAccounts/queueServices/queues/messages/read` | Devolve uma mensagem. |
> | `Microsoft.Storage/storageAccounts/queueServices/queues/messages/*` | Devolve uma mensagem ou o resultado de escrever ou apagar uma mensagem. |

## <a name="notdataactions"></a>NotDataActions

A `NotDataActions` permissão especifica as operações de `DataActions`dados que estão excluídas das permitidas . O acesso concedido por uma função (permissões eficazes) é calculado subtraindo as `NotDataActions` operações das `DataActions` operações. Cada fornecedor de recursos fornece o seu respetivo conjunto de APIs para cumprir as operações de dados.

> [!NOTE]
> Se um utilizador for atribuído a uma função que exclua uma operação de dados em `NotDataActions`, e lhe for atribuída uma segunda função que conceda acesso à mesma operação de dados, o utilizador é autorizado a executar essa operação de dados. `NotDataActions`não é uma regra de negação – é simplesmente uma forma conveniente de criar um conjunto de operações de dados permitidas quando as operações específicas de dados precisam de ser excluídas.
>

## <a name="assignablescopes"></a>Scopes Alsinuáveis

A `AssignableScopes` propriedade especifica os âmbitos (grupos de gestão, subscrições ou grupos de recursos) que têm esta definição de função disponível. Pode disponibilizar o papel para atribuição apenas nos grupos de gestão, subscrições ou grupos de recursos que o exijam. Deve utilizar pelo menos um grupo de gestão, subscrição ou grupo de recursos.

As funções `AssignableScopes` incorporadas definiram-se no âmbito raiz ().`"/"` O âmbito de raiz indica que o papel está disponível para atribuição em todos os âmbitos. Exemplos de âmbitos atribuídos válidos incluem:

> [!div class="mx-tableFixed"]
> | O papel está disponível para atribuição | Exemplo |
> |----------|---------|
> | Uma subscrição | `"/subscriptions/{subscriptionId1}"` |
> | Duas assinaturas | `"/subscriptions/{subscriptionId1}", "/subscriptions/{subscriptionId2}"` |
> | Grupo de recursos de rede | `"/subscriptions/{subscriptionId1}/resourceGroups/Network"` |
> | Um grupo de gestão | `"/providers/Microsoft.Management/managementGroups/{groupId1}"` |
> | Grupo de gestão e uma subscrição | `"/providers/Microsoft.Management/managementGroups/{groupId1}", /subscriptions/{subscriptionId1}",` |
> | Todos os âmbitos (aplicam-se apenas às funções incorporadas) | `"/"` |

Para obter `AssignableScopes` informações sobre funções personalizadas, consulte [papéis personalizados do Azure.](custom-roles.md)

## <a name="next-steps"></a>Passos seguintes

* [Papéis azure embutidos](built-in-roles.md)
* [Papéis personalizados do Azure](custom-roles.md)
* [Operações de fornecedor de recursos do Gestor de Recursos Azure](resource-provider-operations.md)
