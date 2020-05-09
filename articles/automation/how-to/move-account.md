---
title: Mova a sua conta De Automação Azure para outra subscrição
description: Este artigo descreve como mover a sua conta De automação para outra subscrição.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/11/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: bfb2f2d1d0f6a0d11784847344cd3dbcafdb0959
ms.sourcegitcommit: 0fda81f271f1a668ed28c55dcc2d0ba2bb417edd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/07/2020
ms.locfileid: "82900987"
---
# <a name="move-your-azure-automation-account-to-another-subscription"></a>Mova a sua conta De Automação Azure para outra subscrição

A Azure Automation permite-lhe mover alguns recursos para um novo grupo de recursos ou subscrição. Pode movimentar recursos através do portal Azure, PowerShell, do Azure CLI ou da Rest API. Para saber mais sobre o processo, consulte [a Move recursos para um novo grupo de recursos ou subscrição.](../../azure-resource-manager/management/move-resource-group-and-subscription.md)

A conta Automation é um dos recursos que pode mover. Neste artigo, você vai aprender a mover as contas da Automação para outro recurso ou subscrição. Os passos de alto nível para mover a sua conta de Automação são:

1. Retire as suas soluções.
2. Desliga o seu espaço de trabalho.
3. Mova a conta de Automação.
4. Eliminar e recriar o Run As contas.
5. Reative as suas soluções.

