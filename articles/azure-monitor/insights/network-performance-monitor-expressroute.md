---
title: Solução monitor de desempenho de rede em Azure Log Analytics | Microsoft Docs
description: Utilize a capacidade do Monitor ExpressRoute no Monitor de Desempenho da Rede para monitorizar a conectividade e o desempenho de ponta a ponta entre as suas sucursais e a Azure, sobre o Azure ExpressRoute.
ms.topic: conceptual
author: abshamsft
ms.author: absha
ms.date: 11/27/2018
ms.openlocfilehash: 0b005e46e9f9a740fee153e063bebf108c22b303
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101725701"
---
# <a name="expressroute-monitor"></a>ExpressRoute Monitor

> [!IMPORTANT]
> A partir de 1 de julho de 2021, não poderá adicionar novos testes num espaço de trabalho existente ou permitir um novo espaço de trabalho no Network Performance Monitor. Pode continuar a utilizar os testes criados antes de 1 de julho de 2021. Para minimizar a perturbação do serviço nas suas cargas de trabalho atuais, [migrar os seus testes do Monitor de Desempenho da Rede para o novo Monitor de Ligação](../../network-watcher/migrate-to-connection-monitor-from-network-performance-monitor.md) no Azure Network Watcher antes de 29 de fevereiro de 2024.

Pode utilizar a capacidade do Monitor Azure ExpressRoute no [Network Performance Monitor](network-performance-monitor.md) para monitorizar a conectividade e o desempenho de ponta a ponta entre as suas sucursais e a Azure, sobre o Azure ExpressRoute. As principais vantagens são: 

- Autodetecção dos circuitos ExpressRoute associados à sua subscrição.
- Rastreio da utilização da largura de banda, perda e latência no circuito, espreitando e nível de Rede Virtual Azure para ExpressRoute.
- Descoberta da topologia da rede dos seus circuitos ExpressRoute.

![ExpressRoute Monitor](media/network-performance-monitor-expressroute/expressroute-intro.png)

## <a name="configuration"></a>Configuração 
Para abrir a configuração para Monitor de Desempenho de Rede, abra a [solução monitor de desempenho](network-performance-monitor.md) da rede e selecione **Configure**.

### <a name="configure-network-security-group-rules"></a>Configurar regras do grupo de segurança da rede 
Para os servidores em Azure que são utilizados para monitorização através do Network Performance Monitor, configurar regras do Grupo de Segurança da Rede (NSG) para permitir o tráfego de TCP na porta utilizada pelo Network Performance Monitor para transações sintéticas. A porta padrão é 8084. Esta configuração permite que o agente Log Analytics instalado em VMs Azure comunique com um agente de monitorização no local. 

Para obter mais informações sobre os NSGs, consulte [os grupos de segurança da Rede](../../virtual-network/manage-network-security-group.md). 

>[!NOTE]
> Antes de continuar com este passo, instale o agente de servidor no local e o agente do servidor Azure e execute o EnableRules.ps1 script PowerShell. 

 
### <a name="discover-expressroute-peering-connections"></a>Descubra ligações de observação ExpressRoute 
 
1. Selecione a vista **ExpressRoute Peerings.**
2. Selecione **Discover Now** para descobrir todos os perspões privados ExpressRoute que estão ligados às redes virtuais na subscrição Azure ligada a este espaço de trabalho Azure Log Analytics.

    >[!NOTE]
    > A solução atualmente descobre apenas os seus pares privados ExpressRoute. 

    >[!NOTE]
    > Apenas são descobertos os seus pares privados ligados às redes virtuais associadas à subscrição ligada a este espaço de trabalho Log Analytics. Se o ExpressRoute estiver ligado a redes virtuais fora da subscrição ligada a este espaço de trabalho, crie um espaço de trabalho Log Analytics nessas subscrições. Em seguida, utilize o Monitor de Desempenho da Rede para monitorizar os seus olhos. 

    ![Configuração do Monitor ExpressRoute](media/network-performance-monitor-expressroute/expressroute-configure.png)
 
   Após a descoberta estar completa, as ligações privadas descobertas estão listadas numa mesa. A monitorização destes olhos encontra-se inicialmente num estado de incapacidade. 

