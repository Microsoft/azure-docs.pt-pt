---
title: Como gerir atribuições com a PowerShell
description: Aprenda a gerir as atribuições de plantas com o módulo oficial Azure Blueprints PowerShell, Az.Blueprint.
ms.date: 08/27/2020
ms.topic: how-to
ms.openlocfilehash: 3bcb3731bd1270497945fa86406d08b2f9750c85
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89051411"
---
# <a name="how-to-manage-assignments-with-powershell"></a>Como gerir atribuições com a PowerShell

Uma atribuição de plantas pode ser gerida utilizando o módulo **Az.Blueprint** Azure PowerShell. O módulo suporta a busca, criação, atualização e remoção de atribuições. O módulo também pode obter detalhes sobre as definições de planta existentes. Este artigo cobre como instalar o módulo e começar a uizá-lo.

## <a name="add-the-azblueprint-module"></a>Adicione o módulo Az.Blueprint

Para permitir que a Azure PowerShell gere as atribuições de projeto, o módulo deve ser adicionado. Este módulo pode ser utilizado com PowerShell instalado localmente, com [Azure Cloud Shell,](https://shell.azure.com)ou com a [imagem Azure PowerShell Docker](https://hub.docker.com/r/azuresdk/azure-powershell/).

### <a name="base-requirements"></a>Requisitos de base

O módulo Azure Blueprints requer o seguinte software:

- Azure PowerShell 1.5.0 ou superior. Se não estiver ainda instalado, siga [estas instruções](/powershell/azure/install-az-ps).
- PowerShellGet 2.0.1 ou superior. Se não estiver ainda instalado ou atualizado, siga [estas instruções](/powershell/scripting/gallery/installing-psget).

### <a name="install-the-module"></a>Instale o módulo

O módulo Azure Blueprints para PowerShell é **Az.Blueprint**.

1. A partir de um pedido **administrativo** powerShell, executar o seguinte comando:

   ```azurepowershell-interactive
   # Install the Azure Blueprints module from PowerShell Gallery
   Install-Module -Name Az.Blueprint
   ```

   > [!NOTE]
   > Se **a Az.Accounts** já estiver instalada, poderá ser necessário utilizar `-AllowClobber` para forçar a instalação.

1. Validar que o módulo foi importado e é a versão correta (0.2.6):

   ```azurepowershell-interactive
   # Get a list of commands for the imported Az.Blueprint module
   Get-Command -Module 'Az.Blueprint' -CommandType 'Cmdlet'
   ```

## <a name="get-blueprint-definitions"></a>Obtenha definições de projeto

O primeiro passo para trabalhar com uma atribuição é muitas vezes obter uma referência a uma definição de planta.
O `Get-AzBlueprint` cmdlet obtém uma ou mais definições de planta. O cmdlet pode obter definições de projeto de um grupo de gestão com `-ManagementGroupId {mgId}` ou uma subscrição com `-SubscriptionId {subId}` . O parâmetro **Nome** obtém uma definição de planta, mas deve ser usado com **ManagementGroupId** ou **SubscriptionId**. **A versão** pode ser usada com **Nome** para ser mais explícita sobre qual a definição de planta é devolvida. Em vez de **Versão**, o switch `-LatestPublished` agarra a versão mais recentemente publicada.

O exemplo a seguir `Get-AzBlueprint` utiliza-se para obter todas as versões de uma definição de projeto denominada "101-blueprints-definition-subscription" a partir de uma subscrição específica representada como `{subId}` :

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

Os [parâmetros da planta](../concepts/parameters.md#blueprint-parameters) na definição da planta podem ser expandidos para fornecer mais informações.

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

## <a name="get-blueprint-assignments"></a>Obtenha atribuições de planta

Se a atribuição da planta já existir, pode obter uma referência a ela com o `Get-AzBlueprintAssignment` cmdlet. O cmdlet toma **SubscriptionId** e **Name** como parâmetros opcionais. Se **o SubscriptionId** não for especificado, o contexto de subscrição atual é utilizado.

O exemplo a seguir `Get-AzBlueprintAssignment` utiliza-se para obter uma única atribuição de projeto denominada "Grupos de recursos de bloqueio de atribuição" a partir de uma subscrição específica representada como `{subId}` :

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get the blueprint assignment in the specified subscription
$blueprintAssignment = Get-AzBlueprintAssignment -SubscriptionId '{subId}' -Name 'Assignment-lock-resource-groups'

# Display the blueprint assignment object
$blueprintAssignment
```

A saída de exemplo para uma atribuição de plantas é esta:

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

## <a name="create-blueprint-assignments"></a>Criar atribuições de plantas

Se a atribuição da planta ainda não existir, pode criá-la com o `New-AzBlueprintAssignment` cmdlet. Este cmdlet utiliza os seguintes parâmetros:

- **Nome** [obrigatório]
  - Especifica o nome da atribuição da planta
  - Deve ser único e não existir já em **Subscrição**
- **Planta** [necessária]
  - Especifica a definição de planta para atribuir
  - Use `Get-AzBlueprint` para obter o objeto de referência
- **Localização** [necessária]
  - Especifica a região para o objeto de identidade gerida e subscrição atribuído pelo sistema a ser criado em
- **Assinatura** (opcional)
  - Especifica a subscrição para a atribuição para
  - Se não for fornecido, incumprimentos ao contexto de subscrição atual
- **Bloqueio** (opcional)
  - Define o bloqueio de [recursos de planta](../concepts/resource-locking.md) para usar para recursos implantados
  - Opções suportadas: _Nenhuma_, _AllResourcesReadOnly_, _AllResourcesDoNotDelete_
  - Se não for fornecido, incumprimentos a _Nenhum_
- **SystemAssignedIdentity** (opcional)
  - Selecione para criar uma identidade gerida atribuída ao sistema para a atribuição e para implementar os recursos
  - Predefinição para o conjunto de parâmetros de "identidade"
  - Não pode ser usado com **UserAssignedIdentity**
- **Entidade UtilizadorAssignedId (opcional)**
  - Especifica a identidade gerida atribuída pelo utilizador para utilizar para a atribuição e para implantar os recursos
  - Parte do conjunto de parâmetros "identidade"
  - Não pode ser usado com **SystemAssignedIdentity**
- **Parâmetro** (opcional)
  - Uma tabela de [haxixe](/powershell/module/microsoft.powershell.core/about/about_hash_tables) de pares de chaves/valor para definir [parâmetros dinâmicos](../concepts/parameters.md#dynamic-parameters) na atribuição de plantas
  - Padrão para um parâmetro dinâmico é o **padrãoValue** na definição
  - Se um parâmetro não for fornecido e não tiver **um padrão Desaugmento,** o parâmetro não é opcional

    > [!NOTE]
    > **O parâmetro** não suporta secureStrings.

- **Termosgrama de Grupo de Recursos** (opcional)
  - Uma tabela de [haxixe](/powershell/module/microsoft.powershell.core/about/about_hash_tables) de artefactos do grupo de recursos
  - Cada espaço reservado de um grupo de recursos tem pares de chaves/valor para definir dinamicamente **Nome** e **Localização** nesse artefacto do grupo de recursos
  - Se um parâmetro de grupo de recursos não for fornecido e não tiver **um padrãoValue,** o parâmetro do grupo de recursos não é opcional
- **AtribuiçãoFile** (opcional)
  - O caminho para uma representação de ficheiro JSON de uma atribuição de planta
  - Este parâmetro faz parte de um conjunto de parâmetros PowerShell que inclui apenas **Nome,** **Planta,** e **SubscriçãoId,** além dos parâmetros comuns.

### <a name="example-1-provide-parameters"></a>Exemplo 1: Fornecer parâmetros

O exemplo a seguir cria uma nova atribuição da versão '1.1' da definição de planta 'my-blueprint' recolhida com `Get-AzBlueprint` , define a identidade gerida e a localização do objeto de atribuição para 'westus2', bloqueia os recursos com _allResourcesReadOnly_, e define as tabelas de haxixe para **parâmetros** e **ResourceGroupParameter** em subscrição específica representada como `{subId}` :

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

A saída de exemplo para criar uma atribuição de plantas é esta:

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

O exemplo a seguir cria quase a mesma atribuição que o [Exemplo 1](#example-1-provide-parameters). Em vez de passar parâmetros para o cmdlet, o exemplo mostra a utilização de um ficheiro de definição de atribuição JSON e do parâmetro **AssignmentFile.** Além disso, a propriedade **excluida Dos Príncipes** é configurada como parte de **fechaduras.** Não existe um parâmetro PowerShell para **osPrincipals excluídos** e a propriedade só pode ser configurada definindo-a através do ficheiro de definição de atribuição JSON.

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

Para um exemplo do ficheiro de definição de atribuição JSON para uma identidade gerida atribuída pelo utilizador, consulte o órgão de pedido em [Exemplo: Atribuição com identidade gerida atribuída pelo utilizador](/rest/api/blueprints/assignments/createorupdate#examples) para a API REST.

## <a name="update-blueprint-assignments"></a>Atualizar atribuições de plantas

Às vezes é necessário atualizar uma atribuição de plantas que já foi criada. O `Set-AzBlueprintAssignment` cmdlet lida com esta ação. O cmdlet leva a maioria dos mesmos parâmetros que o `New-AzBlueprintAssignment` cmdlet faz, permitindo que tudo o que foi definido na atribuição seja atualizado. As exceções são o _Nome,_ _Planta_e _Subscrição._ Apenas os valores fornecidos são atualizados.

Para entender o que acontece ao atualizar uma atribuição de projeto, consulte [regras para atualizar atribuições](./update-existing-assignments.md#rules-for-updating-assignments).

- **Nome** [obrigatório]
  - Especifica o nome da atribuição do projeto para atualizar
  - Usado para localizar a atribuição para atualizar, não para alterar a atribuição
- **Planta** [necessária]
  - Especifica a definição de planta da atribuição de plantas
  - Use `Get-AzBlueprint` para obter o objeto de referência
  - Usado para localizar a atribuição para atualizar, não para alterar a atribuição
- **Localização** (opcional)
  - Especifica a região para o objeto de identidade gerida e subscrição atribuído pelo sistema a ser criado em
- **Assinatura** (opcional)
  - Especifica a subscrição para a atribuição para
  - Se não for fornecido, incumprimentos ao contexto de subscrição atual
  - Usado para localizar a atribuição para atualizar, não para alterar a atribuição
- **Bloqueio** (opcional)
  - Define o bloqueio de [recursos de planta](../concepts/resource-locking.md) para usar para recursos implantados
  - Opções suportadas: _Nenhuma_, _AllResourcesReadOnly_, _AllResourcesDoNotDelete_
- **SystemAssignedIdentity** (opcional)
  - Selecione para criar uma identidade gerida atribuída ao sistema para a atribuição e para implementar os recursos
  - Predefinição para o conjunto de parâmetros de "identidade"
  - Não pode ser usado com **UserAssignedIdentity**
- **Entidade UtilizadorAssignedId (opcional)**
  - Especifica a identidade gerida atribuída pelo utilizador para utilizar para a atribuição e para implantar os recursos
  - Parte do conjunto de parâmetros "identidade"
  - Não pode ser usado com **SystemAssignedIdentity**
- **Parâmetro** (opcional)
  - Uma tabela de [haxixe](/powershell/module/microsoft.powershell.core/about/about_hash_tables) de pares de chaves/valor para definir [parâmetros dinâmicos](../concepts/parameters.md#dynamic-parameters) na atribuição de plantas
  - Padrão para um parâmetro dinâmico é o **padrãoValue** na definição
  - Se um parâmetro não for fornecido e não tiver **um padrão Desaugmento,** o parâmetro não é opcional

    > [!NOTE]
    > **O parâmetro** não suporta secureStrings.

- **Termosgrama de Grupo de Recursos** (opcional)
  - Uma tabela de [haxixe](/powershell/module/microsoft.powershell.core/about/about_hash_tables) de artefactos do grupo de recursos
  - Cada espaço reservado de um grupo de recursos tem pares de chaves/valor para definir dinamicamente **Nome** e **Localização** nesse artefacto do grupo de recursos
  - Se um parâmetro de grupo de recursos não for fornecido e não tiver **um padrãoValue,** o parâmetro do grupo de recursos não é opcional

O exemplo a seguir atualiza a atribuição da versão '1.1' da definição de planta 'my-blueprint' apanhada `Get-AzBlueprint` com a alteração do modo de bloqueio:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get version '1.1' of the blueprint definition in the specified subscription
$bpDefinition = Get-AzBlueprint -SubscriptionId '{subId}' -Name 'my-blueprint' -Version '1.1'

# Update the existing blueprint assignment
$bpAssignment = Set-AzBlueprintAssignment -Name 'my-blueprint-assignment' -Blueprint $bpDefinition `
    -SubscriptionId '{subId}' -Lock AllResourcesDoNotDelete
```

A saída de exemplo para criar uma atribuição de plantas é esta:

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

## <a name="remove-blueprint-assignments"></a>Remover atribuições de planta

Quando é hora de remover uma designação de planta, o `Remove-AzBlueprintAssignment` cmdlet lida com esta ação. O cmdlet requer **nome** ou **InputObject** para especificar qual a designação a remover. **A SubscriptionId** é _necessária_ e deve ser fornecida em todos os casos.

O exemplo a seguir requer uma atribuição de projeto existente `Get-AzBlueprintAssignment` e, em seguida, remove-a da subscrição específica representada como `{subId}` :

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get the blueprint assignment in the specified subscription
$blueprintAssignment = Get-AzBlueprintAssignment -Name 'Assignment-lock-resource-groups'

# Remove the existing blueprint assignment
Remove-AzBlueprintAssignment -InputObject $blueprintAssignment -SubscriptionId '{subId}'
```

## <a name="code-example"></a>Exemplo de código

Juntando todos os passos, o exemplo a seguir obtém a definição de planta, em seguida, cria, atualiza e remove uma atribuição de planta na subscrição específica representada como `{subId}` :

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