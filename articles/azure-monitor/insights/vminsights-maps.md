---
title: Como ver dependências de aplicações com o Azure Monitor para VMs (pré-visualização) | Documentos da Microsoft
description: O Map é uma funcionalidade do Monitor para VMs do Azure que Deteta componentes da aplicação em sistemas Windows e Linux e mapeia a comunicação entre serviços automaticamente. Este artigo fornece detalhes sobre como usá-lo numa variedade de cenários.
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
ms.date: 10/25/2018
ms.author: magoedte
ms.openlocfilehash: 0bf7c335f552fee103118e57239bcd872e880edb
ms.sourcegitcommit: 1afd2e835dd507259cf7bb798b1b130adbb21840
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2019
ms.locfileid: "56984854"
---
# <a name="using-azure-monitor-for-vms-preview-map-to-understand-application-components"></a>Utilizar o Azure Monitor para VMs (pré-visualização) mapa para compreender os componentes da aplicação
Visualizar os componentes da aplicação detetada em máquinas de virtuais Windows e Linux em execução no seu ambiente pode ser observado de duas formas com o Azure Monitor para as VMs, de uma máquina virtual diretamente ou através de grupos de VMs do Azure Monitor do Azure. 

Este artigo ajuda-o a compreender a experiência de entre as duas perspetivas e como utilizar a funcionalidade de mapa. Para obter informações sobre como configurar o Azure Monitor para VMs, veja [ativar o Azure Monitor para VMs](vminsights-onboard.md).

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure
Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="introduction-to-map-experience"></a>Introdução à experiência de mapa
Antes de explorar a visualização de mapa de uma única máquina virtual ou o grupo de VMs, é importante, fornecemos uma breve introdução à funcionalidade para que entender como as informações são apresentadas e o que representam as visualizações.  

Se selecionar a funcionalidade de mapa diretamente a partir de uma VM ou do Azure Monitor, ela apresenta uma experiência consistente.  A única diferença é do Azure Monitor, que pode ver todos os membros de um cluster num mapa ou a aplicação de várias camadas.

Mapas visualiza as dependências de VMs ao detetar os processos em execução com ligações de rede ativa entre servidores, a latência de ligação de entrada e saída e as portas em qualquer arquitetura ligado a TCP através de um intervalo de tempo especificado.  Expandir uma VM mostra detalhes do processo e apenas esses processos que comunicam com a VM são apresentados. A contagem de clientes de front-end que se ligam à máquina virtual é indicada com o grupo de cliente. A contagem de servidores back-end a VM se conecta ao são indicados em grupos de portas de servidor. Expanda um grupo de porta do servidor para ver a lista detalhada dos servidores ligados através dessa porta.  

Quando clica na máquina virtual, o **propriedades** painel é expandido no lado direito para mostrar as propriedades do item selecionado, tais como informações de sistema comunicadas pelo sistema operativo, as propriedades da VM do Azure e um anel resumir as ligações detetadas. 

![Propriedades do sistema do computador](./media/vminsights-maps/properties-pane-01.png)

No lado direito do painel, clique nas **registo de eventos** ícone para mudar o foco do painel para mostrar uma lista das tabelas de dados recolhidos da VM enviou para o Azure Monitor e está disponível para consulta.  Clicar em qualquer um dos tipos de registos listados abre o **registos** filtrado de página para ver os resultados para esse tipo com uma consulta previamente configurada em relação a máquina virtual específica.  

![Lista de pesquisa de registo no painel de propriedades](./media/vminsights-maps/properties-pane-logs-01.png)

Fechar *Logs** e retornar para o **propriedades** painel e selecione **alertas** para ver alertas que o alerta geradas para a VM a partir de critérios de estado de funcionamento. Mapa integra-se com alertas do Azure para mostrar alertas acionados para o servidor selecionado no intervalo de tempo selecionado. O servidor apresenta um ícone se existem alertas atuais e o painel de alertas de máquina lista os alertas. 

![Alertas de computador no painel de propriedades](./media/vminsights-maps/properties-pane-alerts-01.png)

Para ativar a funcionalidade de mapa apresentar os alertas relevantes, crie uma regra de alerta que é acionado para um computador específico. Para criar alertas adequadas:
- Incluir uma cláusula ao grupo por computador (por exemplo, **por minuto do intervalo de computador**).
- Escolha um alerta com base na medida da métrica.

Para obter mais informações sobre os alertas do Azure e criar regras de alerta, consulte [alertas de unificação de mensagens em fila no Azure Monitor](../../azure-monitor/platform/alerts-overview.md)

O **legenda** opção no canto superior direito descreve as funções e símbolos num mapa.  Para aumentar o zoom para uma análise detalhada das seu mapa e mover o it em torno, os controles de Zoom na parte inferior direita da página define o nível de zoom e ajuste automático na página para o tamanho da página atual.  

## <a name="connection-metrics"></a>Métricas de ligação
O **ligações** painel apresenta métricas de conectividade padrão para a ligação selecionada da VM através da porta TCP. As métricas incluem tempo de resposta, pedidos por minuto, débito de tráfego e links.  

![Exemplo de painel de gráficos de conectividade de rede](./media/vminsights-maps/map-group-network-conn-pane-01.png)  

