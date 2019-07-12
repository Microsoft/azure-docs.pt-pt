---
title: Configure alertas nos eventos de registo de diagnóstico do Gateway de VPN do Azure
description: Passos para configurar alertas de eventos de registo de diagnóstico do Gateway de VPN
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: alzam
ms.openlocfilehash: 4f18581b9ca5770b89be8ca37529c09d635dfb25
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67607120"
---
# <a name="set-up-alerts-on-diagnostic-log-events-from-vpn-gateway"></a>Configure alertas nos eventos de registo de diagnóstico do Gateway de VPN

Este artigo ajuda-o a configurar alertas com base em eventos de registo de diagnóstico do Gateway de VPN do Azure com o Azure Log Analytics. 

Os seguintes registos estão disponíveis no Azure:

|***Name*** | ***Descrição*** |
|---        | ---               |
|GatewayDiagnosticLog | Contém os registos de diagnóstico para eventos de configuração do gateway, principais alterações e eventos de manutenção |
|TunnelDiagnosticLog | Contém eventos de alteração de estado de túnel. Eventos de ligar/desligar Tunnel tenham um motivo resumido para que a alteração de estado, se aplicável |
|RouteDiagnosticLog | Registos de alterações para rotas estáticas e eventos BGP que ocorrem no gateway |
|IKEDiagnosticLog | Regista mensagens de controlo de IKE e eventos no gateway |
|P2SDiagnosticLog | Regista mensagens de controlo de ponto a site e eventos no gateway |

## <a name="setup"></a>Configurar alertas

Os seguintes passos de exemplo irão criar um alerta para um evento de Desativação que envolve um túnel VPN de site a site:


1. No portal do Azure, procure **do Log Analytics** sob **todos os serviços** e selecione **áreas de trabalho do Log Analytics**.

   ![Seleções para aceder a áreas de trabalho do Log Analytics](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert0.png "Create")

2. Selecione **Create** sobre o **do Log Analytics** página.

   ![Página de análise de registo com o botão Criar](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert1.png  "selecione")

3. Selecione **criar novo** e preencha os detalhes.

   ![Detalhes para a criação de uma área de trabalho do Log Analytics](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert2.png  "selecione")

4. Encontrar o gateway de VPN sobre o **Monitor** > **as definições de diagnóstico** painel.

   ![Seleções para encontrar o gateway de VPN em definições de diagnóstico](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert3.png  "selecione")

5. Para ativar diagnósticos, clique duas vezes o gateway e, em seguida, selecione **ativar os diagnósticos**.

   ![Seleções para forem ativados os diagnósticos](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert4.png  "selecione")

6. Preencha os detalhes e certifique-se de que **enviar para o Log Analytics** e **TunnelDiagnosticLog** estão selecionadas. Escolha a área de trabalho de análise de registo que criou no passo 3.

   ![Selecionado caixas de verificação](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert5.png  "selecione")

7. Vá para a descrição geral para o recurso de gateway de rede virtual e selecione **alertas** partir do **monitorização** separador. Em seguida, criar uma nova regra de alerta ou editar uma regra de alerta existente.

   ![Seleções para a criação de uma nova regra de alerta](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert6.png  "selecione")

   ![ponto a site](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert6.png  "selecione")
8. Selecione a área de trabalho do Log Analytics e o recurso.

   ![Seleções para a área de trabalho e de recursos](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert7.png  "selecione")

9. Selecione **pesquisa de registos personalizado** como a lógica de sinal sob **adicionar condição**.

   ![Seleções para uma pesquisa de registos personalizado](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert8.png  "selecione")

10. Introduza a seguinte consulta na caixa de texto **Consulta de pesquisa**. Substitua os valores em português conforme apropriado.

     `AzureDiagnostics |
     where Category  == "TunnelDiagnosticLog" and ResourceId == toupper("<RESOURCEID OF GATEWAY>") and TimeGenerated > ago(5m) and
     remoteIP_s == "<REMOTE IP OF TUNNEL>" and status_s == "Disconnected"`

    Defina o valor de limiar para 0 e selecione **feito**.

    ![Introduzir uma consulta e selecionar um limiar](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert9.png  "selecione")

11. Sobre o **criar regra** página, selecione **criar novo** sob o **grupos de ação** secção. Preencha os detalhes e selecione **OK**.

    ![Detalhes de um novo grupo de ação](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert10.png  "selecione")

12. Sobre o **criar regra** página, preencha os detalhes para **personalizar ações** e certifique-se de que o nome correto é apresentado no **nome do grupo de ação** secção. Selecione **criar regra de alerta** para criar a regra.

    ![Selecione as opções para criar uma regra](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert11.png  "selecione")

## <a name="next-steps"></a>Passos Seguintes

Para configurar alertas em métricas de túnel, consulte [configurar alertas em métricas de Gateway de VPN](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md).
