---
title: Solução de Monitor de Desempenho de Rede no Azure Log Analytics [ Microsoft Docs
description: Utilize a capacidade do Monitor ExpressRoute no Monitor de Desempenho da Rede para monitorizar a conectividade e desempenho de ponta a ponta entre as suas filiais e o Azure, sobre o Azure ExpressRoute.
ms.subservice: logs
ms.topic: conceptual
author: abshamsft
ms.author: absha
ms.date: 11/27/2018
ms.openlocfilehash: 6ac610d7dcf9849b6b439741957684867b9d01aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77660755"
---
# <a name="expressroute-monitor"></a>ExpressRoute Monitor

Pode utilizar a capacidade do Monitor Azure ExpressRoute no Monitor de Desempenho da [Rede](network-performance-monitor.md) para monitorizar a conectividade e desempenho de ponta a ponta entre as suas filiais e o Azure, sobre o Azure ExpressRoute. As principais vantagens são: 

- Deteção automática dos circuitos ExpressRoute associados à sua subscrição.
- Rastreio da utilização da largura de banda, perda e latência no circuito, peering e nível de Rede Virtual Azure para ExpressRoute.
- Descoberta da topologia da rede dos seus circuitos ExpressRoute.

![ExpressRoute Monitor](media/network-performance-monitor-expressroute/expressroute-intro.png)

## <a name="configuration"></a>Configuração 
Para abrir a configuração do Monitor de Desempenho da Rede, abra a [solução](network-performance-monitor.md) do Monitor de Desempenho da Rede e selecione **Configurar**.

### <a name="configure-network-security-group-rules"></a>Configure as regras do grupo de segurança da rede 
Para os servidores do Azure que são utilizados para monitorização através do Network Performance Monitor, configurar as regras do grupo de segurança da rede (NSG) para permitir o tráfego de TCP na porta utilizada pelo Network Performance Monitor para transações sintéticas. A porta padrão é 8084. Esta configuração permite que o agente Log Analytics instalado em VMs Azure se comunique com um agente de monitorização no local. 

Para obter mais informações sobre nsgs, consulte [grupos](../../virtual-network/manage-network-security-group.md)de segurança da Rede . 

>[!NOTE]
> Antes de continuar com este passo, instale o agente de servidor estivado e o agente do servidor Azure e execute o script EnableRules.ps1 PowerShell. 

 
### <a name="discover-expressroute-peering-connections"></a>Descubra ligações de peering ExpressRoute 
 
1. Selecione a vista **ExpressRoute Peerings.**
2. Selecione **Discover Now** para descobrir todos os pares privados ExpressRoute que estão ligados às redes virtuais na subscrição Azure ligada a este espaço de trabalho Azure Log Analytics.

    >[!NOTE]
    > A solução atualmente descobre apenas os pares privados ExpressRoute. 

    >[!NOTE]
    > Apenas são descobertos os pares privados ligados às redes virtuais associadas à subscrição ligada a este espaço de trabalho log Analytics. Se o ExpressRoute estiver ligado a redes virtuais fora da subscrição ligada a este espaço de trabalho, crie um espaço de trabalho log Analytics nessas subscrições. Em seguida, utilize o Monitor de Desempenho da Rede para monitorizar os pares. 

    ![Configuração do Monitor ExpressRoute](media/network-performance-monitor-expressroute/expressroute-configure.png)
 
   Após a descoberta estar completa, as ligações de observação privada descobertas estão listadas numa mesa. A monitorização destes pares encontra-se inicialmente num estado de desativação. 

### <a name="enable-monitoring-of-the-expressroute-peering-connections"></a>Ativar a monitorização das ligações de peering ExpressRoute 

1. Selecione a ligação de observação privada que pretende monitorizar.
2. No painel à direita, selecione a caixa de **verificação 'Peering'.** 
3. Se pretender criar eventos de saúde para esta ligação, selecione **Enable Health Monitoring para este epeering**. 
4. Escolha as condições de monitorização. Pode estabelecer limiares personalizados para a geração de eventos de saúde, entrando em valores-limiar. Sempre que o valor da circunstância ultrapassa o seu limiar selecionado para a ligação de pares, gera-se um evento de saúde. 
5. Selecione **Adicionar Agentes** para escolher os agentes de monitorização que pretende utilizar para monitorizar esta ligação de observação. Certifique-se de que adiciona agentes em ambas as extremidades da ligação. Precisa de pelo menos um agente na rede virtual ligado a este olhar. Também precisa de pelo menos um agente no local ligado a este olhar. 
6. Selecione **Guardar** para salvar a configuração. 

   ![Configuração de monitorização ExpressRoute](media/network-performance-monitor-expressroute/expressroute-configure-discovery.png)


Depois de ativar as regras e selecionar valores e agentes, aguarde 30 a 60 minutos para que os valores possam povoar e os azulejos **de Monitorização ExpressRoute** apareçam. Quando vir os azulejos de monitorização, os seus circuitos ExpressRoute e os recursos de ligação são agora monitorizados pelo Monitor de Desempenho da Rede. 

>[!NOTE]
> Esta capacidade funciona de forma fiável em espaços de trabalho que melhoraram para a nova linguagem de consulta.

## <a name="walkthrough"></a>Instruções 

O painel do Monitor de Desempenho da Rede mostra uma visão geral da saúde dos circuitos ExpressRoute e das ligações de observação. 

![Painel de monitor de desempenho da rede](media/network-performance-monitor-expressroute/npm-dashboard-expressroute.png) 

### <a name="circuits-list"></a>Lista de circuitos 

Para ver uma lista de todos os circuitos ExpressRoute monitorizados, selecione o azulejo dos circuitos ExpressRoute. Pode selecionar um circuito e visualizar o seu estado de saúde, gráficos de tendências para perda de pacotes, utilização da largura de banda e latência. Os gráficos são interativos. Pode selecionar uma janela de tempo personalizada para traçar as tabelas. Arraste o rato sobre uma área na tabela para ampliar e ver pontos de dados de grãos finos. 

![Lista de circuitos ExpressRoute](media/network-performance-monitor-expressroute/expressroute-circuits.png) 

### <a name="trends-of-loss-latency-and-throughput"></a>Tendências de perda, latência e entrada 

A utilização da largura de banda, a latência e os gráficos de perda são interativos. Pode ampliar qualquer secção destes gráficos utilizando controlos de rato. Também pode ver os dados de largura de banda, latência e perda para outros intervalos. Na parte superior esquerda sob o botão **Ações,** selecione **Data/Hora**. 

![Latência ExpressRoute](media/network-performance-monitor-expressroute/expressroute-latency.png) 

### <a name="peerings-list"></a>Lista de pares 

Para apresentar uma lista de todas as ligações a redes virtuais através de um epeering privado, selecione o azulejo **Private Peerings** no painel de instrumentos. Aqui, você pode selecionar uma conexão de rede virtual e ver o seu estado de saúde, gráficos de tendências para perda de pacotes, utilização de largura de banda e latência. 

![Peerings ExpressRoute](media/network-performance-monitor-expressroute/expressroute-peerings.png) 

### <a name="circuit-topology"></a>Topologia de circuito 

Para ver topologia de circuito, selecione o azulejo **topologia.** Esta ação leva-o à vista de topo do circuito ou do olhar selecionado. O diagrama de topologia proporciona a latência para cada segmento na rede, e cada lúpulo camada 3 é representado por um nó do diagrama. Selecionar um salto revela mais detalhes sobre o lúpulo. Para aumentar o nível de visibilidade para incluir lúpulo no local, mova a barra de slider sob **FILTROS**. Mover a barra de slider para a esquerda ou direita aumenta ou diminui o número de lúpulo no gráfico de topologia. A latência em cada segmento é visível, o que permite um isolamento mais rápido de segmentos de alta latência na sua rede.

![Topologia ExpressRoute](media/network-performance-monitor-expressroute/expressroute-topology.png)

### <a name="detailed-topology-view-of-a-circuit"></a>Vista de topologia detalhada de um circuito 

Esta vista mostra ligações de rede virtuais. 

![Ligações de rede virtual ExpressRoute](media/network-performance-monitor-expressroute/expressroute-vnet.png)
 
## <a name="diagnostics"></a>Diagnóstico 

O Network Performance Monitor ajuda-o a diagnosticar vários problemas de conectividade de circuitos. Algumas das questões que pode ver estão listadas abaixo.

Pode ver os códigos de notificação e definir alertas através do **LogAnalytics**. Na página de **Diagnóstico sem PMN,** pode ver descrições de cada mensagem de diagnóstico desencadeada.

| Código de Notificação (Registos) | Descrição |
| --- | --- |
| 5501 | Incapaz de atravessar através da ligação secundária do circuito ExpressRoute |
| 5502 | Incapaz de atravessar a ligação primária do circuito ExpressRoute |
| 5503 | Não é encontrado nenhum circuito para subscrição ligada ao espaço de trabalho | 
| 5508 | Não é possível determinar se o tráfego está a passar por qualquer circuito(s) para o caminho |
| 5510 | O trânsito não está a passar pelo circuito pretendido. | 
| 5511 | O tráfego não está a passar pela rede virtual pretendida | 

**O circuito está avariado.** O Network Performance Monitor informa-o assim que a conectividade entre os seus recursos no local e as redes virtuais Azure se perde. Esta notificação ajuda-o a tomar medidas proactivas antes de receber as escaladas dos utilizadores e reduzir o tempo de inatividade.

![Circuito ExpressRoute está em baixo](media/network-performance-monitor-expressroute/expressroute-circuit-down.png)
 

**O trânsito não flui através do circuito pretendido.** O Monitor de Desempenho da Rede informa-o sempre que o tráfego não flui através do circuito ExpressRoute pretendido. Esta questão pode acontecer se o circuito estiver em baixo e o tráfego fluir pela rota de reserva. Também pode acontecer se houver um problema de encaminhamento. Esta informação ajuda-o a gerir proativamente quaisquer problemas de configuração nas suas políticas de encaminhamento e certifique-se de que a rota mais ideal e segura é usada. 

 

**O trânsito não flui pelo circuito primário.** O Monitor de Desempenho da Rede notifica-o quando o tráfego está a fluir através do circuito secundário expressRoute. Mesmo que não experimente qualquer problema de conectividade neste caso, resolver proativamente os problemas com o circuito primário torna-o mais bem preparado. 

 
![Fluxo de tráfego expressRoute](media/network-performance-monitor-expressroute/expressroute-traffic-flow.png)


**Degradação devido à utilização máxima.** Pode correlacionar a tendência de utilização da largura de banda com a tendência de latência para identificar se a degradação da carga de trabalho azure se deve ou não a um pico de utilização da largura de banda. Então pode agir em conformidade.

![Utilização da largura de banda ExpressRoute](media/network-performance-monitor-expressroute/expressroute-peak-utilization.png)

 

## <a name="next-steps"></a>Passos seguintes
[Pesquisar registos](../../azure-monitor/log-query/log-query-overview.md) para visualizar registos de desempenho de rede detalhados.
