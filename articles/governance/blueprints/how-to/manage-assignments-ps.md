---
title: Como gerir atribuições com a PowerShell
description: Saiba como gerir as atribuições de plantas com o módulo oficial de PowerShell da Azure Blueprints, Az.Blueprint.
ms.date: 09/30/2019
ms.topic: how-to
ms.openlocfilehash: 765ed5e1849443d6ac73fe4507327e97e4fdc4c2
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78364027"
---
# <a name="how-to-manage-assignments-with-powershell"></a>Como gerir atribuições com a PowerShell

Uma atribuição de plantas pode ser gerida utilizando o módulo **Az.Blueprint** Azure PowerShell. O módulo suporta a busca, criação, atualização e remoção de tarefas. O módulo também pode obter detalhes sobre as definições de plantas existentes. Este artigo cobre como instalar o módulo e começar a usá-lo.

## <a name="add-the-azblueprint-module"></a>Adicione o módulo Az.Blueprint

Para permitir que o Azure PowerShell gere as atribuições de plantas, o módulo deve ser adicionado. Este módulo pode ser utilizado com powerShell instalado localmente, com [Azure Cloud Shell,](https://shell.azure.com)ou com a [imagem Azure PowerShell Docker](https://hub.docker.com/r/azuresdk/azure-powershell/).

### <a name="base-requirements"></a>Requisitos de base

O módulo Azure Blueprints requer o seguinte software:

- Azure PowerShell 1.5.0 ou superior. Se não estiver ainda instalado, siga [estas instruções](/powershell/azure/install-az-ps).
- PowerShellGet 2.0.1 ou superior. Se não estiver ainda instalado ou atualizado, siga [estas instruções](/powershell/scripting/gallery/installing-psget).

### <a name="install-the-module"></a>Instalar o módulo

O módulo Blueprints para PowerShell é **Az.Blueprint**.

1. A partir de um pedido **administrativo** da PowerShell, executar o seguinte comando:

   ```azurepowershell-interactive
   # Install the Blueprints module from PowerShell Gallery
   Install-Module -Name Az.Blueprint
   ```

   > [!NOTE]
   > Se as contas já estiverem **instaladas,** poderá ser necessário utilizar `-AllowClobber` para forçar a instalação.

1. Validar que o módulo foi importado e é a versão correta (0.2.6):

   ```azurepowershell-interactive
   # Get a list of commands for the imported Az.Blueprint module
   Get-Command -Module 'Az.Blueprint' -CommandType 'Cmdlet'
   ```

## <a name="get-blueprint-definitions"></a>Obter definições de plantas

O primeiro passo para trabalhar com uma atribuição é muitas vezes obter uma referência a uma definição de planta.
O `Get-AzBlueprint` cmdlet obtém uma ou mais definições de plantas. O cmdlet pode obter definições de plantas de um grupo de gestão com `-ManagementGroupId {mgId}` ou uma subscrição com `-SubscriptionId {subId}`. O parâmetro **nome** obtém uma definição de planta, mas deve ser utilizado com **ManagementGroupId** ou **SubscriptionId**. **A versão** pode ser usada com **o Nome** para ser mais explícito sobre qual a definição de planta devolvida. Em vez de **Versão,** o switch `-LatestPublished` agarra a versão mais recente publicada.

O exemplo seguinte utiliza `Get-AzBlueprint` para obter todas as versões de uma definição de projeto denominada "subscrição de definição de plantas de 101 plantas" a partir de uma subscrição específica representada como `{subId}`:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get all versions of the blueprint definition in the specified subscription
$blueprints = Get-AzBlueprint -SubscriptionId '{subId}' -Name '101-blueprints-definition-subscription'

# Display the blueprint definition object
$blueprints
```

A saída de exemplo para uma definição de planta com várias versões é assim:

```output
Name                 : 101-blueprints-definition-subscription
Id                   : /subscriptions/{subId}/providers/Microsoft.Blueprint/blueprints/101
                       -blueprints-definition-subscription
DefinitionLocationId : {subId}
Versions             : {1.0, 1.1}
TimeCreated          : 2019-02-25
TargetScope          : Subscription
Parameters           : {storageAccount_storageAccountType, storageAccount_location,
                       allowedlocations_listOfAllowedLocations, [Usergrouporapplicationname]:Reader_RoleAssignmentName}
ResourceGroups       : ResourceGroup
```

Os [parâmetros](../concepts/parameters.md#blueprint-parameters) da planta na definição de planta podem ser expandidos para fornecer mais informações.

```azurepowershell-interactive
$blueprints.Parameters
```

```output
Key                                                    Value
---                                                    -----
storageAccount_storageAccountType                      Microsoft.Azure.Commands.Blueprint.Models.PSParameterDefinition
storageAccount_location                                Microsoft.Azure.Commands.Blueprint.Models.PSParameterDefinition
allowedlocations_listOfAllowedLocations                Microsoft.Azure.Commands.Blueprint.Models.PSParameterDefinition
[Usergrouporapplicationname]:Reader_RoleAssignmentName Microsoft.Azure.Commands.Blueprint.Models.PSParameterDefinition
```

## <a name="get-blueprint-assignments"></a>Obtenha tarefas de plantas

Se a atribuição da planta já existir, pode obter uma referência com o `Get-AzBlueprintAssignment` cmdlet. O cmdlet toma o **SubscriptionId** e **o Nome** como parâmetros opcionais. Se o **SubscriptionId** não for especificado, o contexto de subscrição atual é utilizado.

O exemplo seguinte utiliza `Get-AzBlueprintAssignment` para obter uma única atribuição de projeto denominada "Grupos de recursos de bloqueio de atribuição" a partir de uma subscrição específica representada como `{subId}`:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get the blueprint assignment in the specified subscription
$blueprintAssignment = Get-AzBlueprintAssignment -SubscriptionId '{subId}' -Name 'Assignment-lock-resource-groups'

# Display the blueprint assignment object
$blueprintAssignment
```

A saída de exemplo para uma atribuição de plantas é assim:

```output
Name              : Assignment-lock-resource-groups
Id                : /subscriptions/{subId}/providers/Microsoft.Blueprint/blueprintAssignme
                    nts/Assignment-lock-resource-groups
Scope             : /subscriptions/{subId}
LastModified      : 2019-02-19
LockMode          : AllResourcesReadOnly
ProvisioningState : Succeeded
Parameters        :
ResourceGroups    : ResourceGroup
```

## <a name="create-blueprint-assignments"></a>Criar tarefas de plantas

Se a atribuição da planta ainda não existir, pode criá-la com o `New-AzBlueprintAssignment` cmdlet. Este cmdlet utiliza os seguintes parâmetros:

- **Nome** [obrigatório]
  - Especifica o nome da atribuição do projeto
  - Deve ser único e já não existe no **SubscriptionId**
- **Planta** [necessária]
  - Especifica a definição de projeto para atribuir
  - Use `Get-AzBlueprint` para obter o objeto de referência
- **Localização** [necessária]
  - Especifica a região para o objeto de implantação de identidade gerida e de subscrição atribuído pelo sistema a criar em
- **Assinatura** (opcional)
  - Especifica a subscrição a que a atribuição está implantada para
  - Caso não seja fornecido, predefini-se do contexto de subscrição atual
- **Bloqueio** (opcional)
  - Define o bloqueio do [recurso de projeto](../concepts/resource-locking.md) para utilizar para recursos implantados
  - Opções suportadas: _Nenhuma_, _AllResourcesReadOnly_, _AllResourcesDoNotDelete_
  - Se não for fornecido, não presta a _dever_ a None
- **Identidade Atribuída ao Sistema (opcional)**
  - Selecione criar uma identidade gerida atribuída pelo sistema para a atribuição e para implementar os recursos
  - Padrão para o conjunto de parâmetros de "identidade"
  - Não pode ser usado com **userAssignedIdentity**
- **UserAssignedIdentity** (opcional)
  - Especifica a identidade gerida atribuída pelo utilizador para utilizar para a atribuição e para a implantação dos recursos
  - Parte do conjunto de parâmetros de "identidade"
  - Não pode ser usado com **SystemAssignedIdentity**
- **Parâmetro** (opcional)
  - Uma tabela de [hash](/powershell/module/microsoft.powershell.core/about/about_hash_tables) de pares chave/valor para definir [parâmetros dinâmicos](../concepts/parameters.md#dynamic-parameters) na atribuição da planta
  - Padrão para um parâmetro dinâmico é o **padrãoValue** na definição
  - Se um parâmetro não for fornecido e não tiver **valor padrão,** o parâmetro não é opcional

    > [!NOTE]
    > **O parâmetro** não suporta cordas seguras.

- **ResourceGroupParameter** (opcional)
  - Uma tabela de [hash](/powershell/module/microsoft.powershell.core/about/about_hash_tables) de artefactos de grupo de recursos
  - Cada espaço reservado para artefactos de grupo de recursos tem pares chave/valor para definir dinamicamente **nome** e **localização** nesse artefacto do grupo de recursos
  - Se um parâmetro de grupo de recursos não for fornecido e não tiver **defaultValue,** o parâmetro do grupo de recursos não é opcional
- **AssignmentFile** (opcional)
  - O caminho para uma representação de arquivo JSON de uma atribuição de plantas
  - Este parâmetro faz parte de um conjunto de parâmetros PowerShell que inclui apenas **nome,** **planta,** e **Subscrição Id,** além dos parâmetros comuns.

### <a name="example-1-provide-parameters"></a>Exemplo 1: Fornecer parâmetros

O exemplo seguinte cria uma nova atribuição da versão '1.1' da definição de projeto 'my-blueprint' rebuscada com `Get-AzBlueprint`, define a identidade gerida e a localização do objeto de atribuição para 'westus2', bloqueia os recursos com _allResourcesReadOnly,_ e define as tabelas de hash para **parâmetro** e **ResourceGroupParameter** em subscrição específica representada como `{subId}`:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get version '1.1' of the blueprint definition in the specified subscription
$bpDefinition = Get-AzBlueprint -SubscriptionId '{subId}' -Name 'my-blueprint' -Version '1.1'

# Create the hash table for Parameters
$bpParameters = @{storageAccount_storageAccountType='Standard_GRS'}

# Create the hash table for ResourceGroupParameters
# ResourceGroup is the resource group artifact placeholder name
$bpRGParameters = @{ResourceGroup=@{name='storage_rg';location='westus2'}}

# Create the new blueprint assignment
$bpAssignment = New-AzBlueprintAssignment -Name 'my-blueprint-assignment' -Blueprint $bpDefinition `
    -SubscriptionId '{subId}' -Location 'westus2' -Lock AllResourcesReadOnly `
    -Parameter $bpParameters -ResourceGroupParameter $bpRGParameters
```

A saída de exemplo para criar uma atribuição de plantas é assim:

```output
Name              : my-blueprint-assignment
Id                : /subscriptions/{subId}/providers/Microsoft.Blueprint/blueprintAssi
                    gnments/my-blueprint-assignment
Scope             : /subscriptions/{subId}
LastModified      : 2019-03-13
LockMode          : AllResourcesReadOnly
ProvisioningState : Creating
Parameters        : {storageAccount_storageAccountType}
ResourceGroups    : ResourceGroup
```

### <a name="example-2-use-a-json-assignment-definition-file"></a>Exemplo 2: Utilize um ficheiro de definição de atribuição JSON

O exemplo seguinte cria quase a mesma atribuição que o [Exemplo 1](#example-1-provide-parameters).
Em vez de passar parâmetros para o cmdlet, o exemplo mostra a utilização de um ficheiro de definição de definição de atribuição JSON e do parâmetro **AssignmentFile.** Adicionalmente, a propriedade excluída dos Principais é **configurada** como parte de **fechaduras**. Não existe um parâmetro PowerShell para **os Diretores excluídos** e a propriedade só pode ser configurada definindo-a através do ficheiro de definição de definição de atribuição JSON.

```json
{
  "identity": {
    "type": "SystemAssigned"
  },
  "location": "westus2",
  "properties": {
    "description": "Assignment of the 101-blueprint-definition-subscription",
    "blueprintId": "/subscriptions/{subId}/providers/Microsoft.Blueprint/blueprints/101-blueprints-definition-subscription",
    "locks": {
      "mode": "AllResourcesReadOnly",
      "excludedPrincipals": [
          "7be2f100-3af5-4c15-bcb7-27ee43784a1f",
          "38833b56-194d-420b-90ce-cff578296714"
      ]
    },
    "parameters": {
      "storageAccount_storageAccountType": {
        "value": "Standard_GRS"
      }
    },
    "resourceGroups": {
      "ResourceGroup": {
        "name": "storage_rg",
        "location": "westus2"
      }
    }
  }
}
```

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Create the new blueprint assignment
$bpAssignment = New-AzBlueprintAssignment -Name 'my-blueprint-assignment' -SubscriptionId '{subId}' `
    -AssignmentFile '.\assignment.json'
```

Por exemplo, do ficheiro de definição de atribuição JSON para uma identidade gerida atribuída pelo utilizador, consulte o organismo de pedido em [Exemplo: Atribuição com identidade gerida atribuída pelo utilizador](/rest/api/blueprints/assignments/createorupdate#assignment-with-user-assigned-managed-identity) para API REST.

## <a name="update-blueprint-assignments"></a>Atualizar atribuições de plantas

Às vezes é necessário atualizar uma atribuição de plantas que já foi criada. O `Set-AzBlueprintAssignment` cmdlet trata desta ação. O cmdlet leva a maior parte dos mesmos parâmetros que o `New-AzBlueprintAssignment` cmdlet faz, permitindo que tudo o que foi definido na atribuição seja atualizado. As exceções são o _Nome,_ _O Projeto_e o Id _de Subscrição._ Apenas os valores fornecidos são atualizados.

Para entender o que acontece ao atualizar uma atribuição de plantas, consulte [regras para a atualização](./update-existing-assignments.md#rules-for-updating-assignments)de atribuições .

- **Nome** [obrigatório]
  - Especifica o nome da atribuição do projeto para atualizar
  - Usado para localizar a atribuição para atualizar, não para alterar a atribuição
- **Planta** [necessária]
  - Especifica a definição de projeto da atribuição do projeto
  - Use `Get-AzBlueprint` para obter o objeto de referência
  - Usado para localizar a atribuição para atualizar, não para alterar a atribuição
- **Localização** (opcional)
  - Especifica a região para o objeto de implantação de identidade gerida e de subscrição atribuído pelo sistema a criar em
- **Assinatura** (opcional)
  - Especifica a subscrição a que a atribuição está implantada para
  - Caso não seja fornecido, predefini-se do contexto de subscrição atual
  - Usado para localizar a atribuição para atualizar, não para alterar a atribuição
- **Bloqueio** (opcional)
  - Define o bloqueio do [recurso de projeto](../concepts/resource-locking.md) para utilizar para recursos implantados
  - Opções suportadas: _Nenhuma_, _AllResourcesReadOnly_, _AllResourcesDoNotDelete_
- **Identidade Atribuída ao Sistema (opcional)**
  - Selecione criar uma identidade gerida atribuída pelo sistema para a atribuição e para implementar os recursos
  - Padrão para o conjunto de parâmetros de "identidade"
  - Não pode ser usado com **userAssignedIdentity**
- **UserAssignedIdentity** (opcional)
  - Especifica a identidade gerida atribuída pelo utilizador para utilizar para a atribuição e para a implantação dos recursos
  - Parte do conjunto de parâmetros de "identidade"
  - Não pode ser usado com **SystemAssignedIdentity**
- **Parâmetro** (opcional)
  - Uma tabela de [hash](/powershell/module/microsoft.powershell.core/about/about_hash_tables) de pares chave/valor para definir [parâmetros dinâmicos](../concepts/parameters.md#dynamic-parameters) na atribuição da planta
  - Padrão para um parâmetro dinâmico é o **padrãoValue** na definição
  - Se um parâmetro não for fornecido e não tiver **valor padrão,** o parâmetro não é opcional

    > [!NOTE]
    > **O parâmetro** não suporta cordas seguras.

- **ResourceGroupParameter** (opcional)
  - Uma tabela de [hash](/powershell/module/microsoft.powershell.core/about/about_hash_tables) de artefactos de grupo de recursos
  - Cada espaço reservado para artefactos de grupo de recursos tem pares chave/valor para definir dinamicamente **nome** e **localização** nesse artefacto do grupo de recursos
  - Se um parâmetro de grupo de recursos não for fornecido e não tiver **defaultValue,** o parâmetro do grupo de recursos não é opcional

O exemplo seguinte atualiza a atribuição da versão '1.1' da definição de projeto 'my-blueprint' recolhida com `Get-AzBlueprint` alterando o modo de bloqueio:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get version '1.1' of the blueprint definition in the specified subscription
$bpDefinition = Get-AzBlueprint -SubscriptionId '{subId}' -Name 'my-blueprint' -Version '1.1'

# Update the existing blueprint assignment
$bpAssignment = Set-AzBlueprintAssignment -Name 'my-blueprint-assignment' -Blueprint $bpDefinition `
    -SubscriptionId '{subId}' -Lock AllResourcesDoNotDelete
```

A saída de exemplo para criar uma atribuição de plantas é assim:

```output
Name              : my-blueprint-assignment
Id                : /subscriptions/{subId}/providers/Microsoft.Blueprint/blueprintAssi
                    gnments/my-blueprint-assignment
Scope             : /subscriptions/{subId}
LastModified      : 2019-03-13
LockMode          : AllResourcesDoNotDelete
ProvisioningState : Updating
Parameters        : {storageAccount_storageAccountType}
ResourceGroups    : ResourceGroup
```

## <a name="remove-blueprint-assignments"></a>Remover tarefas de plantas

Quando é hora de uma atribuição de plantas ser removida, o `Remove-AzBlueprintAssignment` cmdlet trata desta ação. O cmdlet leva **nome** ou **InputObject** para especificar qual a atribuição da planta a remover. **A Subscrição Id** é _necessária_ e deve ser fornecida em todos os casos.

O exemplo seguinte requer uma atribuição de plantas existente com `Get-AzBlueprintAssignment` e, em seguida, retira-a da subscrição específica representada como `{subId}`:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get the blueprint assignment in the specified subscription
$blueprintAssignment = Get-AzBlueprintAssignment -Name 'Assignment-lock-resource-groups'

# Remove the existing blueprint assignment
Remove-AzBlueprintAssignment -InputObject $blueprintAssignment -SubscriptionId '{subId}'
```

## <a name="end-to-end-code-example"></a>Exemplo de código de ponta a ponta

Juntando todos os passos, o seguinte exemplo obtém a definição de planta, depois cria, atualiza e remove uma atribuição de plantas na subscrição específica representada como `{subId}`:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

#region GetBlueprint
# Get version '1.1' of the blueprint definition in the specified subscription
$bpDefinition = Get-AzBlueprint -SubscriptionId '{subId}' -Name 'my-blueprint' -Version '1.1'
#endregion

#region CreateAssignment
# Create the hash table for Parameters
$bpParameters = @{storageAccount_storageAccountType='Standard_GRS'}

# Create the hash table for ResourceGroupParameters
# ResourceGroup is the resource group artifact placeholder name
$bpRGParameters = @{ResourceGroup=@{name='storage_rg';location='westus2'}}

# Create the new blueprint assignment
$bpAssignment = New-AzBlueprintAssignment -Name 'my-blueprint-assignment' -Blueprint $bpDefinition `
    -SubscriptionId '{subId}' -Location 'westus2' -Lock AllResourcesReadOnly `
    -Parameter $bpParameters -ResourceGroupParameter $bpRGParameters
#endregion CreateAssignment

# Wait for the blueprint assignment to finish deployment prior to the next steps

#region UpdateAssignment
# Update the existing blueprint assignment
$bpAssignment = Set-AzBlueprintAssignment -Name 'my-blueprint-assignment' -Blueprint $bpDefinition `
    -SubscriptionId '{subId}' -Lock AllResourcesDoNotDelete
#endregion UpdateAssignment

# Wait for the blueprint assignment to finish deployment prior to the next steps

#region RemoveAssignment
# Remove the existing blueprint assignment
Remove-AzBlueprintAssignment -InputObject $bpAssignment -SubscriptionId '{subId}'
#endregion
```

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre o [ciclo de vida do esquema](../concepts/lifecycle.md).
- Compreenda como utilizar [parâmetros estáticos e dinâmicos](../concepts/parameters.md).
- Aprenda a personalizar a [ordem de sequenciação do esquema](../concepts/sequencing-order.md).
- Saiba como utilizar o [bloqueio de recursos de esquema](../concepts/resource-locking.md).
- Resolva problemas durante a atribuição de um esquema com a [resolução de problemas gerais](../troubleshoot/general.md).