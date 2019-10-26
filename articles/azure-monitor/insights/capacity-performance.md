---
title: Capacidade e Desempenho solução no Azure Monitor | Microsoft Docs
description: Use a solução Capacidade e Desempenho no monitor para ajudá-lo a entender a capacidade de seus servidores Hyper-V.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 07/13/2017
ms.openlocfilehash: 8b130b800b53afadc40e0c9b9a2b730f24da396e
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72899063"
---
# <a name="plan-hyper-v-virtual-machine-capacity-with-the-capacity-and-performance-solution-deprecated"></a>Planejar a capacidade da máquina virtual do Hyper-V com a solução de Capacidade e Desempenho (preterida)

![Símbolo de Capacidade e Desempenho](./media/capacity-performance/capacity-solution.png)

> [!NOTE]
> A solução Capacidade e Desempenho foi preterida.  Os clientes que já instalaram a solução podem continuar a usá-la, mas não é possível adicionar Capacidade e Desempenho a nenhum espaço de trabalho novo.

Você pode usar a solução Capacidade e Desempenho no monitor para ajudá-lo a entender a capacidade de seus servidores Hyper-V. A solução fornece informações sobre o ambiente do Hyper-V mostrando a utilização geral (CPU, memória e disco) dos hosts e das VMs em execução nesses hosts Hyper-V. As métricas são coletadas para CPU, memória e discos em todos os seus hosts e nas VMs em execução nelas.

A solução:

-   Mostra os hosts com mais alta e menor utilização de CPU e memória
-   Mostra as VMs com mais alta e menor utilização de CPU e memória
-   Mostra as VMs com IOPS mais alto e mais baixo e utilização de taxa de transferência
-   Mostra quais VMs estão em execução em quais hosts
-   Mostra os principais discos com alta taxa de transferência, IOPS e latência em volumes compartilhados do cluster
- Permite personalizar e filtrar com base em grupos

> [!NOTE]
> A versão anterior da solução de Capacidade e Desempenho chamada gerenciamento de capacidade exigia System Center Operations Manager e System Center Virtual Machine Manager. Essa solução atualizada não tem essas dependências.


## <a name="connected-sources"></a>Origens ligadas

A tabela seguinte descreve as origens ligadas que são suportadas por esta solução.

| Origem Ligada | Suporte | Descrição |
|---|---|---|
| [Agentes do Windows](../../azure-monitor/platform/agent-windows.md) | Sim | A solução coleta informações de dados de desempenho e capacidade de agentes do Windows. |
| [Agentes do Linux](../../azure-monitor/learn/quick-collect-linux-computer.md) | Não    | A solução não coleta informações de dados de desempenho e capacidade de agentes diretos do Linux.|
| [Grupo de gerenciamento do SCOM](../../azure-monitor/platform/om-agents.md) | Sim |A solução coleta dados de desempenho e capacidade de agentes em um grupo de gerenciamento do SCOM conectado. Não é necessária uma conexão direta do agente do SCOM com o Log Analytics.|
| [Conta de armazenamento do Azure](../../azure-monitor/platform/collect-azure-metrics-logs.md) | Não | O armazenamento do Azure não inclui dados de desempenho e capacidade.|

## <a name="prerequisites"></a>Pré-requisitos

- Os agentes do Windows ou Operations Manager devem ser instalados no Windows Server 2012 ou em hosts Hyper-V superiores, não em máquinas virtuais.


## <a name="configuration"></a>Configuração

Execute a etapa a seguir para adicionar a solução de Capacidade e Desempenho ao seu espaço de trabalho.

- Adicione a solução Capacidade e Desempenho ao seu espaço de trabalho do Log Analytics usando o processo descrito em [Adicionar soluções de log Analytics do Galeria de soluções](../../azure-monitor/insights/solutions.md).

## <a name="management-packs"></a>Pacotes de gestão

Se o grupo de gerenciamento do SCOM estiver conectado ao seu espaço de trabalho do Log Analytics, os pacotes de gerenciamento a seguir serão instalados no SCOM quando você adicionar essa solução. Estes pacotes de gestão não precisam de configurações nem de manutenção.

- Microsoft. IntelligencePacks. CapacityPerformance

O evento 1201 é semelhante a:


```
New Management Pack with id:"Microsoft.IntelligencePacks.CapacityPerformance", version:"1.10.3190.0" received.
```

Quando a solução de Capacidade e Desempenho for atualizada, o número de versão será alterado.