>[!NOTE]
>Neste artigo, trabalha com o módulo Azure PowerShell Az. Ainda pode utilizar o módulo AzureRM. Para saber mais sobre o módulo Az e compatibilidade AzureRM, consulte [A introdução do novo módulo Azure PowerShell Az](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Para instruções de instalação do módulo Az no seu Executor Híbrido, consulte [Instalar o módulo PowerShell Azure](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Para a sua conta Automation, pode atualizar os seus módulos para a versão mais recente, utilizando [como atualizar os módulos Azure PowerShell em Automação Azure](../automation-update-azure-modules.md).

## <a name="remove-solutions"></a>Remover soluções

Para desligar o seu espaço de trabalho da sua conta Automation, tem de remover estas soluções do seu espaço de trabalho:

- Controlo de Alterações e Inventário
- Gestão de Atualizações
- Iniciar/Parar VMs durante horas de inatividade

1. No portal do Azure, localize o grupo de recursos.
2. Encontre cada solução e selecione **Eliminar** na página **Eliminar Recursos.**

    ![Screenshot de soluções de apagar do portal Azure](../media/move-account/delete-solutions.png)

Se preferir, pode eliminar as soluções utilizando o cmdlet [Remove-AzResource:](https://docs.microsoft.com/powershell/module/Az.Resources/Remove-AzResource?view=azps-3.7.0)

```azurepowershell-interactive
$workspaceName = <myWorkspaceName>
$resourceGroupName = <myResourceGroup>
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "ChangeTracking($workspaceName)" -ResourceGroupName $resourceGroupName
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Updates($workspaceName)" -ResourceGroupName $resourceGroupName
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Start-Stop-VM($workspaceName)" -ResourceGroupName $resourceGroupName
```

### <a name="remove-alert-rules-for-the-startstop-vms-during-off-hours-solution"></a>Remova as regras de alerta para a solução "Start/Stop VMs durante o horário de folga"

Para esta solução, é também necessário remover as regras de alerta criadas pela solução.

1. No portal Azure, dirija-se ao seu grupo de recursos e selecione**Alertas** >  **de Monitorização** > **Gerir regras**de alerta .

   ![Screenshot da página alertas, mostrando a seleção de Regras de Alerta de Gerir](../media/move-account/alert-rules.png)

2. Na página **Regras,** deve ver uma lista dos alertas configurados nesse grupo de recursos. A solução cria estas regras:

    * AutoStop_VM_Child
    * ScheduledStartStop_Parent
    * SequencedStartStop_Parent

3. Selecione as regras uma de cada vez e selecione **Eliminar** para removê-las.

    ![Screenshot da página Regras, solicitando confirmação da eliminação de regras selecionadas](../media/move-account/delete-rules.png)

    > [!NOTE]
    > Se não vir quaisquer regras de alerta na página **Regras,** altere o campo **de Estado** para **Deficientes** para mostrar alertas desativados. Pode tê-los desativado.

4. Ao remover as regras de alerta, deve remover o grupo de ação criado para as notificações de solução "Iniciar/Parar durante horas de folga". No portal Azure, selecione **Monitor** > **Alerts** > **Gerir grupos de ação**.

5. Selecione **StartStop_VM_Notification**. 

6. Na página do grupo de ação, selecione **Delete**.

    ![Screenshot da página do grupo Action](../media/move-account/delete-action-group.png)

Se preferir, pode eliminar o seu grupo de ação utilizando o cmdlet [Remove-AzActionGroup:](https://docs.microsoft.com/powershell/module/az.monitor/remove-azactiongroup?view=azps-3.7.0)

```azurepowershell-interactive
Remove-AzActionGroup -ResourceGroupName <myResourceGroup> -Name StartStop_VM_Notification
```

## <a name="unlink-your-workspace"></a>Desvincular o seu espaço de trabalho

Agora pode desligar o seu espaço de trabalho:

1. No portal Azure, selecione **conta** > de Automação Recursos**Relacionados** > **Recursos Linked espaço de trabalho.** 

2. Selecione espaço de **trabalho Unlink** para desligar o espaço de trabalho da sua conta Automation.

    ![Screenshot de desvincular um espaço de trabalho de uma conta de Automação](../media/move-account/unlink-workspace.png)

## <a name="move-your-automation-account"></a>Mova a sua conta de Automação

Agora pode mover a sua conta de Automação e os seus livros de execução. 

1. No portal Azure, navegue pelo grupo de recursos da sua conta Automation. Selecione > **Mover mover-se para outra subscrição**. **Move**

    ![Screenshot da página do grupo Resource, mude para outra subscrição](../media/move-account/move-resources.png)

2. Selecione os recursos do seu grupo de recursos que pretende mover. Certifique-se de que inclui a sua conta de Automação, livros de execução e recursos do espaço de trabalho Log Analytics.

## <a name="re-create-run-as-accounts"></a>Recriar executar como contas

[Executar Como contas](../manage-runas-account.md) criam um diretor de serviço no Azure Ative Directory para autenticar com recursos Azure. Quando muda de subscrição, a conta Automation já não utiliza a conta Run As existente. Para recriar a Corrida Como contas:

1. Vá à sua conta de Automação na nova subscrição e selecione **Executar como contas** em **Definições de Conta**. Verá que as contas Run As mostram-se incompletas agora.

    ![Screenshot de Run As contas, mostrando incompleto](../media/move-account/run-as-accounts.png)

2. Elimine as contas 'Executar As', uma de cada vez, selecionando **apagar** na página **Propriedades.** 

    > [!NOTE]
    > Se não tiver permissões para criar ou visualizar as contas Run `You do not have permissions to create an Azure Run As account (service principal) and grant the Contributor role to the service principal.` As, consulte a seguinte mensagem: Para mais informações, consulte [permissões necessárias para configurar Executar Como contas](../manage-runas-account.md#permissions).

3. Depois de ter eliminado as contas Run As, selecione **Create** under **Azure Run As account**. 

4. Na página **Add Azure Run As conta,** selecione **Create** para criar o Run As conta e o diretor de serviço. 

5. Repita os passos acima com a conta Azure Classic Run As.

## <a name="enable-solutions"></a>Ativar soluções

Depois de recriar as contas Run As, deve reativar as soluções que removeu antes da mudança: 

1. Para ativar a solução "Change Tracking and Inventory", selecione **Change Tracking and Inventory** na sua conta Automation. Escolha o espaço de trabalho log Analytics que moveu e selecione **Enable**.

2. Repita o passo 1 para a solução "Atualização gestão".

    ![Screenshot de soluções de re-habilitação na sua conta Automation móvel](../media/move-account/reenable-solutions.png)

3. As máquinas que estão a bordo com as suas soluções são visíveis quando tiver ligado o espaço de trabalho existente no Log Analytics. Para ligar a solução "Start/Stop VMs durante as horas de folga", tem de recolocar a solução. Em **Recursos Relacionados,** selecione **Start/Stop VMs** > **Saiba mais sobre e ative a solução** > **Criar** para iniciar a implementação.

4. Na página **Add Solution,** escolha o seu espaço de trabalho log Analytics e a sua conta de Automação.

    ![Screenshot do menu Add Solution](../media/move-account/add-solution-vm.png)

5. Configure a solução descrita nos [VMs start/stop durante a solução de horas de folga na Automação Azure](../automation-solution-vm-management.md).

## <a name="verify-the-move"></a>Verifique o movimento

Quando o movimento estiver concluído, verifique se as capacidades listadas abaixo estão ativadas. 

|Capacidade|Testes|Resolução de problemas|
|---|---|---|
|Runbooks|Um livro de corridas pode funcionar com sucesso e ligar-se aos recursos do Azure.|[Resolver problemas de runbooks](../troubleshoot/runbooks.md)
|Controlo de código fonte|Pode executar uma sincronização manual no seu repositório de controlo de origem.|[Integração do controlo de origem](../source-control-integration.md)|
|Controlo de alterações e inventário|Verifique se vê os dados de inventário atuais das suas máquinas.|[Rastreio de mudança de resolução de problemas](../troubleshoot/change-tracking.md)|
|Gestão de atualizações|Verifique se vê as suas máquinas e que estão saudáveis.</br>Executar uma implementação de atualização de software de teste.|[Gestão de atualização de resolução de problemas](../troubleshoot/update-management.md)|
|Recursos partilhados|Verifique se vê todos os seus recursos partilhados, tais como [credenciais](../shared-resources/credentials.md) e [variáveis.](../shared-resources/variables.md)|

## <a name="next-steps"></a>Passos seguintes

Para aprender sobre a movimentação de recursos em Azure em geral, consulte [os recursos move em Azure](../../azure-resource-manager/management/move-support-resources.md).
