---
title: Monitor de Desempenho de Rede solução no Azure Log Analytics | Microsoft Docs
description: Use o recurso de monitor do ExpressRoute no Monitor de Desempenho de Rede para monitorar a conectividade e o desempenho de ponta a ponta entre suas filiais e o Azure, por meio do Azure ExpressRoute.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: abshamsft
ms.author: absha
ms.date: 11/27/2018
ms.openlocfilehash: 5383402816eddba4c631c240585723b7c7119cef
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72898887"
---
# <a name="expressroute-monitor"></a>Monitor do ExpressRoute

Você pode usar o recurso de monitoramento do Azure ExpressRoute no [Monitor de desempenho de rede](network-performance-monitor.md) para monitorar a conectividade e o desempenho de ponta a ponta entre suas filiais e o Azure, por meio do Azure ExpressRoute. As principais vantagens são: 

- Detecção automática de circuitos do ExpressRoute associados à sua assinatura.
- Acompanhamento da utilização da largura de banda, perda e latência no circuito, emparelhamento e nível de rede virtual do Azure para o ExpressRoute.
- Descoberta da topologia de rede dos circuitos do ExpressRoute.

![Monitor do ExpressRoute](media/network-performance-monitor-expressroute/expressroute-intro.png)

## <a name="configuration"></a>Configuração 
Para abrir a configuração para Monitor de Desempenho de Rede, abra a [solução monitor de desempenho de rede](network-performance-monitor.md) e selecione **Configurar**.

### <a name="configure-network-security-group-rules"></a>Configurar regras do grupo de segurança de rede 
Para os servidores no Azure que são usados para monitoramento via Monitor de Desempenho de Rede, configure as regras do NSG (grupo de segurança de rede) para permitir o tráfego TCP na porta usada pelo Monitor de Desempenho de Rede para transações sintéticas. A porta padrão é 8084. Essa configuração permite que o agente de Log Analytics instalado em VMs do Azure se comunique com um agente de monitoramento local. 

Para obter mais informações sobre NSGs, consulte [grupos de segurança de rede](../../virtual-network/manage-network-security-group.md). 

>[!NOTE]
> Antes de continuar com esta etapa, instale o agente do servidor local e o agente do servidor do Azure e execute o script do PowerShell EnableRules. ps1. 

 
### <a name="discover-expressroute-peering-connections"></a>Descobrir conexões de emparelhamento do ExpressRoute 
 
1. Selecione a exibição **emparelhamentos do ExpressRoute** .
2. Selecione **descobrir agora** para descobrir todos os emparelhamentos privados do ExpressRoute que estão conectados às redes virtuais na assinatura do Azure vinculada a este espaço de trabalho do Azure log Analytics.

    >[!NOTE]
    > Atualmente, a solução descobre somente emparelhamentos privados do ExpressRoute. 

    >[!NOTE]
    > Somente emparelhamentos privados conectados às redes virtuais associadas à assinatura vinculada a esse espaço de trabalho de Log Analytics são descobertos. Se o ExpressRoute estiver conectado a redes virtuais fora da assinatura vinculada a este espaço de trabalho, crie um Log Analytics espaço de trabalho nessas assinaturas. Em seguida, use Monitor de Desempenho de Rede para monitorar esses emparelhamentos. 

    ![Configuração do monitor do ExpressRoute](media/network-performance-monitor-expressroute/expressroute-configure.png)
 
   Após a conclusão da descoberta, as conexões de emparelhamento privado descobertas são listadas em uma tabela. O monitoramento para esses emparelhamentos está inicialmente em um estado desabilitado. 

### <a name="enable-monitoring-of-the-expressroute-peering-connections"></a>Habilitar o monitoramento das conexões de emparelhamento do ExpressRoute 

1. Selecione a conexão de emparelhamento privado que você deseja monitorar.
2. No painel à direita, marque a caixa de seleção **monitorar este emparelhamento** . 
3. Se você pretende criar eventos de integridade para essa conexão, selecione **habilitar monitoramento de integridade para este emparelhamento**. 
4. Escolha as condições de monitoramento. Você pode definir limites personalizados para a geração de eventos de integridade inserindo valores de limite. Sempre que o valor da condição ficar acima do limite selecionado para a conexão de emparelhamento, um evento de integridade será gerado. 
5. Selecione **adicionar agentes** para escolher os agentes de monitoramento que você pretende usar para monitorar essa conexão de emparelhamento. Certifique-se de adicionar agentes em ambas as extremidades da conexão. Você precisa de pelo menos um agente na rede virtual conectada a esse emparelhamento. Você também precisa de pelo menos um agente local conectado a esse emparelhamento. 
6. Selecione **salvar** para salvar a configuração. 

   ![Configuração de monitoramento do ExpressRoute](media/network-performance-monitor-expressroute/expressroute-configure-discovery.png)


Depois de habilitar as regras e selecionar valores e agentes, aguarde de 30 a 60 minutos para que os valores sejam preenchidos e os blocos de **monitoramento do ExpressRoute** sejam exibidos. Quando você vir os blocos de monitoramento, seus circuitos de ExpressRoute e recursos de conexão agora são monitorados pelo Monitor de Desempenho de Rede. 

>[!NOTE]
> Esse recurso funciona de forma confiável em espaços de trabalho que foram atualizados para a nova linguagem de consulta.

## <a name="walkthrough"></a>Instruções 

O painel Monitor de Desempenho de Rede mostra uma visão geral da integridade de circuitos do ExpressRoute e conexões de emparelhamento. 

![Painel do Monitor de Desempenho de Rede](media/network-performance-monitor-expressroute/npm-dashboard-expressroute.png) 

### <a name="circuits-list"></a>Lista de circuitos 

