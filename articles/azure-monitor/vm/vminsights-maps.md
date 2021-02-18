---
title: Ver dependências de aplicativos com monitor Azure para VMs
description: O mapa é uma característica do Azure Monitor para VMs. Descobre automaticamente componentes de aplicações nos sistemas Windows e Linux e mapeia a comunicação entre serviços. Este artigo fornece detalhes sobre como usar a funcionalidade Mapa em vários cenários.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/20/2020
ms.openlocfilehash: ea11a2dbff9c05400f24ecfa86c66395032b8ac9
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100619751"
---
# <a name="use-the-map-feature-of-azure-monitor-for-vms-to-understand-application-components"></a>Utilize a funcionalidade Mapa do Monitor Azure para VMs para entender os componentes da aplicação
No Azure Monitor para VMs, pode ver os componentes de aplicações descobertos nas máquinas virtuais Windows e Linux (VMs) que funcionam no Azure ou no seu ambiente. Pode observar os VMs de duas maneiras. Veja um mapa diretamente de um VM ou veja um mapa do Azure Monitor para ver os componentes em grupos de VMs. Este artigo irá ajudá-lo a entender estes dois métodos de visualização e como usar a funcionalidade Mapa. 

Para obter informações sobre a configuração do Monitor Azure para VMs, consulte [Enable Azure Monitor para VMs](../insights/vminsights-enable-overview.md).

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure
Inicie sessão no [portal do Azure](https://portal.azure.com).

## <a name="introduction-to-the-map-experience"></a>Introdução à experiência do Mapa
Antes de mergulhar na experiência do Mapa, deve entender como apresenta e visualiza a informação. Quer selecione a funcionalidade Mapa diretamente de um VM ou do Azure Monitor, a funcionalidade Mapa apresenta uma experiência consistente. A única diferença é que, a partir do Azure Monitor, um mapa mostra todos os membros de uma aplicação ou cluster de vários níveis.

A funcionalidade Mapa visualiza as dependências de VM descobrindo processos de execução que têm: 

- Ligações de rede ativas entre servidores.
- Latência de ligação de entrada e saída.
- Portas em qualquer arquitetura ligada à TCP sobre um intervalo de tempo especificado.  
 
Expandir um VM para mostrar detalhes do processo e apenas os processos que comunicam com o VM. O grupo de clientes mostra a contagem de clientes frontais que se ligam ao VM. Os grupos de porta-servidores mostram a contagem de servidores de back-end a que o VM se conecta. Expanda um grupo de porta-servidor para ver a lista detalhada de servidores que se conectam sobre essa porta.  

Ao selecionar o VM, o painel **de propriedades** à direita mostra as propriedades do VM. As propriedades incluem informações do sistema relatadas pelo sistema operativo, propriedades do Azure VM, e um gráfico de donuts que resume as ligações descobertas. 

![O painel de propriedades](./media/vminsights-maps/properties-pane-01.png)

No lado direito do painel, selecione **Log Events** para mostrar uma lista de dados que o VM enviou para o Azure Monitor. Estes dados estão disponíveis para consulta.  Selecione qualquer tipo de registo para abrir a página **'Logs',** onde vê os resultados desse tipo de registo. Também se vê uma consulta pré-configurada que é filtrada contra o VM.  

![O painel de eventos de log](./media/vminsights-maps/properties-pane-logs-01.png)

Feche a página **'Logs'** e volte ao painel **'Propriedades'.** Lá, selecione **Alertas** para ver alertas de critérios de saúde VM. A funcionalidade Mapa integra-se com alertas Azure para mostrar alertas para o servidor selecionado no intervalo de tempo selecionado. O servidor apresenta um ícone para alertas atuais e o painel **de alertas** de máquina lista os alertas. 

![O painel de alertas](./media/vminsights-maps/properties-pane-alerts-01.png)

Para tornar a funcionalidade do Mapa apresentar alertas relevantes, crie uma regra de alerta que se aplique a um computador específico:

- Incluir uma cláusula para agrupar alertas por computador (por exemplo, **por intervalo de computador 1 minuto).**
- Baseie o alerta numa métrica.

Para obter mais informações sobre alertas Azure e criar regras de alerta, consulte [alertas unificados no Azure Monitor](../platform/alerts-overview.md).

No canto superior direito, a opção **Legend** descreve os símbolos e papéis no mapa. Para olhar mais de perto o seu mapa e movê-lo ao redor, use os controlos de zoom no canto inferior direito. Pode definir o nível de zoom e encaixar o mapa no tamanho da página.  

## <a name="connection-metrics"></a>Métricas de ligação
O **painel de ligações** apresenta métricas padrão para a ligação selecionada a partir do VM sobre a porta TCP. As métricas incluem tempo de resposta, pedidos por minuto, saída de tráfego e ligações.  

![Gráficos de conectividade de rede no painel de conexões](./media/vminsights-maps/map-group-network-conn-pane-01.png)  

### <a name="failed-connections"></a>Ligações falhadas
O mapa mostra ligações falhadas para processos e computadores. Uma linha vermelha tracejada indica que um sistema de clientes não está a conseguir chegar a um processo ou porta. Para os sistemas que utilizam o agente Desadependido, o agente reporta tentativas falhadas de ligação. A funcionalidade Mapa monitoriza um processo observando tomadas TCP que não estabelecem uma ligação. Esta falha pode resultar de uma firewall, de uma configuração errada no cliente ou servidor ou de um serviço remoto indisponível.

![Uma ligação falhada no mapa](./media/vminsights-maps/map-group-failed-connection-01.png)

Compreender as ligações falhadas pode ajudá-lo a resolver problemas, validar a migração, analisar a segurança e compreender a arquitetura geral do serviço. As ligações falhadas são, por vezes, inofensivas, mas muitas vezes apontam para um problema. As ligações podem falhar, por exemplo, quando um ambiente de falha repentinamente se torna inacessível ou quando dois níveis de aplicação não conseguem comunicar entre si após uma migração em nuvem.

### <a name="client-groups"></a>Grupos de clientes
No mapa, os grupos de clientes representam máquinas de clientes que se ligam à máquina mapeada. Um único grupo de clientes representa os clientes para um processo ou máquina individual.

![Um grupo de clientes no mapa](./media/vminsights-maps/map-group-client-groups-01.png)

Para ver os clientes monitorizados e endereços IP dos sistemas num grupo de clientes, selecione o grupo. O conteúdo do grupo aparece abaixo.  

![Lista de endereços IP de um grupo de clientes no mapa](./media/vminsights-maps/map-group-client-group-iplist-01.png)

Se o grupo incluir clientes monitorizados e não monitorizados, pode selecionar a secção apropriada da tabela de donuts do grupo para filtrar os clientes.

### <a name="server-port-groups"></a>Grupos de porta-servidor
Os grupos de porta-servidores representam portas em servidores que têm ligações de entrada a partir da máquina mapeada. O grupo contém a porta do servidor e uma contagem do número de servidores que têm ligações a essa porta. Selecione o grupo para ver os servidores e ligações individuais. 

![Um grupo de porta de servidor no mapa](./media/vminsights-maps/map-group-server-port-groups-01.png)  

Se o grupo incluir servidores monitorizados e não monitorizados, pode selecionar a secção apropriada da tabela de donuts do grupo para filtrar os servidores.

## <a name="view-a-map-from-a-vm"></a>Ver um mapa de um VM 

Para aceder ao Azure Monitor para VMs diretamente a partir de um VM:

1. No portal Azure, selecione **Máquinas Virtuais.** 
2. Da lista, escolha um VM. Na secção **de Monitorização,** escolha **Insights**.  
3. Selecione o separador **Mapa.**

O mapa visualiza as dependências do VM através da descoberta de grupos de processos de execução e processos que têm ligações de rede ativas ao longo de um intervalo de tempo especificado.  

Por predefinição, o mapa mostra os últimos 30 minutos. Se quiser ver como as dependências pareciam no passado, pode consultar intervalos históricos de tempo de até uma hora. Para executar a consulta, utilize o seletor **TimeRange** no canto superior esquerdo. Você pode fazer uma consulta, por exemplo, durante um incidente ou para ver o estado antes de uma mudança.  

![Screenshot do separador Mapa na secção de Insights de Monitorização do portal Azure mostrando um diagrama das dependências entre máquinas virtuais.](./media/vminsights-maps/map-direct-vm-01.png)

## <a name="view-a-map-from-a-virtual-machine-scale-set"></a>Ver um mapa de um conjunto de escala de máquina virtual

Para aceder ao Azure Monitor para VMs diretamente a partir de um conjunto de escala de máquina virtual:

1. No portal Azure, selecione **conjuntos de escala de máquina virtual**.
2. Da lista, escolha um VM. Em seguida, na secção **de Monitorização,** escolha **Insights**.  
3. Selecione o separador **Mapa.**

O mapa visualiza todas as instâncias na escala definida como um nó de grupo juntamente com as dependências do grupo. O nó expandido lista as instâncias no conjunto de escala. Pode percorrer estes casos 10 de cada vez. 

Para carregar um mapa para uma instância específica, selecione primeiro esta instância no mapa. Em seguida, selecione o botão **elipse** (...) para a direita e escolha **o Mapa do Servidor de Carga**. No mapa que aparece, vê grupos de processos e processos que têm ligações de rede ativas ao longo de um intervalo de tempo especificado. 

Por predefinição, o mapa mostra os últimos 30 minutos. Se quiser ver como as dependências pareciam no passado, pode consultar intervalos históricos de tempo de até uma hora. Para executar a consulta, utilize o seletor **TimeRange.** Você pode fazer uma consulta, por exemplo, durante um incidente ou para ver o estado antes de uma mudança.

![Screenshot do separador Mapa na secção de Insights de Monitorização do portal Azure mostrando um diagrama de dependências entre conjuntos de escala de máquina virtual.](./media/vminsights-maps/map-direct-vmss-01.png)

>[!NOTE]
>Também pode aceder a um mapa para uma instância específica a partir da vista **Instâncias** para o seu conjunto de escala de máquina virtual. Na secção **Definições,** aceda a **Informações**  >  **sobre Casos**.

## <a name="view-a-map-from-azure-monitor"></a>Ver um mapa do Monitor Azure

No Azure Monitor, a funcionalidade Mapa proporciona uma visão global dos seus VMs e das suas dependências. Para aceder à funcionalidade Mapa no Monitor Azure:

1. No portal Azure, selecione **Monitor**. 
2. Na secção **Insights,** escolha **Máquinas Virtuais.**
3. Selecione o separador **Mapa.**

   ![Mapa geral do Monitor Azure de vários VMs](./media/vminsights-maps/map-multivm-azure-monitor-01.png)

Escolha um espaço de trabalho utilizando o seletor **workspace** no topo da página. Se tiver mais do que um espaço de trabalho log Analytics, escolha o espaço de trabalho que está habilitado com a solução e que tenha VMs a reportar-lhe. 

O seletor **do Grupo** devolve subscrições, grupos de recursos, [grupos de computadores](../platform/computer-groups.md)e conjuntos de escala de máquinas virtuais de computadores relacionados com o espaço de trabalho selecionado. A sua seleção aplica-se apenas à funcionalidade Mapa e não transporta para Performance ou Saúde.

Por predefinição, o mapa mostra os últimos 30 minutos. Se quiser ver como as dependências pareciam no passado, pode consultar intervalos históricos de tempo de até uma hora. Para executar a consulta, utilize o seletor **TimeRange.** Você pode fazer uma consulta, por exemplo, durante um incidente ou para ver o estado antes de uma mudança.  

## <a name="next-steps"></a>Passos seguintes

Para identificar estrangulamentos, verifique o desempenho e compreenda a utilização global dos seus VMs, consulte o estado de desempenho do [Azure Monitor para VMs](vminsights-performance.md). 

