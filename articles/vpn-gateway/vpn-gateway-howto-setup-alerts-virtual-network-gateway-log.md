---
title: 'Gateway de VPN do Azure: configurar alertas em eventos de log de diagnóstico'
description: Etapas para configurar alertas em eventos de log de diagnóstico do gateway de VPN
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: alzam
ms.openlocfilehash: eb8f0204771b204af740c4ddc8e359499520a012
ms.sourcegitcommit: 05cdbb71b621c4dcc2ae2d92ca8c20f216ec9bc4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/16/2020
ms.locfileid: "76045939"
---
# <a name="set-up-alerts-on-diagnostic-log-events-from-vpn-gateway"></a>Configurar alertas em eventos de log de diagnóstico do gateway de VPN

Este artigo ajuda você a configurar alertas com base em eventos de log de diagnóstico do gateway de VPN do Azure usando o Azure Log Analytics. 

Os seguintes logs estão disponíveis no Azure:

|***Nome*** | ***Descrição*** |
|---        | ---               |
|GatewayDiagnosticLog | Contém logs de diagnóstico para eventos de configuração do gateway, alterações principais e eventos de manutenção |
|TunnelDiagnosticLog | Contém eventos de alteração de estado de túnel. Eventos Connect/Disconnect de túnel têm um motivo resumido para a alteração de estado, se aplicável |
|RouteDiagnosticLog | Registra alterações em rotas estáticas e eventos BGP que ocorrem no gateway |
|IKEDiagnosticLog | Registra mensagens de controle de IKE e eventos no gateway |
|P2SDiagnosticLog | Registra mensagens de controle de ponto a site e eventos no gateway |

## <a name="setup"></a>Configurar alertas

As etapas de exemplo a seguir criarão um alerta para um evento de desconexão que envolve um túnel VPN site a site:


1. Na portal do Azure, procure **log Analytics** em **todos os serviços** e selecione **log Analytics espaços de trabalho**.

   ![Seleções para ir para Log Analytics espaços de trabalho](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert0.png "Create")

2. Selecione **criar** na página **log Analytics** .

   ![Página Log Analytics com botão criar](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert1.png  "Selecione")

3. Selecione **criar novo** e preencha os detalhes.

   ![Detalhes para a criação de um espaço de trabalho Log Analytics](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert2.png  "Selecione")

4. Localize o gateway de VPN no **Monitor** > folha **configurações de diagnóstico** .

   ![Seleções para localizar o gateway de VPN nas configurações de diagnóstico](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert3.png  "Selecione")

5. Para ativar o diagnóstico, clique duas vezes no gateway e, em seguida, selecione **Ativar diagnóstico**.

   ![Seleções para ativar o diagnóstico](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert4.png  "Selecione")

6. Preencha os detalhes e verifique se **Enviar para log Analytics** e **TunnelDiagnosticLog** estão selecionados. Escolha o espaço de trabalho Log Analytics que você criou na etapa 3.

   ![Caixas de seleção selecionadas](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert5.png  "Selecione")

   > [!NOTE]
   > Pode levar algumas horas para que os dados sejam exibidos inicialmente.

7. Vá para a visão geral do recurso de gateway de rede virtual e selecione **alertas** na guia **monitoramento** . Em seguida, crie uma nova regra de alerta ou edite uma regra de alerta existente.

   ![Seleções para criar uma nova regra de alerta](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert6.png  "Selecione")

   ![ponto a site](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert6.png  "Selecione")
8. Selecione o espaço de trabalho Log Analytics e o recurso.

   ![Seleções de espaço de trabalho e recurso](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert7.png  "Selecione")

9. Selecione **pesquisa de logs personalizada** como a lógica de sinal em **Adicionar condição**.

   ![Seleções para uma pesquisa de logs personalizada](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert8.png  "Selecione")

10. Introduza a seguinte consulta na caixa de texto **Consulta de pesquisa**. Substitua os valores em < > e TimeGenerated conforme apropriado.

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

    Defina o valor do limite como 0 e selecione **concluído**.

    ![Inserindo uma consulta e selecionando um limite](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert9.png  "Selecione")

11. Na página **criar regra** , selecione **criar novo** na seção **grupos de ação** . Preencha os detalhes e selecione **OK**.

    ![Detalhes de um novo grupo de ação](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert10.png  "Selecione")

12. Na página **criar regra** , preencha os detalhes de **Personalizar ações** e verifique se o nome correto aparece na seção **nome do grupo de ações** . Selecione **criar regra de alerta** para criar a regra.

    ![Seleções para criar uma regra](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert11.png  "Selecione")

## <a name="next-steps"></a>Passos seguintes

Para configurar alertas em métricas de túnel, consulte [configurar alertas em métricas de gateway de VPN](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md).