Para obter mais informações sobre como são atualizados os pacotes de gestão da solução, veja [Connect Operations Manager to Log Analytics (Ligar o Operations Manager ao Log Analytics)](../../azure-monitor/platform/om-agents.md).

## <a name="using-the-solution"></a>Utilizar a solução

Quando você adiciona a solução Capacidade e Desempenho ao seu espaço de trabalho, a Capacidade e Desempenho é adicionada ao painel Visão geral. Esse bloco exibe uma contagem do número de hosts do Hyper-V atualmente ativos e o número de máquinas virtuais ativas que foram monitoradas para o período de tempo selecionado.

![Bloco Capacidade e Desempenho](./media/capacity-performance/capacity-tile.png)


### <a name="review-utilization"></a>Examinar a utilização

Clique no bloco Capacidade e Desempenho para abrir o painel de Capacidade e Desempenho. O dashboard inclui as colunas da tabela seguinte. Cada coluna apresenta uma lista de até dez itens que correspondem aos critérios dessa coluna para o âmbito e o intervalo de tempo especificados. Pode executar uma pesquisa de registos que devolve todos os registos ao clicar em **Ver tudo**, na parte inferior da coluna, ou ao clicar no cabeçalho da coluna.

- **Hospedar**
    - **Utilização da CPU do host** Mostra uma tendência gráfica da utilização da CPU de computadores host e uma lista de hosts, com base no período de tempo selecionado. Passe o mouse sobre o gráfico de linhas para exibir detalhes de um ponto específico no tempo. Clique no gráfico para exibir mais detalhes na pesquisa de logs. Clique em qualquer nome de host para abrir a pesquisa de logs e exibir detalhes do contador de CPU para VMs hospedadas.
    - **Utilização de memória do host** Mostra uma tendência gráfica da utilização de memória de computadores host e uma lista de hosts, com base no período de tempo selecionado. Passe o mouse sobre o gráfico de linhas para exibir detalhes de um ponto específico no tempo. Clique no gráfico para exibir mais detalhes na pesquisa de logs. Clique em qualquer nome de host para abrir a pesquisa de logs e exibir detalhes do contador de memória para VMs hospedadas.
- **Máquinas Virtuais**
    - **Utilização da CPU da VM** Mostra uma tendência gráfica da utilização da CPU de máquinas virtuais e de uma lista de máquinas virtuais com base no período de tempo selecionado. Passe o mouse sobre o gráfico de linhas para exibir detalhes de um ponto específico no tempo para as três principais VMs. Clique no gráfico para exibir mais detalhes na pesquisa de logs. Clique em qualquer nome de VM para abrir a pesquisa de logs e exibir detalhes do contador de CPU agregado para a VM.
    - **Utilização de memória da VM** Mostra uma tendência gráfica da utilização de memória de máquinas virtuais e uma lista de máquinas virtuais, com base no período de tempo selecionado. Passe o mouse sobre o gráfico de linhas para exibir detalhes de um ponto específico no tempo para as três principais VMs. Clique no gráfico para exibir mais detalhes na pesquisa de logs. Clique em qualquer nome de VM para abrir a pesquisa de logs e exibir detalhes do contador de memória agregada para a VM.
    - **IOPS de disco total da VM** Mostra uma tendência gráfica do IOPS de disco total para máquinas virtuais e uma lista de máquinas virtuais com o IOPS para cada, com base no período de tempo selecionado. Passe o mouse sobre o gráfico de linhas para exibir detalhes de um ponto específico no tempo para as três principais VMs. Clique no gráfico para exibir mais detalhes na pesquisa de logs. Clique em qualquer nome de VM para abrir a pesquisa de logs e exibir detalhes do contador de IOPS de disco agregado para a VM.
    - **Taxa de transferência total do disco da VM** Mostra uma tendência gráfica da taxa de transferência total do disco para máquinas virtuais e uma lista de máquinas virtuais com a taxa de transferência total do disco para cada, com base no período de tempo selecionado. Passe o mouse sobre o gráfico de linhas para exibir detalhes de um ponto específico no tempo para as três principais VMs. Clique no gráfico para exibir mais detalhes na pesquisa de logs. Clique em qualquer nome de VM para abrir a pesquisa de logs e exibir detalhes de contador de taxa de transferência de disco total agregado para a VM.
