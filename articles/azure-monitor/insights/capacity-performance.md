---
title: Solução de capacidade e desempenho no Azure Monitor | Documentos da Microsoft
description: Utilize a solução de capacidade e desempenho no Monitor para o ajudar a compreender a capacidade dos seus servidores de Hyper-V.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: 51617a6f-ffdd-4ed2-8b74-1257149ce3d4
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 07/13/2017
ms.author: magoedte
ms.openlocfilehash: db2bbd8c3c0e3d7d172ea2948a42f300c2875745
ms.sourcegitcommit: 1afd2e835dd507259cf7bb798b1b130adbb21840
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2019
ms.locfileid: "56983715"
---
# <a name="plan-hyper-v-virtual-machine-capacity-with-the-capacity-and-performance-solution-preview"></a>Planear a capacidade de máquina virtual de Hyper-V com a solução de capacidade e desempenho (pré-visualização)

![Símbolo de capacidade e desempenho](./media/capacity-performance/capacity-solution.png)

> [!NOTE]
> A solução de capacidade e desempenho foi preterida.  Os clientes que já tenham instalado a solução podem continuar a utilizá-lo, mas a capacidade e desempenho não podem ser adicionados a novas áreas de trabalho.

Pode utilizar a solução de capacidade e desempenho no Monitor para o ajudar a compreender a capacidade dos seus servidores de Hyper-V. A solução fornece informações sobre o seu ambiente de Hyper-V, mostrando-lhe a utilização geral (CPU, memória e disco) de anfitriões e as VMs em execução nesses anfitriões de Hyper-V. Métricas são recolhidas de CPU, memória e discos em todos os seus anfitriões e as VMs em execução nos mesmos.

A solução:

-   Mostra os anfitriões com maior e mais baixa utilização da CPU e memória
-   Mostra as VMs com maior e mais baixa utilização da CPU e memória
-   Mostra as VMs com a utilização de IOPS e débito mais elevada e mais baixa
-   Mostra que as VMs estão em execução em que anfitriões
-   Mostra os discos principais com alto débito e latência IOPS em volumes partilhados de cluster
- Permite-lhe personalizar e filtrar com base em grupos

> [!NOTE]
> A versão anterior da solução de capacidade e desempenho chamada de gerenciamento de capacidade necessária do System Center Operations Manager e o System Center Virtual Machine Manager. Esta solução atualizada não tem essas dependências.


## <a name="connected-sources"></a>Origens ligadas

A tabela seguinte descreve as origens ligadas que são suportadas por esta solução.

| Origem Ligada | Suporte | Descrição |
|---|---|---|
| [Agentes do Windows](../../azure-monitor/platform/agent-windows.md) | Sim | A solução recolhe informações de dados de capacidade e desempenho de agentes do Windows. |
| [Agentes do Linux](../../azure-monitor/learn/quick-collect-linux-computer.md) | Não    | A solução não recolhe informações de dados de capacidade e desempenho de agentes diretos do Linux.|
| [Grupo de gestão do SCOM](../../azure-monitor/platform/om-agents.md) | Sim |A solução recolhe dados de capacidade e desempenho de agentes num grupo de gestão ligado do SCOM. Não é necessária uma conexão direta do agente do SCOM para o Log Analytics.|
| [Conta de armazenamento do Azure](../../azure-monitor/platform/collect-azure-metrics-logs.md) | Não | O armazenamento do Azure não inclui dados de capacidade e desempenho.|

## <a name="prerequisites"></a>Pré-requisitos

- Windows ou agentes do Operations Manager tem de estar instalados no Windows Server 2012 ou superior anfitriões de Hyper-V, não as máquinas virtuais.


## <a name="configuration"></a>Configuração

Execute o passo seguinte para adicionar a solução de capacidade e desempenho à sua área de trabalho.

- Adicionar a solução de capacidade e desempenho à sua área de trabalho do Log Analytics através do processo descrito em [adicionar soluções Log Analytics da Galeria de soluções](../../azure-monitor/insights/solutions.md).

## <a name="management-packs"></a>Pacotes de gestão

Se o grupo de gestão do SCOM é ligado à sua área de trabalho do Log Analytics, em seguida, os seguintes pacotes de gestão serão instalados no SCOM quando adicionar esta solução. Estes pacotes de gestão não precisam de configurações nem de manutenção.

- Microsoft.IntelligencePacks.CapacityPerformance

O evento de 1201 é semelhante:


```
New Management Pack with id:"Microsoft.IntelligencePacks.CapacityPerformance", version:"1.10.3190.0" received.
```

