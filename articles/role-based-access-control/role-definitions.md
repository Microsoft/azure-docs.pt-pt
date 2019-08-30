---
title: Entender as definições de função no RBAC para recursos do Azure | Microsoft Docs
description: Saiba mais sobre as definições de função no RBAC (controle de acesso baseado em função) para o gerenciamento de acesso refinado dos recursos do Azure.
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
ms.date: 06/18/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: ''
ms.openlocfilehash: 4bf2e057f4c5dad650834f9b42c75be3aedec46e
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/29/2019
ms.locfileid: "70142848"
---
# <a name="understand-role-definitions-for-azure-resources"></a>Entender as definições de função dos recursos do Azure

Se você estiver tentando entender como uma função funciona ou se estiver criando sua própria [função personalizada para recursos do Azure](custom-roles.md), é útil entender como as funções são definidas. Este artigo descreve os detalhes das definições de função e fornece alguns exemplos.

## <a name="role-definition-structure"></a>Estrutura de definição de função

Uma *definição de função* é uma coleção de permissões. Por vezes é denominada apenas *função*. Uma definição de função lista as operações que podem ser efetuadas, por exemplo, ler, escrever e eliminar. Ele também pode listar as operações que não podem ser executadas ou operações relacionadas aos dados subjacentes. Uma definição de função tem a seguinte estrutura:

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

As operações são especificadas com cadeias de caracteres que têm o seguinte formato:

- `{Company}.{ProviderName}/{resourceType}/{action}`

A `{action}` parte de uma cadeia de caracteres de operação especifica o tipo de operações que você pode executar em um tipo de recurso. Por exemplo, você verá as seguintes subcadeias de caracteres `{action}`em:

| Subcadeia de ação    | Descrição         |
| ------------------- | ------------------- |
| `*` | O caractere curinga concede acesso a todas as operações que correspondem à cadeia de caracteres. |
| `read` | Habilita operações de leitura (GET). |
| `write` | Habilita operações de gravação (PUT ou PATCH). |
| `action` | Habilita operações personalizadas como reiniciar máquinas virtuais (POST). |
| `delete` | Habilita operações de exclusão (DELETE). |

