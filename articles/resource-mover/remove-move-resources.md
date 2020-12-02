---
title: Remover recursos de uma coleção de movimentos em Azure Resource Mover
description: Saiba como remover recursos de uma coleção de movimentos em Azure Resource Mover.
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: how-to
ms.date: 11/30/2020
ms.author: raynew
ms.openlocfilehash: 63548e2bf470c012e0dd8a5f879a51eeb631f453
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96459285"
---
# <a name="manage-move-collections-and-resource-groups"></a>Gerir recolhas de movimentos e grupos de recursos

Este artigo descreve como remover recursos de uma recolha de movimentos, ou remover um grupo de recolha/recursos de movimento, em [Azure Resource Mover](overview.md). As coleções de movimento são usadas para mover recursos Azure entre as regiões de Azure.

## <a name="remove-a-resource-portal"></a>Remover um recurso (portal)

Pode remover recursos numa recolha de movimentos, no portal Resource Mover da seguinte forma:

1. Em **Regiões,** selecione todos os recursos que pretende remover da recolha e selecione **Remover**. 

    ![Botão para selecionar para remover](./media/remove-move-resources/portal-select-resources.png)

2. Nos **recursos de remoção,** clique em **Remover**.

    ![Botão para selecionar para remover recursos de uma coleção de movimento](./media/remove-move-resources/remove-portal.png)

## <a name="remove-a-move-collectionresource-group-portal"></a>Remover um grupo de recolha/recursos de movimento (portal)

Pode remover um grupo de recolha/recursos em movimento no portal.

1. Siga as instruções do procedimento acima para retirar os recursos da recolha. Se estiver a remover um grupo de recursos, certifique-se de que não contém recursos.
2. Elimine a recolha de movimentos ou o grupo de recursos.  

## <a name="remove-a-resource-powershell"></a>Remover um recurso (PowerShell)

Remova um recurso (no nosso exemplo, as máquinas PSDemoVM) de uma coleção utilizando o PowerShell, da seguinte forma:

```azurepowershell-interactive
# Remove a resource using the resource ID
Remove-AzResourceMoverMoveResource -SubscriptionId  <subscription-id> -ResourceGroupName RegionMoveRG-centralus-westcentralus  -MoveCollectionName MoveCollection-centralus-westcentralus -Name PSDemoVM
```
**Resultado esperado**

![Texto de saída após remover um recurso de uma coleção de movimento](./media/remove-move-resources/remove-resource.png)

## <a name="remove-a-collection-powershell"></a>Remover uma coleção (PowerShell)

Remova uma coleção de movimentos inteira usando o PowerShell, da seguinte forma:

1. Siga as instruções acima para remover os recursos da recolha utilizando o PowerShell.
2. Executar:

    ```azurepowershell-interactive
    # Remove a resource using the resource ID
    Remove-AzResourceMoverMoveCollection -SubscriptionId <subscription-id> -ResourceGroupName RegionMoveRG-centralus-westcentralus -MoveCollectionName MoveCollection-centralus-westcentralus
    ```
    **Resultado esperado**
    
    ![Texto de saída após a remoção de uma coleção de movimento](./media/remove-move-resources/remove-collection.png)

## <a name="vm-resource-state-after-removing"></a>Estado de recursos VM após a remoção

O que acontece quando se remove um recurso VM de uma recolha de movimentos depende do estado de recursos, como resumido na tabela.

