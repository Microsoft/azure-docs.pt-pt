---
title: Mover recursos através de regiões usando PowerShell em Azure Resource Mover
description: Saiba como mover recursos através de regiões usando PowerShell em Azure Resource Mover.
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: how-to
ms.date: 09/10/2020
ms.author: raynew
ms.openlocfilehash: 3236e0a95c6a4b4f57ac38ed067011c3d6848b5a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89670800"
---
# <a name="move-resources-across-regions-in-powershell"></a>Mover recursos através de regiões em PowerShell

Saiba como mover os recursos da Azure para uma região diferente usando o PowerShell em Azure Resource Mover. 

> [!NOTE]
> A Azure Resource Mover está atualmente em pré-visualização.



## <a name="before-you-start"></a>Antes de começar

- A sua subscrição Azure deve ter acesso ao Resource Mover, e deverá ter permissões [de Proprietário](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) ou Administrador de Acesso ao [Utilizador](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#user-access-administrator) para a subscrição.
- O Resource Mover não rastreia alterações e atualizações, por isso faça quaisquer alterações necessárias nos recursos, antes de começar a movê-las.
- Quando movimenta recursos com o PowerShell, não pode atualmente editar quaisquer definições da região alvo. Modifique estas definições diretamente no portal.
- Quando adiciona recursos a uma recolha de movimentos, em preparação para a sua deslocação para outra região, os metadados sobre a mudança são armazenados num grupo de recursos criado para o efeito. Atualmente, este grupo de recursos pode residir nas regiões leste dos EUA 2 ou norte da Europa. Os recursos azuis podem ser deslocados entre as regiões públicas utilizando metadados presentes em qualquer uma destas regiões.

## <a name="sample-values"></a>Valores da amostra

Estamos a usar estes valores nos nossos exemplos de script:

**Definição** | **Valor** 
--- | ---
ID da subscrição | id de subscrição
Localização do serviço de mover de recursos | E.U.A. Leste 2
Grupo de recursos de metadados | RegiãoMoveRG-centralus-westcentralus
Localização do grupo de recursos de metadados | E.U.A. Leste 2
Nome de coleção move | MovimentoCollection-centralus-westcentralus
Região de origem |  centralus
Grupo de recursos de origem | PSDemoRM
Região-alvo | E.U.A. Centro-Oeste
Grupo de recursos-alvo | PSDemoRMtgt
VM para mover | PSDemovM

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inscreva-se na sua assinatura Azure.

```azurepowershell-interactive
# Sign in to an Azure subscription
Connect-AzAccount – Subscription "<subscription-id>"
```

## <a name="create-the-resource-group-for-metadata"></a>Criar o grupo de recursos para metadados

Crie um grupo de recursos para reter os metadados de recolha de movimento e informações de configuração.

```azurepowershell-interactive
# Sign in to an Azure subscription
New-AzResourceGroup -Name MoveCollection-centralus-westcentralus -Location "East US 2"
```

**Saída prevista:**

![Texto de saída após criar grupo de recursos](./media/move-region-powershell/output-create-resource-group.png)

## <a name="register-the-resource-provider"></a>Registar o fornecedor de recursos

Registe o fornecedor de recursos Microsoft.Migrate, para que o recurso MoveCollection possa ser criado.

```azurepowershell-interactive
# Register the provider
Register-AzResourceProvider -ProviderNamespace Microsoft.Migrate 

# Wait for registration
While(((Get-AzResourceProvider -ProviderNamespace Microsoft.Migrate)| where {$_.RegistrationState -eq "Registered" -and $_.ResourceTypes.ResourceTypeName -eq "moveCollections"}|measure).Count -eq 0)
  {
      Start-Sleep -Seconds 5
      Write-Output "Waiting for registration to complete."
  }
```

## <a name="create-the-move-collection"></a>Criar a coleção de movimentos

O objeto MoveCollection armazena metadados e informações de configuração sobre os recursos que pretende mover.

- Para que o objeto MoveCollection aceda à subscrição em que está localizado o serviço Azure Resource Mover, necessita de uma [identidade gerida atribuída ao sistema](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) (anteriormente conhecida como Managed Service Identify (MSI)) que seja fidedigna pela subscrição.
- A identidade é atribuída à função de Contribuinte ou administrador de acesso ao utilizador para a subscrição de origem.
- Para atribuir uma função à identidade, precisa de uma função na subscrição (como Proprietário ou Administrador de Acesso ao Utilizador), com estas permissões:
    -  Microsoft.Authorization/roleAssignments/write
    - Microsoft.Autorização/funAsignments/delete


```azurepowershell-interactive
# Create a MoveCollection object
New-AzResourceMoverMoveCollection -Name "MoveCollection-centralus-westcentralus " -ResourceGroupName "RegionMoveRG-centralus-westcentralus " -SubscriptionId "<subscription-id>" -SourceRegion "centralus" -TargetRegion "westcentralus" -Location  "East US 2" -IdentityType SystemAssigned
```
**Saída prevista:**

![Texto de saída após criar coleção de movimento](./media/move-region-powershell/output-move-collection.png)


## <a name="assign-a-managed-identity"></a>Atribuir uma identidade gerida 

Desajeitar o ID de subscrição, recuperar o principal de identidade do objeto MoveCollection e atribuir-lhe as funções Azure.


```azurepowershell-interactive
# Set the subscriptionId
$subscriptionId = "<subscription-id>"

# Retrieve the principal managed identity of the MoveCollection
$moveCollection = Get-AzResourceMoverMoveCollection -SubscriptionId $subscriptionId -ResourceGroupName "RegionMoveRG-centralus-westcentralus " -Name "MoveCollection-centralus-westcentralus "

# Set the IdentityPrincipalID
$identityPrincipalId = $moveCollection.IdentityPrincipalId

# Assign the role to the IdentityPrincipalID
New-AzRoleAssignment -ObjectId $identityPrincipalId -RoleDefinitionName Contributor -Scope "/subscriptions/$subscriptionId"

New-AzRoleAssignment -ObjectId $identityPrincipalId -RoleDefinitionName "User Access Administrator" -Scope "/subscriptions/$subscriptionId"

```

## <a name="add-resources-to-the-move-collection"></a>Adicione recursos à coleção de movimentos

Recupere a identificação do recurso de origem que pretende mover. Em seguida, adicione-o à coleção de movimentos.

```azurepowershell-interactive
# Retrieve the resource ID
Get-AzResource -Name PSDemoVM -ResourceGroupName PSDemoRM
```

**Resultado esperado**

![Texto de saída após recuperar o ID do recurso](./media/move-region-powershell/output-retrieve-resource.png)


```azurepowershell-interactive
# Add the resource to the move collection
New-AzResourceMoverMoveResource -SubscriptionId “<subscription-id>” -ResourceGroupName “RegionMoveRG-centralus-westcentralus ” -MoveCollectionName “MoveCollection-centralus-westcentralus ” -SourceId “/subscriptions/e80eb9fa-c996-4435-aa32-5af6f3d3077c/resourceGroups/PSDemoRM/providers/Microsoft.Compute/virtualMachines/PSDemoVM” -Name “PSDemoVM” -ResourceSettingResourceType “ Microsoft.Compute/virtualMachines” -ResourceSettingTargetResourceName “PSDemoVM”
```

**Saída esperada** 
 ![ Texto de saída após a adição do recurso](./media/move-region-powershell/output-add-resource.png)

## <a name="resolve-dependencies"></a>Resolver dependências

Valide os recursos que adicionou e resolva quaisquer dependências de outros recursos.

```azurepowershell-interactive
# Resolve dependencies
Resolve-AzResourceMoverMoveCollectionDependency -SubscriptionId “<subscription-id>” -ResourceGroupName “RegionMoveRG-centralus-westcentralus” -MoveCollectionName “MoveCollection-centralus-westcentralus”
```
**Saída se existem dependências**

Se o recurso na recolha de movimentos tiver dependências de outros recursos, é emitida uma mensagem falhada.

![Texto de saída após verificação das dependências](./media/move-region-powershell/dependency-error.png)

### <a name="retrieve-dependencies"></a>Recuperar dependências

Se o recurso que pretende mover tiver dependências de outros recursos, pode obter informações sobre as dependências em falta.

```azurepowershell-interactive
# Identify dependencies
Get-AzResourceMoverUnresolvedDependency -MoveCollectionName “MoveCollection-centralus-westcentralus ” -ResourceGroupName “RegionMoveRG-centralus-westcentralus ” -SubscriptionId  “<subscription-id>”
```

**Resultado esperado**

![Texto de saída após recuperar dependências](./media/move-region-powershell/missing-dependencies.png)

Neste exemplo, duas dependências são identificadas como desaparecidas:

- Grupo de recursos de origem: PSDemoRM
- NIC em VM: PSDemoVM62

## <a name="add-dependencies-to-collection"></a>Adicionar dependências à coleção


Utilizando os IDs de recursos que recuperou, identifique os nomes dos recursos em falta e adicione-os à coleção de movimentos.

### <a name="add-the-nic"></a>Adicione o NIC

Recupere o nome e o tipo NIC e adicione-o à coleção.

```azurepowershell-interactive
# Get the NIC name and resource type
Get-AzResource -ResourceId “/subscriptions/ <subscription-id> /resourcegroups/psdemorm/providers/microsoft.network/networkinterfaces/psdemovm62”

```

**Resultado esperado**

![Texto de saída após a recuperação do NIC](./media/move-region-powershell/output-retrieve-nic.png)

Agora adicione o NIC à coleção de movimentos. Este exemplo dá o mesmo nome ao NIC alvo. Mantenha as definições e o caso consistentes.

```azurepowershell-interactive
# Add the NIC to the collection. 
New-AzResourceMoverMoveResource -SubscriptionId “<subscription-id>” -ResourceGroupName “RegionMoveRG-centralus-westcentralus” -MoveCollectionName “MoveCollection-centralus-westcentralus ” -SourceId “/subscriptions/<subscription-id>/resourceGroups/PSDemoRM/providers/Microsoft.Network/networkInterfaces/psdemovm62” -Name “psdemovm62” -ResourceSettingResourceType “Microsoft.Network/networkInterfaces” -ResourceSettingTargetResourceName “psdemovm62”
```

**Resultado esperado**

![Texto de saída após adicionar o NIC à coleção](./media/move-region-powershell/add-nic.png)

## <a name="add-the-source-resource-group"></a>Adicione o grupo de recursos de origem

```azurepowershell-interactive
# Get the resource group name and resource type
Get-AzResource -ResourceId “/subscriptions/<subscription-id>/resourcegroups/psdemorm”

# Add the resource group to the collection. 
New-AzResourceMoverMoveResource -SubscriptionId “<subscription-id>” -ResourceGroupName “RegionMoveRG-centralus-westcentralus” -MoveCollectionName “MoveCollection-centralus-westcentralus” -SourceId “/subscriptions/<subscription-id>/resourcegroups/psdemorm” -Name “psdemorm” -ResourceSettingResourceType “resourcegroups” -ResourceSettingTargetResourceName “psdemorm”
```

**Resultado esperado**

![Texto de saída após a adição do grupo de recursos à coleção](./media/move-region-powershell/add-source-resource-group.png)

## <a name="recheck-dependencies"></a>Rever as dependências

Verifique novamente se há dependências desaparecidas.

```azurepowershell-interactive
Get-AzResourceMoverUnresolvedDependency -MoveCollectionName “MoveCollection-centralus-westcentralus ” -ResourceGroupName “RegionMoveRG-centralus-westcentralus ” -SubscriptionId  “<subscription-id>”
```

**Saída**

Temos dependências adicionais em falta.

![Texto de saída após verificar novamente se há dependências](./media/move-region-powershell/recheck-dependencies.png)

## <a name="retrieve-additional-dependencies"></a>Recuperar dependências adicionais

```azurepowershell-interactive
# Identify dependencies
Get-AzResourceMoverUnresolvedDependency -MoveCollectionName “MoveCollection-centralus-westcentralus ” -ResourceGroupName “RegionMoveRG-centralus-westcentralus” -SubscriptionId  “<subscription-id>”
```

**Resultado esperado**


![Texto de saída após recuperar dependências adicionais](./media/move-region-powershell/additional-missing-dependencies.png)

Neste exemplo, mais três dependências são identificadas como desaparecidas:

- Endereço IP público: psdemovm-ip
- Rede virtual Azure: psdemorm-vnet
- Grupo de segurança da rede (NSG): psdemovm-nsg

## <a name="add-additional-dependencies"></a>Adicionar dependências adicionais

1. [Siga as instruções](#add-dependencies-to-collection) para adicionar estes recursos à recolha do movimento.
2. Depois de adicionar recursos, valide novamente até que todas as dependências sejam adicionadas.


## <a name="prepare-and-move-the-source-resource-group"></a>Preparar e mover o grupo de recursos de origem

Na região de origem, prepara-se recursos antes de os mover. O processo de preparação depende dos recursos que está a mover. Por exemplo, para os recursos apátridas preparar pode preparar e exportar um modelo Azure Resource Manager (ARM). Ou, por recursos imponentes, a replicação pode começar. 

Antes de preparar os recursos de origem, tem de preparar e mover o grupo de recursos de origem.

### <a name="prepare"></a>Preparação

```azurepowershell-interactive
# Prepare the source resource group
Invoke-AzResourceMoverPrepare -SubscriptionId “<subscription-id>” -ResourceGroupName “RegionMoveRG-centralus-westcentralus ” -MoveCollectionName “MoveCollection-centralus-westcentralus ” -MoveResource “PSDemoRM”
```

**Resultado esperado**

![Texto de saída após preparar grupo de recursos de origem](./media/move-region-powershell/prepare-source-resource-group.png)

### <a name="initiate-move"></a>Iniciar movimento

```azurepowershell-interactive
# Initiate move
Invoke-AzResourceMoverInitiateMove -SubscriptionId “<subscription-id>” -ResourceGroupName “RegionMoveRG-centralus-westcentralus ” -MoveCollectionName “MoveCollection-centralus-westcentralus ” -MoveResource “PSDemoRM”

**Expected output**

![Output text after initiating move of the source resource group](./media/move-region-powershell/initiate-move-resource-group.png)


```azurepowershell-interactive
# Commit move
Invoke-AzResourceMoverCommit -SubscriptionId “<subscription-id” -ResourceGroupName “RegionMoveRG-centralus-westcentralus ” -MoveCollectionName “PS-centralus-
westcentralus-demoRM” -MoveResource “PSDemoRM”
```

**Resultado esperado**

![Texto de saída após comprometer grupo de recursos de origem](./media/move-region-powershell/commit-move-resource-group.png)


## <a name="prepare-resources"></a>Preparar recursos

Depois de o grupo de recursos de origem ser transferido para a região alvo, recupere uma lista dos recursos na recolha do movimento e prepare-se para os deslocar.

```azurepowershell-interactive
# Retrieve resources in the collection
Get-AzResourceMoverMoveResource  -SubscriptionId “ <subscription-id> “ -ResourceGroupName “RegionMoveRG-centralus-westcentralus ” -MoveCollectionName “MoveCollection-centralus-westcentralus ”   | Where-Object {  $_.MoveStatusMoveState -eq “PreparePending” } | Select Name
```
**Resultado esperado**

![Texto de saída após recuperar recursos na coleção](./media/move-region-powershell/collection-resources.png)

Agora, prepare os recursos.

```azurepowershell-interactive
# Prepare the resources, using the resource name
Invoke-AzResourceMoverPrepare -SubscriptionId  <subscription-id> -ResourceGroupName RegionMoveRG-centralus-westcentralus  -MoveCollectionName MoveCollection-centralus-westcentralus   -MoveResource $('psdemovm62', 'PSDemoVM-ip', 'PSDemoRM-vnet','PSDemoVM-nsg', ‘PSDemoVM’)
```
Em alternativa, pode preparar e mover recursos utilizando o ID do recurso, em vez do nome:

```azurepowershell-interactive
# Prepare the resources using the resource ID
Invoke-AzResourceMoverPrepare -SubscriptionId  <subscription-id> -ResourceGroupName RegionMoveRG-centralus-westcentralus  -MoveCollectionName MoveCollection-centralus-westcentralus  -MoveResourceInputType MoveResourceSourceId  -MoveResource $('/subscriptions/<subscription-id>/resourceGroups/PSDemoRM/providers/Microsoft.Network/networkSecurityGroups/PSDemoVM-nsg')
```

**Resultado esperado**

![Texto de saída após a preparação dos recursos na coleção](./media/move-region-powershell/collection-prepare.png)

## <a name="initiate-resource-move"></a>Iniciar movimento de recursos

Depois de preparar os recursos, inicie a mudança.

```azurepowershell-interactive
# Initiate the move 
Invoke-AzResourceMoverInitiateMove -SubscriptionId <subscription-id> -ResourceGroupName RegionMoveRG-centralus-westcentralus  -MoveCollectionName MoveCollection-centralus-westcentralus   -MoveResource $('psdemovm62', 'PSDemoVM-ip', 'PSDemoRM-vnet','PSDemoVM-nsg', ‘PSDemoVM’)
```
**Saída esperada** 
 ![ Texto de saída após iniciar movimento de recursos](./media/move-region-powershell/initiate-resource-move.png)

## <a name="discard-or-commit-the-move"></a>Descartar ou cometer o movimento

Após o movimento inicial, pode decidir se quer cometer o movimento ou descartá-lo. 

- **Descartar:** Pode descartar um movimento se estiver a testar e não pretende mover o recurso de origem. Descartar o movimento devolve o recurso a um estado de *movimento iniciado pendente*. Em seguida, pode iniciar o movimento novamente, se necessário.
- **Compromete-se**: Compromete-se a concluir a mudança para a região alvo. Após o compromisso, um recurso de origem estará num estado de *Eliminação de fonte pendente*, e pode decidir se pretende eliminá-lo.

### <a name="discard"></a>Eliminar

Para descartar o movimento:

```azurepowershell-interactive
# Discard the move 
Invoke-AzResourceMoverDiscard -SubscriptionId  <subscription-id> `-ResourceGroupName RegionMoveRG-centralus-westcentralus  -MoveCollectionName MoveCollection-centralus-westcentralus   -MoveResource $('psdemovm62', 'PSDemoVM-ip', 'PSDemoRM-vnet','PSDemoVM-nsg', ‘PSDemoVM’)
```
**Saída esperada** 
 ![ Texto de saída após descartar o movimento](./media/move-region-powershell/discard-move.png)


### <a name="commit"></a>Consolidação

Para cometer o movimento:

```azurepowershell-interactive
# Commit the move 
Invoke-AzResourceMoverCommit -SubscriptionId  <subscription-id> -ResourceGroupName RegionMoveRG-centralus-westcentralus  -MoveCollectionName MoveCollection-centralus-westcentralus   -MoveResource $('psdemovm62', 'PSDemoVM-ip', 'PSDemoRM-vnet','PSDemoVM-nsg', ‘PSDemoVM’)
```
**Resultado esperado**

![Texto de saída após cometer o movimento](./media/move-region-powershell/commit-move.png)

## <a name="delete-source-resources"></a>Eliminar recursos de origem

Depois de cometer a mudança, e verificar se os recursos funcionam como esperado na região alvo, pode eliminar os recursos de origem no [portal Azure](../azure-resource-manager/management/manage-resources-portal.md#delete-resources), [utilizando o PowerShell](../azure-resource-manager/management/manage-resources-powershell.md#delete-resources), ou [Azure CLI](../azure-resource-manager/management/manage-resources-cli.md#delete-resources).

## <a name="next-steps"></a>Passos seguintes

[Saiba como](remove-move-resources.md) remover recursos de uma coleção de movimentos.