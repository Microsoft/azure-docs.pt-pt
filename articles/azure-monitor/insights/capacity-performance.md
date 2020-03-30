---
title: Solução de Capacidade e Desempenho no Monitor Azure [ Monitor De capacidade e desempenho] Microsoft Docs
description: Utilize a solução capacidade e desempenho no Monitor para ajudá-lo a compreender a capacidade dos seus servidores Hyper-V.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/13/2017
ms.openlocfilehash: 75c65cf9f76e711a3aeed764de8b92ed619bad2f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77666948"
---
# <a name="plan-hyper-v-virtual-machine-capacity-with-the-capacity-and-performance-solution-deprecated"></a>Plan hiper-V capacidade de máquina virtual com a solução capacidade e desempenho (depreciada)

![Símbolo de capacidade e desempenho](./media/capacity-performance/capacity-solution.png)

> [!NOTE]
> A solução capacidade e desempenho foi depreciada.  Os clientes que já instalaram a solução podem continuar a utilizá-la, mas a Capacidade e desempenho não podem ser adicionadas a novos espaços de trabalho.

Pode utilizar a solução capacidade e desempenho no Monitor para o ajudar a compreender a capacidade dos seus servidores Hyper-V. A solução fornece insights sobre o seu ambiente Hyper-V mostrando-lhe a utilização geral (CPU, memória e disco) dos anfitriões e dos VMs em execução nesses anfitriões Hiper-V. As métricas são recolhidas para CPU, memória e discos em todos os seus anfitriões e vMs em execução neles.

A solução:

-   Mostra anfitriões com CPU mais alta e mais baixa e utilização de memória
-   Mostra VMs com CPU mais alto e mais baixo e utilização de memória
-   Mostra VMs com IOPS mais alto e mais baixo e utilização de resultados
-   Mostra quais vMs estão correndo em que anfitriões
-   Mostra os discos superiores com alta entrada, IOPS e latência em volumes partilhados de cluster
- Permite-lhe personalizar e filtrar com base em grupos

> [!NOTE]
> A versão anterior da solução de Capacidade e Desempenho chamada Gestão de Capacidade exigia tanto o Gestor de Operações do Centro de Sistema como o Gestor de Máquinas Virtuais do System Center. Esta solução atualizada não tem essas dependências.


## <a name="connected-sources"></a>Origens ligadas

A tabela seguinte descreve as origens ligadas que são suportadas por esta solução.

| Origem Ligada | Suporte | Descrição |
|---|---|---|
| [Agentes do Windows](../../azure-monitor/platform/agent-windows.md) | Sim | A solução recolhe informações de capacidade e de desempenho dos agentes do Windows. |
| [Agentes do Linux](../../azure-monitor/learn/quick-collect-linux-computer.md) | Não    | A solução não recolhe informações de capacidade e de desempenho de agentes diretos do Linux.|
| [Grupo de gestão scom](../../azure-monitor/platform/om-agents.md) | Sim |A solução recolhe dados de capacidade e desempenho de agentes de um grupo de gestão SCOM conectado. Não é necessária uma ligação direta do agente SCOM ao Log Analytics.|
| [Conta de armazenamento azure](../../azure-monitor/platform/collect-azure-metrics-logs.md) | Não | O armazenamento azure não inclui dados de capacidade e desempenho.|

## <a name="prerequisites"></a>Pré-requisitos

- Os agentes do Windows ou DoGestor de Operações devem ser instalados no Windows Server 2012 ou em anfitriões hiper-V superiores, e não em máquinas virtuais.


## <a name="configuration"></a>Configuração

Execute o passo seguinte para adicionar a solução capacidade e desempenho ao seu espaço de trabalho.

- Adicione a solução de capacidade e desempenho ao seu espaço de trabalho Log Analytics utilizando o processo descrito nas [soluções Add Log Analytics da Galeria solutions](../../azure-monitor/insights/solutions.md).

## <a name="management-packs"></a>Pacotes de gestão

Se o seu grupo de gestão SCOM estiver ligado ao seu espaço de trabalho Log Analytics, os seguintes pacotes de gestão serão instalados no SCOM quando adicionar esta solução. Estes pacotes de gestão não precisam de configurações nem de manutenção.

- Microsoft.IntelligencePacks.CapacityPerformance

O evento 1201 assemelha-se:


```
New Management Pack with id:"Microsoft.IntelligencePacks.CapacityPerformance", version:"1.10.3190.0" received.
```

