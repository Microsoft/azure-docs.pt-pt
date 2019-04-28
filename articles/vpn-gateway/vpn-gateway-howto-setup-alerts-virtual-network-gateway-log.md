---
title: Como configurar alertas nos eventos de registo de diagnóstico do Gateway de VPN do Azure
description: Passos para configurar alertas de eventos de registo de diagnóstico do Gateway de VPN
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptional
ms.date: 04/22/2019
ms.author: alzam
ms.openlocfilehash: 39a6d2e6201dd0635149159cb3727fd3c40f710a
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/24/2019
ms.locfileid: "63769740"
---
# <a name="setting-up-alerts-on-vpn-gateway-diagnostic-log-events"></a>Configuração de alertas nos eventos de registo de diagnóstico do Gateway de VPN

Este artigo ajuda-o a configurar alertas com base em eventos de registo de diagnóstico do Gateway de VPN.


## <a name="setup"></a>Configurar alertas de Monitor do Azure com base em eventos de registo de diagnóstico com o portal

Os passos de exemplo abaixo, irão criar um alerta para um evento de desativação de túnel VPN de site a site



1. Procure "Log Analytics" em todos os serviços e escolha "Áreas de trabalho do Log Analytics" ![ponto a site](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert0.png "Create")

2. Na página do Log Analytics, clique em "Criar".
![ponto a site](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert1.png  "selecione")

3. Verifique a área de trabalho "Criar nova" e preencha os detalhes.
![ponto a site](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert2.png  "selecione")

4. Encontrar o gateway de VPN sob o "Monitor" > Painel "Definições de diagnóstico" ![ponto a site](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert3.png  "selecione")

5. Para ativar os diagnósticos, faça duplo clique no gateway e, em seguida, clique em "Ativar o diagnóstico" ![ponto a site](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert4.png  "selecione")

6. Preencha os detalhes e certifique-se de que a opção "Enviar para o Log Analytics" e "TunnelDiagnosticLog" são verificados. Escolha a área de trabalho de análise de registo que foi criada no passo 3.
![ponto a site](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert5.png  "selecione")

7. Navegue para a descrição geral de recursos do gateway de rede virtual e selecione "Alertas" no separador monitorização, em seguida, criar uma nova regra de alerta ou editar uma regra de alerta existente.
![ponto a site](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert6.png  "selecione")

8. Selecione a área de trabalho do Log Analytics e o recurso.
![ponto a site](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert7.png  "selecione")

9. Selecione "personalizado pesquisa de registos" como a lógica de sinal em "Adicionar condição" ![ponto a site](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert8.png  "selecione")

10. Introduza a seguinte consulta na caixa de texto de "Consulta de pesquisa" substituindo os valores em português conforme apropriado.

    AzureDiagnostics | em que categoria = = "TunnelDiagnosticLog" e ResourceId = = toupper ("<RESOURCEID OF GATEWAY>") e TimeGenerated > ago(5m) e remoteIP_s = = "<REMOTE IP OF TUNNEL>" e status_s = = "Desligado"

    Defina o valor de limiar para 0 e clique em "Concluído"

    ![ponto a site](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert9.png  "selecione")

11. Na página "Criar regra", clique em "Criar nova" na seção grupos de ação. Preencha os detalhes e clique em OK

![ponto a site](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert10.png  "selecione")

12. Na página "Criar regra", preencha os detalhes para "Personalizar Action" e certifique-se de que o nome do grupo de ação correto é apresentado na secção "Nome do grupo de ação". Clique em "Criar regra de alerta" para criar a regra.
![ponto a site](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert11.png  "selecione")

## <a name="next-steps"></a>Passos Seguintes

Para configurar alertas em métricas de túnel, consulte [como configurar alertas em métricas de Gateway de VPN](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md).
