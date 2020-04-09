---
title: 'Azure VPN Gateway: Configure alertas sobre eventos de registo de diagnóstico'
description: Passos para configurar alertas em eventos de registo de diagnóstico VPN Gateway
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: alzam
ms.openlocfilehash: 49510b26e0b2a9c69dd65faf0f343e86d1a068db
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878906"
---
# <a name="set-up-alerts-on-diagnostic-log-events-from-vpn-gateway"></a>Configurar alertas sobre eventos de registo de diagnóstico a partir de VPN Gateway

Este artigo ajuda-o a configurar alertas com base em eventos de registo de diagnóstico do Azure VPN Gateway utilizando o Azure Log Analytics. 

Os seguintes registos estão disponíveis no Azure:

|***Nome*** | ***Descrição*** |
|---        | ---               |
|GatewayDiagnosticLog | Contém registos de diagnóstico para eventos de configuração de gateway, alterações primárias e eventos de manutenção |
|TúnelDiagnosticLog | Contém eventos de mudança de estado de túnel. Os eventos de ligação/desconexão do túnel têm uma razão resumida para a mudança de estado, se aplicável |
|RouteDiagnosticLog | Regista alterações nas rotas estáticas e eventos de BGP que ocorrem na porta de entrada |
|IKEDiagnosticLog | Regista mensagens de controlo IKE e eventos no portal |
|P2SDiagnosticlog | Regista mensagens e eventos de controlo ponto-a-local no portal. A informação de fonte de ligação é fornecida apenas para ligações IKEv2 |

## <a name="set-up-alerts-in-the-azure-portal"></a><a name="setup"></a>Criar alertas no portal Azure

Os seguintes passos de exemplo criarão um alerta para um evento de desconexão que envolve um túnel VPN local-a-local:


1. No portal Azure, procure **log Analytics** em todos **os serviços** e selecione espaços de **trabalho Log Analytics**.

   ![Seleções para ir a log analytics espaços de trabalho](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert0.png "Criar")

2. Selecione **Criar** na página **Log Analytics.**

   ![Log Analytics página com botão Criar](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert1.png  "Selecione")

3. Selecione **Criar Novo** e preencha os detalhes.

   ![Detalhes para criar um espaço de trabalho Log Analytics](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert2.png  "Selecione")

4. Encontre o gateway VPN na lâmina de**definições** de Diagnóstico do **Monitor.** > 

   ![Seleções para encontrar o gateway VPN em definições de diagnóstico](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert3.png  "Selecione")

5. Para ligar os diagnósticos, clique duas vezes no gateway e, em seguida, selecione **Ligar os diagnósticos**.

   ![Seleções para ligar diagnósticos](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert4.png  "Selecione")

6. Preencha os detalhes e certifique-se de que **enviar para Log Analytics** e **TunnelDiagnosticLog** são selecionados. Escolha o espaço de trabalho de Log Analytics que criou no passo 3.

   ![Caixas de verificação selecionadas](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert5.png  "Selecione")

   > [!NOTE]
   > Pode levar algumas horas para os dados aparecerem inicialmente.

7. Vá à visão geral do recurso de gateway da rede virtual e selecione **Alertas** do separador **Monitor.** Em seguida, crie uma nova regra de alerta ou edite uma regra de alerta existente.

   ![Seleções para criar uma nova regra de alerta](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert6.png  "Selecione")

   ![ponto-a-local](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert6.png  "Selecione")
8. Selecione o espaço de trabalho log Analytics e o recurso.

   ![Seleções para espaço de trabalho e recursos](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert7.png  "Selecione")

9. Selecione **a pesquisa** de registo personalizada como a lógica de sinal sob a condição de **adicionar**.

   ![Seleções para uma pesquisa de log personalizada](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert8.png  "Selecione")

10. Introduza a seguinte consulta na caixa de texto **Consulta de pesquisa**. Substitua os valores em <> e TimeGenerated conforme apropriado.

    ```
    AzureDiagnostics
    | where Category == "TunnelDiagnosticLog"
    | where _ResourceId == tolower("<RESOURCEID OF GATEWAY>")
    | where TimeGenerated > ago(5m) 
    | where remoteIP_s == "<REMOTE IP OF TUNNEL>"
    | where status_s == "Disconnected"
    | project TimeGenerated, OperationName, instance_s, Resource, ResourceGroup, _ResourceId 
    | sort by TimeGenerated asc
    ```

    Desloque o valor limiar para 0 e selecione **Done**.

    ![Entrar numa consulta e selecionar um limiar](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert9.png  "Selecione")

11. Na página de **regra Criar,** selecione **Criar Novo** sob a secção **ACTION GROUPS.** Preencha os detalhes e selecione **OK**.

    ![Detalhes para um novo grupo de ação](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert10.png  "Selecione")

12. Na página de **regras Criar,** preencha os detalhes para **Personalizar ações** e certifique-se de que o nome correto aparece na secção ACTION **GROUP NAME.** Selecione Criar a regra de **alerta** para criar a regra.

    ![Seleções para criar uma regra](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert11.png  "Selecione")