Quando a solução Capacidade e Desempenho for atualizada, o número da versão mudará.

Para obter mais informações sobre como são atualizados os pacotes de gestão da solução, veja [Connect Operations Manager to Log Analytics (Ligar o Operations Manager ao Log Analytics)](../../azure-monitor/platform/om-agents.md).

## <a name="using-the-solution"></a>Utilizar a solução

Quando adiciona a solução capacidade e desempenho ao seu espaço de trabalho, a Capacidade e desempenho são adicionadas ao painel de visão geral. Este azulejo apresenta uma contagem do número de anfitriões hiper-V atualmente ativos e do número de máquinas virtuais ativas que foram monitorizadas durante o período de tempo selecionado.

![Capacidade e telha de desempenho](./media/capacity-performance/capacity-tile.png)


### <a name="review-utilization"></a>Rever a utilização

Clique no azulejo capacidade e desempenho para abrir o painel de capacidade e desempenho. O dashboard inclui as colunas da tabela seguinte. Cada coluna apresenta uma lista de até dez itens que correspondem aos critérios dessa coluna para o âmbito e o intervalo de tempo especificados. Pode executar uma pesquisa de registos que devolve todos os registos ao clicar em **Ver tudo**, na parte inferior da coluna, ou ao clicar no cabeçalho da coluna.

- **Anfitriões**
    - **Utilização do CPU anfitrião** Mostra uma tendência gráfica da utilização do CPU dos computadores hospedeiros e uma lista de anfitriões, com base no período de tempo selecionado. Passe sobre a tabela de linhas para ver detalhes para um determinado ponto no tempo. Clique na tabela para ver mais detalhes na pesquisa de registo. Clique em qualquer nome de anfitrião para abrir a pesquisa de registo e ver detalhes do contador CPU para VMs hospedados.
    - **Utilização da memória** do anfitrião Mostra uma tendência gráfica da utilização da memória dos computadores hospedeiros e uma lista de anfitriões, com base no período de tempo selecionado. Passe sobre a tabela de linhas para ver detalhes para um determinado ponto no tempo. Clique na tabela para ver mais detalhes na pesquisa de registo. Clique em qualquer nome de anfitrião para abrir a pesquisa de registo e ver detalhes do contador de memória para VMs hospedados.
- **Máquinas Virtuais**
    - **Utilização do CPU vm** Mostra uma tendência gráfica da utilização do CPU de máquinas virtuais e uma lista de máquinas virtuais, com base no período de tempo selecionado. Passe sobre a tabela de linhas para ver detalhes para um ponto específico no tempo para os 3 VMs top 3. Clique na tabela para ver mais detalhes na pesquisa de registo. Clique em qualquer nome VM para abrir a pesquisa de registo e ver detalhes agregados do contador cpu para o VM.
    - **Utilização da memória VM** Mostra uma tendência gráfica da utilização da memória de máquinas virtuais e uma lista de máquinas virtuais, com base no período de tempo selecionado. Passe sobre a tabela de linhas para ver detalhes para um ponto específico no tempo para os 3 VMs top 3. Clique na tabela para ver mais detalhes na pesquisa de registo. Clique em qualquer nome VM para abrir a pesquisa de registo e ver detalhes agregados do contador de memória para o VM.
    - **VM Total DeIOTos** de disco Mostra uma tendência gráfica do iOPS total do disco para máquinas virtuais e uma lista de máquinas virtuais com o IOPS para cada um, com base no período de tempo selecionado. Passe sobre a tabela de linhas para ver detalhes para um ponto específico no tempo para os 3 VMs top 3. Clique na tabela para ver mais detalhes na pesquisa de registo. Clique em qualquer nome VM para abrir a pesquisa de registo e ver detalhes de contador iOPS de disco agregado para o VM.
    - **VM Total Disk** Mostra uma tendência gráfica da produção total de disco para máquinas virtuais e uma lista de máquinas virtuais com a entrada total do disco para cada um, com base no período de tempo selecionado. Passe sobre a tabela de linhas para ver detalhes para um ponto específico no tempo para os 3 VMs top 3. Clique na tabela para ver mais detalhes na pesquisa de registo. Clique em qualquer nome VM para abrir a pesquisa de registo e ver detalhes agregados do contador de entrada total do disco para o VM.
- **Volumes partilhados agrupados**
    - **Total de Produção** Mostra a soma de ambas as leituras e escritos em volumes partilhados agrupados.
    - **IOPS totais** Mostra a soma das operações de entrada/saída por segundo em volumes partilhados agrupados.
    - **Latência Total** Mostra a latência total em volumes partilhados agrupados.
