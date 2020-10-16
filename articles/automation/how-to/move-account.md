---
title: Mude a sua conta Azure Automation para outra subscrição
description: Este artigo diz como mover a sua conta Automation para outra subscrição.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/11/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 562ea5e0e9e4851ed59bd3ef917be2f9c48cd2a7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86185556"
---
# <a name="move-your-azure-automation-account-to-another-subscription"></a>Mude a sua conta Azure Automation para outra subscrição

A Azure Automation permite-lhe mover alguns recursos para um novo grupo de recursos ou subscrição. Pode mover recursos através do portal Azure, PowerShell, Azure CLI ou da API REST. Para saber mais sobre o processo, consulte [mover recursos para um novo grupo de recursos ou subscrição.](../../azure-resource-manager/management/move-resource-group-and-subscription.md)

A conta Automation é um dos recursos que pode mover. Neste artigo, você aprenderá a mover as contas da Automação para outro recurso ou subscrição. Os passos de alto nível para mover a sua conta de Automação são:

1. Desative as suas características.
2. Desvincula o teu espaço de trabalho.
3. Mover a conta Automation.
4. Eliminar e recriar as contas Run As.
5. Re-capacitar as suas funcionalidades.

## <a name="remove-features"></a>Remover funcionalidades

Para desvincular o seu espaço de trabalho da sua conta Demômpa, tem de remover os recursos de funcionalidade no seu espaço de trabalho:

- Controlo de Alterações e Inventário
- Gestão de Atualizações
- Iniciar/Parar VMs durante horas de inatividade

1. No portal do Azure, localize o grupo de recursos.
2. Encontre cada recurso e selecione **Eliminar** na página **Eliminar Recursos.**

    ![Screenshot de apagar recursos de recurso do portal Azure](../media/move-account/delete-solutions.png)

Se preferir, pode eliminar os recursos utilizando o [cmdlet Remove-AzResource:](/powershell/module/Az.Resources/Remove-AzResource?view=azps-3.7.0)

```azurepowershell-interactive
$workspaceName = <myWorkspaceName>
$resourceGroupName = <myResourceGroup>
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "ChangeTracking($workspaceName)" -ResourceGroupName $resourceGroupName
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Updates($workspaceName)" -ResourceGroupName $resourceGroupName
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Start-Stop-VM($workspaceName)" -ResourceGroupName $resourceGroupName
```

### <a name="remove-alert-rules-for-startstop-vms-during-off-hours"></a>Remover as regras de alerta para VMs de início/paragem durante as horas de folga

Para os VMs de arranque/paragem durante o horário de folga, também é necessário remover as regras de alerta criadas pela funcionalidade.

1. No portal Azure, vá ao seu grupo de recursos e selecione Alertas **de Monitorização**Gerir as  >  **Alerts**  >  **regras de alerta**.

   ![Screenshot da página alertas, mostrando a seleção de regras de alerta de Gestão](../media/move-account/alert-rules.png)

2. Na página Do Regimento, deverá consultar uma lista dos alertas configurados nesse grupo de recursos. A funcionalidade cria estas regras:

    * AutoStop_VM_Child
    * ScheduledStartStop_Parent
    * SequencedStartStop_Parent

3. Selecione as regras uma de cada vez e **selecione Eliminar** para removê-las.

    ![Screenshot da página Do Regimento, solicitando confirmação da eliminação para regras selecionadas](../media/move-account/delete-rules.png)

    > [!NOTE]
    > Se não vir nenhuma regra de alerta na página Regras, altere o campo **'Estado** para **Deficiente'** para mostrar alertas desativado. 

4. Quando remover as regras de alerta, deve remover o grupo de ação criado para VMs iniciar/parar durante as notificações fora de horas. No portal Azure, **Monitor**selecione  >  **Monitor Alerts**  >  **Gerir grupos de ação**.

5. Selecione **StartStop_VM_Notification**. 

6. Na página do grupo de ação, selecione **Delete**.

    ![Screenshot da página do grupo Action](../media/move-account/delete-action-group.png)

Se preferir, pode eliminar o seu grupo de ação utilizando o cmdlet [Remove-AzActionGroup:](/powershell/module/az.monitor/remove-azactiongroup?view=azps-3.7.0)

```azurepowershell-interactive
Remove-AzActionGroup -ResourceGroupName <myResourceGroup> -Name StartStop_VM_Notification
```

## <a name="unlink-your-workspace"></a>Desvincula o seu espaço de trabalho

Agora pode desvincular o seu espaço de trabalho:

1. No portal Azure, **selecione Automatização**  >  **conta Relacionados Recursos**Linked espaço de  >  **trabalho**. 

