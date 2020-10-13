---
title: 'Azure VPN Gateway: Alertas de configuração em eventos de registo de recursos de diagnóstico'
description: Saiba como configurar alertas com base em eventos de registo de recursos a partir do Azure VPN Gateway, utilizando o Azure Monitor Log Analytics.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/03/2020
ms.author: alzam
ms.openlocfilehash: e2451796cb99f57501ed74aba1dc7a3077b51881
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89441617"
---
# <a name="set-up-alerts-on-resource-log-events-from-vpn-gateway"></a>Configurar alertas em eventos de registo de recursos a partir do VPN Gateway

Este artigo ajuda-o a configurar alertas baseados em eventos de registo de recursos a partir do Azure VPN Gateway usando a Azure Monitor Log Analytics. 

Os seguintes registos de recursos estão disponíveis no Azure:

|***Nome*** | ***Descrição*** |
|---        | ---               |
|GatewayDiagnosticLog | Contém registos de recursos para eventos de configuração de gateways, alterações primárias e eventos de manutenção |
|TúnelDiagnosticLog | Contém eventos de mudança de estado de túnel. Os eventos de ligação/desconexão do túnel têm uma razão resumida para a mudança do estado, se aplicável |
|RouteDiagnosticLog | Registos alterações em rotas estáticas e eventos de BGP que ocorrem no gateway |
|IKEDiagnosticLog | Regista mensagens e eventos de controlo do IKE no gateway |
|P2SDiagnosticLog | Regista mensagens de controlo ponto-a-local e eventos no gateway. As informações sobre a origem da ligação são fornecidas apenas para ligações IKEv2 |

## <a name="set-up-alerts-in-the-azure-portal"></a><a name="setup"></a>Configurar alertas no portal Azure

Os seguintes passos de exemplo criam um alerta para um evento de desconexão que envolve um túnel VPN local-local:


1. No portal Azure, procure **o Log Analytics** em Todos os **serviços** e selecione **log analytics espaços de trabalho**.

   ![Seleções para ir aos espaços de trabalho do Log Analytics](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert0.png "Criar")

2. Selecione **Criar** na página **'Registar Analytics'.**

   ![Registar página de Analytics com botão Criar](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert1.png  "Selecione")

3. Selecione **Criar Novo** e preencha os detalhes.

   ![Detalhes para criar um espaço de trabalho Log Analytics](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert2.png  "Selecione")

4. Encontre o seu gateway **Monitor**VPN na lâmina de  >  **definição de diagnóstico do** monitor.

   ![Seleções para encontrar a porta de entrada VPN nas definições de Diagnóstico](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert3.png  "Selecione")

5. Para ligar os diagnósticos, clique duas vezes no gateway e, em seguida, selecione **Ligue os diagnósticos**.

   ![Seleções para ligar diagnósticos](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert4.png  "Selecione")

6. Preencha os detalhes e certifique-se de que o **Envio para Registar Análise** e **TúnelDiagnosticLog são selecionados.** Escolha o log analytics workspace que criou no passo 3.

   ![Caixas de verificação selecionadas](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert5.png  "Selecione")

   > [!NOTE]
   > Pode levar algumas horas para que os dados apareçam inicialmente.

7. Vá à visão geral do recurso de gateway de rede virtual e selecione **Alertas** a partir do **separador Monitor.** Em seguida, crie uma nova regra de alerta ou edite uma regra de alerta existente.

   ![Seleções para criar uma nova regra de alerta](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert6.png  "Selecione")

   ![ponto a local](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert6.png  "Selecione")
8. Selecione o espaço de trabalho Log Analytics e o recurso.

   ![Seleções para espaço de trabalho e recursos](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert7.png  "Selecione")

9. Selecione **a pesquisa de registo personalizado** como a lógica do sinal em **condições de adicionar**.

   ![Seleções para uma pesquisa personalizada de registos](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert8.png  "Selecione")

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

    Desacione o valor limiar para 0 e selecione **Feito**.

    ![Inserindo uma consulta e selecionando um limiar](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert9.png  "Selecione")

11. Na página **'Criar' regras,** selecione **Criar Novo** na secção **GRUPOS DE AÇÃO.** Preencha os detalhes e selecione **OK**.

    ![Detalhes para um novo grupo de ação](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert10.png  "Selecione")

12. Na página **'Criar regras',** preencha os detalhes para **Personalizar Ações** e certifique-se de que o nome correto aparece na secção **NOME DO GRUPO DE AÇÃO.** Selecione **Criar a regra de alerta** para criar a regra.

    ![Seleções para criar uma regra](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert11.png  "Selecione")

## <a name="set-up-alerts-by-using-powershell"></a><a name="setuppowershell"></a>Configurar alertas utilizando o PowerShell

Os passos de exemplo a seguir criam um alerta para um evento de desconexão que envolve um túnel VPN local-local.

1. Criar um espaço de trabalho Log Analytics:

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

4. Crie uma regra de alerta baseada numa pesquisa de registo personalizado:

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

Para configurar alertas sobre as métricas dos túneis, consulte [Configurar alertas nas métricas do Gateway VPN](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md).
