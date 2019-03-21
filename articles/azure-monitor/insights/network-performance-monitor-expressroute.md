---
title: Solução de Monitor de desempenho no Azure Log Analytics de rede | Documentos da Microsoft
description: Utilize a capacidade de Monitor do ExpressRoute no Monitor de desempenho de rede para monitorizar a conectividade de ponto-a-ponto e de desempenho entre suas sucursais e o Azure, através do Azure ExpressRoute.
services: log-analytics
documentationcenter: ''
author: abshamsft
manager: carmonm
editor: ''
ms.assetid: 5b9c9c83-3435-488c-b4f6-7653003ae18a
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: abshamsft
ms.openlocfilehash: d0819b57307fc037b3be6ab04ed9ec6c8720a618
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58103592"
---
# <a name="expressroute-monitor"></a>Monitor do ExpressRoute

Pode utilizar a capacidade de Monitor do ExpressRoute do Azure no [Monitor de desempenho de rede](network-performance-monitor.md) para monitorizar a conectividade de ponto-a-ponto e de desempenho entre suas sucursais e o Azure, através do Azure ExpressRoute. As principais vantagens são: 

- Circuitos de Autodetection de ExpressRoute associados à sua subscrição.
- Controlo de utilização de largura de banda, perda e latência no circuito, peering e nível de rede Virtual do Azure para o ExpressRoute.
- Deteção da topologia de rede dos circuitos de ExpressRoute.

![Monitor do ExpressRoute](media/network-performance-monitor-expressroute/expressroute-intro.png)

## <a name="configuration"></a>Configuração 
Para abrir a configuração de Monitor de desempenho de rede, abra a [solução Monitor de desempenho de rede](network-performance-monitor.md) e selecione **configurar**.

### <a name="configure-network-security-group-rules"></a>Configurar regras de grupo de segurança de rede 
Para os servidores no Azure que são utilizados para monitorização pelo Monitor de desempenho de rede, configure regras de grupo (NSG) de segurança de rede para permitir o tráfego TCP na porta utilizada pelo Monitor de desempenho de rede para transações sintéticas. A porta predefinida é 8084. Esta configuração permite que o agente do Log Analytics instalado em VMs do Azure para comunicar com uma local do agente de monitorização. 

Para obter mais informações sobre NSGs, consulte [grupos de segurança de rede](../../virtual-network/manage-network-security-group.md). 

>[!NOTE]
> Antes de continuar com este passo, instale o agente do server no local e o agente do servidor do Azure e executar o script EnableRules.ps1 PowerShell. 

 
### <a name="discover-expressroute-peering-connections"></a>Detetar ligações de peering do ExpressRoute 
 
1. Selecione o **Peerings do ExpressRoute** vista.
2. Selecione **detetar agora** para detetar todos os ExpressRoute peerings privados que estão ligados a redes virtuais na subscrição do Azure associado a esta área de trabalho do Log Analytics do Azure.

    >[!NOTE]
    > A solução atualmente Deteta apenas peerings privada do ExpressRoute. 

    >[!NOTE]
    > Apenas são detetados peerings privados ligadas às redes virtuais associadas à subscrição que está associada a esta área de trabalho do Log Analytics. Se o ExpressRoute é ligado a redes virtuais fora da subscrição ligada a esta área de trabalho, crie uma área de trabalho do Log Analytics nessas subscrições. Em seguida, utilize o Monitor de desempenho de rede para monitorizar os peerings. 

    ![Configuração de Monitor do ExpressRoute](media/network-performance-monitor-expressroute/expressroute-configure.png)
 
   Depois de concluída a deteção, as ligações de peering privadas detetadas estão listadas numa tabela. A monitorização para estes peerings é inicialmente desabilitado. 

### <a name="enable-monitoring-of-the-expressroute-peering-connections"></a>Ativar a monitorização das ligações de peering do ExpressRoute 

1. Selecione a ligação de peering privada que pretende monitorizar.
2. No painel à direita, selecione o **monitorizar este Peering** caixa de verificação. 
3. Se pretende criar eventos de estado de funcionamento para esta ligação, selecione **ativar a monitorização de estado de funcionamento para este peering**. 
4. Escolha a monitorização de condições. Pode definir limiares personalizados para a geração de evento de estado de funcionamento ao introduzir os valores de limiar. Sempre que o valor da condição mais do seu limiar selecionado para a ligação de peering, é gerado um evento de estado de funcionamento. 
5. Selecione **adicionar agentes** para escolher os agentes de monitorização que pretende utilizar para monitorizar esta ligação de peering. Certifique-se de que adicione agentes em ambas as extremidades da ligação. Tem de, pelo menos, um agente na rede virtual ligado a este peering. Também precisa de, pelo menos, um agente no local ligado a este peering. 
6. Selecione **guardar** para guardar a configuração. 

   ![Configuração de monitorização de ExpressRoute](media/network-performance-monitor-expressroute/expressroute-configure-discovery.png)


Depois de ativar as regras e valores select e agentes, aguarde 30 e 60 minutos para os valores preencher e o **a monitorização de ExpressRoute** mosaicos a aparecer. Quando vir os mosaicos de monitorização, os seus circuitos do ExpressRoute e os recursos de ligação agora são monitorizados pelo Monitor de desempenho de rede. 

>[!NOTE]
> Esse recurso funciona de forma fiável em áreas de trabalho que tem atualizado para a nova linguagem de consulta.

## <a name="walkthrough"></a>Instruções 

O dashboard de Monitor de desempenho de rede mostra uma descrição geral do Estado de funcionamento dos circuitos do ExpressRoute e ligações de peering. 

![Dashboard de Monitor de desempenho de rede](media/network-performance-monitor-expressroute/npm-dashboard-expressroute.png) 

