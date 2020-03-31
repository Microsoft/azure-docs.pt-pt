---
title: Ver dependências de aplicativos com Monitor Azure para VMs
description: O mapa é uma característica do Monitor Azure para VMs. Descobre automaticamente componentes de aplicações nos sistemas Windows e Linux e mapeia a comunicação entre serviços. Este artigo fornece detalhes sobre como usar a funcionalidade Map em vários cenários.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/20/2020
ms.openlocfilehash: acb96984a49e4ad8535f87a41da11b3b63ae207b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80283859"
---
# <a name="use-the-map-feature-of-azure-monitor-for-vms-to-understand-application-components"></a>Utilize a função Map do Monitor Azure para VMs para entender os componentes da aplicação
No Azure Monitor para VMs, pode ver componentes de aplicações descobertos em máquinas virtuais Windows e Linux (VMs) que funcionam em Azure ou no seu ambiente. Pode observar os VMs de duas maneiras. Veja um mapa diretamente de um VM ou veja um mapa do Monitor Azure para ver os componentes em grupos de VMs. Este artigo irá ajudá-lo a entender estes dois métodos de visualização e como usar a funcionalidade Map. 

Para obter informações sobre a configuração do Monitor Azure para VMs, consulte [o Monitor Enable Azure para VMs](vminsights-enable-overview.md).

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure
Inicie sessão no [Portal do Azure](https://portal.azure.com).

## <a name="introduction-to-the-map-experience"></a>Introdução à experiência do Mapa
Antes de mergulhar na experiência do Mapa, deve entender como apresenta e visualiza a informação. Quer selecione a funcionalidade Map diretamente de um VM ou do Monitor Azure, a funcionalidade Map apresenta uma experiência consistente. A única diferença é que do Monitor Azure, um mapa mostra todos os membros de uma aplicação ou cluster de vários níveis.

A funcionalidade Map visualiza as dependências vM através da descoberta de processos de execução que têm: 

- Ligações de rede ativas entre servidores.
- Latência de ligação de entrada e saída.
- Portas em qualquer arquitetura ligada ao TCP em um intervalo de tempo especificado.  
 
Expandir um VM para mostrar detalhes do processo e apenas os processos que comunicam com o VM. O grupo de clientes mostra a contagem de clientes front-end que se ligam ao VM. Os grupos de porta-servidor mostram a contagem de servidores de back-end a que o VM se liga. Expanda um grupo de porta de servidor para ver a lista detalhada de servidores que se ligam sobre essa porta.  

Quando seleciona o VM, o painel **Properties** à direita mostra as propriedades do VM. As propriedades incluem informações do sistema relatadas pelo sistema operativo, propriedades do VM Azure, e um gráfico de donuts que resume as ligações descobertas. 

![O painel propriedades](./media/vminsights-maps/properties-pane-01.png)

No lado direito do painel, selecione **Log Events** para mostrar uma lista de dados que o VM enviou para o Monitor Azure. Estes dados estão disponíveis para consulta.  Selecione qualquer tipo de registo para abrir a página **Registos,** onde vê os resultados desse tipo de registo. Também vê uma consulta pré-configurada que é filtrada contra o VM.  

![O painel de Eventos de Log](./media/vminsights-maps/properties-pane-logs-01.png)

Feche a página **De Registos** e volte ao painel **Properties.** Lá, selecione **Alertas** para visualizar alertas de critérios de saúde VM. A funcionalidade Map integra-se com alertas Azure para mostrar alertas para o servidor selecionado na faixa hortemsa selecionada. O servidor apresenta um ícone para alertas atuais e o painel de alertas de **máquinas** lista os alertas. 

![O painel alertas](./media/vminsights-maps/properties-pane-alerts-01.png)

Para tornar a funcionalidade map os alertas relevantes, crie uma regra de alerta que se aplique a um computador específico:

- Incluir uma cláusula para agrupar alertas por computador (por exemplo, **por intervalo de computador 1 minuto).**
- Baseie o alerta numa métrica.

Para obter mais informações sobre alertas Azure e criar regras de alerta, consulte [alertas unificados no Monitor Azure](../../azure-monitor/platform/alerts-overview.md).

No canto superior direito, a opção **Legend** descreve os símbolos e papéis no mapa. Para ver mais de perto o seu mapa e movê-lo, use os controlos de zoom no canto inferior direito. Pode definir o nível de zoom e encaixar o mapa no tamanho da página.  

## <a name="connection-metrics"></a>Métricas de ligação
O painel de **ligações** apresenta métricas padrão para a ligação selecionada do VM sobre a porta TCP. As métricas incluem tempo de resposta, pedidos por minuto, entrada de tráfego e links.  

![Gráficos de conectividade de rede no painel de Ligações](./media/vminsights-maps/map-group-network-conn-pane-01.png)  

### <a name="failed-connections"></a>Ligações falhadas
O mapa mostra ligações falhadas para processos e computadores. Uma linha vermelha tracejada indica que um sistema de cliente está falhando em alcançar um processo ou porta. Para sistemas que utilizam o agente Dependency, o agente reporta sobre tentativas de ligação falhadas. A função Map monitoriza um processo observando tomadas TCP que não conseguem estabelecer uma ligação. Esta falha pode resultar de uma firewall, de uma configuração errada no cliente ou servidor, ou de um serviço remoto indisponível.

![Uma ligação falhada no mapa](./media/vminsights-maps/map-group-failed-connection-01.png)

Compreender ligações falhadas pode ajudá-lo a resolver problemas, validar a migração, analisar a segurança e entender a arquitetura geral do serviço. As ligações falhadas são, por vezes, inofensivas, mas muitas vezes apontam para um problema. As ligações podem falhar, por exemplo, quando um ambiente de failover de repente se torna inacessível ou quando dois níveis de aplicação não podem comunicar entre si após uma migração na nuvem.

### <a name="client-groups"></a>Grupos de clientes
No mapa, os grupos de clientes representam máquinas de clientes que se ligam à máquina mapeada. Um único grupo de clientes representa os clientes para um processo ou máquina individual.

![Um grupo de clientes no mapa](./media/vminsights-maps/map-group-client-groups-01.png)

Para ver os clientes monitorizados e endereços IP dos sistemas num grupo de clientes, selecione o grupo. O conteúdo do grupo aparece abaixo.  

![Uma lista de endereços IP de um grupo de clientes no mapa](./media/vminsights-maps/map-group-client-group-iplist-01.png)

Se o grupo incluir clientes monitorizados e não monitorizados, pode selecionar a secção adequada do gráfico de donuts do grupo para filtrar os clientes.

### <a name="server-port-groups"></a>Grupos de porta-servidor
Os grupos de porta-servidor representam portas em servidores que têm ligações de entrada a partir da máquina mapeada. O grupo contém a porta do servidor e uma contagem do número de servidores que têm ligações a essa porta. Selecione o grupo para ver os servidores e ligações individuais. 

![Um grupo de porta de servidor no mapa](./media/vminsights-maps/map-group-server-port-groups-01.png)  

Se o grupo incluir servidores monitorizados e não monitorizados, pode selecionar a secção adequada do gráfico de donuts do grupo para filtrar os servidores.

## <a name="view-a-map-from-a-vm"></a>Veja um mapa de um VM 

Para aceder ao Monitor Azure para VMs diretamente de um VM:

1. No portal Azure, selecione **Máquinas Virtuais**. 
2. Na lista, escolha um VM. Na secção **de Monitorização,** escolha **Insights**.  
3. Selecione o separador **Mapa.**

O mapa visualiza as dependências do VM através da descoberta de grupos e processos de processo sinuoso que têm ligações de rede ativas ao longo de um intervalo de tempo especificado.  

Por padrão, o mapa mostra os últimos 30 minutos. Se quiser ver como as dependências pareciam no passado, pode consultar para intervalos históricos de tempo de até uma hora. Para executar a consulta, utilize o seletor **TimeRange** no canto superior esquerdo. Você pode fazer uma consulta, por exemplo, durante um incidente ou ver o estado antes de uma mudança.  

![Visão geral do mapa de VM direto](./media/vminsights-maps/map-direct-vm-01.png)

## <a name="view-a-map-from-a-virtual-machine-scale-set"></a>Veja um mapa de um conjunto de escala de máquina virtual

Para aceder ao Monitor Azure para VMs diretamente de um conjunto de escala de máquina virtual:

1. No portal Azure, selecione **conjuntos**de escala de máquina virtual .
2. Na lista, escolha um VM. Em seguida, na secção **de Monitorização,** escolha **Insights**.  
3. Selecione o separador **Mapa.**

O mapa visualiza todas as instâncias na escala definida como um nó de grupo juntamente com as dependências do grupo. O nó expandido enumera as instâncias no conjunto de escala. Pode percorrer estas instâncias 10 de cada vez. 

Para carregar um mapa para uma instância específica, primeiro selecione essa instância no mapa. Em seguida, selecione o botão **de elipse** (...) à direita e escolha o Mapa do **Servidor de Carga**. No mapa que aparece, você vê grupos de processos e processos que têm conexões de rede ativas ao longo de um intervalo de tempo especificado. 

Por padrão, o mapa mostra os últimos 30 minutos. Se quiser ver como as dependências pareciam no passado, pode consultar para intervalos históricos de tempo de até uma hora. Para executar a consulta, utilize o seletor **TimeRange.** Você pode fazer uma consulta, por exemplo, durante um incidente ou ver o estado antes de uma mudança.

![Visão geral do mapa de VM direto](./media/vminsights-maps/map-direct-vmss-01.png)

>[!NOTE]
>Também pode aceder a um mapa para uma instância específica a partir da vista **Instances** para o seu conjunto de escala de máquina virtual. Na secção **Definições,** vá a**Insights de** **Instâncias** > .

## <a name="view-a-map-from-azure-monitor"></a>Veja um mapa do Monitor Azure

No Azure Monitor, a funcionalidade Map proporciona uma visão global dos seus VMs e das suas dependências. Para aceder à funcionalidade Map no Monitor Azure:

1. No portal Azure, selecione **Monitor**. 
2. Na secção **Insights,** escolha **Máquinas Virtuais**.
3. Selecione o separador **Mapa.**

   ![Mapa geral do Monitor Azure de vários VMs](./media/vminsights-maps/map-multivm-azure-monitor-01.png)

Escolha um espaço de trabalho utilizando o seletor **workspace** na parte superior da página. Se tiver mais de um espaço de trabalho log Analytics, escolha o espaço de trabalho que está habilitado com a solução e que tem VMs a reportar-lhe. 

O seletor **do Grupo** devolve subscrições, grupos de recursos, [grupos informáticos](../../azure-monitor/platform/computer-groups.md)e conjuntos de computadores de escala de máquinas virtuais que estão relacionados com o espaço de trabalho selecionado. A sua seleção aplica-se apenas à funcionalidade Map e não passa para performance ou saúde.

Por padrão, o mapa mostra os últimos 30 minutos. Se quiser ver como as dependências pareciam no passado, pode consultar para intervalos históricos de tempo de até uma hora. Para executar a consulta, utilize o seletor **TimeRange.** Você pode fazer uma consulta, por exemplo, durante um incidente ou ver o estado antes de uma mudança.  

## <a name="next-steps"></a>Passos seguintes

Para identificar estrangulamentos, verificar o desempenho e compreender a utilização geral dos seus VMs, consulte o [estado de desempenho do Monitor De visão para VMs](vminsights-performance.md). 