## <a name="set-up-alerts-by-using-powershell"></a><a name="setuppowershell"></a>Configurar alertas utilizando o PowerShell

Os seguintes passos de exemplo criam um alerta para um evento de desconexão que envolve um túnel VPN local-a-local.

1. Criar um espaço de trabalho de Log Analytics:

   ```powershell
   $Location           = 'westus2'
   $ResourceGroupName  = 'TestRG1'
   $Sku                = 'pergb2018'
   $WorkspaceName      = 'LogAnalyticsWS123'

   New-AzOperationalInsightsWorkspace -Location $Location -Name $WorkspaceName -Sku $Sku -ResourceGroupName $ResourceGroupName
   ```

2. Ligue os diagnósticos para o gateway VPN:

   ```powershell
   $ResourceGroupName  = 'TestRG1'
   $VpnGatewayName     = 'VNet1GW'
   $WorkspaceName      = 'LogAnalyticsWS123'

   $VpnGateway         = Get-AzVirtualNetworkGateway -Name $VpnGatewayName -ResourceGroupName $ResourceGroupName
   $Workspace          = Get-AzOperationalInsightsWorkspace -Name $WorkspaceName -ResourceGroupName $ResourceGroupName

   Set-AzDiagnosticSetting `
       -Name 'VPN tunnel' `
       -ResourceId $VpnGateway.Id `
       -WorkspaceId $Workspace.ResourceId `
       -Enabled $true `
       -Category 'TunnelDiagnosticLog'
   ```

3. Criar um grupo de ação.

   Este código cria um grupo de ação que envia uma notificação por e-mail quando um alerta é desencadeado:

   ```powershell
   $ActionGroupName            = 'EmailAdmins'   # Max. 60 characters long
   $ActionGroupShortName       = 'EmailAdmins'   # Max. 12 characters long
   $ActionGroupReceiverName    = 'My receiver Name'
   $EmailAddress               = 'xyz@contoso.com'
   $ResourceGroupName          = 'TestRG1'

   $ActionGroupReceiver = New-AzActionGroupReceiver -Name $ActionGroupReceiverName -UseCommonAlertSchema -EmailReceiver -EmailAddress $EmailAddress

   Set-AzActionGroup `
      -ResourceGroupName $ResourceGroupName `
      -Name $ActionGroupName `
      -ShortName $ActionGroupShortName `
      -Receiver @($ActionGroupReceiver)
   ```

4. Criar uma regra de alerta com base numa pesquisa de registo personalizada:

   ```powershell
   $ActionGroupName    = 'EmailAdmins'
   $EmailSubject       = 'Redmond VPN tunnel is disconnected'
   $Location           = 'westus2'
   $RemoteIp           = '104.42.209.46'
   $ResourceGroupName  = 'TestRG1'
   $VpnGatewayName     = 'VNet1GW'
   $WorkspaceName      = 'LogAnalyticsWS123'

   $VpnGateway         = Get-AzVirtualNetworkGateway -Name $VpnGatewayName -ResourceGroupName $ResourceGroupName
   $Workspace          = Get-AzOperationalInsightsWorkspace -Name $WorkspaceName -ResourceGroupName $ResourceGroupName

   $Query = @"
   AzureDiagnostics |
   where Category == "TunnelDiagnosticLog" |
   where TimeGenerated > ago(5m) |
   where _ResourceId == tolower("$($VpnGateway.id)") |
   where remoteIP_s == "$($RemoteIp)" |
   where status_s == "Disconnected" |
   project TimeGenerated, OperationName, instance_s, Resource, ResourceGroup, _ResourceId |
   sort by TimeGenerated asc
   "@

   $Source             = New-AzScheduledQueryRuleSource -Query $Query -DataSourceId $Workspace.ResourceId
   $Schedule           = New-AzScheduledQueryRuleSchedule -FrequencyInMinutes 5 -TimeWindowInMinutes 5
   $TriggerCondition   = New-AzScheduledQueryRuleTriggerCondition -ThresholdOperator 'GreaterThan' -Threshold 0

   $ActionGroup        = Get-AzActionGroup -ResourceGroupName $ResourceGroupName -Name $ActionGroupName
   $AznsActionGroup    = New-AzScheduledQueryRuleAznsActionGroup -ActionGroup $ActionGroup.Id -EmailSubject $EmailSubject
   $AlertingAction     = New-AzScheduledQueryRuleAlertingAction -AznsAction $AznsActionGroup -Severity '1' -Trigger $TriggerCondition

   New-AzScheduledQueryRule `
       -ResourceGroupName $ResourceGroupName `
       -Location $Location `
       -Action $AlertingAction `
       -Enabled $true `
       -Description 'The tunnel between Azure and Redmond with IP address 104.42.209.46 is disconnected' `
       -Schedule $Schedule `
       -Source $Source `
       -Name 'The Azure to Redmond tunnel is disconnected'
   ```

## <a name="next-steps"></a>Passos seguintes

Para configurar alertas sobre as métricas do túnel, consulte [A configuração de alertas nas métricas vpn Gateway](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md).
