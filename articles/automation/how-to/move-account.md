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
ms.openlocfilehash: 01f3995a80375f2deada13c36c48600243a17623
ms.sourcegitcommit: 1902adaa68c660bdaac46878ce2dec5473d29275
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2019
ms.locfileid: "57733369"
---
# <a name="move-your-automation-account-to-another-subscription"></a>Mover a sua conta de automatização para outra subscrição

Azure fornece-lhe a capacidade de mover alguns recursos para um novo grupo de recursos ou subscrição com o mesmo inquilino nativamente através do portal do Azure, PowerShell, CLI do Azure ou REST API. Para saber mais sobre o processo, veja [mover recursos para um novo grupo de recursos ou subscrição](../../azure-resource-manager/resource-group-move-resources.md). Contas de automatização são um dos recursos que podem ser movidos, mas há etapas especiais necessárias quando mover a sua conta de automatização.

Os passos de alto nível para mover a sua conta de automatização para são:

* Remover as suas soluções
* Desassociar a área de trabalho
* Mover a conta de automatização
* Eliminar e recriar a execução como contas
* Volte a ativar a suas soluções

## <a name="remove-solutions"></a>Remover soluções

Para desassociar a área de trabalho da sua conta de automatização, alteração e inventário, gestão de atualizações e iniciar/parar VMs durante a desativar soluções de horas tem de ser removida da sua área de trabalho.

No seu grupo de recursos, selecione cada **solução** e clique em **eliminar**. Sobre o **eliminar recursos** página, confirme os recursos a ser removido e clique em **eliminar**.

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

Para o **iniciar/parar VM** solução, terá também de remover as regras de alerta criadas pela solução.

No portal do Azure, navegue para o grupo de recursos e selecione **alertas** sob **monitorização**. Sobre o **alertas** página, selecione **gerir regras de alerta**

![Página que mostra que clicar num alerta gerir regras de alertas](../media/move-account/alert-rules.png)

Sobre o **regras** página, deverá ver uma lista de todos os alertas configurados nesse grupo de recursos. O **iniciar/parar VMs** solução cria regras de alerta 3

* AutoStop_VM_Child
* ScheduledStartStop_Parent
* SequencedStartStop_Parent

Selecione estas 3 regras de alerta e clique em **eliminar**. Esta ação irá remover estas regras de alerta.

![As regras de página com as regras de selecionado e que está a ser eliminado](../media/move-account/delete-rules.png)

> [!NOTE]
> Se não vir quaisquer regras de alerta no **regras** página, altere a **estado** para mostrar **desativada** alertas como pode ter desativado-los.

Assim que as regras de alerta são removidas, terá de remover o grupo de ação que foi criado para as notificações para a solução do iniciar/parar VM.

No portal do Azure, aceda a **Monitor**, selecione **alertas**e clique em **gerir grupos de ação**.

Selecione o seu grupo de ação na lista, terá o nome **StartStop_VM_Notification**. Na página de grupos de ação, clique em **eliminar**

![Página do grupo de ação, clicando em delete](../media/move-account/delete-action-group.png)

Da mesma forma, pode eliminar o grupo de ação com o PowerShell. Esta ação é feita com o [Remove-AzureRmActionGroup](/powershell/module/azurerm.insights/remove-azurermactiongroup) cmdlet como visto no exemplo a seguir:

```azurepowershell-interactive
Remove-AzureRmActionGroup -ResourceGroupName <myResourceGroup> -Name StartStop_VM_Notification
```

## <a name="unlink-your-workspace"></a>Desassociar a área de trabalho

No portal do Azure, aceda ao seu **conta de automatização**. Sob **recursos relacionados**, clique em **ligado área de trabalho**. Clique em **desassociar área de trabalho** ao desassociar a área de trabalho da sua conta de automatização.

![A desassociar uma área de trabalho a partir de uma conta de automatização](../media/move-account/unlink-workspace.png)

## <a name="move-your-automation-account"></a>Mover a sua conta de automatização

Depois de todos os itens anteriores foram removidos, pode continuar remover a sua conta de automatização e os seus runbooks. No portal do Azure, navegue para o grupo de recursos da sua conta de automatização. Selecione **mover** e, em seguida **mover para outra subscrição**.