Quando a solução de capacidade e desempenho é atualizada, o número de versão será alterado.

Para obter mais informações sobre como são atualizados os pacotes de gestão da solução, veja [Connect Operations Manager to Log Analytics (Ligar o Operations Manager ao Log Analytics)](../../azure-monitor/platform/om-agents.md).

## <a name="using-the-solution"></a>Utilizar a solução

Quando adiciona a solução de capacidade e desempenho para a área de trabalho, é adicionada a capacidade e desempenho para o dashboard de descrição geral. Este mosaico apresenta uma contagem do número de anfitriões de Hyper-V atualmente ativas e o número de máquinas virtuais ativas que foram monitorizados para o período de tempo selecionado.

![Mosaico de capacidade e desempenho](./media/capacity-performance/capacity-tile.png)


### <a name="review-utilization"></a>Utilização de revisão

Clique no mosaico da capacidade e desempenho para abrir o dashboard de capacidade e desempenho. O dashboard inclui as colunas da tabela seguinte. Cada coluna apresenta uma lista de até dez itens que correspondem aos critérios dessa coluna para o âmbito e o intervalo de tempo especificados. Pode executar uma pesquisa de registos que devolve todos os registos ao clicar em **Ver tudo**, na parte inferior da coluna, ou ao clicar no cabeçalho da coluna.

- **Anfitriões**
    - **Utilização da CPU do anfitrião** mostra uma tendência gráfica da utilização da CPU de computadores anfitriões e uma lista de anfitriões, com base no período de tempo selecionado. Paire o rato sobre o gráfico de linhas para ver os detalhes de um ponto específico no tempo. Clique no gráfico para ver mais detalhes na pesquisa de registos. Clique em qualquer nome de anfitrião para abrir a pesquisa de registos e ver detalhes do contador de CPU para as VMs alojadas.
    - **Utilização da memória do anfitrião** mostra uma tendência gráfica da utilização da memória de computadores anfitriões e uma lista de anfitriões, com base no período de tempo selecionado. Paire o rato sobre o gráfico de linhas para ver os detalhes de um ponto específico no tempo. Clique no gráfico para ver mais detalhes na pesquisa de registos. Clique em qualquer nome de anfitrião para abrir a pesquisa de registos e ver detalhes do contador de memória para VMs alojadas.
- **Máquinas Virtuais**
    - **Utilização de CPU VM** mostra uma tendência gráfica da utilização da CPU de máquinas virtuais e uma lista de máquinas virtuais, com base no período de tempo selecionado. Paire o rato sobre o gráfico de linhas para ver os detalhes de um ponto específico no tempo para as VMs de 3 principais. Clique no gráfico para ver mais detalhes na pesquisa de registos. Clique em qualquer nome VM para abrir a pesquisa de registos e ver os detalhes de contador de CPU agregados para a VM.
    - **Utilização de memória de VM** mostra uma tendência gráfica da utilização da memória das máquinas virtuais e uma lista de máquinas virtuais, com base no período de tempo selecionado. Paire o rato sobre o gráfico de linhas para ver os detalhes de um ponto específico no tempo para as VMs de 3 principais. Clique no gráfico para ver mais detalhes na pesquisa de registos. Clique em qualquer nome VM para abrir a pesquisa de registos e ver detalhes do contador de agregados de memória para a VM.
    - **VM Total de IOPS de disco** mostra uma tendência gráfica do disco total IOPS para máquinas virtuais e uma lista de máquinas virtuais com o IOPS para cada, com base no período de tempo selecionado. Paire o rato sobre o gráfico de linhas para ver os detalhes de um ponto específico no tempo para as VMs de 3 principais. Clique no gráfico para ver mais detalhes na pesquisa de registos. Clique em qualquer nome VM para abrir a pesquisa de registos e ver detalhes de contador de agregados de IOPS de disco para a VM.
    - **Débito de disco Total de VM** mostra uma tendência gráfica da taxa de transferência total do disco para máquinas virtuais e uma lista de máquinas virtuais com o débito de disco total para cada, com base no período de tempo selecionado. Paire o rato sobre o gráfico de linhas para ver os detalhes de um ponto específico no tempo para as VMs de 3 principais. Clique no gráfico para ver mais detalhes na pesquisa de registos. Clique em qualquer nome VM para abrir a pesquisa de registos e ver detalhes de contador de débito agregado total do disco para a VM.
- **Volumes Partilhados de cluster**
    - **Total de débito** mostra a soma de ambas as leituras e gravações nos volumes partilhados em cluster.
    - **Total de IOPS** mostra a soma das operações de entrada/saída por segundo em volumes partilhados em cluster.
    - **Total de latência** mostra a latência total em volumes partilhados de cluster.
