---
title: Mover um espaço de trabalho Log Analytics no Azure Monitor | Microsoft Docs
description: Saiba como mover seu espaço de trabalho Log Analytics para outra assinatura ou grupo de recursos.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/13/2019
ms.openlocfilehash: fd7ff7aa2275defba57aa24b5ef0b9adc78a5355
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74093790"
---
# <a name="move-a-log-analytics-workspace-to-different-subscription-or-resource-group"></a>Mover um espaço de trabalho Log Analytics para uma assinatura ou grupo de recursos diferente

Neste artigo, você aprenderá as etapas para mover Log Analytics espaço de trabalho para outro grupo de recursos ou assinatura na mesma região. Você pode aprender mais sobre como mover recursos do Azure por meio do portal do Azure, do PowerShell, do CLI do Azure ou da API REST. em [mover recursos para um novo grupo de recursos ou assinatura](../../azure-resource-manager/resource-group-move-resources.md). 

> [!IMPORTANT]
> Não é possível mover um espaço de trabalho para uma região diferente.

## <a name="verify-active-directory-tenant"></a>Verificar Active Directory locatário
As assinaturas de origem e destino do espaço de trabalho devem existir dentro do mesmo locatário Azure Active Directory. Use Azure PowerShell para verificar se ambas as assinaturas têm a mesma ID de locatário.

``` PowerShell
(Get-AzSubscription -SubscriptionName <your-source-subscription>).TenantId
(Get-AzSubscription -SubscriptionName <your-destination-subscription>).TenantId
```

## <a name="remove-solutions"></a>Remover soluções
As soluções gerenciadas que estão instaladas no espaço de trabalho serão movidas com a operação de movimentação do espaço de trabalho Log Analytics. Como você deve remover o link do espaço de trabalho para qualquer conta de automação, no entanto, as soluções que dependem desse link devem ser removidas.

As soluções que devem ser removidas incluem o seguinte: 

- Gestão de Atualizações
- Monitorização de Alterações
- Iniciar/Parar VMs durante horas de inatividade


### <a name="azure-portal"></a>Portal do Azure
Use o procedimento a seguir para remover as soluções usando o portal do Azure:

1. Abra o menu do grupo de recursos no qual as soluções estão instaladas.
2. Selecione as soluções a serem removidas.
3. Clique em **excluir recursos** e confirme os recursos a serem removidos clicando em **excluir**.

![Excluir soluções](media/move-workspace/delete-solutions.png)

### <a name="powershell"></a>PowerShell

Para remover as soluções usando o PowerShell, use o cmdlet [Remove-AzResource](/powershell/module/az.resources/remove-azresource?view=azps-2.8.0) , conforme mostrado no exemplo a seguir:

``` PowerShell
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "ChangeTracking(<workspace-name>)" -ResourceGroupName <resource-group-name>
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Updates(<workspace-name>)" -ResourceGroupName <resource-group-name>
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Start-Stop-VM(<workspace-name>)" -ResourceGroupName <resource-group-name>
```

## <a name="remove-alert-rules"></a>Remover regras de alerta
Para a solução **iniciar/parar VMs** , você também precisa remover as regras de alerta criadas pela solução. Use o procedimento a seguir no portal do Azure para remover essas regras.

1. Abra o menu **monitorar** e, em seguida, selecione **alertas**.
2. Clique em **gerenciar regras de alerta**.
3. Selecione as três regras de alerta a seguir e clique em **excluir**.

   - AutoStop_VM_Child
   - ScheduledStartStop_Parent
   - SequencedStartStop_Parent

    ![Excluir regras](media/move-workspace/delete-rules.png)

## <a name="unlink-automation-account"></a>Desvincular conta de automação
Use o procedimento a seguir para desvincular a conta de automação do espaço de trabalho usando o portal do Azure:

1. Abra o menu **contas de automação** e selecione a conta a ser removida.
2. Na seção **recursos relacionados** do menu, selecione espaço de **trabalho vinculado**. 
3. Clique em **desvincular espaço de trabalho** para desvincular o espaço de trabalho da sua conta de automação.

    ![Desvincular espaço de trabalho](media/move-workspace/unlink-workspace.png)

## <a name="move-your-workspace"></a>Mover seu espaço de trabalho

### <a name="azure-portal"></a>Portal do Azure
Use o procedimento a seguir para mover seu espaço de trabalho usando o portal do Azure:

1. Abra o menu **log Analytics espaços de trabalho** e selecione seu espaço de trabalho.
2. Na página **visão geral** , clique em **alterar** ao lado de **grupo de recursos** ou **assinatura**.
3. Uma nova página é aberta com uma lista de recursos relacionados ao espaço de trabalho. Selecione os recursos a serem movidos para a mesma assinatura de destino e grupo de recursos que o espaço de trabalho. 
4. Selecione uma **assinatura** de destino e um **grupo de recursos**. Se você estiver movendo o espaço de trabalho para outro grupo de recursos na mesma assinatura, você não verá a opção de **assinatura** .
5. Clique em **OK** para mover o espaço de trabalho e os recursos selecionados.

    ![Portal](media/move-workspace/portal.png)

### <a name="powershell"></a>PowerShell
Para mover seu espaço de trabalho usando o PowerShell, use o [move-AzResource](/powershell/module/AzureRM.Resources/Move-AzureRmResource) como no exemplo a seguir:

``` PowerShell
Move-AzResource -ResourceId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyResourceGroup01/providers/Microsoft.OperationalInsights/workspaces/MyWorkspace" -DestinationSubscriptionId "00000000-0000-0000-0000-000000000000" -DestinationResourceGroupName "MyResourceGroup02"
```



> [!IMPORTANT]
> Após a operação de movimentação, as soluções removidas e o link da conta de automação devem ser reconfigurados para colocar o espaço de trabalho de volta ao estado anterior.


## <a name="next-steps"></a>Passos seguintes
- Para obter uma lista dos recursos que dão suporte à movimentação, consulte [mover suporte de operação para recursos](../../azure-resource-manager/move-support-resources.md).
