---
title: Remover recursos de uma coleção de movimentos em Azure Resource Mover
description: Saiba como remover recursos de uma coleção de movimentos em Azure Resource Mover.
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: how-to
ms.date: 09/08/2020
ms.author: raynew
ms.openlocfilehash: 241ccbda67f7a2518d0c44a0d362673922ad4284
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89653130"
---
# <a name="remove-resources-from-a-move-collection"></a>Remover recursos de uma coleção de movimentação

Este artigo descreve como remover recursos de uma coleção de movimentos no [Azure Resource Mover](overview.md). As coleções de movimento são usadas para mover recursos Azure entre as regiões de Azure.

## <a name="remove-a-resource-portal"></a>Remover um recurso (portal)

Remover no portal de mudanças de recursos da seguinte forma:

1. Em **Regiões ,** selecione os recursos que pretende remover da recolha > **Remover**.

    ![Botão para selecionar para remover](./media/remove-move-resources/portal-select-resources.png)

1. Nos **recursos de remoção,** clique em **Remover**.

    ![Botão para selecionar para remover recursos de uma coleção de movimento](./media/remove-move-resources/remove-portal.png)

## <a name="remove-a-resource-powershell"></a>Remover um recurso (PowerShell)

Remova um recurso (no nosso exemplo, as máquinas PSDemoVM) de uma coleção utilizando o PowerShell, da seguinte forma:

```azurepowershell-interactive
# Remove a resource using the resource ID
Remove-AzResourceMoverMoveResource -SubscriptionId  <subscription-id> -ResourceGroupName RegionMoveRG-centralus-westcentralus  -MoveCollectionName MoveCollection-centralus-westcentralus - Name PSDemoVM
```
**Saída esperada** 
 ![ Texto de saída após remover um recurso de uma coleção de movimento](./media/remove-move-resources/remove-resource.png)

## <a name="remove-a-collection-powershell"></a>Remover uma coleção (PowerShell)

Remova uma coleção de movimentos inteira usando o PowerShell, da seguinte forma:

```azurepowershell-interactive
# Remove a resource using the resource ID
Remove-AzResourceMoverMoveResource -SubscriptionId  <subscription-id> -ResourceGroupName RegionMoveRG-centralus-westcentralus  -MoveCollectionName MoveCollection-centralus-westcentralus 
```
**Saída esperada** 
 ![ Texto de saída após a remoção de uma coleção de movimento](./media/remove-move-resources/remove-collection.png)

## <a name="vm-resource-state-after-removing"></a>Estado de recursos VM após a remoção

O que acontece quando se remove um recurso VM de uma recolha de movimentos depende do estado de recursos, como resumido na tabela.

###  <a name="remove-vm-state"></a>Remover o estado de VM
**Estado dos recursos** | **VM** | **Redes**
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