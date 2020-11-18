---
title: Mover um espaço de trabalho log Analytics no Azure Monitor Microsoft Docs
description: Saiba como mover o seu espaço de trabalho Log Analytics para outro grupo de subscrição ou recursos.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/12/2020
ms.openlocfilehash: 8d7fde6661a4a133f689016559f010767c662417
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94699751"
---
# <a name="move-a-log-analytics-workspace-to-different-subscription-or-resource-group"></a>Mover um espaço de trabalho Log Analytics para diferentes grupos de subscrição ou recursos

Neste artigo, você vai aprender os passos para mover o log analytics espaço de trabalho para outro grupo de recursos ou subscrição na mesma região. Você pode aprender mais sobre a movimentação de recursos Azure através do portal Azure, PowerShell, o Azure CLI, ou a API REST. na [Move recursos para um novo grupo de recursos ou subscrição](../../azure-resource-manager/management/move-resource-group-and-subscription.md). 

> [!IMPORTANT]
> Não se pode mover um espaço de trabalho para uma região diferente.

## <a name="verify-active-directory-tenant"></a>Verifique o inquilino do Diretório Ativo
A fonte de espaço de trabalho e as assinaturas de destino devem existir dentro do mesmo inquilino do Azure Ative Directory. Use a Azure PowerShell para verificar se ambas as subscrições têm o mesmo ID do inquilino.

``` PowerShell
(Get-AzSubscription -SubscriptionName <your-source-subscription>).TenantId
(Get-AzSubscription -SubscriptionName <your-destination-subscription>).TenantId
```

## <a name="workspace-move-considerations"></a>Considerações de movimento de espaço de trabalho
- As soluções geridas que são instaladas no espaço de trabalho serão movidas com a operação de movimento do espaço de trabalho Log Analytics. 
- As teclas do espaço de trabalho (primária e secundária) são regeradas com a operação de movimento do espaço de trabalho. Se mantiver uma cópia das chaves do seu espaço de trabalho no cofre da chave, atualize-as com as novas teclas geradas após o movimento do espaço de trabalho. 
- Os agentes conectados permanecerão ligados e manterão o envio de dados para o espaço de trabalho após a mudança. 
- Uma vez que a operação de movimento requer que não existam Serviços Ligados do espaço de trabalho, as soluções que dependem desse link devem ser removidas para permitir a movimentação do espaço de trabalho. Soluções que devem ser removidas antes de poder desvincular a sua conta de automação:
  - Gestão de Atualizações
  - Monitorização de Alterações
  - Iniciar/Parar VMs durante horas de inatividade
  - Centro de Segurança do Azure

>[!IMPORTANT]
> **Clientes Azure Sentinel**
> - Atualmente, depois de o Azure Sentinel ser implantado num espaço de trabalho, a deslocação do espaço de trabalho para outro grupo de recursos ou subscrição não é suportada. 
> - Se já moveu o espaço de trabalho, desative todas as regras ativas de acordo com **o Analytics** e reative-as após cinco minutos. Esta deve ser uma solução eficaz na maioria dos casos, no entanto, para reiterar, não é apoiada e levada a cabo por sua conta e risco.
> 
> **Recriar alertas**
> - Todos os alertas devem ser recriados após um movimento porque as permissões são baseadas no ID de Recursos Azure do espaço de trabalho, que muda durante um movimento de espaço de trabalho.
>
> **Atualizar caminhos de recursos**
> - Após uma mudança no espaço de trabalho, qualquer Azure ou recursos externos que apontem para o espaço de trabalho devem ser revistos e atualizados para apontar para o novo caminho-alvo de recursos.
> 
>   *Exemplos:*
>   - [Regras de alerta do Monitor Azure](alerts-resource-move.md)
>   - Aplicações de terceiros
>   - Scripting personalizado
>

### <a name="delete-solutions-in-azure-portal"></a>Eliminar soluções no portal Azure
Utilize o seguinte procedimento para remover as soluções utilizando o portal Azure:

1. Abra o menu para o grupo de recursos onde estão instaladas quaisquer soluções.
2. Selecione as soluções a remover.
3. Clique **em Eliminar Recursos** e, em seguida, confirme os recursos a remover clicando em **Eliminar**.

