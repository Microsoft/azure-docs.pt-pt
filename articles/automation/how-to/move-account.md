---
title: Mover a sua conta de automatização do Azure para outra subscrição
description: Este artigo descreve como mover a sua conta de automatização para outra subscrição
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/11/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 59d433bfb888eaa41cc8f66bdf3ad28c16efbe5c
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58225965"
---
# <a name="move-your-azure-automation-account-to-another-subscription"></a>Mover a sua conta de automatização do Azure para outra subscrição

Azure fornece-lhe a capacidade de mover alguns recursos para um novo grupo de recursos ou subscrição. Pode mover os recursos através do portal do Azure, PowerShell, a CLI do Azure ou a API REST. Para saber mais sobre o processo, veja [mover recursos para um novo grupo de recursos ou subscrição](../../azure-resource-manager/resource-group-move-resources.md). 

Contas de automatização do Azure são um dos recursos que podem ser movidos. Neste artigo, irá aprender os passos para mover as contas de automatização para outro recurso ou subscrição.

Os passos de alto nível para mover a sua conta de automatização são:

1. Remova as suas soluções.
2. Desassocie a área de trabalho.
3. Mova a conta de automatização.
4. Eliminar e voltar a criar as contas Run as.
5. Voltar a ativar suas soluções.

## <a name="remove-solutions"></a>Remover soluções

Para desassociar a área de trabalho da sua conta de automatização, essas soluções têm de ser removidas da sua área de trabalho:
- **Controlo de alterações e inventário**
- **Gestão de Atualizações** 
- **Iniciar/parar VMs durante as horas de inatividade** 

No seu grupo de recursos, encontrar cada solução e selecione **eliminar**. Sobre o **eliminar recursos** página, confirme os recursos a ser removido e selecione **eliminar**.

![Eliminar soluções a partir do portal do Azure](../media/move-account/delete-solutions.png)

Pode realizar a mesma tarefa com o [Remove-AzureRmResource](/powershell/module/azurerm.resources/remove-azurermresource) cmdlet, conforme mostrado no exemplo a seguir:

```azurepowershell-interactive
$workspaceName = <myWorkspaceName>
$resourceGroupName = <myResourceGroup>
Remove-AzureRmResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "ChangeTracking($workspaceName)" -ResourceGroupName $resourceGroupName
Remove-AzureRmResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Updates($workspaceName)" -ResourceGroupName $resourceGroupName
Remove-AzureRmResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Start-Stop-VM($workspaceName)" -ResourceGroupName $resourceGroupName
```

### <a name="additional-steps-for-startstop-vms"></a>Passos adicionais para iniciar/parar VMs

Para o **iniciar/parar VMs** solução, terá também de remover as regras de alerta criadas pela solução.

No portal do Azure, aceda ao seu grupo de recursos e selecione **monitorização** > **alertas** > **gerir regras de alerta**.

![Seleção de apresentação de página de regras de alerta gerir de alertas](../media/move-account/alert-rules.png)

Sobre o **regras** página, deverá ver uma lista dos alertas configurados nesse grupo de recursos. O **iniciar/parar VMs** solução cria três regras de alerta:

* AutoStop_VM_Child
* ScheduledStartStop_Parent
* SequencedStartStop_Parent

Selecione estas três regras de alerta e, em seguida, selecionam **eliminar**. Esta ação irá remover estas regras de alerta.

![Página de regras solicitando a confirmação da eliminação de regras selecionadas](../media/move-account/delete-rules.png)

> [!NOTE]
> Se não vir quaisquer regras de alerta no **regras** página, altere a **estado** para mostrar **desativada** alertas, porque poderá ter desativado-los.

Quando as regras de alerta são removidas, remover o grupo de ação que foi criado para o **iniciar/parar VMs** notificações de solução.

No portal do Azure, selecione **Monitor** > **alertas** > **gerir grupos de ação**.

Selecione **StartStop_VM_Notification** da lista. Na página de grupo de ação, selecione **eliminar**.

![Página do grupo de ação, selecione eliminar](../media/move-account/delete-action-group.png)

Da mesma forma, pode eliminar o grupo de ação com o PowerShell com o [Remove-AzureRmActionGroup](/powershell/module/azurerm.insights/remove-azurermactiongroup) cmdlet, conforme mostrado no exemplo seguinte:

```azurepowershell-interactive
Remove-AzureRmActionGroup -ResourceGroupName <myResourceGroup> -Name StartStop_VM_Notification
```

## <a name="unlink-your-workspace"></a>Desassociar a área de trabalho