Para ver uma lista de todos os circuitos do ExpressRoute monitorados, selecione o bloco de circuitos do ExpressRoute. Você pode selecionar um circuito e exibir seu estado de integridade, gráficos de tendência para perda de pacotes, utilização de largura de banda e latência. Os gráficos são interativos. Você pode selecionar uma janela de tempo personalizada para plotar os gráficos. Arraste o mouse sobre uma área no gráfico para ampliar e ver os pontos de dados refinados. 

![Lista de circuitos do ExpressRoute](media/network-performance-monitor-expressroute/expressroute-circuits.png) 

### <a name="trends-of-loss-latency-and-throughput"></a>Tendências de perda, latência e taxa de transferência 

Os gráficos de utilização de largura de banda, latência e perda são interativos. Você pode ampliar qualquer seção desses gráficos usando os controles do mouse. Você também pode ver os dados de largura de banda, latência e perda para outros intervalos. No canto superior esquerdo, no botão **ações** , selecione **data/hora**. 

![Latência de ExpressRoute](media/network-performance-monitor-expressroute/expressroute-latency.png) 

### <a name="peerings-list"></a>Lista de emparelhamentos 

Para exibir uma lista de todas as conexões com redes virtuais por emparelhamento privado, selecione o bloco **emparelhamentos privados** no painel. Aqui, você pode selecionar uma conexão de rede virtual e exibir seu estado de integridade, gráficos de tendência para perda de pacotes, utilização de largura de banda e latência. 

![Emparelhamentos do ExpressRoute](media/network-performance-monitor-expressroute/expressroute-peerings.png) 

### <a name="circuit-topology"></a>Topologia de circuito 

Para exibir a topologia do circuito, selecione o bloco **topologia** . Essa ação leva você para a exibição de topologia do circuito ou emparelhamento selecionado. O diagrama de topologia fornece a latência para cada segmento na rede e cada salto de camada 3 é representado por um nó do diagrama. A seleção de um salto revela mais detalhes sobre o salto. Para aumentar o nível de visibilidade para incluir saltos locais, mova a barra deslizante sob **filtros**. Mover a barra deslizante para a esquerda ou para a direita aumenta ou diminui o número de saltos no grafo de topologia. A latência em cada segmento está visível, o que permite um isolamento mais rápido de segmentos de alta latência em sua rede.

![Topologia do ExpressRoute](media/network-performance-monitor-expressroute/expressroute-topology.png)

### <a name="detailed-topology-view-of-a-circuit"></a>Exibição de topologia detalhada de um circuito 

Essa exibição mostra as conexões de rede virtual. 

![Conexões de rede virtual do ExpressRoute](media/network-performance-monitor-expressroute/expressroute-vnet.png)
 
## <a name="diagnostics"></a>Diagnósticos 

Monitor de Desempenho de Rede ajuda a diagnosticar vários problemas de conectividade de circuito. Alguns dos problemas que você pode ver estão listados abaixo.

Você pode ver os códigos de notificação e definir alertas neles por meio de **LogAnalytics**. Na página **diagnóstico do NPM** , você pode ver descrições para cada mensagem de diagnóstico disparada.

| Código de notificação (logs) | Descrição |
| --- | --- |
| 5501 | Não é possível percorrer a conexão secundária do circuito do ExpressRoute |
| 5502 | Não é possível percorrer a conexão primária do circuito do ExpressRoute |
| 5503 | Nenhum circuito foi encontrado para a assinatura vinculada ao espaço de trabalho | 
| 5508 | Não é possível determinar se o tráfego está passando por qualquer circuito para o caminho |
| 5510 | O tráfego não está passando pelo circuito pretendido | 
| 5511 | O tráfego não está passando pela rede virtual pretendida | 

**O circuito está inoperante.** O Monitor de Desempenho de Rede notifica assim que a conectividade entre seus recursos locais e as redes virtuais do Azure é perdida. Essa notificação ajuda você a tomar uma ação proativa antes de receber escalonamentos de usuários e reduzir o tempo de inatividade.

![O circuito do ExpressRoute está inoperante](media/network-performance-monitor-expressroute/expressroute-circuit-down.png)
 

**O tráfego não flui pelo circuito pretendido.** Monitor de Desempenho de Rede notifica você sempre que o tráfego não fluir pelo circuito de ExpressRoute pretendido. Esse problema pode ocorrer se o circuito estiver inoperante e o tráfego estiver fluindo pela rota de backup. Isso também pode ocorrer se houver um problema de roteamento. Essas informações ajudam a gerenciar proativamente quaisquer problemas de configuração em suas políticas de roteamento e garantir que a rota mais adequada e segura seja usada. 

 

**O tráfego não flui pelo circuito primário.** Monitor de Desempenho de Rede notifica quando o tráfego está fluindo pelo circuito de ExpressRoute secundário. Mesmo que você não enfrente nenhum problema de conectividade nesse caso, a solução de problemas de forma proativa com o circuito principal o torna melhor preparado. 

 
![Fluxo de tráfego de ExpressRoute](media/network-performance-monitor-expressroute/expressroute-traffic-flow.png)


**Degradação devido à utilização de pico.** Você pode correlacionar a tendência de utilização da largura de banda com a tendência de latência para identificar se a degradação da carga de trabalho do Azure é devido a um pico na utilização da largura de banda ou não. Em seguida, você pode agir de acordo.

![Utilização de largura de banda do ExpressRoute](media/network-performance-monitor-expressroute/expressroute-peak-utilization.png)

 

## <a name="next-steps"></a>Passos seguintes
[Pesquisar logs](../../azure-monitor/log-query/log-query-overview.md) para exibir registros de dados de desempenho de rede detalhados.
