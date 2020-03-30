---
title: Mova um espaço de trabalho log Analytics no Monitor Azure [ Microsoft Docs
description: Saiba como mover o seu espaço de trabalho Log Analytics para outra subscrição ou grupo de recursos.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/13/2019
ms.openlocfilehash: 9213ddf034e725f6e31c9280d47bd13e4703b3f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77659497"
---
# <a name="move-a-log-analytics-workspace-to-different-subscription-or-resource-group"></a>Mova um espaço de trabalho log Analytics para diferentes subscrições ou grupo de recursos

Neste artigo, você aprenderá os passos para mover o espaço de trabalho log Analytics para outro grupo de recursos ou subscrição na mesma região. Você pode aprender mais sobre mover recursos Azure através do portal Azure, PowerShell, o Azure CLI ou a REST API. [na Move recursos para um novo grupo de recursos ou subscrição.](../../azure-resource-manager/management/move-resource-group-and-subscription.md) 

> [!IMPORTANT]
> Não se pode mover um espaço de trabalho para outra região.

## <a name="verify-active-directory-tenant"></a>Verificar inquilino de Diretório Ativo
As assinaturas de fonte de espaço de trabalho e destino devem existir dentro do mesmo inquilino azure Ative Directory. Utilize o Azure PowerShell para verificar se ambas as subscrições têm o mesmo ID de inquilino.

``` PowerShell
(Get-AzSubscription -SubscriptionName <your-source-subscription>).TenantId
(Get-AzSubscription -SubscriptionName <your-destination-subscription>).TenantId
```

## <a name="workspace-move-considerations"></a>Workspace move considerações
As soluções geridas instaladas no espaço de trabalho serão movidas com a operação de movimento do espaço de trabalho Log Analytics. Os agentes conectados permanecerão ligados e manterão o envio de dados para o espaço de trabalho após a mudança. Uma vez que a operação de movimento requer que não exista ligação do espaço de trabalho a qualquer conta de automação, as soluções que dependem desse link devem ser removidas.

Soluções que devem ser removidas antes de poder desligar a sua conta de automação:

- Gestão de Atualizações
- Monitorização de Alterações
- Iniciar/Parar VMs durante horas de inatividade


### <a name="delete-in-azure-portal"></a>Eliminar no portal do Azure
Utilize o seguinte procedimento para remover as soluções utilizando o portal Azure:

1. Abra o menu para o grupo de recursos onde estão instaladas quaisquer soluções.
2. Selecione as soluções para remover.
3. Clique em **Eliminar Recursos** e, em seguida, confirmar os recursos a remover clicando **em Eliminar**.

![Eliminar soluções](media/move-workspace/delete-solutions.png)

### <a name="delete-using-powershell"></a>Eliminar usando powerShell

Para remover as soluções utilizando o PowerShell, utilize o cmdlet [Remove-AzResource,](/powershell/module/az.resources/remove-azresource?view=azps-2.8.0) tal como mostrado no seguinte exemplo:

``` PowerShell
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "ChangeTracking(<workspace-name>)" -ResourceGroupName <resource-group-name>
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Updates(<workspace-name>)" -ResourceGroupName <resource-group-name>
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Start-Stop-VM(<workspace-name>)" -ResourceGroupName <resource-group-name>
```

### <a name="remove-alert-rules"></a>Remover regras de alerta
Para a solução **Start/Stop VMs,** também é necessário remover as regras de alerta criadas pela solução. Utilize o seguinte procedimento no portal Azure para remover estas regras.

1. Abra o menu **Monitor** e, em seguida, selecione **Alertas**.
2. Clique em **gerir as regras**de alerta .
3. Selecione as seguintes três regras de alerta e, em seguida, clique em **Eliminar**.

   - AutoStop_VM_Child
   - ScheduledStartStop_Parent
   - SequencedStartStop_Parent

    ![Excluir regras](media/move-workspace/delete-rules.png)

## <a name="unlink-automation-account"></a>Conta Desvinculação de Automação
Utilize o seguinte procedimento para desligar a conta Automation a partir do espaço de trabalho utilizando o portal Azure:

1. Abra o menu de **contas da Automação** e, em seguida, selecione a conta para remover.
2. Na secção **Recursos Relacionados** do menu, selecione **Espaço de trabalho Linked**. 
3. Clique em **Deslink espaço** de trabalho para desligar o espaço de trabalho da sua conta Automation.

    ![Unlink workspace (Desassociar a área de trabalho)](media/move-workspace/unlink-workspace.png)

## <a name="move-your-workspace"></a>Mova o seu espaço de trabalho

### <a name="azure-portal"></a>Portal do Azure
Utilize o seguinte procedimento para mover o seu espaço de trabalho utilizando o portal Azure:

1. Abra o menu de espaços de **trabalho Log Analytics** e, em seguida, selecione o seu espaço de trabalho.
2. Na página **'Visão Geral',** clique em **alterar** ao lado do **grupo de recursos** ou da **subscrição**.
3. Uma nova página abre com uma lista de recursos relacionados com o espaço de trabalho. Selecione os recursos para passar para o mesmo grupo de recursos e subscrição de destino que o espaço de trabalho. 
4. Selecione um grupo de **subscrição** e **recursos**de destino. Se estiver a mover o espaço de trabalho para outro grupo de recursos na mesma subscrição, não verá a opção **Subscrição.**
5. Clique em **OK** para mover o espaço de trabalho e os recursos selecionados.

    ![Portal](media/move-workspace/portal.png)

### <a name="powershell"></a>PowerShell
Para mover o seu espaço de trabalho utilizando o PowerShell, utilize o [Move-AzResource](/powershell/module/AzureRM.Resources/Move-AzureRmResource) como no seguinte exemplo:

``` PowerShell
Move-AzResource -ResourceId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyResourceGroup01/providers/Microsoft.OperationalInsights/workspaces/MyWorkspace" -DestinationSubscriptionId "00000000-0000-0000-0000-000000000000" -DestinationResourceGroupName "MyResourceGroup02"
```



> [!IMPORTANT]
> Após o funcionamento do movimento, as soluções removidas e a ligação da conta Automation devem ser reconfiguradas para trazer o espaço de trabalho de volta ao seu estado anterior.


## <a name="next-steps"></a>Passos seguintes
- Para obter uma lista dos recursos de apoio, consulte o apoio da [operação Move para recursos](../../azure-resource-manager/management/move-support-resources.md).