No portal do Azure, selecione **conta de automatização** > **recursos relacionados** > **ligado área de trabalho**. Selecione **desassociar área de trabalho** ao desassociar a área de trabalho da sua conta de automatização.

![Desassociar área de trabalho de uma conta de automatização](../media/move-account/unlink-workspace.png)

## <a name="move-your-automation-account"></a>Mover a sua conta de automatização

Depois de remover os itens anteriores, pode continuar remover a sua conta de automatização e os seus runbooks. No portal do Azure, navegue para o grupo de recursos da sua conta de automatização. Selecione **mover** > **mover para outra subscrição**.

![Página de grupo de recursos, mover para outra subscrição](../media/move-account/move-resources.png)

Selecione os recursos no seu grupo de recursos que pretende mover. Certifique-se de que incluir sua **conta de automatização**, **Runbook**, e **área de trabalho do Log Analytics** recursos.

Após a migração estiver concluída, existem passos adicionais necessários para fazer tudo funcionar.

## <a name="re-create-run-as-accounts"></a>Voltar a criar contas Run as

[Contas Run as](../manage-runas-account.md) criar um principal de serviço no Azure Active Directory para autenticar com recursos do Azure. Quando altera as subscrições, a conta de automatização já não utiliza a conta Run As existente.

Aceda à sua conta de automatização na subscrição nova e selecione **contas Run as** sob **definições de conta**. Verá que as contas Run as mostram como incompletas agora.

![Contas Run as estão incompletas](../media/move-account/run-as-accounts.png)

Selecione cada conta Run As. Sobre o **propriedades** página, selecione **eliminar** para eliminar a conta Run As.

> [!NOTE]
> Se não tiver permissões para criar ou ver as contas Run as, verá a seguinte mensagem: `You do not have permissions to create an Azure Run As account (service principal) and grant the Contributor role to the service principal.` Para saber mais sobre as permissões necessárias para configurar uma conta Run As, consulte [as permissões necessárias para configurar contas Run as](../manage-runas-account.md#permissions).

Depois das contas Run as são eliminadas, selecione **Create** sob **conta Run As do Azure**. Sobre o **adicionar Azure conta Run As** página, selecione **criar** para criar a conta Run As e o serviço principal. Repita os passos anteriores com o **conta Run As clássica**.

## <a name="enable-solutions"></a>Ativar soluções

Depois de voltar a criar as contas Run as, terá de voltar a ativar as soluções que removeu antes da movimentação. Para ativar a **controlo de alterações e inventário** e **gestão de atualizações**, selecione a respetiva capacidade na sua conta de automatização. Escolha a área de trabalho do Log Analytics que movidos e selecione **ativar**.

![Volte a ativar soluções na sua conta de automatização movida](../media/move-account/reenable-solutions.png)

As máquinas que são carregadas com as suas soluções serão visíveis quando se ligar a área de trabalho do Log Analytics existente.

Para ativar a **iniciar/parar VMs** durante a solução de horário comercial, terá de voltar a implementar a solução. Sob **recursos relacionados**, selecione **iniciar/parar VMs** > **Saiba mais sobre e ative a solução** > **criar** para iniciar a implementação.

Sobre o **Adicionar solução** página, escolha a sua conta da área de trabalho do Log Analytics e a automação.  

![Adicionar o menu de solução](../media/move-account/add-solution-vm.png)

Para obter instruções detalhadas sobre como configurar a solução, consulte [iniciar/parar VMs durante a solução de horário comercial na automatização do Azure](../automation-solution-vm-management.md).

## <a name="post-move-verification"></a>Verificação de pós-migração

Quando a migração estiver concluída, verifique a lista seguinte de tarefas que devem ser verificados:

|Capacidade|Testes|Resolução de problemas de ligação|
|---|---|---|
|Runbooks|Um runbook pode executar e ligar aos recursos do Azure com êxito.|[Resolver problemas de runbooks](../troubleshoot/runbooks.md)
| Controlo de origem|Pode executar uma sincronização manual no seu repositório de controle de origem.|[Integração do controlo de origem](../source-control-integration.md)|
|Controlo de alterações e inventário|Certifique-se de que ver dados de inventário atual das suas máquinas.|[Resolver problemas de controlo de alterações](../troubleshoot/change-tracking.md)|
|Gestão de atualizações|Certifique-se de que ver as máquinas e estão em bom Estados.</br>Execute uma implementação de atualização de software de teste.|[Resolver problemas de gestão de atualizações](../troubleshoot/update-management.md)|

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre como mover os recursos no Azure, veja [mover os recursos no Azure](../../azure-resource-manager/move-support-resources.md).