### <a name="enable-monitoring-of-the-expressroute-peering-connections"></a>Permitir a monitorização das ligações de observação ExpressRoute 

1. Selecione a ligação de espreitamento privada que pretende monitorizar.
2. No painel à direita, selecione a caixa **de verificação 'Peering' do Monitor.** 
3. Se pretender criar eventos de saúde para esta ligação, selecione **Enable Health Monitoring para este espreitê-lo**. 
4. Escolha as condições de monitorização. Pode definir limiares personalizados para a geração de eventos de saúde introduzindo valores-limiar. Sempre que o valor da condição for superior ao limiar selecionado para a ligação de pares, é gerado um evento de saúde. 
5. Selecione **Adicionar Agentes** para escolher os agentes de monitorização que pretende utilizar para monitorizar esta ligação de observação. Certifique-se de que adiciona agentes em ambas as extremidades da ligação. Precisa de pelo menos um agente na rede virtual ligado a este olhar. Também precisa de pelo menos um agente no local ligado a este espreitar. 
6. **Selecione Guardar** para guardar a configuração. 

   ![Configuração de monitorização ExpressRoute](media/network-performance-monitor-expressroute/expressroute-configure-discovery.png)


Depois de ativar as regras e selecionar valores e agentes, aguarde 30 a 60 minutos para que os valores povoem e os azulejos **de monitorização ExpressRoute** apareçam. Quando vê os azulejos de monitorização, os circuitos ExpressRoute e os recursos de ligação são agora monitorizados pelo Network Performance Monitor. 

>[!NOTE]
> Esta capacidade funciona de forma fiável em espaços de trabalho que melhoraram para a nova linguagem de consulta.

## <a name="walkthrough"></a>Instruções 

O painel de controlo de desempenho da rede mostra uma visão geral da saúde dos circuitos ExpressRoute e das ligações de observação. 

![Painel de monitorização de desempenho da rede](media/network-performance-monitor-expressroute/npm-dashboard-expressroute.png) 

### <a name="circuits-list"></a>Lista de circuitos 

Para ver uma lista de todos os circuitos ExpressRoute monitorizados, selecione o azulejo dos circuitos ExpressRoute. Pode selecionar um circuito e ver o seu estado de saúde, gráficos de tendências para perda de pacotes, utilização da largura de banda e latência. Os gráficos são interativos. Pode selecionar uma janela de tempo personalizada para traçar as tabelas. Arraste o rato sobre uma área na tabela para ampliar e ver pontos de dados de grãos finos. 

![Lista de circuitos ExpressRoute](media/network-performance-monitor-expressroute/expressroute-circuits.png) 

### <a name="trends-of-loss-latency-and-throughput"></a>Tendências de perda, latência e produção 

A utilização da largura de banda, a latência e os gráficos de perdas são interativos. Pode aproximar-se de qualquer secção destes gráficos utilizando controlos de rato. Também pode ver a largura de banda, latência e dados de perda para outros intervalos. No canto superior esquerdo sob o botão **Ações,** selecione  **Data/Hora**. 

![ExpressRoute latência](media/network-performance-monitor-expressroute/expressroute-latency.png) 

### <a name="peerings-list"></a>Lista de pares 

Para apresentar uma lista de todas as ligações a redes virtuais através de um espreitamento privado, selecione o azulejo **Private Peerings** no painel de instrumentos. Aqui, pode selecionar uma ligação de rede virtual e ver o seu estado de saúde, gráficos de tendências para perda de pacotes, utilização de largura de banda e latência. 

![Peerings ExpressRoute](media/network-performance-monitor-expressroute/expressroute-peerings.png) 

### <a name="circuit-topology"></a>Topologia do circuito 

