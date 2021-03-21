---
title: Solução de capacidade e desempenho no Azure Monitor | Microsoft Docs
description: Utilize a solução de capacidade e desempenho no Monitor para o ajudar a compreender a capacidade dos seus servidores Hiper-V.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/13/2017
ms.openlocfilehash: aa08b9f5db5e6371bcd20b473a0c755db3199e7c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101704298"
---
# <a name="plan-hyper-v-virtual-machine-capacity-with-the-capacity-and-performance-solution-deprecated"></a>Plan Hyper-V capacidade de máquina virtual com a solução capacidade e desempenho (depreciada)

![Símbolo de capacidade e desempenho](./media/capacity-performance/capacity-solution.png)

> [!NOTE]
> A solução capacidade e desempenho foi depreciada.  Os clientes que já instalaram a solução podem continuar a usá-la, mas a Capacidade e o Desempenho não podem ser adicionados a novos espaços de trabalho.

Pode utilizar a solução capacidade e desempenho no Monitor para o ajudar a compreender a capacidade dos seus servidores Hiper-V. A solução fornece insights sobre o seu ambiente Hiper-V mostrando-lhe a utilização geral (CPU, memória e disco) dos anfitriões e dos VMs em execução nesses anfitriões Hiper-V. As métricas são recolhidas para CPU, memória e discos em todos os seus anfitriões e os VMs que estão a funcionar neles.

A solução:

-   Mostra anfitriões com CPU mais alto e mais baixo e utilização da memória
-   Mostra VMs com CPU mais alto e mais baixo e utilização da memória
-   Mostra VMs com iops mais alto e mais baixo e utilização de produção
-   Mostra quais vMs estão a ser executando em que anfitriões
-   Mostra os discos superiores com alta produção, IOPS e latência em volumes compartilhados de cluster
- Permite-lhe personalizar e filtrar com base em grupos

> [!NOTE]
> A versão anterior da solução capacidade e desempenho chamada Gestão de Capacidades exigia tanto o Gestor de Operações do System Center como o Gestor de Máquinas Virtual do System Center. Esta solução atualizada não tem essas dependências.


## <a name="connected-sources"></a>Origens ligadas

A tabela seguinte descreve as origens ligadas que são suportadas por esta solução.