2. Selecione **Desvincular o espaço de trabalho** para desvincular o espaço de trabalho da sua conta Automation.

    ![Screenshot de desvincular um espaço de trabalho de uma conta de Automação](../media/move-account/unlink-workspace.png)

## <a name="move-your-automation-account"></a>Mover a sua conta de Automação

Pode agora mover a sua conta Automation e os seus runbooks. 

1. No portal Azure, consulte o grupo de recursos da sua conta Automation. Selecione **Mover**  >  **Mover para outra subscrição**.

    ![Screenshot da página do grupo de recursos, mover-se para outra subscrição](../media/move-account/move-resources.png)

2. Selecione os recursos do seu grupo de recursos que pretende mover. Certifique-se de que inclui a sua conta de Automação, livros de execução e recursos do espaço de trabalho Log Analytics.

## <a name="re-create-run-as-accounts"></a>Recriar Run As accounts

[Executar Como as contas](../manage-runas-account.md) criam um principal serviço no Azure Ative Directory para autenticar com recursos Azure. Quando altera as subscrições, a conta Automation deixou de utilizar a conta Run As existente. Para recriar as contas Run As:

1. Aceda à sua conta de Automação na nova subscrição e selecione **Executar como contas** em **Definições de Conta**. Verá que as contas run as mostram como incompletas agora.

    ![Screenshot de Run As contas, mostrando incompleto](../media/move-account/run-as-accounts.png)

2. Elimine as contas Run As, uma de cada vez, selecionando **Eliminar** na página **Propriedades.** 

    > [!NOTE]
    > Se não tiver permissões para criar ou ver as contas Run As, vê a seguinte mensagem: `You do not have permissions to create an Azure Run As account (service principal) and grant the Contributor role to the service principal.` Para mais informações, consulte [permissões necessárias para configurar executar como contas](../manage-runas-account.md#permissions).

3. Depois de eliminar as contas Run As, selecione **Criar** na **conta Azure Run As**. 

4. Na página 'Add Azure Run As as', **selecione Criar** para criar a conta Como e o principal do serviço. 

5. Repita os passos acima com a conta Azure Classic Run As.

## <a name="enable-features"></a>Ativar funcionalidades

Depois de recriar as contas Run As, tem de voltar a ativar as funcionalidades que removeu antes da mudança:

1. Para ligar o 'Change Tracking and Inventory', selecione **Change Tracking and Inventory** na sua conta Demôm automação. Escolha o espaço de trabalho Log Analytics que mudou e selecione **Enable**.

2. Repita o passo 1 para a Gestão de Atualização.

    ![Screenshot de funcionalidades de re-habilitação na sua conta de Automação movida](../media/move-account/reenable-solutions.png)

3. As máquinas que estão ativadas com as suas funcionalidades são visíveis quando conecta o espaço de trabalho do Log Analytics existente. Para ligar os VMs iniciar/parar durante o período de folga, tem de o voltar a ativar. Nos **Recursos Relacionados**, selecione **VMs iniciar/parar**  >  **Saiba mais sobre e permita que a solução**  >  **Criar** para iniciar a implementação.

4. Na página Add Solution, escolha o seu espaço de trabalho Log Analytics e a conta Automation.

    ![Screenshot do menu Add Solution](../media/move-account/add-solution-vm.png)

5. Configure a função tal como descrito nos [VMs iniciar/parar durante a visão geral fora de horas](../automation-solution-vm-management.md).

## <a name="verify-the-move"></a>Verifique o movimento

Quando o movimento estiver completo, verifique se as capacidades listadas abaixo estão ativadas. 

|Funcionalidade|Testes|Resolução de problemas|
|---|---|---|
|Runbooks|Um runbook pode executar e conectar-se com sucesso aos recursos da Azure.|[Resolver problemas de runbooks](../troubleshoot/runbooks.md)
|Controlo de código fonte|Pode executar uma sincronização manual no seu repositório de controlo de origem.|[Integração do controlo de origem](../source-control-integration.md)|
|Controlo de alterações e inventário|Verifique se vê os dados atuais do inventário das suas máquinas.|[Rastreio de mudança de resolução de problemas](../troubleshoot/change-tracking.md)|
|Gestão de atualizações|Verifique se vê as suas máquinas e que estão saudáveis.</br>Executar uma implementação de atualização de software de teste.|[Gestão de atualização de resolução de problemas](../troubleshoot/update-management.md)|
|Recursos partilhados|Verifique se vê todos os seus recursos partilhados, tais como [credenciais](../shared-resources/credentials.md) e [variáveis.](../shared-resources/variables.md)|

## <a name="next-steps"></a>Passos seguintes

Para aprender sobre a movimentação de recursos em Azure, consulte [os recursos move em Azure.](../../azure-resource-manager/management/move-support-resources.md)