Aqui está a definição da função [colaborador](built-in-roles.md#contributor) no formato JSON. A operação curinga`*`() em `Actions` indica que a entidade de segurança atribuída a essa função pode executar todas as ações ou, em outras palavras, pode gerenciar tudo. Isso inclui ações definidas no futuro, pois o Azure adiciona novos tipos de recursos. As operações em `NotActions` são subtraídas `Actions`de. No caso da função de [colaborador](built-in-roles.md#contributor) , `NotActions` o Remove a capacidade dessa função de gerenciar o acesso aos recursos e também atribuir acesso aos recursos.

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

## <a name="management-and-data-operations"></a>Operações de gerenciamento e dados

O `Actions` controle de acesso baseado em função para operações de gerenciamento é especificado `NotActions` nas propriedades e de uma definição de função. Aqui estão alguns exemplos de operações de gerenciamento no Azure:

- Gerenciar o acesso a uma conta de armazenamento
- Criar, atualizar ou excluir um contêiner de BLOBs
- Excluir um grupo de recursos e todos os seus recursos

O acesso de gerenciamento não é herdado para seus dados desde que o método de autenticação de contêiner esteja definido como "conta de usuário do Azure AD" e não "chave de acesso". Essa separação impede que funções com curingas`*`() tenham acesso irrestrito aos seus dados. Por exemplo, se um usuário tiver uma função de [leitor](built-in-roles.md#reader) em uma assinatura, ele poderá exibir a conta de armazenamento, mas, por padrão, eles não poderão exibir os dados subjacentes.

Anteriormente, o controle de acesso baseado em função não era usado para operações de dados. Autorização para operações de dados variadas entre provedores de recursos. O mesmo modelo de autorização de controle de acesso baseado em função usado para operações de gerenciamento foi estendido para operações de dados.

Para dar suporte a operações de dados, novas propriedades de dados foram adicionadas à estrutura de definição de função. As operações de dados são especificadas `DataActions` nas `NotDataActions` Propriedades e. Ao adicionar essas propriedades de dados, a separação entre gerenciamento e dados é mantida. Isso impede que as atribuições de função atuais`*`com curingas () acessem de repente os dados. Aqui estão algumas operações de dados que podem ser especificadas `DataActions` em `NotDataActions`e:

- Ler uma lista de BLOBs em um contêiner
- Gravar um blob de armazenamento em um contêiner
- Excluir uma mensagem em uma fila

Aqui está a definição da função de [leitor de dados do blob de armazenamento](built-in-roles.md#storage-blob-data-reader) , que `Actions` inclui `DataActions` operações nas propriedades e. Essa função permite que você leia o contêiner de BLOBs e também os dados de blob subjacentes.

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

Somente operações de `DataActions` dados podem ser adicionadas às `NotDataActions` Propriedades e. Os provedores de recursos identificam quais operações são operações de dados `isDataAction` , definindo `true`a propriedade como. Para ver uma lista das operações onde `isDataAction` está `true`, consulte operações do provedor de [recursos](resource-provider-operations.md). Não é necessário que as funções que não têm operações de dados `DataActions` tenham `NotDataActions` e propriedades dentro da definição de função.

A autorização para todas as chamadas de API de operação de gerenciamento é tratada pelo Azure Resource Manager. A autorização para chamadas de API de operação de dados é tratada por um provedor de recursos ou Azure Resource Manager.

### <a name="data-operations-example"></a>Exemplo de operações de dados

Para entender melhor como funcionam as operações de gerenciamento e dados, vamos considerar um exemplo específico. Alice recebeu a função de [proprietário](built-in-roles.md#owner) no escopo da assinatura. Bob recebeu a função [colaborador de dados de blob de armazenamento](built-in-roles.md#storage-blob-data-contributor) em um escopo de conta de armazenamento. O diagrama a seguir mostra esse exemplo.

![O controle de acesso baseado em função foi estendido para dar suporte a operações de gerenciamento e de dados](./media/role-definitions/rbac-management-data.png)

A função de [proprietário](built-in-roles.md#owner) para Alice e a função de [colaborador de dados de blob de armazenamento](built-in-roles.md#storage-blob-data-contributor) para Bob têm as seguintes ações:

Owner

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

Como Alice tem uma ação curinga`*`() em um escopo de assinatura, suas permissões herdam para permitir que executem todas as ações de gerenciamento. Alice pode ler, gravar e excluir contêineres. No entanto, Alice não pode executar operações de dados sem executar etapas adicionais. Por exemplo, por padrão, Alice não pode ler os BLOBs dentro de um contêiner. Para ler os BLOBs, Alice teria que recuperar as chaves de acesso de armazenamento e usá-las para acessar os BLOBs.

As permissões de Bob são restritas apenas `Actions` ao `DataActions` e especificadas na função [colaborador de dados do blob de armazenamento](built-in-roles.md#storage-blob-data-contributor) . Com base na função, Bob pode executar operações de gerenciamento e de dados. Por exemplo, Bob pode ler, gravar e excluir contêineres na conta de armazenamento especificada e também pode ler, gravar e excluir os BLOBs.

Para obter mais informações sobre gerenciamento e segurança do plano de dados para armazenamento, consulte o [Guia de segurança do armazenamento do Azure](../storage/common/storage-security-guide.md).

### <a name="what-tools-support-using-rbac-for-data-operations"></a>Quais ferramentas dão suporte ao uso de RBAC para operações de dados?

Para exibir e trabalhar com operações de dados, você deve ter as versões corretas das ferramentas ou dos SDKs:

| Ferramenta  | Version  |
|---------|---------|
| [Azure PowerShell](/powershell/azure/install-az-ps) | 1.1.0 ou posterior |
| [CLI do Azure](/cli/azure/install-azure-cli) | 2.0.30 ou posterior |
| [Azure para .NET](/dotnet/azure/) | 2.8.0-Preview ou posterior |
| [SDK do Azure para Go](/azure/go/azure-sdk-go-install) | 15.0.0 ou posterior |
| [Azure para Java](/java/azure/) | 1.9.0 ou posterior |
| [Azure para Python](/azure/python/) | 0.40.0 ou posterior |
| [Azure SDK for Ruby](https://rubygems.org/gems/azure_sdk) (Azure SDK para Ruby) | 0.17.1 ou posterior |

Para exibir e usar as operações de dados na API REST, você deve definir o parâmetro de **versão de API** para a seguinte versão ou posterior:

- 2018-07-01

## <a name="actions"></a>Ações

A `Actions` permissão especifica as operações de gerenciamento que a função permite executar. É uma coleção de cadeias de caracteres de operação que identificam operações protegíveis de provedores de recursos do Azure. Aqui estão alguns exemplos de operações de gerenciamento que podem ser usadas `Actions`no.

| Cadeia de operação    | Descrição         |
| ------------------- | ------------------- |
| `*/read` | Concede acesso a operações de leitura para todos os tipos de recursos de todos os provedores de recursos do Azure.|
| `Microsoft.Compute/*` | Concede acesso a todas as operações para todos os tipos de recursos no provedor de recursos Microsoft. COMPUTE.|
| `Microsoft.Network/*/read` | Concede acesso a operações de leitura para todos os tipos de recursos no provedor de recursos Microsoft. Network.|
| `Microsoft.Compute/virtualMachines/*` | Concede acesso a todas as operações de máquinas virtuais e seus tipos de recursos filho.|
| `microsoft.web/sites/restart/Action` | Concede acesso para reiniciar um aplicativo Web.|

## <a name="notactions"></a>NotActions

A `NotActions` permissão especifica as operações de gerenciamento que são excluídas do `Actions`permitido. Use a `NotActions` permissão se o conjunto de operações que você deseja permitir for definido com mais facilidade, excluindo operações restritas. O acesso concedido por uma função (permissões efetivas) é calculado subtraindo as `NotActions` operações `Actions` das operações.

> [!NOTE]
> Se um usuário recebe uma função que exclui uma operação no `NotActions`e recebe uma segunda função que concede acesso à mesma operação, o usuário tem permissão para executar essa operação. `NotActions`Não é uma regra de negação – é simplesmente uma maneira conveniente de criar um conjunto de operações permitidas quando operações específicas precisam ser excluídas.
>

## <a name="dataactions"></a>DataActions

A `DataActions` permissão especifica as operações de dados que a função permite que sejam executadas em seus dados dentro desse objeto. Por exemplo, se um usuário tiver acesso de dados de blob de leitura a uma conta de armazenamento, ele poderá ler os BLOBs dentro dessa conta de armazenamento. Aqui estão alguns exemplos de operações de dados que podem ser usadas `DataActions`no.

| Cadeia de operação    | Descrição         |
| ------------------- | ------------------- |
| `Microsoft.Storage/storageAccounts/ blobServices/containers/blobs/read` | Retorna um BLOB ou uma lista de BLOBs. |
| `Microsoft.Storage/storageAccounts/ blobServices/containers/blobs/write` | Retorna o resultado da gravação de um blob. |
| `Microsoft.Storage/storageAccounts/ queueServices/queues/messages/read` | Retorna uma mensagem. |
| `Microsoft.Storage/storageAccounts/ queueServices/queues/messages/*` | Retorna uma mensagem ou o resultado da gravação ou exclusão de uma mensagem. |

## <a name="notdataactions"></a>NotDataActions

A `NotDataActions` permissão especifica as operações de dados que são excluídas do `DataActions`permitido. O acesso concedido por uma função (permissões efetivas) é calculado subtraindo as `NotDataActions` operações `DataActions` das operações. Cada provedor de recursos fornece seu respectivo conjunto de APIs para atender às operações de dados.

> [!NOTE]
> Se um usuário recebe uma função que exclui uma operação de dados no `NotDataActions`e recebe uma segunda função que concede acesso à mesma operação de dados, o usuário tem permissão para executar essa operação de dados. `NotDataActions`Não é uma regra de negação – é simplesmente uma maneira conveniente de criar um conjunto de operações de dados permitidas quando operações de dados específicas precisam ser excluídas.
>

## <a name="assignablescopes"></a>AssignableScopes

A `AssignableScopes` propriedade especifica os escopos (assinaturas, grupos de recursos ou recursos) que têm essa definição de função disponível. Você pode tornar a função disponível para atribuição somente nas assinaturas ou grupos de recursos que a exigem, e não obstruindo a experiência do usuário para o restante das assinaturas ou grupos de recursos. Você deve usar pelo menos uma assinatura, grupo de recursos ou ID de recurso.

As funções internas foram `AssignableScopes` definidas para o escopo raiz (`"/"`). O escopo raiz indica que a função está disponível para atribuição em todos os escopos. Entre os exemplos de escopos atribuíveis válidos estão:

| Cenário | Exemplo |
|----------|---------|
| A função está disponível para atribuição em uma única assinatura | `"/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"` |
| A função está disponível para atribuição em duas assinaturas | `"/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e", "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624"` |
| A função está disponível para atribuição somente no grupo de recursos de rede | `"/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network"` |
| A função está disponível para atribuição em todos os escopos (aplica-se somente a funções internas) | `"/"` |

Para obter informações `AssignableScopes` sobre funções personalizadas, consulte [funções personalizadas para recursos do Azure](custom-roles.md).

## <a name="next-steps"></a>Passos Seguintes

* [Built-in roles for Azure resources](built-in-roles.md) (Funções incorporadas para recursos do Azure)
* [Custom roles for Azure resources](custom-roles.md) (Funções personalizadas para recursos do Azure)
* [Operações do provedor de recursos Azure Resource Manager](resource-provider-operations.md)
