---
title: Como ver dependências de aplicações com o Azure Monitor para VMs (pré-visualização) | Documentos da Microsoft
description: O Map é uma funcionalidade do Azure Monitor para as VMs. Automaticamente Deteta componentes da aplicação em sistemas Windows e Linux e mapeia a comunicação entre serviços. Este artigo fornece detalhes sobre como utilizar a funcionalidade de mapa em vários cenários.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/09/2019
ms.author: magoedte
ms.openlocfilehash: f6273e9b6c7ed0c4685479976343497f01201b0b
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67206764"
---
# <a name="use-the-map-feature-of-azure-monitor-for-vms-preview-to-understand-application-components"></a>Utilizar a funcionalidade de mapa do Azure Monitor para VMs (pré-visualização) para compreender os componentes da aplicação
No Azure Monitor para as VMs, pode ver os componentes da aplicação detetada em Windows e Linux máquinas de virtuais (VMs) que são executadas no Azure ou em seu ambiente. Pode observar que as VMs de duas formas. Ver um mapa diretamente a partir de uma VM ou ver um mapa do Azure Monitor para ver os componentes em grupos de VMs. Este artigo ajuda-o a compreender esses dois métodos de visualização e como utilizar a funcionalidade de mapa. 

Para obter informações sobre como configurar o Azure Monitor para VMs, veja [ativar o Azure Monitor para VMs](vminsights-enable-overview.md).

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure
Inicie sessão no [portal do Azure](https://portal.azure.com).

## <a name="introduction-to-the-map-experience"></a>Introdução à experiência do mapa
Antes de explorar a experiência de mapa, deve compreender como ela apresenta e visualiza informações. Se selecionar a funcionalidade de mapa diretamente a partir de uma VM ou do Azure Monitor, a funcionalidade de mapa apresenta uma experiência consistente. A única diferença é que, do Azure Monitor, um mapa mostra todos os membros de um aplicativo de várias camadas ou um cluster.

A funcionalidade de mapa visualiza as dependências VM ao detetar os processos em execução com: 

- Ligações de rede ativas entre servidores.
- Latência de ligação de entrada e saída.
- Portas em qualquer arquitetura ligado a TCP através de um intervalo de tempo especificado.  
 
Expanda uma VM para mostrar apenas esses processos que comunicam com a VM e os detalhes do processo. O grupo de cliente mostra a contagem de clientes de front-end que se ligam na VM. Os grupos de porta do servidor mostram a contagem de servidores de back-end, que a VM se conecta ao. Expanda um grupo de porta do servidor para ver a lista detalhada de servidores que ligam através dessa porta.  

Quando seleciona a VM, o **propriedades** painel à direita mostra propriedades da VM. As propriedades incluem informações de sistema comunicadas pelo sistema operativo, as propriedades da VM do Azure e um gráfico de anel que resume as ligações detetadas. 

![O painel de propriedades](./media/vminsights-maps/properties-pane-01.png)

No lado direito do painel, selecione **registo de eventos** para mostrar uma lista de dados que a VM tem enviados para o Azure Monitor. Estes dados estão disponíveis para consulta.  Selecione qualquer tipo de registo para abrir o **registos** página, onde verá os resultados para esse tipo de registo. Verá também uma consulta pré-configurada que é filtrada em relação a VM.  

![O painel de registo de eventos](./media/vminsights-maps/properties-pane-logs-01.png)

Fechar o **Logs** página e voltar para o **propriedades** painel. ALi, marque **alertas** para ver alertas de estado de funcionamento-os critérios VM. A funcionalidade de mapa integra-se com alertas do Azure para mostrar alertas para o servidor selecionado no intervalo de tempo selecionado. O servidor apresenta um ícone para alertas atuais e o **máquina alertas** painel lista os alertas. 

![O painel de alertas](./media/vminsights-maps/properties-pane-alerts-01.png)

Para tornar a funcionalidade de mapa apresentar os alertas relevantes, crie uma regra de alerta que se aplica a um computador específico:

- Incluir uma cláusula para alertas de grupo por computador (por exemplo, **por minuto do intervalo de computador**).
- O alerta de base na métrica.

Para obter mais informações sobre os alertas do Azure e criar regras de alerta, consulte [Unified alertas no Azure Monitor](../../azure-monitor/platform/alerts-overview.md).

No canto superior direito, o **legenda** opção descreve as funções e os símbolos no mapa. Para uma análise detalhada no seu mapa e movê-lo em torno, utilize os controlos de zoom no canto inferior direito. Pode definir o nível de zoom e ajustar o mapa para o tamanho da página.  

## <a name="connection-metrics"></a>Métricas de ligação
O **ligações** painel apresenta métricas padrão para a ligação selecionada da VM através da porta TCP. As métricas incluem tempo de resposta, pedidos por minuto, débito de tráfego e links.  

![Gráficos de conectividade de rede no painel de ligações](./media/vminsights-maps/map-group-network-conn-pane-01.png)  

### <a name="failed-connections"></a>Ligações falhadas
O mapa mostra ligações falhadas para processos e computadores. Uma linha tracejada vermelha indica que um sistema de cliente está a conseguir chegar a um processo ou a porta. Para sistemas que utilizam o agente de dependência, o agente de relatórios de tentativas de falha de ligação. A funcionalidade de mapa monitoriza um processo observando Soquetes TCP que não obedeçam a estabelecer uma ligação. Esta falha pode resultar de uma firewall, uma configuração incorreta no cliente ou servidor ou um serviço remoto indisponível.

![Falha de ligação no mapa](./media/vminsights-maps/map-group-failed-connection-01.png)

Noções sobre ligações falhadas podem ajudá-lo a resolver problemas, validar a migração, analisar a segurança e compreender a arquitetura geral do serviço. Ligações falhadas, às vezes, são inofensivos, mas, muitas vezes, apontam para um problema. Ligações podem falhar, por exemplo, quando um ambiente de ativação pós-falha, de repente, fique inacessível ou quando duas camadas da aplicação não consegue comunicar entre si após a migração para a cloud.

### <a name="client-groups"></a>Grupos de clientes
No mapa, grupos de clientes representam máquinas de cliente que ligar à máquina mapeada. Um único grupo de clientes representa os clientes para uma máquina ou processo individual.

![Um grupo de clientes no mapa](./media/vminsights-maps/map-group-client-groups-01.png)

Para ver os endereços IP dos sistemas num grupo de clientes e clientes monitorizados, selecione o grupo. O conteúdo do grupo serão apresentados abaixo.  

![Lista de um grupo de clientes de endereços IP no mapa](./media/vminsights-maps/map-group-client-group-iplist-01.png)

Se o grupo inclui clientes monitorizados e não monitorizados, pode selecionar a seção apropriada de gráfico de anel do grupo para filtrar os clientes.

### <a name="server-port-groups"></a>Grupos de portas de servidor
Grupos de portas de servidor representam portas nos servidores que têm ligações a partir da máquina mapeada de entrada. O grupo contém a porta do servidor e uma contagem do número de servidores com ligações para essa porta. Selecione o grupo para ver as ligações e os servidores individuais. 

![Um grupo de porta do servidor no mapa](./media/vminsights-maps/map-group-server-port-groups-01.png)  

Se o grupo inclui servidores monitorizados e não monitorizados, pode selecionar a seção apropriada de gráfico de anel do grupo para filtrar os servidores.

## <a name="view-a-map-from-a-vm"></a>Ver um mapa de uma VM 

Para aceder ao Azure Monitor para as VMs diretamente a partir de uma VM:

1. No portal do Azure, selecione **máquinas virtuais**. 
2. Na lista, escolha uma VM. Na **monitorização** secção, escolha **Insights (pré-visualização)** .  
3. Selecione o **mapa** separador.

O mapa visualiza dependências da VM ao detetar que executam o processo de grupos e processos que têm ligações de rede do Active Directory através de um intervalo de tempo especificado.  

Por predefinição, o mapa mostra os últimos 30 minutos. Se quiser ver como dependências viu no passado, pode consultar para intervalos de tempo do histórico de até uma hora. Para executar a consulta, utilize o **TimeRange** Seletor no canto superior esquerdo. Pode executar uma consulta, por exemplo, durante um incidente ou para ver o estado antes de uma alteração.  

![Descrição geral direta do mapa de VM](./media/vminsights-maps/map-direct-vm-01.png)

## <a name="view-a-map-from-a-virtual-machine-scale-set"></a>Ver um mapa de um conjunto de dimensionamento de máquina virtual

Para aceder ao Azure Monitor para as VMs diretamente a partir de um conjunto de dimensionamento de máquinas virtuais:

1. No portal do Azure, selecione **conjuntos de dimensionamento de máquinas virtuais**.
2. Na lista, escolha uma VM. Em seguida, no **monitorização** secção, escolha **Insights (pré-visualização)** .  
3. Selecione o **mapa** separador.

O mapa visualiza todas as instâncias no conjunto como um nó de grupo, juntamente com as dependências do grupo de dimensionamento. O nó expandido lista as instâncias no conjunto de dimensionamento. Pode rolar por estas instâncias 10 cada vez. 

Para carregar um mapa de uma instância específica, selecione primeiro essa instância no mapa. Em seguida, selecione o **reticências** botão (...) à direita e selecione **carregar mapa do servidor**. No mapa que aparece, verá os grupos de processo e os processos que têm ligações de rede do Active Directory através de um intervalo de tempo especificado. 

Por predefinição, o mapa mostra os últimos 30 minutos. Se quiser ver como dependências viu no passado, pode consultar para intervalos de tempo do histórico de até uma hora. Para executar a consulta, utilize o **TimeRange** Seletor. Pode executar uma consulta, por exemplo, durante um incidente ou para ver o estado antes de uma alteração.

![Descrição geral direta do mapa de VM](./media/vminsights-maps/map-direct-vmss-01.png)

>[!NOTE]
>Também pode acessar um mapa de uma instância específica do **instâncias** vista para o conjunto de dimensionamento de máquina virtual. Na **definições** secção, aceda à **instâncias** > **Insights (pré-visualização)** .

## <a name="view-a-map-from-azure-monitor"></a>Ver um mapa do Azure Monitor
No Azure Monitor, a funcionalidade de mapa fornece uma vista global das suas VMs e as respetivas dependências. Para acessar a funcionalidade de mapa no Azure Monitor:

1. No portal do Azure, selecione **Monitor**. 
2. Na **Insights** secção, escolha **máquinas virtuais (pré-visualização)** .
3. Selecione o **mapa** separador.

   ![Mapa de descrição geral do Monitor do Azure de várias VMs](./media/vminsights-maps/map-multivm-azure-monitor-01.png)

Escolha uma área de trabalho utilizando o **área de trabalho** Seletor na parte superior da página. Se tiver mais do que uma área de trabalho do Log Analytics, escolha a área de trabalho que é habilitada com a solução e que tem VMs que reportam ao mesmo. 

O **grupo** Seletor de subscrições, grupos de recursos, de devolve [grupos de computadores](../../azure-monitor/platform/computer-groups.md)e conjuntos de dimensionamento de máquinas virtuais de computadores que estão relacionadas com a área de trabalho selecionada. A seleção aplica-se apenas à funcionalidade do mapa e não passa para o desempenho ou o estado de funcionamento.

Por predefinição, o mapa mostra os últimos 30 minutos. Se quiser ver como dependências viu no passado, pode consultar para intervalos de tempo do histórico de até uma hora. Para executar a consulta, utilize o **TimeRange** Seletor. Pode executar uma consulta, por exemplo, durante um incidente ou para ver o estado antes de uma alteração.  

## <a name="next-steps"></a>Passos Seguintes
- Para saber como utilizar a funcionalidade de estado de funcionamento, veja [estado de funcionamento da VM do Azure de modo de exibição](vminsights-health.md). 
- Para identificar afunilamentos, verificar o desempenho e compreender a utilização geral das suas VMs, veja [ver o estado de desempenho para o Azure Monitor para VMs](vminsights-performance.md). 