### <a name="failed-connections"></a>Ligações com falhas
Ligações falhadas são apresentadas no mapa para processos e computadores, com uma linha vermelha tracejada que indica que é um sistema de cliente que está a conseguir chegar a um processo ou a porta. Ligações falhadas forem comunicadas a partir de qualquer sistema com o agente de dependência se esse sistema for a tentar a falha de ligação. Mapa mede este processo ao observar os sockets TCP que não obedeçam a estabelecer uma ligação. Esta falha pode resultar de uma firewall, uma configuração incorreta no cliente ou servidor ou um serviço remoto indisponível.

![Exemplo de falha de ligação no mapa](./media/vminsights-maps/map-group-failed-connection-01.png)

Noções sobre ligações falhadas podem ajudar na resolução de problemas, a validação da migração, a análise de segurança e compreender a arquitetura geral do serviço. Ligações falhadas, às vezes, são inofensivos, mas, muitas vezes, apontam diretamente para um problema, como um ambiente de ativação pós-falha, de repente, tornando-se inacessível ou duas camadas da aplicação que está a ser não é possível comunicar entre si após a migração para a cloud.

### <a name="client-groups"></a>Grupos de Clientes
Grupos de clientes no mapa representam máquinas de cliente que tenham ligações à máquina mapeadas. Um único grupo de cliente representa os clientes para uma máquina ou processo individual.

![Exemplo de grupos de cliente no mapa](./media/vminsights-maps/map-group-client-groups-01.png)

Para ver os endereços IP dos sistemas num grupo de clientes e clientes monitorizados, selecione o grupo. O conteúdo do grupo está listado sob o grupo.  

![Exemplo de lista do endereço do cliente grupo IP no mapa](./media/vminsights-maps/map-group-client-group-iplist-01.png)

Se o grupo inclui clientes permaneciam não monitorizados e monitorados, pode selecionar a secção adequada do gráfico em anel no grupo para filtrar os clientes.

### <a name="server-port-groups"></a>Grupos de portas de servidor
Grupos de portas de servidor representam as portas de servidor nos servidores que têm ligações a partir da máquina mapeada de entrada. O grupo contém a porta do servidor e uma contagem do número de servidores com ligações para essa porta. Selecione o grupo para ver os servidores individuais e as conexões listadas. 

![Exemplo de grupo de porta do servidor no mapa](./media/vminsights-maps/map-group-server-port-groups-01.png)  

Se o grupo inclui servidores monitorizados e permaneciam não monitorizados, pode selecionar a secção adequada do gráfico em anel no grupo para filtrar os servidores.

## <a name="view-map-directly-from-a-virtual-machine"></a>Ver mapa diretamente a partir de uma máquina virtual 

Para aceder ao Azure Monitor para as VMs diretamente a partir de uma máquina virtual, faça o seguinte.

1. No portal do Azure, selecione **máquinas virtuais**. 
2. Na lista, escolha uma VM e, no **monitorização** , escolha **Insights (pré-visualização)**.  
3. Selecione o **mapa** separador.

Mapa visualiza as dependências de VMs, o que está a executar o processo grupos e os processos com ligações de rede ativa, através de um intervalo de tempo especificado.  Por predefinição, o mapa mostra os últimos 30 minutos.  Utilizar o **TimeRange** Seletor no canto superior esquerdo, pode consultar para intervalos de tempo do histórico de até uma hora para mostrar como dependências viu no passado (por exemplo, durante um incidente ou antes de ocorrer uma alteração).  

![Descrição geral direta do mapa de VM](./media/vminsights-maps/map-direct-vm-01.png)

## <a name="view-map-from-azure-monitor"></a>Vista de mapa do Monitor do Azure
Azure monitor, a funcionalidade de mapa fornece uma vista global das suas máquinas virtuais e as respetivas dependências.  Para acessar a funcionalidade de mapa do Azure Monitor, execute o seguinte. 

1. No portal do Azure, selecione **Monitor**. 
2. Escolher **máquinas virtuais (pré-visualização)** no **Insights** secção.
3. Selecione o **mapa** separador.

![Monitor com várias VMS mapa descrição geral do Azure](./media/vminsights-maps/map-multivm-azure-monitor-01.png)

Partir do **área de trabalho** Seletor na parte superior da página, se tiver mais do que uma área de trabalho do Log Analytics, selecione a área de trabalho que está ativada com a solução e tem máquinas virtuais que reportam ao mesmo. O **grupo** Seletor irá devolver subscrições, grupos de recursos [grupos de computadores](../../azure-monitor/platform/computer-groups.md)e conjuntos de dimensionamento VM de computadores relacionados com a área de trabalho selecionada. A seleção de apenas aplica-se para a funcionalidade de mapa e não vão ser transferidos para o desempenho ou de mapa.

Por predefinição, o mapa mostra os últimos 30 minutos. Utilizar o **TimeRange** Seletor, pode consultar para intervalos de tempo do histórico de até uma hora para mostrar como dependências viu no passado (por exemplo, durante um incidente ou antes de ocorrer uma alteração).   

## <a name="next-steps"></a>Passos Seguintes
Para saber como utilizar a funcionalidade de estado de funcionamento, veja [Ver estado de funcionamento do Azure VM](vminsights-health.md), ou para identificar afunilamentos e a utilização geral com o desempenho de VMs, veja [vista de Azure Monitor de desempenho de VMs](vminsights-performance.md). 