- **Densidade de anfitrião** o mosaico superior mostra o número total de anfitriões e máquinas virtuais disponíveis para a solução. Clique no mosaico superior para ver detalhes adicionais na pesquisa de registos. Também apresenta uma lista de todos os anfitriões e o número de máquinas virtuais que estão alojados. Clique num host para explorar os resultados VM numa pesquisa de registos.


![Painel de anfitriões do dashboard](./media/capacity-performance/dashboard-hosts.png)

![Painel de máquinas virtuais de dashboard](./media/capacity-performance/dashboard-vms.png)


### <a name="evaluate-performance"></a>Avaliar o desempenho

Ambientes de computação de produção diferir significativamente de uma organização para outra. Além disso, as cargas de trabalho de capacidade e desempenho poderão estar dependentes de como as VMs estão em execução, e o que considere normal. Procedimentos específicos para o ajudar a medir o desempenho seria provavelmente não se aplicam ao seu ambiente. Portanto, mais generalizada prescritivas é mais adequada para o ajudar a documentação de orientação. A Microsoft publica uma variedade de artigos de orientação prescritiva para ajudar a medir o desempenho.

Para resumir, a solução recolhe dados de capacidade e desempenho de uma variedade de origens, incluindo os contadores de desempenho. Utilizar esses dados de capacidade e desempenho apresentado em várias superfícies na solução e comparar os resultados para as à [avaliando o desempenho no Hyper-V](https://msdn.microsoft.com/library/cc768535.aspx) artigo. Embora o artigo foi publicado há algum tempo, as métricas, considerações e diretrizes ainda são válidas. O artigo contém links para outros recursos úteis.


## <a name="sample-log-searches"></a>Pesquisas de registo de exemplo

A tabela seguinte disponibiliza pesquisas de registo de exemplo para dados de capacidade e desempenho recolhidos e calculado por esta solução.


| Consulta | Descrição |
|:--- |:--- |
| Todas as configurações de memória do anfitrião | Desempenho &#124; onde ObjectName = = "Capacidade e desempenho" e CounterName = = "Anfitrião MB de memória atribuída" &#124; MB de resumir = avg(CounterValue) por InstanceName |
| Todas as configurações de memória VM | Desempenho &#124; onde ObjectName = = "Capacidade e desempenho" e CounterName = = "VM MB de memória atribuída" &#124; MB de resumir = avg(CounterValue) por InstanceName |
| Divisão do IOPS de disco Total entre todas as VMs | Desempenho &#124; onde ObjectName = = "Capacidade e desempenho" e (CounterName = = "VHD leituras/s" ou CounterName = = "VHD escritas/s") &#124; resumir AggregatedValue = avg(CounterValue) por bin (TimeGenerated, 1 hora), CounterName, InstanceName |
| Divisão do débito de disco Total entre todas as VMs | Desempenho &#124; onde ObjectName = = "Capacidade e desempenho" e (CounterName = = "VHD leitura MB/s" ou CounterName = = "MB/s de escrita do VHD") &#124; resumir AggregatedValue = avg(CounterValue) por bin (TimeGenerated, 1 hora), CounterName, InstanceName |
| Divisão do IOPS Total entre todas as CSVs | Desempenho &#124; onde ObjectName = = "Capacidade e desempenho" e (CounterName = = "Leituras/s CSV" ou CounterName = = "CSV escritas/s") &#124; resumir AggregatedValue = avg(CounterValue) por bin (TimeGenerated, 1 hora), CounterName, InstanceName |
| Divisão do débito Total entre todas as CSVs | Desempenho &#124; onde ObjectName = = "Capacidade e desempenho" e (CounterName = = "Leituras/s CSV" ou CounterName = = "CSV escritas/s") &#124; resumir AggregatedValue = avg(CounterValue) por bin (TimeGenerated, 1 hora), CounterName, InstanceName |
| Divisão da latência Total entre todas as CSVs | Desempenho &#124; onde ObjectName = = "Capacidade e desempenho" e (CounterName = = "Latência de leitura do CSV" ou CounterName = = "Latência de escrita de CSV") &#124; resumir AggregatedValue = avg(CounterValue) por bin (TimeGenerated, 1 hora), CounterName, InstanceName |


## <a name="next-steps"></a>Passos Seguintes
* Uso [pesquisas de registos no Log Analytics](../../azure-monitor/log-query/log-query-overview.md) para ver os dados detalhados de capacidade e desempenho.