###  <a name="remove-vm-state"></a>Remover o estado de VM
**Estado dos recursos** | **VM** | **Rede**
--- | --- | --- 
**Adicionado para mover coleção** | Apagar da coleção de movimentos. | Apagar da coleção de movimentos. 
**Dependências resolvidas/preparar pendentes** | Excluir da coleção de movimentos  | Apagar da coleção de movimentos. 
**Preparar em curso**<br/> (ou qualquer outro estado em curso) | A operação de eliminação falha com erro.  | A operação de eliminação falha com erro.
**Preparar falhou** | Apague da coleção de movimentos.<br/>Elimine qualquer coisa criada na região alvo, incluindo discos de réplica. <br/><br/> Os recursos de infraestrutura criados durante a mudança devem ser eliminados manualmente. | Apague da coleção de movimentos.  
**Iniciar a mudança pendente** | Apagar da coleção de movimentos.<br/><br/> Elimine qualquer coisa criada na região alvo, incluindo VM, discos de réplica, etc.  <br/><br/> Os recursos de infraestrutura criados durante a mudança devem ser eliminados manualmente. | Apagar da coleção de movimentos.
**Iniciar movimento falhou** | Apagar da coleção de movimentos.<br/><br/> Elimine qualquer coisa criada na região alvo, incluindo VM, discos de réplica, etc.  <br/><br/> Os recursos de infraestrutura criados durante a mudança devem ser eliminados manualmente. | Apagar da coleção de movimentos.
**Comprometer-se pendente** | Recomendamos que deite fora o movimento de modo a que os recursos-alvo sejam eliminados primeiro.<br/><br/> O recurso remonta ao **movimento Iniciar pendente,** e pode continuar a partir daí. | Recomendamos que deite fora o movimento de modo a que os recursos-alvo sejam eliminados primeiro.<br/><br/> O recurso remonta ao **movimento Iniciar pendente,** e pode continuar a partir daí. 
**Cometer falhou** | Recomendamos que elimine o de modo a que os recursos-alvo sejam eliminados primeiro.<br/><br/> O recurso remonta ao **movimento Iniciar pendente,** e pode continuar a partir daí. | Recomendamos que deite fora o movimento de modo a que os recursos-alvo sejam eliminados primeiro.<br/><br/> O recurso remonta ao **movimento Iniciar pendente,** e pode continuar a partir daí.
**Devoluções concluídas** | O recurso remonta ao **movimento Iniciar pendente.**<br/><br/> É eliminado da coleção de movimentos, juntamente com qualquer coisa criada no alvo - VM, discos de réplica, cofre etc.  <br/><br/> Os recursos de infraestrutura criados durante a mudança devem ser eliminados manualmente. <br/><br/> Os recursos de infraestrutura criados durante a mudança devem ser eliminados manualmente. |  O recurso remonta ao **movimento Iniciar pendente.**<br/><br/> É apagado da coleção de movimentos.
**Devoluções falhadas** | Recomendamos que elimine os movimentos de modo a que os recursos-alvo sejam eliminados primeiro.<br/><br/> Depois disso, o recurso volta para o **movimento Iniciar pendente,** e você pode continuar a partir daí. | Recomendamos que elimine os movimentos de modo a que os recursos-alvo sejam eliminados primeiro.<br/><br/> Depois disso, o recurso volta para o **movimento Iniciar pendente,** e você pode continuar a partir daí.
**Eliminar fonte pendente** | Apagado da coleção de movimentos.<br/><br/> Não apaga nada criado na região alvo.  | Apagado da coleção de movimentos.<br/><br/> Não apaga nada criado na região alvo.
**Eliminar fonte falhou** | Apagado da coleção de movimentos.<br/><br/> Não apaga nada criado na região alvo. | Apagado da coleção de movimentos.<br/><br/> Não apaga nada criado na região alvo.

## <a name="sql-resource-state-after-removing"></a>Estado de recursos SQL após a remoção

O que acontece quando remove um recurso Azure SQL de uma recolha de movimentos depende do estado de recursos, como resumido na tabela.

**Estado dos recursos** | **SQL** 
--- | --- 
**Adicionado para mover coleção** | Apagar da coleção de movimentos. 
**Dependências resolvidas/preparar pendentes** | Excluir da coleção de movimentos 
**Preparar em curso**<br/> (ou qualquer outro estado em curso)  | A operação de eliminação falha com erro. 
**Preparar falhou** | Excluir da coleção de movimentos<br/><br/>Elimine tudo o que for criado na região alvo. 
**Iniciar a mudança pendente** |  Excluir da coleção de movimentos<br/><br/>Elimine tudo o que for criado na região alvo. A base de dados SQL existe neste momento e será eliminada. 
**Iniciar movimento falhou** | Excluir da coleção de movimentos<br/><br/>Elimine tudo o que for criado na região alvo. A base de dados SQL existe neste momento e deve ser eliminada. 
**Comprometer-se pendente** | Recomendamos que deite fora o movimento de modo a que os recursos-alvo sejam eliminados primeiro.<br/><br/> O recurso remonta ao **movimento Iniciar pendente,** e pode continuar a partir daí.
**Cometer falhou** | Recomendamos que deite fora o movimento de modo a que os recursos-alvo sejam eliminados primeiro.<br/><br/> O recurso remonta ao **movimento Iniciar pendente,** e pode continuar a partir daí. 
**Devoluções concluídas** |  O recurso remonta ao **movimento Iniciar pendente.**<br/><br/> É eliminado da coleção de movimentos, juntamente com qualquer coisa criada no alvo, incluindo bases de dados SQL. 
**Devoluções falhadas** | Recomendamos que elimine os movimentos de modo a que os recursos-alvo sejam eliminados primeiro.<br/><br/> Depois disso, o recurso volta para o **movimento Iniciar pendente,** e você pode continuar a partir daí. 
**Eliminar fonte pendente** | Apagado da coleção de movimentos.<br/><br/> Não apaga nada criado na região alvo. 
**Eliminar fonte falhou** | Apagado da coleção de movimentos.<br/><br/> Não apaga nada criado na região alvo. 

## <a name="next-steps"></a>Passos seguintes

Tente [mover um VM](tutorial-move-region-virtual-machines.md) para outra região com o Resource Mover.