![Página de grupo de recursos selecionando mover para outra subscrição](../media/move-account/move-resources.png)

Selecione os recursos no seu grupo de recursos que pretende mover. Certifique-se de que incluir sua **conta de automatização**, **Runbook**, e **área de trabalho do Log Analytics** recursos.

Quando a migração estiver concluída, existem passos adicionais que é necessário para que tudo a funcionar.

## <a name="recreate-run-as-accounts"></a>Recriar a execução como contas

[Contas Run as](../manage-runas-account.md) criar um principal de serviço no Azure Active Directory para autenticar com recursos do Azure. Quando altera as subscrições, a conta Run As existente já não é utilizável pela conta de automatização.

Navegue até à sua conta de automatização na subscrição nova e selecione **contas Run as** sob **definições de conta**. Verá que as contas Run as mostram como incompletas agora.

![Contas Run as que mostra como incompleto](../media/move-account/run-as-accounts.png)

Clique em cada execução, como a conta e, no **propriedades** página, clique em **eliminar** para eliminar a conta Run As.

> ! [NOTA] Se não tem permissões para criar ou ver as contas Run as verá a seguinte mensagem `You do not have permissions to create an Azure Run As account (service principal) and grant the Contributor role to the service principal.`. Para saber mais sobre as permissões necessárias para configurar uma conta Run As, consulte [as permissões necessárias para configurar contas Run as](../manage-runas-account.md#permissions).

Assim que as contas Run as são eliminadas, clique em **criar** sobre o **conta Run as Azure**. Sobre o **adicionar Azure conta Run as** página, clique em **criar** para criar a conta Run As e principal de serviço. Repita os passos anteriores com o **do Azure conta Run as clássica**.

## <a name="enable-solutions"></a>Ativar soluções

Depois de ter sido recriadas as contas Run as, terá de voltar a ativar as soluções que remover antes da movimentação. Para habilitar **controlo de alterações e inventário** e **gestão de atualizações**, selecione a respetiva capacidade na sua conta de automatização. Escolha a área de trabalho do Log Analytics que movidos e clique em **ativar**.

![Volte a ativar soluções na sua conta de automatização movido](../media/move-account/reenable-solutions.png)

As máquinas que são carregadas com as suas soluções aparecerá novamente, uma vez que está se conectando a área de trabalho do Log Analytics existente.

Para reativar a iniciar/parar VMs durante a solução de horário comercial, terá de voltar a implementar a solução. Sob **recursos relacionados**, selecione **iniciar/parar VM**. Clique em **Saiba mais sobre e ative a solução** e clique em **criar** para iniciar a implementação.

Sobre o **Adicionar solução** página, escolha a sua área de trabalho do Log Analytics e a conta de automatização.  

![Contas Run as que mostra como incompleto](../media/move-account/add-solution-vm.png)

Para obter instruções detalhadas sobre como configurar a solução, consulte [iniciar/parar VMs durante a solução de horário comercial na automatização do Azure](../automation-solution-vm-management.md)

## <a name="post-move-verification"></a>Verificação de movimentação de postagem

Quando a migração estiver concluída, certifique-se verificar os diferentes cenários na sua conta de automatização para garantir que está tudo a funcionar conforme esperado. A tabela seguinte mostra uma lista de tarefas que devem ser verificados após a movimentação tiver sido concluída:

|Capacidade|Testes|Resolução de problemas de ligação|
|---|---|---|
|Runbooks|Um Runbook pode executar e ligar aos recursos do Azure com êxito.|[Resolver problemas de runbooks](../troubleshoot/runbooks.md)
| Controlo de Origem|Pode executar uma sincronização manual no seu repositório de controle de origem.|[Integração do controlo de origem](../source-control-integration.md)|
|Controlo de Alterações e Inventário|Certifique-se de que ver dados de inventário atual das suas máquinas.|[Resolver problemas de controlo de alterações](../troubleshoot/change-tracking.md)|
|Gestão de Atualizações|Verifique se pode ver as máquinas e estão em bom Estados</br>Execute uma implementação de atualização de software de teste.|[Resolver problemas de gestão de atualizações](../troubleshoot/update-management.md)|

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre como mover os recursos no Azure, veja [mover os recursos no Azure](../../azure-resource-manager/move-support-resources.md)
