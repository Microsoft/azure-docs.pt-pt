---
title: Mova a sua conta De Automação Azure para outra subscrição
description: Este artigo descreve como mover a sua conta de Automação para outra subscrição
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/11/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 1aa759a2984764169eb28935e095d0f7c0f90c08
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75969834"
---
# <a name="move-your-azure-automation-account-to-another-subscription"></a>Mova a sua conta De Automação Azure para outra subscrição

O Azure oferece-lhe a capacidade de transferir alguns recursos para um novo grupo de recursos ou subscrição. Pode movimentar recursos através do portal Azure, PowerShell, do Azure CLI ou da Rest API. Para saber mais sobre o processo, consulte [a Move recursos para um novo grupo de recursos ou subscrição.](../../azure-resource-manager/management/move-resource-group-and-subscription.md)

As contas da Azure Automation são um dos recursos que podem ser movidos. Neste artigo, você aprenderá os passos para mover as contas da Automação para outro recurso ou subscrição.

Os passos de alto nível para mover a sua conta de Automação são:

1. Retire as suas soluções.
2. Desliga o seu espaço de trabalho.
3. Mova a conta de Automação.
4. Eliminar e recriar o Run As contas.
5. Reative as suas soluções.

## <a name="remove-solutions"></a>Remover soluções

Para desligar o seu espaço de trabalho a partir da sua conta Automation, estas soluções devem ser removidas do seu espaço de trabalho:
- **Controlo de Alterações e Inventário**
- **Gestão de Atualizações**
- **VMs de arranque/paragem durante o horário de folga**

No seu grupo de recursos, encontre cada solução e selecione **Eliminar**. Na página **Eliminar Recursos,** confirme os recursos a remover e selecione **Eliminar**.

![Eliminar soluções do portal Azure](../media/move-account/delete-solutions.png)

Pode realizar a mesma tarefa com o cmdlet [Remove-AzureRmResource,](/powershell/module/azurerm.resources/remove-azurermresource) como mostra o seguinte exemplo:

```azurepowershell-interactive
$workspaceName = <myWorkspaceName>
$resourceGroupName = <myResourceGroup>
Remove-AzureRmResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "ChangeTracking($workspaceName)" -ResourceGroupName $resourceGroupName
Remove-AzureRmResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Updates($workspaceName)" -ResourceGroupName $resourceGroupName
Remove-AzureRmResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Start-Stop-VM($workspaceName)" -ResourceGroupName $resourceGroupName
```

### <a name="additional-steps-for-startstop-vms"></a>Passos adicionais para VMs de início/paragem

Para a solução **Start/Stop VMs,** também é necessário remover as regras de alerta criadas pela solução.

No portal Azure, dirija-se ao seu grupo de recursos e selecione**Alertas** >  **de Monitorização** > **Gerir regras**de alerta .

![Página de alertas mostrando seleção de regras de 'Gerir alerta'](../media/move-account/alert-rules.png)

Na página **Regras,** deve ver uma lista dos alertas configurados nesse grupo de recursos. A solução **Start/Stop VMs** cria três regras de alerta:

* AutoStop_VM_Child
* ScheduledStartStop_Parent
* SequencedStartStop_Parent

Selecione estas três regras de alerta e, em seguida, **selecione Eliminar**. Esta ação eliminará estas regras de alerta.

![Página de regras solicitando confirmação da eliminação de regras selecionadas](../media/move-account/delete-rules.png)

> [!NOTE]
> Se não vir nenhuma regra de alerta na página **Regras,** altere o **Estado** para mostrar alertas **de deficientes,** porque pode tê-los desativado.

Quando as regras de alerta forem removidas, remova o grupo de ação criado para as notificações de solução **Start/Stop VMs.**

No portal Azure, selecione **Monitor** > **Alerts** > **Gerir grupos de ação**.

Selecione **StartStop_VM_Notification** da lista. Na página do grupo de ação, selecione **Delete**.

![Página do grupo de ação, selecione eliminar](../media/move-account/delete-action-group.png)

Da mesma forma, pode eliminar o seu grupo de ação utilizando o PowerShell com o cmdlet [Remove-AzureRmActionGroup,](/powershell/module/azurerm.insights/remove-azurermactiongroup) como se pode ver no seguinte exemplo:

```azurepowershell-interactive
Remove-AzureRmActionGroup -ResourceGroupName <myResourceGroup> -Name StartStop_VM_Notification
```

## <a name="unlink-your-workspace"></a>Desvincular o seu espaço de trabalho

No portal Azure, selecione **conta** > de Automação Recursos**Relacionados** > **Recursos Linked espaço de trabalho.** Selecione espaço de **trabalho Unlink** para desligar o espaço de trabalho da sua conta Automation.

