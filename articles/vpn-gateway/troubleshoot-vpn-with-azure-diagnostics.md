---
title: Resolução de problemas Azure VPN Gateway usando registos de diagnóstico
description: Resolução de problemas Azure VPN Gateway usando registos de diagnóstico
services: vpn-gateway
author: stegag
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 03/15/2021
ms.author: stegag
ms.openlocfilehash: 232e084e44696c6aa88a9dd33092c48a96e35f85
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104772009"
---
# <a name="troubleshoot-azure-vpn-gateway-using-diagnostic-logs"></a>Resolução de problemas Azure VPN Gateway usando registos de diagnóstico

Este artigo ajuda a entender os diferentes registos disponíveis para diagnósticos VPN Gateway e como usá-los para resolver eficazmente problemas de gateway VPN.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

Os seguintes registos estão disponíveis em Azure:

|***Nome** _ | _ *_Descrição_** |
|---        | ---               |
|**GatewayDiagnosticLog** | Contém registos de diagnóstico para eventos de configuração de gateways, alterações primárias e eventos de manutenção. |
|**TúnelDiagnosticLog** | Contém eventos de mudança de estado de túnel. Os eventos de ligação/desconexão do túnel têm uma razão resumida para a mudança do estado, se aplicável. |
|**RouteDiagnosticLog** | Regista alterações em rotas estáticas e eventos de BGP que ocorrem no gateway. |
|**IKEDiagnosticLog** | Regista mensagens e eventos de controlo do IKE no gateway. |
|**P2SDiagnosticLog** | Regista mensagens de controlo ponto-a-local e eventos no gateway. |

Note que há várias colunas disponíveis nestas tabelas. Neste artigo, estamos apenas a apresentar os mais relevantes para um consumo mais fácil de registos.

## <a name="set-up-logging"></a><a name="setup"></a>Configurar a exploração madeireira

Para saber como configurar eventos de registo de diagnóstico a partir do Gateway Azure VPN usando a Azure Log Analytics, consulte [Configurar alertas em eventos de registo de diagnóstico a partir do Gateway VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log).

## <a name="gatewaydiagnosticlog"></a><a name="GatewayDiagnosticLog"></a>GatewayDiagnosticLog

As alterações de configuração são auditadas na tabela **GatewayDiagnosticLog.** Pode levar alguns minutos até que as alterações que executa sejam refletidas nos registos.

Aqui tem uma consulta de amostra como referência.

```
AzureDiagnostics  
| where Category == "GatewayDiagnosticLog"  
| project TimeGenerated, OperationName, Message, Resource, ResourceGroup  
| sort by TimeGenerated asc
```

Esta consulta no **GatewayDiagnosticLog** mostrar-lhe-á várias colunas.

|***Nome** _ | _ *_Descrição_** |
|---        | ---               |
|**TimeGenerated** | o calendário de cada evento, no espaço de tempo UTC.|
|**Operação Nome** |o evento que aconteceu. Pode ser um dos *SetGatewayConfiguration, SetConnectionConfiguration, HostMaintenanceEvent, GatewayTenantPrimaryChanged, MigrateCustomerSubscription, GatewayResourceMove, ValidateGatewayConfiguration*.|
|**Mensagem** | o detalhe do que está a acontecer e lista resultados bem sucedidos/falhas.|

O exemplo abaixo mostra a atividade registada quando uma nova configuração foi aplicada:

:::image type="content" source="./media/troubleshoot-vpn-with-azure-diagnostics/image-26-set-gateway.png" alt-text="Exemplo de uma operação set gateway vista em GatewayDiagnosticLog.":::


Note que uma ConfiguraçãoConfiguration será registada sempre que alguma configuração for modificada tanto num Gateway VPN como num Gateway de Rede Local.
Cruzar a referência aos resultados da tabela **GatewayDiagnosticLog** com os da tabela **TunnelDiagnosticLog** pode ajudar-nos a determinar se uma falha de conectividade do túnel começou ao mesmo tempo que uma configuração foi alterada, ou se ocorreu uma manutenção. Se assim for, temos um grande ponteiro para a possível causa de raiz.

## <a name="tunneldiagnosticlog"></a><a name="TunnelDiagnosticLog"></a>TúnelDiagnosticLog

A tabela **TunnelDiagnosticLog** é muito útil para inspecionar os estados históricos de conectividade do túnel.

Aqui tem uma consulta de amostra como referência.

```
AzureDiagnostics
| where Category == "TunnelDiagnosticLog"
| project TimeGenerated, OperationName, instance_s, Resource, ResourceGroup
| sort by TimeGenerated asc
```