Para visualizar a topologia do circuito, selecione o azulejo **topologia.** Esta ação leva-o à vista topologia do circuito ou do persiório selecionado. O diagrama de topologia fornece a latência para cada segmento da rede, e cada bloco de 3 lúpulo é representado por um nó do diagrama. A seleção de um salto revela mais detalhes sobre o lúpulo. Para aumentar o nível de visibilidade para incluir lúpulo no local, mova a barra de slider sob **filtros**. Mover a barra de slider para a esquerda ou direita aumenta ou diminui o número de lúpulo no gráfico de topologia. A latência em cada segmento é visível, o que permite um isolamento mais rápido de segmentos de alta latência na sua rede.

![Topologia ExpressRoute](media/network-performance-monitor-expressroute/expressroute-topology.png)

### <a name="detailed-topology-view-of-a-circuit"></a>Vista detalhada da topologia de um circuito 

Esta visualização mostra ligações de rede virtuais. 

![Conexões de rede virtual ExpressRoute](media/network-performance-monitor-expressroute/expressroute-vnet.png)
 
## <a name="diagnostics"></a>Diagnóstico 

O Monitor de Desempenho da Rede ajuda-o a diagnosticar vários problemas de conectividade do circuito. Algumas das questões que pode ver estão listadas abaixo.

Pode ver os códigos de notificação e definir alertas sobre os mesmos através **do LogAnalytics**. Na página **de Diagnóstico SNPM,** pode ver descrições de cada mensagem de diagnóstico desencadeada.

| Código de Notificação (Registos) | Description |
| --- | --- |
| 5501 | Incapaz de atravessar através da ligação secundária do circuito ExpressRoute |
| 5502 | Incapaz de atravessar através da ligação primária do circuito ExpressRoute |
| 5503 | Não se encontra nenhum circuito para subscrição ligada ao espaço de trabalho | 
| 5508 | Não é possível determinar se o tráfego está a passar por qualquer circuito(s) para o caminho |
| 5510 | O trânsito não está a passar pelo circuito pretendido. | 
| 5511 | O tráfego não está a passar pela rede virtual pretendida | 

**O circuito está em baixo.** O Monitor de Desempenho da Rede notifica-o assim que a conectividade entre os recursos no local e as redes virtuais Azure é perdida. Esta notificação ajuda-o a tomar medidas proactivas antes de receber escaladas de utilizador e reduzir o tempo de inatividade.

![O circuito ExpressRoute está em baixo](media/network-performance-monitor-expressroute/expressroute-circuit-down.png)
 

**O tráfego não flui através do circuito pretendido.** O Monitor de Desempenho da Rede notifica-o sempre que o tráfego não fluir pelo circuito ExpressRoute pretendido. Este problema pode acontecer se o circuito estiver em baixo e o tráfego estiver a fluir pela rota de reserva. Também pode acontecer se houver um problema de encaminhamento. Estas informações ajudam-no a gerir proativamente quaisquer problemas de configuração nas suas políticas de encaminhamento e a certificar-se de que a rota mais ideal e segura é utilizada. 

 

**O tráfego não flui através do circuito primário.** O Monitor de Desempenho da Rede notifica-o quando o tráfego está a fluir através do circuito ExpressRoute secundário. Mesmo que não experimente problemas de conectividade neste caso, resolver proativamente os problemas com o circuito primário torna-o mais bem preparado. 

 
![Fluxo de tráfego ExpressRoute](media/network-performance-monitor-expressroute/expressroute-traffic-flow.png)


**Degradação devido à utilização máxima.** Pode correlacionar a tendência de utilização da largura de banda com a tendência de latência para identificar se a degradação da carga de trabalho do Azure se deve a um pico de utilização da largura de banda ou não. Então pode agir em conformidade.

![Utilização da largura de banda ExpressRoute](media/network-performance-monitor-expressroute/expressroute-peak-utilization.png)

 

## <a name="next-steps"></a>Passos seguintes
[Faça uma pesquisa de registos](../logs/log-query-overview.md) para visualizar registos de dados de desempenho detalhados da rede.