- **Volumes compartilhados clusterizados**
    - **Taxa de transferência total** Mostra a soma de leituras e gravações em volumes compartilhados clusterizados.
    - **IOPS total** Mostra a soma das operações de entrada/saída por segundo em volumes compartilhados clusterizados.
    - **Latência total** Mostra a latência total em volumes compartilhados clusterizados.
- **Densidade de host** O bloco superior mostra o número total de hosts e máquinas virtuais disponíveis para a solução. Clique no bloco superior para exibir detalhes adicionais na pesquisa de logs. Também lista todos os hosts e o número de máquinas virtuais que são hospedadas. Clique em um host para analisar os resultados da VM em uma pesquisa de log.


![folha de hosts de painel](./media/capacity-performance/dashboard-hosts.png)

![folha de máquinas virtuais do painel](./media/capacity-performance/dashboard-vms.png)


### <a name="evaluate-performance"></a>Avaliar o desempenho

Ambientes de computação de produção diferem muito de uma organização para outra. Além disso, as cargas de trabalho de capacidade e desempenho podem depender de como suas VMs estão em execução e o que você considera normal. Os procedimentos específicos para ajudá-lo a medir o desempenho provavelmente não se aplicariam ao seu ambiente. Portanto, diretrizes prescritivas mais generalizadas são mais adequadas para ajudar. A Microsoft publica uma variedade de artigos de diretrizes prescritivas para ajudá-lo a medir o desempenho.

Para resumir, a solução coleta dados de desempenho e capacidade de uma variedade de fontes, incluindo contadores de desempenho. Use esses dados de desempenho e capacidade apresentados em várias superfícies na solução e compare os resultados com eles no artigo [medindo o desempenho no Hyper-V](https://msdn.microsoft.com/library/cc768535.aspx) . Embora o artigo tenha sido publicado há algum tempo, as métricas, as considerações e as diretrizes ainda são válidas. O artigo contém links para outros recursos úteis.


## <a name="sample-log-searches"></a>Pesquisas de registo de exemplo

A tabela a seguir fornece pesquisas de log de exemplo para dados de desempenho e capacidade coletados e calculados por essa solução.


| Consulta | Descrição |
|:--- |:--- |
| Todas as configurações de memória do host | Perf &#124; , em que ObjectName = = "capacidade e desempenho" e CounterName = = "host atribuído à &#124; memória MB" resumir MB = Méd (Comvalue) por InstanceName |
| Todas as configurações de memória da VM | Desempenho &#124; em que ObjectName = = "capacidade e desempenho" e CounterName = = "VM atribuído à memória &#124; MB" resumir MB = Méd (valor máximo) por InstanceName |
| Divisão do IOPS de disco total em todas as VMs | Perf &#124; , em que ObjectName = = "capacidade e desempenho" e (CounterName = = "VHD reads/s" ou CounterName = = "VHD writes &#124; /s") resume AggregatedValue = AVG (comvalue) por bin (TimeGenerated, 1h), CounterName, InstanceName |
| Divisão da taxa de transferência total do disco em todas as VMs | Perf &#124; , em que ObjectName = = "capacidade e desempenho" e (CounterName = = "VHD Read MB/s" ou CounterName = = "VHD Write MB/s &#124; ") resume AggregatedValue = AVG (comvalue) por bin (TimeGenerated, 1h), CounterName, InstanceName |
| Divisão do IOPS total em todos os CSVs | Perf &#124; , em que ObjectName = = "capacidade e desempenho" e (CounterName = = "CSV reads/s" ou CounterName = = "CSV writes &#124; /s") resume AggregatedValue = AVG (comvalue) por bin (TimeGenerated, 1h), CounterName, InstanceName |
| Divisão da taxa de transferência total em todas as CSVs | Perf &#124; , em que ObjectName = = "capacidade e desempenho" e (CounterName = = "CSV reads/s" ou CounterName = = "CSV writes &#124; /s") resume AggregatedValue = AVG (comvalue) por bin (TimeGenerated, 1h), CounterName, InstanceName |
| Divisão da latência total em todas as CSVs | Perf &#124; , em que ObjectName = = "capacidade e desempenho" e (CounterName = = "latência de leitura de CSV" ou CounterName = = "latência &#124; de gravação de CSV") resume AggregatedValue = AVG (comvalue) por bin (TimeGenerated, 1h), CounterName, InstanceName |


## <a name="next-steps"></a>Passos seguintes
* Use [pesquisas de log no log Analytics](../../azure-monitor/log-query/log-query-overview.md) para exibir dados detalhados de capacidade e desempenho.