| Origem Ligada | Suporte | Description |
|---|---|---|
| [Agentes do Windows](../agents/agent-windows.md) | Yes | A solução recolhe informações de capacidade e dados de desempenho de agentes do Windows. |
| [Agentes do Linux](../vm/quick-collect-linux-computer.md) | No    | A solução não recolhe informações de capacidade e dados de desempenho de agentes linux diretos.|
| [Grupo de gestão SCOM](../agents/om-agents.md) | Yes |A solução recolhe dados de capacidade e desempenho de agentes de um grupo de gestão SCOM conectado. Não é necessária uma ligação direta do agente SCOM ao Log Analytics.|
| [Conta de armazenamento Azure](../essentials/resource-logs.md#send-to-log-analytics-workspace) | No | O armazenamento do azul não inclui dados de capacidade e desempenho.|

## <a name="prerequisites"></a>Pré-requisitos

- Os agentes do Windows ou Operations Manager devem ser instalados no Windows Server 2012 ou em anfitriões Hiper-V mais altos, e não em máquinas virtuais.


## <a name="configuration"></a>Configuração

Execute o passo seguinte para adicionar a solução capacidade e desempenho ao seu espaço de trabalho.

- Adicione a solução de Capacidade e Desempenho ao seu espaço de trabalho Log Analytics utilizando o processo descrito nas [soluções Add Log Analytics da Galeria de Soluções.](./solutions.md)

## <a name="management-packs"></a>Pacotes de gestão

Se o seu grupo de gestão SCOM estiver ligado ao seu espaço de trabalho Log Analytics, então os seguintes pacotes de gestão serão instalados na SCOM quando adicionar esta solução. Estes pacotes de gestão não precisam de configurações nem de manutenção.

- Microsoft.IntelligencePacks.CapacityPerformance

O evento de 1201 assemelha-se:


```
New Management Pack with id:"Microsoft.IntelligencePacks.CapacityPerformance", version:"1.10.3190.0" received.
```

Quando a solução capacidade e desempenho for atualizada, o número da versão mudará.

Para obter mais informações sobre como são atualizados os pacotes de gestão da solução, veja [Connect Operations Manager to Log Analytics (Ligar o Operations Manager ao Log Analytics)](../agents/om-agents.md).

## <a name="using-the-solution"></a>Utilizar a solução

Quando adiciona a solução capacidade e desempenho ao seu espaço de trabalho, a capacidade e desempenho são adicionados ao painel de visão geral. Este azulejo apresenta uma contagem do número de anfitriões Hiper-V atualmente ativos e o número de máquinas virtuais ativas que foram monitorizadas durante o período de tempo selecionado.

![Capacidade e azulejo de desempenho](./media/capacity-performance/capacity-tile.png)


### <a name="review-utilization"></a>Rever utilização

Clique no azulejo de Capacidade e Desempenho para abrir o painel de capacidade e desempenho. O dashboard inclui as colunas da tabela seguinte. Cada coluna apresenta uma lista de até dez itens que correspondem aos critérios dessa coluna para o âmbito e o intervalo de tempo especificados. Pode executar uma pesquisa de registos que devolve todos os registos ao clicar em **Ver tudo**, na parte inferior da coluna, ou ao clicar no cabeçalho da coluna.

- **Anfitriões**
    - **Utilização cpu anfitrião** Mostra uma tendência gráfica da utilização do CPU dos computadores anfitriões e uma lista de anfitriões, com base no período de tempo selecionado. Passe sobre o gráfico de linha para ver detalhes para um ponto específico no tempo. Clique na tabela para ver mais detalhes na pesquisa de registo. Clique em qualquer nome anfitrião para abrir a pesquisa de registo e ver os dados do contador cpu para VMs hospedados.
    - **Utilização da memória do anfitrião** Mostra uma tendência gráfica da utilização da memória dos computadores anfitriões e uma lista de anfitriões, com base no período de tempo selecionado. Passe sobre o gráfico de linha para ver detalhes para um ponto específico no tempo. Clique na tabela para ver mais detalhes na pesquisa de registo. Clique em qualquer nome anfitrião para abrir a pesquisa de registo e ver os detalhes do contador de memória para VMs hospedados.
- **Máquinas Virtuais**
    - **Utilização do CPU VM** Mostra uma tendência gráfica da utilização do CPU de máquinas virtuais e uma lista de máquinas virtuais, com base no período de tempo selecionado. Passe sobre o gráfico de linha para ver detalhes para um ponto específico no tempo para os 3 VMs mais altos. Clique na tabela para ver mais detalhes na pesquisa de registo. Clique em qualquer nome VM para abrir a pesquisa de registo e ver os detalhes do contador de CPU agregados para o VM.
    - **Utilização da memória VM** Mostra uma tendência gráfica da utilização da memória de máquinas virtuais e uma lista de máquinas virtuais, com base no período de tempo selecionado. Passe sobre o gráfico de linha para ver detalhes para um ponto específico no tempo para os 3 VMs mais altos. Clique na tabela para ver mais detalhes na pesquisa de registo. Clique em qualquer nome VM para abrir a pesquisa de registo e ver os detalhes do contador de memória agregado para o VM.
    - **VM Total Discos IOPS** Mostra uma tendência gráfica do iOPS total do disco para máquinas virtuais e uma lista de máquinas virtuais com o IOPS para cada um, com base no período de tempo selecionado. Passe sobre o gráfico de linha para ver detalhes para um ponto específico no tempo para os 3 VMs mais altos. Clique na tabela para ver mais detalhes na pesquisa de registo. Clique em qualquer nome VM para abrir a pesquisa de registo e ver os detalhes do contador agregado do disco IOPS para o VM.
    - **Produção total de disco VM** Mostra uma tendência gráfica da produção total de disco para máquinas virtuais e uma lista de máquinas virtuais com a produção total de disco para cada uma, com base no período de tempo selecionado. Passe sobre o gráfico de linha para ver detalhes para um ponto específico no tempo para os 3 VMs mais altos. Clique na tabela para ver mais detalhes na pesquisa de registo. Clique em qualquer nome VM para abrir a pesquisa de registo e ver detalhes agregados do contador de registos totais do disco para o VM.
- **Volumes compartilhados agrupados**
    - **Produção total** Mostra a soma de ambos os leituras e escreve em volumes partilhados agrupados.
    - **Total de iOPS** Mostra a soma das operações de entrada/saída por segundo em volumes partilhados agrupados.
    - **Latência total** Mostra a latência total em volumes compartilhados agrupados.
- **Densidade do hospedeiro** O azulejo superior mostra o número total de anfitriões e máquinas virtuais disponíveis para a solução. Clique no azulejo superior para ver detalhes adicionais na pesquisa de registo. Também lista todos os anfitriões e o número de máquinas virtuais que estão hospedadas. Clique num anfitrião para perfurar os resultados do VM numa pesquisa de registo.


![lâmina de anfitriões de tablier](./media/capacity-performance/dashboard-hosts.png)

![lâmina de máquinas virtuais de tablier](./media/capacity-performance/dashboard-vms.png)


### <a name="evaluate-performance"></a>Avaliar o desempenho

Os ambientes de computação de produção diferem muito de uma organização para outra. Além disso, as cargas de trabalho de capacidade e desempenho podem depender de como os seus VMs estão a funcionar, e do que considera normal. Procedimentos específicos para ajudá-lo a medir o desempenho provavelmente não se aplicariam ao seu ambiente. Então, orientação prescritiva mais generalizada é mais adequada para ajudar. A Microsoft publica uma variedade de artigos de orientação prescritivo para ajudá-lo a medir o desempenho.

Resumindo, a solução recolhe dados de capacidade e desempenho de várias fontes, incluindo contadores de desempenho. Utilize essa capacidade e dados de desempenho que apresentaram em várias superfícies na solução e compare os seus resultados com os do [artigo De Medição em Hiper-V.](https://www.microsoft.com/en-us/download/details.aspx?id=56495) Embora o artigo tenha sido publicado há algum tempo, as métricas, considerações e orientações ainda são válidas. O artigo contém ligações a outros recursos úteis.


## <a name="sample-log-searches"></a>Pesquisas de registo de exemplo

A tabela seguinte fornece pesquisas de registo de amostras para dados de capacidade e desempenho recolhidos e calculados por esta solução.


| Consulta | Description |
|:--- |:--- |
| Todas as configurações de memória do anfitrião | Perf &#124; onde ObjectName == "Capacidade e Desempenho" e ContraName == "Host Assigned Memory MB" &#124; resumir MB = avg (CounterValue) by InstanceName |
| Todas as configurações de memória VM | Perf &#124; onde ObjectName == "Capacidade e Desempenho" e ContraName == "VM Memória Atribuída MB" &#124; resumir MB = avg (ContraValue) por ExemploName |
| Desagregação do total de IOPS do disco em todos os VMs | Perf &#124; onde ObjectName == "Capacidade e Desempenho" e (ContraName == "VHD Reads/s" ou CounterName == "VHD Writes/s") &#124; resumir AggregatedValue = avg(CounterValue) by bin (TimeGenerated, 1h), CounterName, InstanceName |
| Desagregação da produção total de disco em todos os VMs | Perf &#124; onde ObjectName == "Capacidade e Desempenho" e (ContraName == "VHD Read MB/s" ou CounterName == "VHD Write MB/s") &#124; resumir AggregatedValue = avg(CounterValue) by bin (TimeGenerated, 1h), CounterName, InstanceName |
| Repartição do Total de IOPS em todos os CSVs | Perf &#124; onde ObjectName == "Capacidade e Desempenho" e (Contraname == "CSV Reads/s" ou CounterName == "CSV Writes/s") &#124; resumir AggregatedValue = avg(CounterValue) by bin (TimeGenerated, 1h), CounterName, InstanceName |
| Repartição da produção total em todos os CSVs | Perf &#124; onde ObjectName == "Capacidade e Desempenho" e (Contraname == "CSV Reads/s" ou CounterName == "CSV Writes/s") &#124; resumir AggregatedValue = avg(CounterValue) by bin (TimeGenerated, 1h), CounterName, InstanceName |
| Desagregação da Latência Total em todos os CSVs | Perf &#124; onde ObjectName == "Capacidade e Desempenho" e (Contraname == "CSV Read Latency" ou CounterName == "CSV Write Latency") &#124; resumir AggregatedValue = avg (CounterValue) by bin (TimeGenerated, 1h), CounterName, InstanceName |


## <a name="next-steps"></a>Passos seguintes
* Utilize pesquisas de Log no Log Analytics para visualizar [dados](../logs/log-query-overview.md) detalhados de capacidade e desempenho.

