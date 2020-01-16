---
title: Mover sua conta de automação do Azure para outra assinatura
description: Este artigo descreve como mover sua conta de automação para outra assinatura
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/11/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 1aa759a2984764169eb28935e095d0f7c0f90c08
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75969834"
---
# <a name="move-your-azure-automation-account-to-another-subscription"></a>Mover sua conta de automação do Azure para outra assinatura

O Azure fornece a capacidade de mover alguns recursos para um novo grupo de recursos ou assinatura. Você pode mover recursos por meio do portal do Azure, do PowerShell, do CLI do Azure ou da API REST. Para saber mais sobre o processo, consulte [mover recursos para um novo grupo de recursos ou assinatura](../../azure-resource-manager/management/move-resource-group-and-subscription.md).

As contas de automação do Azure são um dos recursos que podem ser movidos. Neste artigo, você aprenderá as etapas para mover as contas de automação para outro recurso ou assinatura.

As etapas de alto nível para mover sua conta de automação são:

1. Remova suas soluções.
2. Desvincular seu espaço de trabalho.
3. Mova a conta de automação.
4. Exclua e recrie as contas Executar como.
5. Habilite novamente suas soluções.

## <a name="remove-solutions"></a>Remover soluções

Para desvincular seu espaço de trabalho da sua conta de automação, essas soluções devem ser removidas do seu espaço de trabalho:
- **Controle de Alterações e inventário**
- **Gestão de Atualizações**
- **Iniciar/parar VMs fora do horário de expediente**

Em seu grupo de recursos, localize cada solução e selecione **excluir**. Na página **excluir recursos** , confirme os recursos a serem removidos e selecione **excluir**.

![Excluir soluções do portal do Azure](../media/move-account/delete-solutions.png)

Você pode realizar a mesma tarefa com o cmdlet [Remove-AzureRmResource](/powershell/module/azurerm.resources/remove-azurermresource) , conforme mostrado no exemplo a seguir:

```azurepowershell-interactive
$workspaceName = <myWorkspaceName>
$resourceGroupName = <myResourceGroup>
Remove-AzureRmResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "ChangeTracking($workspaceName)" -ResourceGroupName $resourceGroupName
Remove-AzureRmResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Updates($workspaceName)" -ResourceGroupName $resourceGroupName
Remove-AzureRmResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Start-Stop-VM($workspaceName)" -ResourceGroupName $resourceGroupName
```

### <a name="additional-steps-for-startstop-vms"></a>Etapas adicionais para iniciar/parar VMs

Para a solução **iniciar/parar VMs** , você também precisa remover as regras de alerta criadas pela solução.

Na portal do Azure, vá para o grupo de recursos e selecione **monitoramento** > **alertas** > **gerenciar regras de alerta**.

![Página de alertas mostrando a seleção de gerenciar regras de alerta](../media/move-account/alert-rules.png)

Na página **regras** , você verá uma lista dos alertas configurados nesse grupo de recursos. A solução **iniciar/parar VMs** cria três regras de alerta:

* AutoStop_VM_Child
* ScheduledStartStop_Parent
* SequencedStartStop_Parent

Selecione essas três regras de alerta e, em seguida, selecione **excluir**. Esta ação removerá essas regras de alerta.

![Página de regras solicitando confirmação de exclusão para regras selecionadas](../media/move-account/delete-rules.png)

> [!NOTE]
> Se você não vir nenhuma regra de alerta na página **regras** , altere o **status** para mostrar alertas **desabilitados** , pois você pode tê-los desabilitados.

Quando as regras de alerta forem removidas, remova o grupo de ações que foi criado para as notificações de solução **iniciar/parar VMs** .

Na portal do Azure, selecione **monitorar** > **alertas** > **gerenciar grupos de ações**.

Selecione **StartStop_VM_Notification** na lista. Na página grupo de ações, selecione **excluir**.

![Grupo de ações página, selecione Excluir](../media/move-account/delete-action-group.png)

Da mesma forma, você pode excluir o grupo de ações usando o PowerShell com o cmdlet [Remove-AzureRmActionGroup](/powershell/module/azurerm.insights/remove-azurermactiongroup) , como mostrado no exemplo a seguir:

```azurepowershell-interactive
Remove-AzureRmActionGroup -ResourceGroupName <myResourceGroup> -Name StartStop_VM_Notification
```

## <a name="unlink-your-workspace"></a>Desvincular seu espaço de trabalho

Na portal do Azure, selecione **conta de automação** > **recursos relacionados** > **espaço de trabalho vinculado**. Selecione **desvincular espaço de trabalho** para desvincular o espaço de trabalho da sua conta de automação.