Esta consulta no **TunnelDiagnosticLog** mostrar-lhe-á várias colunas.


|***Nome** _ | _ *_Descrição_** |
|---        | ---               |
|**TimeGenerated** | o calendário de cada evento, no espaço de tempo UTC.|
|**Operação Nome** | o evento que aconteceu. Pode ser *ligado ao túnel* ou *ao TúnelDis.*|
| **Exemplo \_ s** | a instância de papel gateway que desencadeou o evento. Pode ser GatewayTenantWorker \_ IN \_ 0 ou GatewayTenantWorker \_ IN \_ 1, que são os nomes das duas instâncias do gateway.|
| **Recurso** | indica o nome do gateway VPN. |
| **Grupo de Recursos** | indica o grupo de recursos onde está o portal.|


Exemplo de saída:

:::image type="content" source="./media/troubleshoot-vpn-with-azure-diagnostics/image-16-tunnel-connected.png" alt-text="Exemplo de um Evento Conectado de Túnel visto no TunnelDiagnosticLog.":::


O **TunnelDiagnosticLog** é muito útil para resolver problemas passados sobre desconexões inesperadas de VPN. A sua natureza leve oferece a possibilidade de analisar grandes intervalos de tempo ao longo de vários dias com pouco esforço.
Só depois de identificar o tempotamp de uma desconexão, pode mudar para a análise mais detalhada da tabela **IKEdiagnosticLog** para aprofundar o raciocínio das desconexões, devendo estar relacionadas com o IPsec.