### <a name="circuits-list"></a>Lista de circuitos 

Para ver uma lista de todos os circuitos do ExpressRoute monitorizados, selecione o mosaico de circuitos do ExpressRoute. Pode selecionar um circuito e ver o estado de funcionamento, gráficos de tendências para perda de pacotes, utilização de largura de banda e latência. Os gráficos são interativos. Pode selecionar uma janela de tempo personalizado para desenhar gráficos. Arraste o mouse sobre uma área no gráfico para ampliar e ver os pontos de dados detalhados. 

![Lista de circuitos do ExpressRoute](media/network-performance-monitor-expressroute/expressroute-circuits.png) 

### <a name="trends-of-loss-latency-and-throughput"></a>Tendências de perda, latência e débito 

Os gráficos de utilização, latência e perda de largura de banda são interativos. Pode ampliar para qualquer seção destes gráficos ao utilizar controlos de mouse. Também pode ver a largura de banda, latência e dados de perda de outros intervalos. No canto superior esquerdo, sob o **ações** botão, selecione **data/hora**. 

![Latência de ExpressRoute](media/network-performance-monitor-expressroute/expressroute-latency.png) 

### <a name="peerings-list"></a>Lista de Peerings 

Para abrir uma lista de todas as ligações a redes virtuais através do peering privado, selecione o **Peerings privados** mosaico no dashboard. Aqui, pode selecionar um virtual ligação de rede e para ver o estado de funcionamento, gráficos de tendências para perda de pacotes, utilização de largura de banda e latência. 

![Peerings do ExpressRoute](media/network-performance-monitor-expressroute/expressroute-peerings.png) 

### <a name="circuit-topology"></a>Topologia de circuito 

Para ver a topologia de circuito, selecione o **topologia** mosaico. Esta ação leva-o para a vista de topologia selecionado do circuito ou o peering. O diagrama de topologia fornece a latência para cada segmento da rede, e cada salto de camada 3 é representado por um nó do diagrama. Selecionar um salto revela mais detalhes sobre o salto. Para aumentar o nível de visibilidade para incluir saltos no local, mover a barra de controlo de deslize sob **filtros**. Mover a barra de controlo de deslize para a esquerda ou direita aumenta ou diminui o número de saltos no gráfico de topologia. A latência em cada segmento é visível, que permitem o isolamento mais rápido de segmentos de alta latência na sua rede.

![Topologia do ExpressRoute](media/network-performance-monitor-expressroute/expressroute-topology.png)

### <a name="detailed-topology-view-of-a-circuit"></a>Vista de topologia detalhados de um circuito 

Esta vista mostra as ligações de rede virtual. 

![Ligações de rede virtual do ExpressRoute](media/network-performance-monitor-expressroute/expressroute-vnet.png)
 
## <a name="diagnostics"></a>Diagnóstico 

Monitor de desempenho de rede ajuda-o a diagnosticar vários problemas de conectividade do circuito. Alguns dos problemas que pode ver estão listados abaixo.

Pode ver os códigos de notificação e definir alertas nos mesmos via **LogAnalytics**. Sobre o **diagnóstico de NPM** página, pode ver as descrições para todas as mensagens de diagnóstico acionada.

| Código de notificação (Logs) | Descrição |
| --- | --- |
| 5501 | Não é possível percorrer a ligação secundária do circuito do ExpressRoute |
| 5502 | Não é possível percorrer a ligação primária do circuito do ExpressRoute |
| 5503 | Foi encontrado nenhum circuito para a subscrição ligada à área de trabalho | 
| 5508 | Não é possível determinar se tráfego passa através de quaisquer circuitos para o caminho |
| 5510 | O tráfego não passa pelo circuito pretendido | 
| 5511 | O tráfego não passa através da rede virtual pretendida | 

**Circuito está inativo.** Monitor de desempenho de rede notifica-o assim que a conectividade entre a recursos no local e redes virtuais do Azure é perdida. Esta notificação ajuda-o a tomar medidas proativas, antes de receber os escalonamentos de usuário e reduzir o tempo de inatividade.

![Circuito do ExpressRoute está inativo](media/network-performance-monitor-expressroute/expressroute-circuit-down.png)
 

**Tráfego não fluir pelo circuito pretendido.** Monitor de desempenho de rede notifica-o sempre que o tráfego não está a fluir pelo circuito do ExpressRoute pretendido. Este problema pode acontecer se o circuito está inativo e tráfego estiver fluindo por meio da rota de cópia de segurança. Também pode acontecer se existir um problema de encaminhamento. Estas informações ajudam-na gerir problemas de configuração nas suas políticas de encaminhamento e certifique-se de que a rota mais segura e ideal é usada de forma proativa. 

 

**Fluxo do tráfego não pela circuito primário.** Monitor de desempenho de rede notifica-o quando o tráfego estiver fluindo por meio do circuito de ExpressRoute secundário. Apesar de não ter quaisquer problemas de conectividade nesse caso, proativamente a resolver problemas com o circuito primário torna-o melhor preparado. 

 
![Fluxo de tráfego do ExpressRoute](media/network-performance-monitor-expressroute/expressroute-traffic-flow.png)


**Degradação devido a utilização de pico.** Pode correlacionar a tendência de utilização de largura de banda com a tendência de latência para identificar se a degradação de carga de trabalho do Azure é devido a um pico na utilização de largura de banda ou não. Em seguida, agir em conformidade.

![Utilização de largura de banda do ExpressRoute](media/network-performance-monitor-expressroute/expressroute-peak-utilization.png)

 

## <a name="next-steps"></a>Passos Seguintes
[Pesquisar registos](../../azure-monitor/log-query/log-query-overview.md) para ver os registos de dados de desempenho de rede detalhada.
