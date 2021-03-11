---
title: Mover recursos através de regiões usando PowerShell em Azure Resource Mover
description: Saiba como mover recursos através de regiões usando PowerShell em Azure Resource Mover.
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: tutorial
ms.date: 02/21/2021
ms.author: raynew
ms.openlocfilehash: b728170521570ff0d83b67671109e82adb7fa7b0
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102584425"
---
# <a name="move-resources-across-regions-in-powershell"></a>Mover recursos através de regiões em PowerShell

Este artigo explica como mover os recursos Azure para uma região de Azure diferente, usando a PowerShell em [Azure Resource Mover](overview.md).

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Verifique os pré-requisitos e requisitos.
> * Crie a coleção de movimentos.
> * Adicione recursos à recolha de movimentos e resolva as dependências.
> * Prepare e mova o grupo de recursos de origem. 
> * Prepare e mova os outros recursos.
> * Decida se quer descartar ou cometer o movimento. 
> * Opcionalmente, remova recursos na região de origem após a mudança.

> [!NOTE]
> Os tutoriais mostram o caminho mais rápido para experimentar um cenário e usam opções padrão. 

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de começar. Em seguida, inscreva-se no [portal Azure](https://portal.azure.com).

## <a name="prerequisites"></a>Pré-requisitos
**Requisito** | **Descrição**
--- | ---
**Permissões de subscrição** | Verifique se tem acesso *ao Proprietário* na subscrição que contém os recursos que pretende mover<br/><br/> **Por que preciso de acesso ao proprietário?** A primeira vez que adiciona um recurso para um par de origem e destino específico numa subscrição do Azure, o Resource Mover cria uma [identidade gerida atribuída ao sistema](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) (anteriormente conhecida como Managed Service Identify (MSI)) que é fidedigna pela subscrição. Para criar a identidade e atribuir-lhe a função necessária (administrador de Acesso ao Utilizador ou Colaborador na subscrição de origem), a conta que utiliza para adicionar recursos necessita de permissões *do Proprietário* na subscrição. [Saiba mais](../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles) sobre os papéis do Azure.
**Suporte de mudança de recursos** | [Reveja](common-questions.md) as regiões apoiadas e outras questões comuns.
**Suporte VM** |  Verifique se quaisquer VMs que pretendam mover são suportados.<br/><br/> - [Verifique os](support-matrix-move-region-azure-vm.md#windows-vm-support) VMs do Windows suportados.<br/><br/> - [Verifique as](support-matrix-move-region-azure-vm.md#linux-vm-support) versões de Linux VMs e kernel suportadas.<br/><br/> - Verifique as definições [de computação](support-matrix-move-region-azure-vm.md#supported-vm-compute-settings)suportada, [armazenamento](support-matrix-move-region-azure-vm.md#supported-vm-storage-settings)e [rede.](support-matrix-move-region-azure-vm.md#supported-vm-networking-settings)
**Suporte SQL** | Se quiser mover recursos SQL, reveja a [lista de requisitos sql](tutorial-move-region-sql.md#check-sql-requirements).
**Assinatura de destino** | A subscrição na região de destino precisa de quota suficiente para criar os recursos que está a mover na região alvo. Se não tiver quota, [solicite limites adicionais.](../azure-resource-manager/management/azure-subscription-service-limits.md)
**Taxas da região de destino** | Verifique os preços e os encargos associados à região-alvo para a qual está a mover VMs. Use a [calculadora de preços](https://azure.microsoft.com/pricing/calculator/) para ajudá-lo.

### <a name="review-powershell-requirements"></a>Rever os requisitos powerShell

A maioria das operações de recursos de movimento são as mesmas, quer usando o portal Azure ou PowerShell, com algumas exceções.

**Operação** | **PowerShell** | **Portal**
--- | --- | ---
**Criar uma coleção de movimentos** | Uma coleção de movimentos (uma lista de todos os recursos que está a mover) é criada automaticamente. As permissões de identidade necessárias são atribuídas no backend pelo portal. | Utiliza-se cmdlets PowerShell para:<br/><br/> - Criar um grupo de recursos para a recolha de movimentos e especificar a localização para o mesmo.<br/><br/> - Atribuir uma identidade gerida à coleção.<br/><br/> - Adicione recursos à coleção.
**Remover uma coleção de movimento** | Não é possível remover diretamente uma coleção de movimentos no portal. | Utilize um cmdlet PowerShell para remover uma coleção de movimentos.
**Operações de movimento de recursos**<br/><br/> (Preparar, iniciar movimento, cometer etc.).| Passos únicos com validação automática por Resource Mover. | PowerShell cmdlets para:<br/><br/> 1) Validar as dependências.<br/><br/> 2) Execute o movimento.
**Eliminar recursos de origem** | Diretamente no portal Desemprovida de Recursos. | PowerShell cmdlets ao nível do tipo de recurso.


### <a name="sample-values"></a>Valores da amostra

Estamos a usar estes valores nos nossos exemplos de script:

**Definição** | **Valor** 
--- | ---
ID da subscrição | id de subscrição
Região de origem |  E.U.A. Central
Região-alvo | E.U.A. Centro-Oeste
Grupo de recursos (com metadados para recolha de movimentos) | RG-MoveCollection-demoRMS
Nome de coleção move | PS-centralus-westcentralus-demoRMS
Grupo de recursos (região de origem) | PSDemoRM 
Grupo de recursos (região alvo) | PsDemoRM-target
Localização do serviço De Movimento de Recursos | E.U.A. Leste 2
IdentidadeType | SistemaAsstribuído
VM para mover | PSDemovM


## <a name="sign-into-azure"></a>Iniciar sessão no Azure

Inscreva-se na sua assinatura Azure com o Connect-AzAccount cmdlet:

```azurepowershell-interactive
Connect-AzAccount – Subscription "<subscription-id>"
```

## <a name="set-up-the-move-collection"></a>Configurar a coleção de movimentos

O objeto MoveCollection armazena metadados e informações de configuração sobre os recursos que pretende mover. Para configurar uma coleção de movimentos, faça o seguinte:

- Crie um grupo de recursos para a recolha de movimentos.
- Registe o prestador de serviços na subscrição, para que o recurso MoveCollection possa ser criado.
- Crie o objeto MoveCollection com identidade gerida. Para que o objeto MoveCollection aceda à subscrição em que o serviço Desempreitado está localizado, necessita de uma [identidade gerida atribuída ao sistema](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) (anteriormente conhecida como Identidade de Serviço Gerido (MSI)) que seja fidedigna pela subscrição.
- Conceder acesso à subscrição de Resource Mover para a identidade gerida.

### <a name="create-the-resource-group"></a>Criar o grupo de recursos

Criar um grupo de recursos para a recolha de metadados e informações de configuração do movimento, da seguinte forma:

```azurepowershell-interactive
New-AzResourceGroup -Name "RG-MoveCollection-demoRMS" -Location "East US 2"
```
**Saída:**

![Texto de saída após criar grupo de recursos](./media/tutorial-move-region-powershell/create-metadata-resource-group.png)

### <a name="register-the-resource-provider"></a>Registar o fornecedor de recursos

1. Registe o fornecedor de recursos Microsoft.Migrate, para que o recurso MoveCollection possa ser criado da seguinte forma:

    ```azurepowershell-interactive
    Register-AzResourceProvider -ProviderNamespace Microsoft.Migrate
    
2. Wait for registration:

    ```azurepowershell-interactive 
    While(((Get-AzResourceProvider -ProviderNamespace Microsoft.Migrate)| where {$_.RegistrationState -eq "Registered" -and $_.ResourceTypes.ResourceTypeName -eq "moveCollections"}|measure).Count -eq 0)
    {
        Start-Sleep -Seconds 5
        Write-Output "Waiting for registration to complete."
    }
    ```
### <a name="create-a-movecollection-object"></a>Criar um objeto MoveCollection

Crie um objeto MoveCollection e atribua-lhe uma identidade gerida da seguinte forma: 

```azurepowershell-interactive
New-AzResourceMoverMoveCollection -Name "PS-centralus-westcentralus-demoRMS"  -ResourceGroupName "RG-MoveCollection-demoRMS" -SourceRegion "centralus" -TargetRegion "westcentralus" -Location "centraluseuap" -IdentityType "SystemAssigned"
```

**Saída:**

![Texto de saída após criar coleção de movimento](./media/tutorial-move-region-powershell/output-move-collection.png)


### <a name="grant-access-to-the-managed-identity"></a>Conceder acesso à identidade gerida

Conceder o acesso de identidade gerido à subscrição de Resource Mover da seguinte forma. Deve ser o proprietário da assinatura.

1. Recupere os detalhes de identidade do objeto MoveCollection.

    ```azurepowershell-interactive
    $moveCollection = Get-AzResourceMoverMoveCollection -SubscriptionId $subscriptionId -ResourceGroupName "RG-MoveCollection-demoRMS" -Name "PS-centralus-westcentralus-demoRMS"

    $identityPrincipalId = $moveCollection.IdentityPrincipalId   
    ``` 

2. Atribua as funções necessárias à identidade para que o Azure Resource Mover possa aceder à sua subscrição para ajudar a mover recursos.

    ```azurepowershell-interactive
    New-AzRoleAssignment -ObjectId $identityPrincipalId -RoleDefinitionName Contributor -Scope "/subscriptions/$subscriptionId"

    New-AzRoleAssignment -ObjectId $identityPrincipalId -RoleDefinitionName "User Access Administrator" -Scope "/subscriptions/$subscriptionId"
    ``` 

## <a name="add-resources-to-the-move-collection"></a>Adicione recursos à coleção de movimentos

Recupere as identificações para os recursos de origem existentes que pretende mover. Crie o objeto de definições de recursos de destino e, em seguida, adicione recursos à recolha de movimentos.

> [!NOTE]
> Os recursos adicionados a uma recolha de movimentos devem estar na mesma subscrição, mas podem estar em diferentes grupos de recursos.

Adicione os recursos da seguinte forma:

1. Obtenha o ID do recurso de origem:

    ```azurepowershell-interactive
    Get-AzResource -Name PSDemoVM -ResourceGroupName PSDemoRM
    ```

    **Saída**

    ![Texto de saída após recuperar o ID do recurso](./media/tutorial-move-region-powershell/output-retrieve-resource.png)

2. Crie o objeto de definição de recursos-alvo de acordo com o recurso que está a mover. No nosso caso, é um VM.

    ```azurepowershell-interactive
    $targetResourceSettingsObj = New-Object Microsoft.Azure.PowerShell.Cmdlets.ResourceMover.Models.Api202101.VirtualMachineResourceSettings
    ```

3. Desajei o tipo de recurso e o nome do recurso alvo para o objeto.

    ```azurepowershell-interactive
    $targetResourceSettingsObj.ResourceType = "Microsoft.Compute/virtualMachines"
    $targetResourceSettingsObj.TargetResourceName = "PSDemoVM"
    ```
    > [!NOTE]
    > O nosso VM alvo tem o mesmo nome que o VM na região origem. Pode escolher um nome diferente.

4. Adicione os recursos de origem à recolha do movimento, utilizando o ID de recurso e o objeto de definição de destino que recuperou/criou.

    ```azurepowershell-interactive
    Add-AzResourceMoverMoveResource -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS" -SourceId "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx xxxxxxxxxxxx/resourceGroups/
    PSDemoRM/providers/Microsoft.Compute/virtualMachines/PSDemoVM" -Name "PSDemoVM" -ResourceSetting $targetResourceSettingsObj
    ```

    **Saída** ![ Texto de saída após a adição do recurso](./media/tutorial-move-region-powershell/output-add-resource.png)

## <a name="validate-and-add-dependencies"></a>Validar e adicionar dependências

Verifique se os recursos adicionados têm alguma dependência de outros recursos, e adicione se necessário. 

1. Validar as dependências da seguinte forma:

    ```azurepowershell-interactive
    Resolve-AzResourceMoverMoveCollectionDependency -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS"
    ```
    **Saída (quando existem dependências)**

    ![Texto de saída após validação de dependências](./media/tutorial-move-region-powershell/dependency-output.png)

2. Dependências de falta de identidade:

    - Para recuperar uma lista de todas as dependências em falta:

        ```azurepowershell-interactive
        Get-AzResourceMoverUnresolvedDependency -MoveCollectionName "PS-centralus-westcentralus-demoRMS" -ResourceGroupName "RG-MoveCollection-demoRMS" -DependencyLevel Descendant"
        ```
        **Saída** ![ Texto de saída após recuperar uma lista de todas as dependências](./media/tutorial-move-region-powershell/dependencies-list.png)  

    - Para recuperar apenas dependências de primeiro nível (dependências diretas para o recurso):

        ```azurepowershell-interactive
        Get-AzResourceMoverUnresolvedDependency -MoveCollectionName "PS-centralus-westcentralus-demoRMS" -ResourceGroupName "RG-MoveCollection-demoRMS" -DependencyLevel Direct
        ```
        **Saída** ![ Texto de saída após recuperar uma lista de dependências de primeiro nível](./media/tutorial-move-region-powershell/dependencies-list-direct.png)  

3. Para adicionar quaisquer dependências pendentes pendentes, repita as instruções acima para [adicionar recursos à recolha de movimentos](#add-resources-to-the-move-collection), e revalidar até não existirem recursos pendentes.

> [!NOTE]
> Se por alguma razão pretender retirar os recursos da recolha de recursos, siga as instruções [deste artigo](remove-move-resources.md).

## <a name="add-the-source-resource-group"></a>Adicione o grupo de recursos de origem

Adicione o grupo de recursos de origem que contém recursos que pretende mover para a recolha de movimentos.

1. Recupere a identificação do grupo de recursos.

    ```azurepowershell-interactive
    Get-AzResourceMoverUnresolvedDependency -MoveCollectionName "PS-centralus-westcentralus-demoRMS" -ResourceGroupName "RG-MoveCollection-demoRMS" -DependencyLevel Direct
    ```
    **Saída** ![ Texto de saída após a recuperação do ID do grupo de recursos de origem](./media/tutorial-move-region-powershell/source-resource-group-id.png)  

    > [!NOTE]
    > Estamos a usar um grupo de recursos que já está na região alvo.

 
2. Utilize o ID recuperado para adicionar o grupo de recursos à coleção.

    ```azurepowershell-interactive
    Add-AzResourceMoverMoveResource -ResourceGroupName "RG-MoveCollection-demoRMS"  -MoveCollectionName "PS-centralus-westcentralus-demoRMS" -SourceId "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/psdemorm"  -Name "psdemorm"  -ExistingTargetId "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/PSDemoRM-target"
    ```
    **Saída** ![ Texto de saída após a adição do grupo de recursos de origem à coleção de movimentos](./media/tutorial-move-region-powershell/add-source-resource-group.png)

3. Verifique as dependências para se certificar de que não perdeu nada depois de adicionar o grupo de recursos.

    ```azurepowershell-interactive
    Resolve-AzResourceMoverMoveCollectionDependency -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS"
    ```
4. Vemos que não há dependências pendentes.

    **Saída** ![ Texto de saída após verificação das dependências](./media/tutorial-move-region-powershell/all-dependencies-added.png)


## <a name="prepare-resources"></a>Preparar recursos

Normalmente é preciso preparar recursos na região de origem antes da mudança. Por exemplo:

- Para mover recursos apátridas, tais como redes virtuais Azure, adaptadores de rede, equilibradores de carga e grupos de segurança de rede, poderá ser necessário exportar um modelo de Gestor de Recursos Azure.
- Para mover recursos imponentes como VMs Azure e bases de dados SQL, você pode precisar começar a replicar recursos da fonte para a região de destino.

Neste tutorial, já que estamos a mover VMs, precisamos de preparar o grupo de recursos de origem, e depois iniciar e comprometer o seu movimento, antes de começarmos a preparar VMs.

> [!NOTE]
> Se tiver um grupo de recursos-alvo existente, pode comprometer diretamente o movimento para o grupo de recursos de origem e saltar as fases de preparação e iniciar a mudança.

  
### <a name="prepare-the-source-resource-group"></a>Preparar o grupo de recursos de origem:

1. Preparar o grupo de recursos:

    ```azurepowershell-interactive
    Invoke-AzResourceMoverPrepare -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS"  -MoveResource “PSDemoRM”
    ```
    **Saída**

    ![Texto de saída após preparar grupo de recursos de origem](./media/tutorial-move-region-powershell/prepare-source-resource-group.png)

2. Inicie a deslocação do grupo de recursos de origem.

    ```azurepowershell-interactive
    "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS"  -MoveResource “PSDemoRM”
    ```
    ![Texto de saída após iniciar movimento do grupo de recursos de origem](./media/tutorial-move-region-powershell/initiate-move-source-resource-group.png)

3. Comprometa o movimento para o grupo de recursos de origem.

    ```azurepowershell-interactive
    Invoke-AzResourceMoverCommit -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS"  -MoveResource “PSDemoRM”
    ```
    **Saída**

    ![Texto de saída após cometer o grupo de recursos de origem](./media/tutorial-move-region-powershell/commit-move-source-resource-group.png)


### <a name="prepare-vm-resources"></a>Preparar recursos VM

Depois de preparar e mover o grupo de recursos de origem, podemos preparar recursos VM para a mudança.

1. Valide as dependências antes de preparar os recursos VM.

    ```azurepowershell-interactive
    $resp = Invoke-AzResourceMoverPrepare -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS"  -MoveResource $('psdemovm') -ValidateOnly
    ```
    **Saída**

    ![Texto de saída após validação do VM antes de prepará-lo](./media/tutorial-move-region-powershell/validate-vm-before-move.png)

2. Obtenha os recursos dependentes que precisam ser preparados juntamente com o VM.

    ```azurepowershell-interactive
    $resp.AdditionalInfo[0].InfoMoveResource
    ```
    **Saída**

    ![Texto de saída após recuperar recursos VM dependentes](./media/tutorial-move-region-powershell/get-resources-before-prepare.png)

3. Inicie o processo de preparação para todos os recursos dependentes.

    ```azurepowershell-interactive
    Invoke-AzResourceMoverPrepare -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS"  -MoveResource $('PSDemoVM','psdemovm111', 'PSDemoRM-vnet','PSDemoVM-nsg')
    ```
    **Saída**

    ![Texto de saída após preparar todos os recursos](./media/tutorial-move-region-powershell/initiate-prepare-all.png)


    > [!NOTE]
    > Pode fornecer o ID do recurso de origem em vez do nome do recurso como parâmetros de entrada para o cmdlet Preparar, bem como nos cmdlets Iniciar e Comprometer. Para fazê-lo, execute:


    ```azurepowershell-interactive
        Invoke-AzResourceMoverPrepare -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS" -MoveResourceInputType MoveResourceSourceId  -MoveResource $('/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/PSDemoRMS/providers/Microsoft.Network/networkSecurityGroups/PSDemoVM-nsg')
    ```

## <a name="initiate-move-of-vm-resources"></a>Iniciar a movimentação de recursos VM

1. Verifique se os recursos VM estão num estado *de iniciação pendente:*

    ```azurepowershell-interactive
    Get-AzResourceMoverMoveResource  -SubscriptionId “ xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx “ -ResourceGroupName “RG-MoveCollection-demoRMS” -MoveCollectionName “PS-centralus-westcentralus-demoRMS ”   | Where-Object {  $_.MoveStatusMoveState -eq “InitiateMovePending” } | Select Name
    ```    

    **Saída**

    ![Texto de saída após verificação do estado de início](./media/tutorial-move-region-powershell/verify-initiate-move-pending.png)

2. Iniciar o movimento:

    ```azurepowershell-interactive
    Invoke-AzResourceMoverInitiateMove -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS"  -MoveResource $('psdemovm111', 'PSDemoRM-vnet','PSDemoVM-nsg', ‘PSDemoVM’) -MoveResourceInputType "MoveResourceId"
    ```    

    **Saída**

    ![Texto de saída após iniciar a movimentação de recursos](./media/tutorial-move-region-powershell/initiate-resources-move.png)


## <a name="discard-or-commit"></a>Descartar ou comprometer-se?

Após o movimento inicial, pode decidir se quer cometer o movimento ou descartá-lo. 

- **Descartar:** Pode descartar um movimento se estiver a testar e não pretende mover o recurso de origem. Descartar o movimento devolve o recurso a um estado de *movimento iniciado pendente*. Em seguida, pode iniciar o movimento novamente, se necessário.
- **Compromete-se**: Compromete-se a concluir a mudança para a região alvo. Após o compromisso, um recurso de origem estará num estado de *Eliminação de fonte pendente*, e pode decidir se pretende eliminá-lo.

### <a name="discard-the-move"></a>Descartar o movimento

Para descartar o movimento:

```azurepowershell-interactive
Invoke-AzResourceMoverDiscard -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS"  -MoveResource $('psdemovm111', 'PSDemoRM-vnet','PSDemoVM-nsg', ‘PSDemoVM’) -MoveResourceInputType "MoveResourceId"
```
**Saída**

![Texto de saída após descartar o movimento](./media/tutorial-move-region-powershell/discard-move.png)


### <a name="commit-the-move"></a>Cometa o movimento

1. Cometa o movimento da seguinte forma:

    ```azurepowershell-interactive
    Invoke-AzResourceMoverCommit -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS"  -MoveResource $('psdemovm111', 'PSDemoRM-vnet','PSDemoVM-nsg', ‘PSDemoVM’) -MoveResourceInputType "MoveResourceId"
    ```
    **Saída**

    ![Texto de saída após cometer o movimento](./media/tutorial-move-region-powershell/commit-move.png)

2. Verifique se todos os recursos se deslocaram para a região alvo:

    ```azurepowershell-interactive
    Get-AzResourceMoverMoveResource  -ResourceGroupName “RG-MoveCollection-demoRMS ” -MoveCollectionName “PS-centralus-westcentralus-demoRMS”   
    ```
    Todos os recursos estão agora num estado *de exclusão pendente* na região alvo.

## <a name="delete-source-resources"></a>Eliminar recursos de origem

Depois de cometer a mudança, e verificar se os recursos funcionam como esperado na região alvo, pode eliminar cada recurso de origem no [portal Azure](../azure-resource-manager/management/manage-resources-portal.md#delete-resources), [utilizando o PowerShell](../azure-resource-manager/management/manage-resources-powershell.md#delete-resources), ou [Azure CLI](../azure-resource-manager/management/manage-resources-cli.md#delete-resources).

## <a name="next-steps"></a>Passos seguintes

Neste tutorial:

> [!div class="checklist"]
> * Mudou os VMs do Azure para outra região de Azure usando a PowerShell.
> * Os recursos transferidos associados aos VMs para outra região.

Agora, tentando mover VMs Azure usando o portal

> [!div class="nextstepaction"]
> [Mover VMs Azure no portal](./tutorial-move-region-virtual-machines.md)