![Eliminar soluções](media/move-workspace/delete-solutions.png)

### <a name="delete-using-powershell"></a>Eliminar usando PowerShell

Para remover as soluções utilizando o PowerShell, utilize o cmdlet [Remove-AzResource,](/powershell/module/az.resources/remove-azresource?view=azps-2.8.0) como mostra o seguinte exemplo:

``` PowerShell
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "ChangeTracking(<workspace-name>)" -ResourceGroupName <resource-group-name>
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Updates(<workspace-name>)" -ResourceGroupName <resource-group-name>
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Start-Stop-VM(<workspace-name>)" -ResourceGroupName <resource-group-name>
```

### <a name="remove-alert-rules-for-startstop-vms-solution"></a>Remover regras de alerta para a solução de VMs iniciar/parar
Para remover a solução **Iniciar/Parar VMs,** também é necessário remover as regras de alerta criadas pela solução. Utilize o seguinte procedimento no portal Azure para remover estas regras.

1. Abra o menu **Monitor** e, em seguida, selecione **Alertas**.
2. Clique **em Gerir as regras de alerta**.
3. Selecione as três seguintes regras de alerta e, em seguida, clique em **Eliminar**.

   - AutoStop_VM_Child
   - ScheduledStartStop_Parent
   - SequencedStartStop_Parent

    ![Excluir regras](media/move-workspace/delete-rules.png)

## <a name="unlink-automation-account"></a>Desvincular a conta de Automação
Utilize o seguinte procedimento para desvincular a conta de Automação do espaço de trabalho utilizando o portal Azure:

1. Abra o menu **de contas Automation e,** em seguida, selecione a conta para remover.
2. Na secção **Recursos Relacionados** do menu, selecione Espaço de **trabalho Linked**. 
3. Clique **em Deslink Espaço de trabalho** para desvincular o espaço de trabalho da sua conta Automation.

    ![Unlink workspace (Desassociar a área de trabalho)](media/move-workspace/unlink-workspace.png)

## <a name="move-your-workspace"></a>Mova o seu espaço de trabalho

### <a name="azure-portal"></a>Portal do Azure
Utilize o seguinte procedimento para mover o seu espaço de trabalho utilizando o portal Azure:

1. Abra o menu **de espaços de trabalho Log Analytics** e, em seguida, selecione o seu espaço de trabalho.
2. Na página **'Vista Geral',** clique em **alterar** ao lado de um **grupo de recursos** ou **subscrição**.
3. Uma nova página abre com uma lista de recursos relacionados com o espaço de trabalho. Selecione os recursos para se mover para o mesmo grupo de subscrição de destino e recursos que o espaço de trabalho. 
4. Selecione um grupo **de subscrição** e **recursos de** destino. Se estiver a mover o espaço de trabalho para outro grupo de recursos na mesma subscrição, não verá a opção **Subscrição.**
5. Clique **em OK** para mover o espaço de trabalho e os recursos selecionados.

    ![O Screenshot mostra o painel de visão geral no espaço de trabalho do Log Analytics com opções para alterar o grupo de recursos e o nome da subscrição.](media/move-workspace/portal.png)

### <a name="powershell"></a>PowerShell
Para mover o seu espaço de trabalho utilizando o PowerShell, utilize o [Move-AzResource](/powershell/module/AzureRM.Resources/Move-AzureRmResource) como no exemplo seguinte:

``` PowerShell
Move-AzResource -ResourceId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyResourceGroup01/providers/Microsoft.OperationalInsights/workspaces/MyWorkspace" -DestinationSubscriptionId "00000000-0000-0000-0000-000000000000" -DestinationResourceGroupName "MyResourceGroup02"
```

> [!IMPORTANT]
> Após a operação de deslocação, as soluções removidas e a ligação de conta Automation devem ser reconfiguradas para trazer o espaço de trabalho de volta ao seu estado anterior.


## <a name="next-steps"></a>Passos seguintes
- Para obter uma lista de recursos que apoiam o movimento, consulte [o suporte de operação move para recursos](../../azure-resource-manager/management/move-support-resources.md).