![Desvincular um espaço de trabalho de uma conta de automação](../media/move-account/unlink-workspace.png)

## <a name="move-your-automation-account"></a>Mover sua conta de automação

Depois de remover os itens anteriores, você pode continuar a remover sua conta de automação e seus runbooks. Na portal do Azure, navegue até o grupo de recursos da sua conta de automação. Selecione **mover** > **mover para outra assinatura**.

![Página grupo de recursos, mover para outra assinatura](../media/move-account/move-resources.png)

Selecione os recursos em seu grupo de recursos que você deseja mover. Certifique-se de incluir seus recursos de **conta de automação**, **runbook**e espaço de **trabalho do log Analytics** .

Após a conclusão da movimentação, há etapas adicionais necessárias para fazer tudo funcionar.

## <a name="re-create-run-as-accounts"></a>Recriar contas Executar como

[As contas Executar como](../manage-runas-account.md) criam uma entidade de serviço no Azure Active Directory para autenticar com os recursos do Azure. Quando você altera as assinaturas, a conta de automação não usa mais a conta Executar como existente.

Acesse sua conta de automação na nova assinatura e selecione **contas Executar como** em **configurações de conta**. Você verá que as contas Executar como são mostradas como incompletas agora.

![As contas Executar como estão incompletas](../media/move-account/run-as-accounts.png)

Selecione cada conta Executar como. Na página **Propriedades** , selecione **excluir** para excluir a conta Executar como.

> [!NOTE]
> Se você não tiver permissões para criar ou exibir as contas Executar como, verá a seguinte mensagem: `You do not have permissions to create an Azure Run As account (service principal) and grant the Contributor role to the service principal.` para saber mais sobre as permissões necessárias para configurar uma conta Executar como, consulte [permissões necessárias para configurar contas Executar como](../manage-runas-account.md#permissions).

Depois que as contas Executar como forem excluídas, selecione **criar** na **conta Executar como do Azure**. Na página **adicionar conta Executar como do Azure** , selecione **criar** para criar a conta Executar como e a entidade de serviço. Repita as etapas anteriores com a **conta Executar como clássica do Azure**.

## <a name="enable-solutions"></a>Ativar soluções

Depois de recriar as contas Executar como, você reabilitará as soluções que você removeu antes da movimentação. Para ativar **controle de alterações e inventário** e **Gerenciamento de atualizações**, selecione o respectivo recurso em sua conta de automação. Escolha o Log Analytics espaço de trabalho que você moveu e selecione **habilitar**.

![Reabilitar soluções em sua conta de automação movida](../media/move-account/reenable-solutions.png)

Os computadores que estiverem integrados com suas soluções ficarão visíveis quando você conectar o espaço de trabalho do Log Analytics existente.

Para ativar a solução **iniciar/parar VMs** fora do horário comercial, você precisará reimplantar a solução. Em **recursos relacionados**, selecione **iniciar/parar VMs** > **saiba mais sobre e habilite a solução** > **criar** para iniciar a implantação.

Na página **Adicionar solução** , escolha o espaço de trabalho log Analytics e a conta de automação.

![Adicionar menu de solução](../media/move-account/add-solution-vm.png)

Para obter instruções detalhadas sobre como configurar a solução, consulte [iniciar/parar VMs fora do horário comercial solução na automação do Azure](../automation-solution-vm-management.md).

## <a name="post-move-verification"></a>Verificação de pós-movimentação

Quando a movimentação for concluída, verifique a seguinte lista de tarefas que devem ser verificadas:

|Capacidade|Testes|Link de solução de problemas|
|---|---|---|
|Runbooks|Um runbook pode ser executado com êxito e se conectar aos recursos do Azure.|[Resolver problemas de runbooks](../troubleshoot/runbooks.md)
|Controlo de código fonte|Você pode executar uma sincronização manual em seu repositório de controle do código-fonte.|[Integração do controlo de origem](../source-control-integration.md)|
|Controle de alterações e inventário|Verifique se você vê os dados de inventário atuais de seus computadores.|[Solucionar problemas de controle de alterações](../troubleshoot/change-tracking.md)|
|Gestão de atualizações|Verifique se você vê seus computadores e se eles estão íntegros.</br>Executar uma implantação de atualização de software de teste.|[Solucionar problemas de gerenciamento de atualizações](../troubleshoot/update-management.md)|
|Recursos partilhados|Verifique se você vê todos os seus recursos compartilhados, como [credenciais](../shared-resources/credentials.md), [variáveis](../shared-resources/variables.md), etc.|

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre como mover recursos no Azure, consulte [mover recursos no Azure](../../azure-resource-manager/management/move-support-resources.md).