![Desvincular um espaço de trabalho a partir de uma conta de Automação](../media/move-account/unlink-workspace.png)

## <a name="move-your-automation-account"></a>Mova a sua conta de Automação

Depois de remover os itens anteriores, pode continuar a remover a sua conta Deautomação e os seus livros de execução. No portal Azure, navegue pelo grupo de recursos da sua conta Automation. Selecione > **Mover mover-se para outra subscrição**. **Move**

![Página de grupo de recursos, mude para outra subscrição](../media/move-account/move-resources.png)

Selecione os recursos do seu grupo de recursos que pretende mover. Certifique-se de que inclui a sua **conta de Automação,** **Livro de Execução**e Recursos **do espaço de trabalho Log Analytics.**

Após a mudança estar concluída, são necessários passos adicionais para que tudo funcione.

## <a name="re-create-run-as-accounts"></a>Recriar executar como contas

[Executar Como contas](../manage-runas-account.md) criam um diretor de serviço no Azure Ative Directory para autenticar com recursos Azure. Quando muda de subscrição, a conta Automation já não utiliza a conta Run As existente.

Vá à sua conta De automação na nova subscrição e selecione **Executar como contas** em **Definições de Conta**. Verá que as contas Run As mostram-se incompletas agora.

![Executar Como as contas estão incompletas](../media/move-account/run-as-accounts.png)

Selecione cada conta Executar Como conta. Na página **Propriedades,** selecione **Eliminar** para eliminar a conta 'Executar As'.

> [!NOTE]
> Se não tiver permissões para criar ou visualizar as contas 'Executar `You do not have permissions to create an Azure Run As account (service principal) and grant the Contributor role to the service principal.` As', verá a seguinte mensagem: Para conhecer as permissões necessárias para configurar uma conta 'Executar As', consulte [permissões necessárias para configurar executar como contas](../manage-runas-account.md#permissions).

Depois de eliminar as contas Como as contas são eliminadas, selecione **Criar** sob **o Azure Run Como conta**. Na página **Add Azure Run As conta,** selecione **Create** para criar o Run As conta e o diretor de serviço. Repita os passos anteriores com a **conta Azure Classic Run As**.

## <a name="enable-solutions"></a>Ativar soluções

Depois de recriar as contas Run As, irá reativar as soluções que removeu antes da mudança. Para ativar o **Change Tracking e O Inventário** e a Gestão de **Atualizações,** selecione a respetiva capacidade na sua conta Deautomação. Escolha o espaço de trabalho log Analytics que moveu e selecione **Enable**.

![Reativar soluções na sua conta De automação móvel](../media/move-account/reenable-solutions.png)

As máquinas que estão a bordo com as suas soluções serão visíveis quando tiver ligado o espaço de trabalho existente no Log Analytics.

Para ligar os **VMs iniciar/parar** durante a solução off-hours, terá de reimplantar a solução. Em **Recursos Relacionados,** selecione **Start/Stop VMs** > **Saiba mais sobre e ative a solução** > **Criar** para iniciar a implementação.

Na página **Add Solution,** escolha a sua conta log analytics workspace e automação.

![Adicionar menu de solução](../media/move-account/add-solution-vm.png)

Para obter instruções detalhadas sobre a configuração da solução, consulte [Os VMs start/stop durante a solução off-hours em Automação Azure](../automation-solution-vm-management.md).

## <a name="post-move-verification"></a>Verificação pós-movimento

Quando o movimento estiver concluído, verifique a seguinte lista de tarefas que devem ser verificadas:

|Capacidade|Testes|Link de resolução de problemas|
|---|---|---|
|Runbooks|Um livro de corridas pode funcionar com sucesso e ligar-se aos recursos do Azure.|[Livros de corridas de resolução de problemas](../troubleshoot/runbooks.md)
|Controlo de código fonte|Pode executar uma sincronização manual no seu repo de controlo de origem.|[Integração do controlo de origem](../source-control-integration.md)|
|Alterar o rastreio e o inventário|Verifique se vê os dados de inventário atuais das suas máquinas.|[Rastreio de mudança de resolução de problemas](../troubleshoot/change-tracking.md)|
|Gestão de atualizações|Verifique se vê as suas máquinas e que estão saudáveis.</br>Executar uma implementação de atualização de software de teste.|[Gestão de atualização de resolução de problemas](../troubleshoot/update-management.md)|
|Recursos partilhados|Verifique se vê todos os seus recursos partilhados, tais como [Credenciais,](../shared-resources/credentials.md) [Variáveis,](../shared-resources/variables.md)etc.|

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre a movimentação de recursos em Azure, consulte [recursos move em Azure.](../../azure-resource-manager/management/move-support-resources.md)
