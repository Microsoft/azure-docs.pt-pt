---
title: Como gerenciar atribuições com o PowerShell
description: Saiba como gerenciar atribuições de plano gráfico com o módulo oficial do PowerShell para plantas oficiais do Azure, AZ. Blueprint.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/14/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: beaa3f4c5ab272592e7fae5a95b40a9b586aaf65
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/03/2019
ms.locfileid: "70232883"
---
# <a name="how-to-manage-assignments-with-powershell"></a>Como gerenciar atribuições com o PowerShell

Uma atribuição de Blueprint pode ser gerenciada usando o módulo **AZ. blueprint** Azure PowerShell. O módulo dá suporte à busca, criação, atualização e remoção de atribuições. O módulo também pode buscar detalhes sobre as definições de plano gráfico existentes. Este artigo aborda como instalar o módulo e começar a usá-lo.

## <a name="add-the-azblueprint-module"></a>Adicione o módulo AZ. Blueprint

Para habilitar Azure PowerShell para gerenciar atribuições de plano gráfico, o módulo deve ser adicionado. Esse módulo pode ser usado com o PowerShell instalado localmente, com [Azure cloud Shell](https://shell.azure.com)ou com a [imagem Azure PowerShell Docker](https://hub.docker.com/r/azuresdk/azure-powershell/).

### <a name="base-requirements"></a>Requisitos de base

O módulo especificações técnicas do Azure requer o seguinte software:

- Azure PowerShell 1.5.0 ou superior. Se não estiver ainda instalado, siga [estas instruções](/powershell/azure/install-az-ps).
- PowerShellGet 2.0.1 ou superior. Se não estiver ainda instalado ou atualizado, siga [estas instruções](/powershell/gallery/installing-psget).

### <a name="install-the-module"></a>Instalar o módulo

O módulo de plantas para o PowerShell é **AZ. Blueprint**.

1. Em um prompt **administrativo** do PowerShell, execute o seguinte comando:

   ```azurepowershell-interactive
   # Install the Blueprints module from PowerShell Gallery
   Install-Module -Name Az.Blueprint
   ```

   > [!NOTE]
   > Se **AZ.** accounts já estiver instalado, pode ser necessário usar `-AllowClobber` para forçar a instalação.

1. Valide se o módulo foi importado e se é a versão correta (0.1.0):

   ```azurepowershell-interactive
   # Get a list of commands for the imported Az.Blueprint module
   Get-Command -Module 'Az.Blueprint' -CommandType 'Cmdlet'
   ```

## <a name="get-blueprint-definitions"></a>Obter definições de Blueprint

A primeira etapa para trabalhar com uma atribuição geralmente obtém uma referência a uma definição de Blueprint.
O `Get-AzBlueprint` cmdlet obtém uma ou mais definições de Blueprint. O cmdlet pode obter definições de plano gráfico de um grupo `-ManagementGroupId {mgId}` de gerenciamento com o `-SubscriptionId {subId}`ou uma assinatura com o. O parâmetro **Name** Obtém uma definição de Blueprint, mas deve ser usado com **ManagementGroupId** ou **SubscriptionId**. A **versão** pode ser usada com o **nome** para ser mais explícito sobre qual definição de Blueprint é retornada. Em vez da **versão**, a `-LatestPublished` opção captura a versão publicada mais recentemente.

O exemplo a seguir `Get-AzBlueprint` usa para obter todas as versões de uma definição Blueprint denominada ' 101-Blueprints-Definition-Subscription ' de uma assinatura `{subId}`específica representada como:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get all versions of the blueprint definition in the specified subscription
$blueprints = Get-AzBlueprint -SubscriptionId '{subId}' -Name '101-blueprints-definition-subscription'

# Display the blueprint definition object
$blueprints
```

A saída de exemplo para uma definição de plano gráfico com várias versões é semelhante a esta:

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

Os [parâmetros de plano gráfico](../concepts/parameters.md#blueprint-parameters) na definição do Blueprint podem ser expandidos para fornecer mais informações.

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

## <a name="get-blueprint-assignments"></a>Obter atribuições de Blueprint

Se a atribuição Blueprint já existir, você poderá obter uma referência a ela com o `Get-AzBlueprintAssignment` cmdlet. O cmdlet usa **SubscriptionId** e **nome** como parâmetros opcionais. Se **SubscriptionId** não for especificado, o contexto de assinatura atual será usado.

O exemplo a seguir `Get-AzBlueprintAssignment` usa para obter uma única atribuição de Blueprint chamada "Assignment-Lock-Resource-Groups" de uma assinatura `{subId}`específica representada como:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get the blueprint assignment in the specified subscription
$blueprintAssignment = Get-AzBlueprintAssignment -SubscriptionId '{subId}' -Name 'Assignment-lock-resource-groups'

# Display the blueprint assignment object
$blueprintAssignment
```

A saída de exemplo de uma atribuição Blueprint tem esta aparência:

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

## <a name="create-blueprint-assignments"></a>Criar atribuições de Blueprint

Se a atribuição Blueprint ainda não existir, você poderá criá-la com `New-AzBlueprintAssignment` o cmdlet. Esse cmdlet usa os seguintes parâmetros:

- **Nome** do necessária
  - Especifica o nome da atribuição Blueprint
  - Deve ser exclusivo e ainda não existir em **SubscriptionId**
- **Plano gráfico** necessária
  - Especifica a definição do Blueprint a ser atribuída
  - Use `Get-AzBlueprint` para obter o objeto de referência
- **Local** do necessária
  - Especifica a região da identidade gerenciada atribuída pelo sistema e o objeto de implantação de assinatura a ser criado no
- **Assinatura** do adicional
  - Especifica a assinatura na qual a atribuição é implantada
  - Se não for fornecido, o padrão será o contexto da assinatura atual
- **Bloquear** adicional
  - Define o [bloqueio de recursos do Blueprint](../concepts/resource-locking.md) a ser usado para recursos implantados
  - Opções com suporte: _None_, _AllResourcesReadOnly_, _AllResourcesDoNotDelete_
  - Se não for fornecido, o padrão será _None_
- **SystemAssignedIdentity** adicional
  - Selecione para criar uma identidade gerenciada atribuída pelo sistema para a atribuição e para implantar os recursos
  - Padrão para o conjunto de parâmetros "Identity"
  - Não pode ser usado com **UserAssignedIdentity**
- **UserAssignedIdentity** adicional
  - Especifica a identidade gerenciada atribuída pelo usuário a ser usada para a atribuição e para implantar os recursos
  - Parte do conjunto de parâmetros "Identity"
  - Não pode ser usado com **SystemAssignedIdentity**
- **Parâmetro** do adicional
  - Uma [tabela de hash](/powershell/module/microsoft.powershell.core/about/about_hash_tables) de pares chave/valor para definir [parâmetros dinâmicos](../concepts/parameters.md#dynamic-parameters) na atribuição Blueprint
  - O padrão para um parâmetro dinâmico é o **DefaultValue** na definição
  - Se um parâmetro não for fornecido e não tiver **DefaultValue**, o parâmetro não será opcional

    > [!NOTE]
    > O **parâmetro** não dá suporte a SecureStrings.

- **ResourceGroupParameter** adicional
  - Uma [tabela de hash](/powershell/module/microsoft.powershell.core/about/about_hash_tables) de artefatos do grupo de recursos
  - Cada espaço reservado de artefato do grupo de recursos terá um par de chave/valor para configurar dinamicamente o **nome** e/ou o **local** nesse artefato do grupo de recursos
  - Se um parâmetro de grupo de recursos não for fornecido e não tiver **DefaultValue**, o parâmetro de grupo de recursos não será opcional

O exemplo a seguir cria uma nova atribuição da versão ' 1,1 ' da definição do plano gráfico ' meu plano gráfico ' buscada com `Get-AzBlueprint`, define a identidade gerenciada e o local do objeto de atribuição como ' westus2 ', bloqueia os recursos com _AllResourcesReadOnly_ e define as tabelas de hash para o **parâmetro** e **ResourceGroupParameter** em uma assinatura específica representada `{subId}`como:

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

A saída de exemplo para a criação de uma atribuição de plano gráfico tem esta aparência:

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

## <a name="update-blueprint-assignments"></a>Atualizar atribuições do Blueprint

Às vezes, é necessário atualizar uma atribuição de Blueprint que já foi criada. O `Set-AzBlueprintAssignment` cmdlet manipula essa ação. O cmdlet usa a maioria dos mesmos parâmetros que o `New-AzBlueprintAssignment` cmdlet faz, permitindo que qualquer coisa que tenha sido definida na atribuição seja atualizada. As exceções a isso são o _nome_, o _Blueprint_e a _SubscriptionId_. Somente os valores fornecidos são atualizados.

Para entender o que acontece ao atualizar uma atribuição de Blueprint, consulte [regras para atualizar atribuições](./update-existing-assignments.md#rules-for-updating-assignments).

- **Nome** do necessária
  - Especifica o nome da atribuição Blueprint a ser atualizada
  - Usado para localizar a atribuição a ser atualizada, não para alterar a atribuição
- **Plano gráfico** necessária
  - Especifica a definição do Blueprint da atribuição Blueprint
  - Use `Get-AzBlueprint` para obter o objeto de referência
  - Usado para localizar a atribuição a ser atualizada, não para alterar a atribuição
- **Local** do adicional
  - Especifica a região da identidade gerenciada atribuída pelo sistema e o objeto de implantação de assinatura a ser criado no
- **Assinatura** do adicional
  - Especifica a assinatura na qual a atribuição é implantada
  - Se não for fornecido, o padrão será o contexto da assinatura atual
  - Usado para localizar a atribuição a ser atualizada, não para alterar a atribuição
- **Bloquear** adicional
  - Define o [bloqueio de recursos do Blueprint](../concepts/resource-locking.md) a ser usado para recursos implantados
  - Opções com suporte: _None_, _AllResourcesReadOnly_, _AllResourcesDoNotDelete_
- **SystemAssignedIdentity** adicional
  - Selecione para criar uma identidade gerenciada atribuída pelo sistema para a atribuição e para implantar os recursos
  - Padrão para o conjunto de parâmetros "Identity"
  - Não pode ser usado com **UserAssignedIdentity**
- **UserAssignedIdentity** adicional
  - Especifica a identidade gerenciada atribuída pelo usuário a ser usada para a atribuição e para implantar os recursos
  - Parte do conjunto de parâmetros "Identity"
  - Não pode ser usado com **SystemAssignedIdentity**
- **Parâmetro** do adicional
  - Uma [tabela de hash](/powershell/module/microsoft.powershell.core/about/about_hash_tables) de pares chave/valor para definir [parâmetros dinâmicos](../concepts/parameters.md#dynamic-parameters) na atribuição Blueprint
  - O padrão para um parâmetro dinâmico é o **DefaultValue** na definição
  - Se um parâmetro não for fornecido e não tiver **DefaultValue**, o parâmetro não será opcional

    > [!NOTE]
    > O **parâmetro** não dá suporte a SecureStrings.

- **ResourceGroupParameter** adicional
  - Uma [tabela de hash](/powershell/module/microsoft.powershell.core/about/about_hash_tables) de artefatos do grupo de recursos
  - Cada espaço reservado de artefato do grupo de recursos terá um par de chave/valor para configurar dinamicamente o **nome** e/ou o **local** nesse artefato do grupo de recursos
  - Se um parâmetro de grupo de recursos não for fornecido e não tiver **DefaultValue**, o parâmetro de grupo de recursos não será opcional

O exemplo a seguir atualiza a atribuição da versão ' 1,1 ' da definição do plano gráfico ' meu plano gráfico ' buscada com `Get-AzBlueprint` alterando o modo de bloqueio:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get version '1.1' of the blueprint definition in the specified subscription
$bpDefinition = Get-AzBlueprint -SubscriptionId '{subId}' -Name 'my-blueprint' -Version '1.1'

# Update the existing blueprint assignment
$bpAssignment = Set-AzBlueprintAssignment -Name 'my-blueprint-assignment' -Blueprint $bpDefinition `
    -SubscriptionId '{subId}' -Lock AllResourcesDoNotDelete
```

A saída de exemplo para a criação de uma atribuição de plano gráfico tem esta aparência:

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

## <a name="remove-blueprint-assignments"></a>Remover atribuições de Blueprint

Quando é o momento de uma atribuição de plano gráfico ser removida `Remove-AzBlueprintAssignment` , o cmdlet manipula essa ação. O cmdlet usa **Name** ou **InputObject** para especificar qual atribuição de Blueprint deve ser removida. **SubscriptionId** é _necessário_ e deve ser fornecido em todos os casos.

O exemplo a seguir busca uma atribuição de plano gráfico `Get-AzBlueprintAssignment` existente com e, em seguida, a remove da `{subId}`assinatura específica representada como:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get the blueprint assignment in the specified subscription
$blueprintAssignment = Get-AzBlueprintAssignment -Name 'Assignment-lock-resource-groups'

# Remove the existing blueprint assignment
Remove-AzBlueprintAssignment -InputObject $blueprintAssignment -SubscriptionId '{subId}'
```

## <a name="end-to-end-code-example"></a>Exemplo de código de ponta a ponta

Reunindo todas as etapas, o exemplo a seguir obtém a definição do plano gráfico, depois cria, atualiza e remove uma atribuição Blueprint na assinatura específica representada `{subId}`como:

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

- Saiba mais sobre o [ciclo de vida do plano gráfico](../concepts/lifecycle.md).
- Compreenda como utilizar [parâmetros estáticos e dinâmicos](../concepts/parameters.md).
- Aprenda a personalizar a [ordem de sequenciação do esquema](../concepts/sequencing-order.md).
- Saiba como utilizar o [bloqueio de recursos de esquema](../concepts/resource-locking.md).
- Resolva problemas durante a atribuição de um esquema com a [resolução de problemas gerais](../troubleshoot/general.md).