- **Densidade do hospedeiro** O azulejo superior mostra o número total de anfitriões e máquinas virtuais disponíveis para a solução. Clique no azulejo superior para ver detalhes adicionais na pesquisa de registo. Também lista todos os anfitriões e o número de máquinas virtuais que estão hospedadas. Clique num hospedeiro para perfurar os resultados do VM numa pesquisa de registo.


![lâmina anfitriã do painel de instrumentos](./media/capacity-performance/dashboard-hosts.png)

![lâmina de máquinas virtuais do painel](./media/capacity-performance/dashboard-vms.png)


### <a name="evaluate-performance"></a>Avaliar o desempenho

Os ambientes de computação de produção diferem muito de uma organização para outra. Além disso, a capacidade e a carga de trabalho de desempenho podem depender de como os seus VMs estão funcionando, e do que considera normal. Procedimentos específicos para ajudá-lo a medir o desempenho provavelmente não se aplicariam ao seu ambiente. Assim, uma orientação prescritiva mais generalizada é mais adequada para ajudar. A Microsoft publica uma variedade de artigos de orientação prescritivos para ajudá-lo a medir o desempenho.

Resumindo, a solução recolhe dados de capacidade e desempenho de várias fontes, incluindo contadores de desempenho. Utilize essa capacidade e dados de desempenho apresentados em várias superfícies na solução e compare os seus resultados com os do desempenho de medição no artigo [Hyper-V.](https://msdn.microsoft.com/library/cc768535.aspx) Embora o artigo tenha sido publicado há algum tempo, as métricas, considerações e orientações ainda são válidas. O artigo contém ligações a outros recursos úteis.


## <a name="sample-log-searches"></a>Pesquisas de registo de exemplo

O quadro seguinte fornece pesquisas de registo de amostras de dados de capacidade e desempenho recolhidos e calculados por esta solução.


| Consulta | Descrição |
|:--- |:--- |
| Todas as configurações de memória do anfitrião | Perf &#124; onde objectname == "Capacidade e desempenho" e ContraNome == "Host Assigned Memory MB" &#124; resumir MB = avg(ContraValue) por ExemploName |
| Todas as configurações de memória VM | Perf &#124; onde objectname == "Capacidade e desempenho" e ContraNome == "VM Assigned Memory MB" &#124; resumo MB = avg(ContraValue) por ExemploName |
| Desagregação do IOPS total do disco em todos os VMs | Perf &#124; onde o ObjectName == "Capacidade e Desempenho" e (ContraNome == "VHD Reads/s" ou ContraNome == "VHD Writes/s") resume&#124; AgregadaValue = avg(CounterValue) por bin (TimeGenerated, 1h), CounterName, InstanceName |
| Avaria da produção total de disco em todos os VMs | Perf &#124; onde o ObjectName == "Capacidade e Desempenho" e (ContraNome == "VHD Read MB/s" ou ContraNome == "VHD Write MB/s") &#124; resumir AgregadValue = avg(CounterValue) por bin (TimeGenerated, 1h), CounterName, InstanceName |
| Desagregação do Total de IOPS em todos os CSVs | Perf &#124; onde o ObjectName == "Capacidade e Desempenho" e (ContraNome == "CSV Reads/s" ou ContraNome == "CSV Writes/s") &#124; resumir AgregadaValue = avg(CounterValue) por bin (TimeGenerated, 1h), CounterName, InstanceName |
| Desagregação da Produção Total em todos os CSVs | Perf &#124; onde o ObjectName == "Capacidade e Desempenho" e (ContraNome == "CSV Reads/s" ou ContraNome == "CSV Writes/s") &#124; resumir AgregadaValue = avg(CounterValue) por bin (TimeGenerated, 1h), CounterName, InstanceName |
| Desagregação da Latência Total em todos os CSVs | Perf &#124; onde o ObjectName == "Capacidade e Desempenho" e (ContraNome == "CSV Read Latência" ou ContraNome == "CSV Write Latency") &#124; resumir AgregadValue = avg(CounterValue) por bin (TimeGenerated, 1h), CounterName, InstanceName |


## <a name="next-steps"></a>Passos seguintes
* Utilize pesquisas de [registo no Log Analytics](../../azure-monitor/log-query/log-query-overview.md) para visualizar dados detalhados de Capacidade e Desempenho.