Algumas dicas para a resolução de problemas:
- Se vir um evento de desconexão numa instância de gateway, seguido de um evento de ligação na instância de **gateway diferente** em poucos segundos, está a olhar para um portão de falha. Este é geralmente um comportamento esperado devido à manutenção em uma instância de gateway. Para saber mais sobre este comportamento, consulte [sobre a redundância de gateway da Azure VPN.](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-highlyavailable#about-azure-vpn-gateway-redundancy)
- O mesmo comportamento será observado se executar intencionalmente um Gateway Reset no lado Azure - o que provoca um reinício da instância de gateway ativa. Para saber mais sobre este comportamento, consulte [Reset a VPN Gateway](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-resetgw-classic).
- Se vir um evento de desconexão numa instância de gateway, seguido de um evento de ligação na **mesma** instância do gateway em poucos segundos, poderá estar a olhar para uma falha de rede que proceda a uma pausa de DPD ou a uma desconexão enviada erroneamente pelo dispositivo no local.

## <a name="routediagnosticlog"></a><a name="RouteDiagnosticLog"></a>RouteDiagnosticLog

A tabela **RouteDiagnosticLog** traça a atividade de rotas ou rotas estáticas modificadas recebidas via BGP.

Aqui tem uma consulta de amostra como referência.

```
AzureDiagnostics
| where Category == "RouteDiagnosticLog"
| project TimeGenerated, OperationName, Message, Resource, ResourceGroup
```

Esta consulta no **RouteDiagnosticLog** mostrar-lhe-á várias colunas.

|***Nome** _ | _ *_Descrição_** |
|---        | ---               |
|**TimeGenerated** | o calendário de cada evento, no espaço de tempo UTC.|
|**Operação Nome** | o evento que aconteceu. Pode ser um dos *StaticRouteUpdate, BgpRouteUpdate, BgpConnectedEvent, BgpDisconnectedEvent*.|
| **Mensagem** | o detalhe do que a operação está a acontecer.|

A saída mostrará informações úteis sobre pares BGP ligados/desligados e rotas trocadas.

Exemplo:


:::image type="content" source="./media/troubleshoot-vpn-with-azure-diagnostics/image-31-bgp-route.png" alt-text="Exemplo da atividade de troca de rotas BGP vista no RouteDiagnosticLog.":::


## <a name="ikediagnosticlog"></a><a name="IKEDiagnosticLog"></a>IKEDiagnosticLog

A tabela **IKEDiagnosticLog** oferece registo de depuragem verbose para IKE/IPsec. Isto é muito útil para rever quando se desconectam desconexões ou falha na ligação de cenários VPN.

Aqui tem uma consulta de amostra como referência.

```
AzureDiagnostics  
| where Category == "IKEDiagnosticLog" 
| extend Message1=Message
| parse Message with * "Remote " RemoteIP ":" * "500: Local " LocalIP ":" * "500: " Message2
| extend Event = iif(Message has "SESSION_ID",Message2,Message1)
| project TimeGenerated, RemoteIP, LocalIP, Event, Level 
| sort by TimeGenerated asc
```

Esta consulta no **IKEDiagnosticLog** mostrar-lhe-á várias colunas.


|***Nome** _ | _ *_Descrição_** |
|---        | ---               |
|**TimeGenerated** | o calendário de cada evento, no espaço de tempo UTC.|
| **RemoteIP** | o endereço IP do dispositivo VPN no local. Nos cenários do mundo real, é útil filtrar pelo endereço IP do dispositivo relevante no local deve haver mais do que um. |
|**LocalIP** | o endereço IP do Gateway VPN estamos a resolver problemas. Em cenários reais, é útil filtrar pelo endereço IP do gateway VPN relevante deve haver mais de um na sua subscrição. |
|**Evento** | contém uma mensagem de diagnóstico útil para a resolução de problemas. Geralmente começam com uma palavra-chave e referem-se às ações realizadas pelo Gateway Azure: **\[ SEND \]** indica um evento causado por um pacote IPSec enviado pelo Gateway Azure.  **\[ O \] RECEIVED** indica um evento em consequência de um pacote recebido do dispositivo no local.  **\[ O \] LOCAL** indica uma ação tomada localmente pelo Gateway Azure. |


Note como as colunas RemoteIP, LocalIP e Event não estão presentes na lista de colunas originais na base de dados AzureDiagnostics, mas são adicionadas à consulta através da análise da saída da coluna "Mensagem" para simplificar a sua análise.

Dicas de resolução de problemas:

- Para identificar o início de uma negociação ipSec, é necessário encontrar a mensagem inicial DO \_ INIT SA. Tal mensagem pode ser enviada por ambos os lados do túnel. Quem enviar o primeiro pacote é chamado de "iniciador" na terminologia do IPsec, enquanto o outro lado torna-se o "responder". A primeira mensagem SA \_ INIT é sempre aquela em que rCookie = 0.

- Se o túnel do IPsec não se estabelecer, o Azure continuará a tentar a cada poucos segundos. Por esta razão, a resolução de problemas de resolução de problemas de "VPN down" é muito conveniente no IKEdiagnosticLog porque não é preciso esperar por um momento específico para reproduzir o problema. Além disso, o fracasso será sempre o mesmo sempre que tentamos, para que possas ampliar uma "amostra" de negociação falhada a qualquer momento.

- O SA \_ INIT contém os parâmetros IPSec que o par quer usar para esta negociação do IPSEC. O documento oficial   
[Os parâmetros IPsec/IKE predefinidos](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices#ipsec) listam os parâmetros IPsec suportados pelo Gateway Azure com definições predefinidas.


## <a name="p2sdiagnosticlog"></a><a name="P2SDiagnosticLog"></a>P2SDiagnosticLog

A última tabela disponível para diagnósticos VPN é **P2SDiagnosticLog**. Esta tabela traça a atividade do Ponto a Local.

Aqui tem uma consulta de amostra como referência.

```
AzureDiagnostics  
| where Category == "P2SDiagnosticLog"  
| project TimeGenerated, OperationName, Message, Resource, ResourceGroup
```

Esta consulta no **P2SDiagnosticLog** mostrar-lhe-á várias colunas.

|***Nome** _ | _ *_Descrição_** |
|---        | ---               |
|**TimeGenerated** | o calendário de cada evento, no espaço de tempo UTC.|
|**Operação Nome** | o evento que aconteceu. Será *P2SLogEvent*.|
| **Mensagem** | o detalhe do que a operação está a acontecer.|

A saída mostrará todas as definições de Ponto para Local que o gateway aplicou, bem como as políticas IPsec em vigor.

:::image type="content" source="./media/troubleshoot-vpn-with-azure-diagnostics/image-28-p2s-log-event.png" alt-text="Exemplo da ligação ponto a local vista no P2SDiagnosticLog.":::

Além disso, sempre que um cliente se ligar via IKEv2 ou OpenVPN Point to Site, a tabela registará a atividade do pacote, as conversas EAP/RADIUS e os resultados bem sucedidos/falha do utilizador.

:::image type="content" source="./media/troubleshoot-vpn-with-azure-diagnostics/image-29-eap.png" alt-text="Exemplo de autenticação EAP visto no P2SDiagnosticLog.":::

## <a name="next-steps"></a>Passos Seguintes

Para configurar alertas nos registos de recursos do túnel, consulte [Configurar alertas nos registos de recursos do Gateway VPN](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md).

