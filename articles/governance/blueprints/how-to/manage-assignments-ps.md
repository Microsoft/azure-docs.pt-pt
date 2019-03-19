---
title: Como gerir as atribuições com o PowerShell
description: Saiba como gerir a atribuição de esquema com o módulo do PowerShell de esquemas de Azure oficial, Az.Blueprint.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/14/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 47026975a5142760f90a1a5712b532519f969fd7
ms.sourcegitcommit: 4133f375862fdbdec07b70de047d70c66ac29d50
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/15/2019
ms.locfileid: "57995674"
---
# <a name="how-to-manage-assignments-with-powershell"></a>Como gerir as atribuições com o PowerShell

Uma atribuição do esquema pode ser gerenciada com o **Az.Blueprint** módulo Azure PowerShell. O módulo suporta a obter, criar, atualizar e remover atribuições. O módulo também pode obter os detalhes sobre as definições de esquema existentes. Este artigo abrange como instalar o módulo e começar a utilizar.

## <a name="add-the-azblueprint-module"></a>Adicione o módulo de Az.Blueprint

Para ativar o Azure PowerShell para gerir a atribuição de esquema, o módulo tem de ser adicionado. Este módulo pode ser utilizado com o PowerShell instalado localmente, com [Azure Cloud Shell](https://shell.azure.com), ou com o [imagem do Docker do Azure PowerShell](https://hub.docker.com/r/azuresdk/azure-powershell/).

### <a name="base-requirements"></a>Requisitos de base

O módulo de esquemas do Azure necessita do seguinte software:

- O Azure PowerShell 1.5.0 ou superior. Se não estiver ainda instalado, siga [estas instruções](/powershell/azure/install-az-ps).
- PowerShellGet 2.0.1 ou superior. Se não estiver ainda instalado ou atualizado, siga [estas instruções](/powershell/gallery/installing-psget).

### <a name="install-the-module"></a>Instalar o módulo

O módulo de planos gráficos para o PowerShell está **Az.Blueprint**.

1. De um **administrativas** linha de comandos do PowerShell, execute o seguinte comando:

   ```azurepowershell-interactive
   # Install the Blueprints module from PowerShell Gallery
   Install-Module -Name Az.Blueprint
   ```

   > [!NOTE]
   > Se **Az.Accounts** é já instalado, poderá ser necessário utilizar `-AllowClobber` para forçar a instalação.

1. Valide que o módulo foi importado e é a versão correta (0.1.0):

   ```azurepowershell-interactive
   # Get a list of commands for the imported Az.Blueprint module
   Get-Command -Module 'Az.Blueprint' -CommandType 'Cmdlet'
   ```

## <a name="get-blueprint-definitions"></a>Obtém as definições de esquema

O primeiro passo para trabalhar com uma atribuição, muitas vezes, é obter uma referência a uma definição de esquema.
O `Get-AzBlueprint` cmdlet obtém uma ou mais definições de esquema. O cmdlet pode obter definições do esquema de um grupo de gestão `-ManagementGroupId {mgId}` ou uma subscrição com `-SubscriptionId {subId}`. O **Name** parâmetro obtém uma definição de esquema, mas tem de ser utilizado com **ManagementGroupId** ou **SubscriptionId**. **Versão** podem ser utilizados com **nome** para ser mais explícito sobre qual da definição do esquema é devolvida. Em vez de **versão**, o comutador `-LatestPublished` grabs versões publicadas mais recentemente.

O exemplo seguinte utiliza `Get-AzBlueprint` para obter todas as versões de uma definição de esquema com o nome "101 planos gráficos – definição de subscrições" de uma subscrição específica representada como `{subId}`:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get all versions of the blueprint definition in the specified subscription
$blueprints = Get-AzBlueprint -SubscriptionId '{subId}' -Name '101-blueprints-definition-subscription'

# Display the blueprint definition object
$blueprints
```

O resultado de exemplo para obter uma definição de esquema com várias versões tem esta aparência:

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

O [esquema parâmetros](../concepts/parameters.md#blueprint-parameters) sobre o plano gráfico definição pode ser expandida para fornecer mais informações.

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

## <a name="get-blueprint-assignments"></a>Obter atribuição de esquema

Se a atribuição do esquema já existir, pode obter uma referência a ele com o `Get-AzBlueprintAssignment` cmdlet. O cmdlet assume **SubscriptionId** e **nome** como parâmetros opcionais. Se **SubscriptionId** não for especificada, é utilizado o contexto de subscrição atual.

O exemplo seguinte utiliza `Get-AzBlueprintAssignment` para obter uma atribuição do esquema único com o nome 'Atribuição-bloqueio-resource-groups ' a partir de uma subscrição específica representada como `{subId}`:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get the blueprint assignment in the specified subscription
$blueprintAssignment = Get-AzBlueprintAssignment -SubscriptionId '{subId}' -Name 'Assignment-lock-resource-groups'

# Display the blueprint assignment object
$blueprintAssignment
```

O resultado de exemplo para uma atribuição do esquema tem esta aparência:

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

## <a name="create-blueprint-assignments"></a>Criar atribuição de esquema

Se a atribuição do esquema ainda não existir, pode criar com o `New-AzBlueprintAssignment` cmdlet. Este cmdlet utiliza os seguintes parâmetros:

- **Nome** [necessário]
  - Especifica o nome da atribuição do esquema
  - Tem de ser exclusivo e não existir na **SubscriptionId**
- **Esquema** [necessário]
  - Especifica a definição do esquema para atribuir
  - Utilize `Get-AzBlueprint` para obter o objeto de referência
- **Localização** [necessário]
  - Especifica a região para atribuído de sistema gerido identidade e subscrição do objeto de implementação a ser criada no
- **Subscrição** (opcional)
  - Especifica a subscrição que a atribuição é implementada para
  - Se não for indicado, o padrão é o contexto da subscrição atual
- **Bloqueio** (opcional)
  - Define a [esquema de bloqueio do recurso](../concepts/resource-locking.md) a utilizar para os recursos implementados
  - Opções suportadas: _None_, _AllResourcesReadOnly_, _AllResourcesDoNotDelete_
  - Se não for indicado, o padrão é _None_
- **SystemAssignedIdentity** (opcional)
  - Selecione para criar uma identidade gerida atribuído de sistema para a atribuição e para implementar os recursos
  - Predefinição para o conjunto de parâmetros de "identity"
  - Não pode ser utilizado com **UserAssignedIdentity**
- **UserAssignedIdentity** (opcional)
  - Especifica a utilizador atribuído identidade gerida a utilizar para a atribuição de e para implementar os recursos
  - Parte do conjunto de parâmetro "identity"
  - Não pode ser utilizado com **SystemAssignedIdentity**
- **Parâmetro** (opcional)
  - R [tabela de hash](/powershell/module/microsoft.powershell.core/about/about_hash_tables) de pares chave/valor para a definição [parâmetros dinâmicos](../concepts/parameters.md#dynamic-parameters) sobre a atribuição do esquema
  - Predefinido para um parâmetro dinâmico é o **defaultValue** na definição
  - Se um parâmetro não for fornecido e não tem nenhum **defaultValue**, o parâmetro não é opcional

    > [!NOTE]
    > **Parâmetro** não suporta secureStrings.

- **ResourceGroupParameter** (optional)
  - R [tabela de hash](/powershell/module/microsoft.powershell.core/about/about_hash_tables) dos artefatos do grupo de recursos
  - Cada marcador de posição do artefacto de grupo do recurso terão uma pares chave/valor para definir dinamicamente **Name** e/ou **localização** nesse artefacto de grupo de recursos
  - Se um parâmetro de grupo de recursos não for fornecido e não tem nenhum **defaultValue**, o parâmetro do grupo de recursos não é opcional

O exemplo seguinte cria uma nova atribuição da versão '1.1' da definição do esquema 'meu esquema' obtida com `Get-AzBlueprint`, define a localização de objeto de atribuição e a identidade gerida para 'westus2', que bloqueia os recursos com  _AllResourcesReadOnly_e define as tabelas de hash para ambos **parâmetro** e **ResourceGroupParameter** subscrição específica representados como `{subId}`:

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
    -SubscriptionId '{subId}' -Location 'westus2' -Lock AllResourcesReadyOnly `
    -Parameter $bpParameters -ResourceGroupParameter $bpRGParameters
```

O resultado de exemplo para a criação de uma atribuição do esquema tem esta aparência:

```output
Name              : my-blueprint-assignment
Id                : /subscriptions/{subId}/providers/Microsoft.Blueprint/blueprintAssi
                    gnments/my-blueprint-assignment
Scope             : /subscriptions/{subId}
LastModified      : 2019-03-13
LockMode          : AllResourcesReadyOnly
ProvisioningState : Creating
Parameters        : {storageAccount_storageAccountType}
ResourceGroups    : ResourceGroup
```

## <a name="update-blueprint-assignments"></a>Atribuição de esquema de atualização

Por vezes, é necessário atualizar uma atribuição do esquema que já foi criada. O `Set-AzBlueprintAssignment` cmdlet processa esta ação. O cmdlet assume a maioria dos parâmetros mesmo que o `New-AzBlueprintAssignment` cmdlet faz, permitindo que tudo o que foi definido na atribuição para ser atualizado. As exceções desta funcionalidade são os _Name_, _esquema_, e _SubscriptionId_. Apenas os valores fornecidos são atualizados.

Para compreender o que acontece quando a atualização de uma atribuição do esquema, veja [regras para atualizar as atribuições](./update-existing-assignments.md#rules-for-updating-assignments).

- **Nome** [necessário]
  - Especifica o nome da atribuição de esquema para atualizar
  - Utilizado para localizar a atribuição para atualizar, não para alterar a atribuição
- **Esquema** [necessário]
  - Especifica a definição do esquema de atribuição do esquema
  - Utilize `Get-AzBlueprint` para obter o objeto de referência
  - Utilizado para localizar a atribuição para atualizar, não para alterar a atribuição
- **Localização** (opcional)
  - Especifica a região para atribuído de sistema gerido identidade e subscrição do objeto de implementação a ser criada no
- **Subscrição** (opcional)
  - Especifica a subscrição que a atribuição é implementada para
  - Se não for indicado, o padrão é o contexto da subscrição atual
  - Utilizado para localizar a atribuição para atualizar, não para alterar a atribuição
- **Bloqueio** (opcional)
  - Define a [esquema de bloqueio do recurso](../concepts/resource-locking.md) a utilizar para os recursos implementados
  - Opções suportadas: _None_, _AllResourcesReadOnly_, _AllResourcesDoNotDelete_
- **SystemAssignedIdentity** (opcional)
  - Selecione para criar uma identidade gerida atribuído de sistema para a atribuição e para implementar os recursos
  - Predefinição para o conjunto de parâmetros de "identity"
  - Não pode ser utilizado com **UserAssignedIdentity**
- **UserAssignedIdentity** (opcional)
  - Especifica a utilizador atribuído identidade gerida a utilizar para a atribuição de e para implementar os recursos
  - Parte do conjunto de parâmetro "identity"
  - Não pode ser utilizado com **SystemAssignedIdentity**
- **Parâmetro** (opcional)
  - R [tabela de hash](/powershell/module/microsoft.powershell.core/about/about_hash_tables) de pares chave/valor para a definição [parâmetros dinâmicos](../concepts/parameters.md#dynamic-parameters) sobre a atribuição do esquema
  - Predefinido para um parâmetro dinâmico é o **defaultValue** na definição
  - Se um parâmetro não for fornecido e não tem nenhum **defaultValue**, o parâmetro não é opcional

    > [!NOTE]
    > **Parâmetro** não suporta secureStrings.

- **ResourceGroupParameter** (optional)
  - R [tabela de hash](/powershell/module/microsoft.powershell.core/about/about_hash_tables) dos artefatos do grupo de recursos
  - Cada marcador de posição do artefacto de grupo do recurso terão uma pares chave/valor para definir dinamicamente **Name** e/ou **localização** nesse artefacto de grupo de recursos
  - Se um parâmetro de grupo de recursos não for fornecido e não tem nenhum **defaultValue**, o parâmetro do grupo de recursos não é opcional

O exemplo seguinte atualiza a atribuição da versão '1.1' da definição do esquema 'meu esquema' obtida com `Get-AzBlueprint` ao alterar o modo de bloqueio:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get version '1.1' of the blueprint definition in the specified subscription
$bpDefinition = Get-AzBlueprint -SubscriptionId '{subId}' -Name 'my-blueprint' -Version '1.1'

# Update the existing blueprint assignment
$bpAssignment = Set-AzBlueprintAssignment -Name 'my-blueprint-assignment' -Blueprint $bpDefinition `
    -SubscriptionId '{subId}' -Lock AllResourcesDoNotDelete
```

O resultado de exemplo para a criação de uma atribuição do esquema tem esta aparência:

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

## <a name="remove-blueprint-assignments"></a>Remover atribuição de esquema

Quando chegou a hora para uma atribuição do esquema a ser removida, o `Remove-AzBlueprintAssignment` cmdlet processa esta ação. O cmdlet utiliza qualquer um **Name** ou **InputObject** para especificar que para remover a atribuição de esquema. **SubscriptionId** é _necessário_ e tem de ser fornecido em todos os casos.

O exemplo seguinte obtém uma atribuição de esquema existentes com `Get-AzBlueprintAssignment` e, em seguida, remove-o da subscrição específica, representada como `{subId}`:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get the blueprint assignment in the specified subscription
$blueprintAssignment = Get-AzBlueprintAssignment -Name 'Assignment-lock-resource-groups'

# Remove the existing blueprint assignment
Remove-AzBlueprintAssignment -InputObject $blueprintAssignment -SubscriptionId '{subId}'
```

## <a name="end-to-end-code-example"></a>Exemplo de código-a-ponto

Género, reúne todas as etapas, o exemplo seguinte obtém a definição do esquema, em seguida, cria, atualiza e remove uma atribuição do esquema na subscrição específica representada como `{subId}`:

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
    -SubscriptionId '{subId}' -Location 'westus2' -Lock AllResourcesReadyOnly `
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

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre o [ciclo de vida de esquema](../concepts/lifecycle.md).
- Compreender como usar [parâmetros estáticos e dinâmicos](../concepts/parameters.md).
- Aprenda a personalizar a [esquema de ordem de sequenciamento](../concepts/sequencing-order.md).
- Descubra como tornar a usar [esquema de bloqueio do recurso](../concepts/resource-locking.md).
- Resolver problemas durante a atribuição de um plano gráfico com [resolução de problemas gerais](../troubleshoot/general.md).